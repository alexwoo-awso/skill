# Security & Compliance for KSeF

Security requirements, compliance, and best practices.

**⚠️ SECURITY WARNING:**
All code examples in this document are **educational and conceptual**. Before production use:
1. Conduct security review
2. Use dedicated, tested tools instead of custom implementations
3. Never run unverified code from external sources
4. Implement principle of least privilege
5. Regularly update dependencies and conduct security audits

---

## VAT White List

### Automatic Verification

```python
import requests
from datetime import datetime

def verify_contractor_white_list(nip, bank_account, date=None):
    """
    Checks contractor on VAT white list
    API: https://wl-api.mf.gov.pl/
    """
    if date is None:
        date = datetime.now().strftime('%Y-%m-%d')

    url = f"https://wl-api.mf.gov.pl/api/search/nip/{nip}?date={date}"

    try:
        response = requests.get(url, timeout=10)
        response.raise_for_status()
        data = response.json()
    except Exception as e:
        return {
            'valid': False,
            'reason': f'API error: {e}',
            'risk': 'UNKNOWN'
        }

    # Check VAT status
    subject = data['result']['subject']

    if subject['statusVat'] != 'Czynny':
        return {
            'valid': False,
            'reason': f"Contractor VAT: {subject['statusVat']}",
            'risk': 'HIGH',
            'details': subject
        }

    # Check bank account
    accounts = subject.get('accountNumbers', [])

    # Normalize account number (remove spaces)
    bank_account_normalized = bank_account.replace(' ', '')

    for acc in accounts:
        if acc.replace(' ', '') == bank_account_normalized:
            return {
                'valid': True,
                'name': subject['name'],
                'status': subject['statusVat'],
                'verified_account': acc
            }

    # Account not on list
    return {
        'valid': False,
        'reason': 'Bank account not on white list',
        'risk': 'HIGH',
        'valid_accounts': accounts,
        'details': subject
    }
```

### Payment Integration

```python
def before_payment_check(invoice, payment):
    """
    Verification before executing transfer
    """
    # 1. Check white list
    verification = verify_contractor_white_list(
        nip=invoice.seller_nip,
        bank_account=payment.to_account,
        date=payment.date.strftime('%Y-%m-%d')
    )

    if not verification['valid']:
        # Hold payment
        payment.status = 'HOLD'
        payment.hold_reason = verification['reason']

        # Send alert
        send_critical_alert(
            level='HIGH',
            title='Payment held - VAT White List',
            message=f"Contractor: {invoice.seller_name} ({invoice.seller_nip})\n"
                   f"Amount: {payment.amount} PLN\n"
                   f"Reason: {verification['reason']}\n"
                   f"Invoice: {invoice.number}",
            invoice=invoice,
            payment=payment
        )

        return False

    # 2. Check if requires MPP
    if invoice.total_gross > 15000 and invoice.has_attachment_15_goods:
        if payment.type != 'MPP':
            send_warning_alert(
                title='Invoice requires MPP',
                message=f"Invoice {invoice.number} requires split payment mechanism"
            )
            return False

    return True
```

---

## Token Security

### Token Storage

```python
from cryptography.fernet import Fernet
import os

class SecureTokenStorage:
    """
    Encrypted KSeF token storage
    """
    def __init__(self, encryption_key=None):
        if encryption_key is None:
            # Load from environment variable
            encryption_key = os.environ.get('KSEF_ENCRYPTION_KEY')

        if encryption_key is None:
            raise ValueError("Missing encryption key")

        self.cipher = Fernet(encryption_key.encode())

    def store_token(self, token_name, token_value):
        """Store token (encrypted)"""
        encrypted = self.cipher.encrypt(token_value.encode())
        # Save to database or vault
        db.tokens.insert({
            'name': token_name,
            'value': encrypted,
            'created_at': datetime.now()
        })

    def retrieve_token(self, token_name):
        """Retrieve token (decrypt)"""
        record = db.tokens.find_one({'name': token_name})
        if not record:
            raise ValueError(f"Token {token_name} does not exist")

        decrypted = self.cipher.decrypt(record['value'])
        return decrypted.decode()

    def rotate_token(self, token_name, new_token_value):
        """Token rotation (best practice: every 90 days)"""
        # Archive old token
        old_record = db.tokens.find_one({'name': token_name})
        db.tokens_archive.insert({
            **old_record,
            'archived_at': datetime.now()
        })

        # Store new
        self.store_token(token_name, new_token_value)
```

### Vault Integration (HashiCorp)

```python
import hvac

class VaultTokenStorage:
    """
    Storage in HashiCorp Vault (production)
    """
    def __init__(self, vault_url, vault_token):
        self.client = hvac.Client(url=vault_url, token=vault_token)

    def store_token(self, token_name, token_value):
        self.client.secrets.kv.v2.create_or_update_secret(
            path=f'ksef/tokens/{token_name}',
            secret={'value': token_value}
        )

    def retrieve_token(self, token_name):
        secret = self.client.secrets.kv.v2.read_secret_version(
            path=f'ksef/tokens/{token_name}'
        )
        return secret['data']['data']['value']
```

---

## Audit Trail

### Operation Logging

```python
import logging
from datetime import datetime

class AuditLogger:
    """
    Immutable audit log (append-only)
    """
    def __init__(self):
        self.logger = logging.getLogger('ksef_audit')

    def log_operation(self, operation_type, user, entity_type, entity_id, details=None):
        """
        Every operation MUST be logged
        """
        audit_entry = {
            'timestamp': datetime.now().isoformat(),
            'operation': operation_type,  # CREATE, READ, UPDATE, DELETE
            'user': user,
            'entity_type': entity_type,  # INVOICE, PAYMENT, etc.
            'entity_id': entity_id,
            'details': details or {},
            'ip_address': self._get_client_ip(),
            'user_agent': self._get_user_agent(),
            'session_id': self._get_session_id()
        }

        # Save to immutable storage (append-only)
        audit_db.insert(audit_entry)

        # Log to file (for compliance)
        self.logger.info(json.dumps(audit_entry))

    def log_ai_decision(self, invoice, prediction, action):
        """
        Special logging for AI decisions
        """
        self.log_operation(
            operation_type='AI_CLASSIFICATION',
            user='AI_SYSTEM',
            entity_type='INVOICE',
            entity_id=invoice.id,
            details={
                'model_name': 'ExpenseClassifier',
                'model_version': '2.1',
                'prediction': prediction['category'],
                'confidence': prediction['confidence'],
                'action_taken': action,
                'reviewed_by_human': action == 'MANUAL_REVIEW'
            }
        )
```

### Audit Review

```python
def audit_report(start_date, end_date, user=None):
    """
    Generate audit report
    """
    query = {
        'timestamp': {
            '$gte': start_date.isoformat(),
            '$lte': end_date.isoformat()
        }
    }

    if user:
        query['user'] = user

    entries = audit_db.find(query).sort('timestamp', 1)

    report = {
        'period': f"{start_date} - {end_date}",
        'total_operations': 0,
        'by_type': {},
        'by_user': {},
        'entries': []
    }

    for entry in entries:
        report['total_operations'] += 1
        report['by_type'][entry['operation']] = \
            report['by_type'].get(entry['operation'], 0) + 1
        report['by_user'][entry['user']] = \
            report['by_user'].get(entry['user'], 0) + 1
        report['entries'].append(entry)

    return report
```

---

## Backup and Disaster Recovery

### 3-2-1 Strategy

**NOTE:** The following examples are conceptual. In production, use dedicated backup tools (pg_basebackup, Barman, AWS Backup) instead of directly calling shell commands.

```python
import shutil
import subprocess
from datetime import datetime, timedelta
from pathlib import Path

class BackupManager:
    """
    3 copies, 2 media, 1 off-site
    """
    def __init__(self):
        self.backup_local_1 = Path('/backups/local_ssd')
        self.backup_local_2 = Path('/backups/local_hdd')
        self.backup_cloud = 's3://ksef-backups/'
        self.db_name = 'ksef_production'

    def _validate_path(self, path):
        """Path validation (prevent path traversal)"""
        path = Path(path).resolve()
        # Ensure path is in allowed directory
        allowed_dirs = [self.backup_local_1, self.backup_local_2]
        if not any(path.is_relative_to(d) for d in allowed_dirs):
            raise ValueError(f"Invalid path: {path}")
        return path

    def daily_backup(self):
        """
        Daily backup (automatic cron)
        """
        timestamp = datetime.now().strftime('%Y%m%d_%H%M%S')
        db_dump = f'ksef_db_{timestamp}.sql'

        # 1. Database dump (SAFE - use subprocess.run)
        try:
            with open(db_dump, 'w') as f:
                subprocess.run(
                    ['pg_dump', self.db_name],
                    stdout=f,
                    check=True,
                    timeout=3600  # 1 hour max
                )
        except subprocess.CalledProcessError as e:
            raise RuntimeError(f"Backup failed: {e}")

        # 2. Copy 1 - SSD (fast restore)
        shutil.copy(db_dump, self.backup_local_1 / db_dump)

        # 3. Copy 2 - HDD (cheaper medium)
        shutil.copy(db_dump, self.backup_local_2 / db_dump)

        # 4. Copy 3 - Cloud (off-site) - SAFE
        subprocess.run(
            ['aws', 's3', 'cp', db_dump, self.backup_cloud],
            check=True,
            timeout=7200  # 2 hours max
        )

        # 5. Delete old backups (>30 days)
        self.cleanup_old_backups(days=30)

    def restore_from_backup(self, backup_file):
        """
        Restore from backup
        NOTE: In production use dedicated tools (pg_restore, Barman)
        """
        # Path validation
        backup_file = self._validate_path(backup_file)

        if not backup_file.exists():
            raise FileNotFoundError(f"Backup does not exist: {backup_file}")

        try:
            # 1. Stop application (SAFE - hardcoded list)
            subprocess.run(
                ['systemctl', 'stop', 'ksef-app'],
                check=True,
                timeout=60
            )

            # 2. Restore database (SAFE - no shell injection)
            with open(backup_file, 'r') as f:
                subprocess.run(
                    ['psql', self.db_name],
                    stdin=f,
                    check=True,
                    timeout=3600
                )

            # 3. Verification
            if not self.verify_database_integrity():
                raise RuntimeError("Integrity verification failed")

            # 4. Start application
            subprocess.run(
                ['systemctl', 'start', 'ksef-app'],
                check=True,
                timeout=60
            )

            return True

        except Exception as e:
            # Rollback - restart from previous version
            subprocess.run(['systemctl', 'start', 'ksef-app'])
            raise RuntimeError(f"Restore failed: {e}")
```

### KSeF Synchronization

```python
def disaster_recovery():
    """
    Disaster recovery procedure
    """
    # 1. Restore database from latest backup
    latest_backup = find_latest_backup()
    restore_from_backup(latest_backup)

    # 2. Sync with KSeF (last 7 days)
    # KSeF is source of truth for invoices
    last_week = datetime.now() - timedelta(days=7)
    sync_invoices_from_ksef(date_from=last_week)

    # 3. Verify integrity
    mismatches = verify_data_integrity()
    if mismatches:
        log_critical(f"Detected {len(mismatches)} discrepancies")
        notify_admin(mismatches)

    # 4. Notify
    send_notification("System recovered successfully", severity='INFO')
```

---

## GDPR / RODO

### Personal Data in Invoices

```python
def anonymize_invoice_for_archive(invoice, retention_years=10):
    """
    Anonymization after retention period
    """
    retention_date = invoice.issue_date + timedelta(days=365 * retention_years)

    if datetime.now() > retention_date:
        # Data to anonymize (GDPR)
        invoice.buyer_name = "***ANONYMIZED***"
        invoice.buyer_address = "***"
        invoice.buyer_email = None
        invoice.buyer_phone = None

        # Keep NIP (fiscally required)
        # invoice.buyer_nip - KEEP

        invoice.anonymized_at = datetime.now()
        invoice.save()
```

### Data Deletion Request (Right to be Forgotten)

```python
def handle_gdpr_deletion_request(contractor_nip):
    """
    ⚠️ NOTE: Invoices are subject to retention obligation (5-10 years)
    Cannot be deleted during retention period!
    """
    # 1. Check if retention period has passed
    invoices = get_all_invoices_for_contractor(contractor_nip)

    for invoice in invoices:
        retention_date = invoice.issue_date + timedelta(days=365 * 10)

        if datetime.now() < retention_date:
            return {
                'status': 'REJECTED',
                'reason': 'Invoices subject to retention obligation',
                'retention_until': retention_date
            }

    # 2. If period passed - anonymize
    for invoice in invoices:
        anonymize_invoice_for_archive(invoice)

    return {
        'status': 'COMPLETED',
        'anonymized_invoices': len(invoices)
    }
```

---

## Access Control (RBAC)

### Roles and Permissions

```python
ROLES = {
    'ADMIN': [
        'invoice.create', 'invoice.read', 'invoice.update', 'invoice.delete',
        'payment.create', 'payment.read', 'payment.update', 'payment.delete',
        'user.manage', 'settings.manage'
    ],
    'ACCOUNTANT': [
        'invoice.create', 'invoice.read', 'invoice.update',
        'payment.create', 'payment.read', 'payment.update',
        'report.generate'
    ],
    'VIEWER': [
        'invoice.read', 'payment.read', 'report.view'
    ]
}

def check_permission(user, permission):
    """
    Check if user has permission
    """
    user_role = user.role
    allowed_permissions = ROLES.get(user_role, [])

    if permission not in allowed_permissions:
        audit_logger.log_operation(
            operation_type='PERMISSION_DENIED',
            user=user.username,
            entity_type='PERMISSION',
            entity_id=permission
        )
        raise PermissionError(f"User {user.username} lacks permission: {permission}")

    return True
```

---

## SSL/TLS Certificates

### KSeF Connections

```python
import ssl
import certifi

def secure_ksef_connection():
    """
    Always use HTTPS with certificate verification
    """
    session = requests.Session()

    # 1. Verify certificate (NEVER verify=False)
    session.verify = certifi.where()

    # 2. Use strong cipher suites
    session.mount('https://', requests.adapters.HTTPAdapter(
        max_retries=3,
        pool_connections=10,
        pool_maxsize=20
    ))

    # 3. Set timeout
    session.request = lambda *args, **kwargs: \
        requests.Session.request(session, *args, timeout=30, **kwargs)

    return session
```

---

## Secure Coding Practices

### 1. DO NOT use risky functions

**❌ NEVER:**
```python
# DANGEROUS - Command injection
os.system(f"pg_dump {database_name}")  # ❌
eval(user_input)  # ❌
exec(code_from_api)  # ❌
subprocess.run(cmd, shell=True)  # ❌
```

**✅ INSTEAD:**
```python
# SAFE - Argument list, no shell
subprocess.run(
    ['pg_dump', database_name],
    check=True,
    timeout=3600,
    capture_output=True
)
```

### 2. Input Validation

```python
def validate_invoice_number(number):
    """Validate before use in queries"""
    # Only alphanumeric, dashes, slashes
    import re
    if not re.match(r'^[A-Z0-9/-]+$', number):
        raise ValueError("Invalid invoice number")
    if len(number) > 50:
        raise ValueError("Invoice number too long")
    return number
```

### 3. Principle of Least Privilege

```python
# Database user with minimal permissions
DB_CONFIG = {
    'user': 'ksef_readonly',  # Only SELECT for reports
    'user': 'ksef_app',       # SELECT + INSERT + UPDATE for app
    'user': 'ksef_admin',     # All permissions (admin only)
}
```

### 4. Use Dedicated Tools

Instead of custom backup scripts:
- **PostgreSQL:** pg_basebackup, Barman, pgBackRest
- **AWS:** AWS Backup, RDS Automated Backups
- **Monitoring:** Prometheus, Grafana, DataDog

---

## Security Checklist

- [ ] KSeF tokens encrypted (Fernet/Vault)
- [ ] VAT white list checked before each payment
- [ ] Audit trail of all operations
- [ ] 3-2-1 backup (daily)
- [ ] HTTPS with certificate verification
- [ ] RBAC (access control)
- [ ] Retention policy compliant with law (10 years)
- [ ] GDPR - anonymization after retention period
- [ ] Monitoring and alerts
- [ ] Disaster recovery plan (tested quarterly)

---

**Compliance:** Implementation of above practices supports compliance with:
- VAT Act
- GDPR / RODO
- Accounting Act
- ISO 27001 standards (optional)

[← Back to main SKILL](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-en/SKILL.md)
