# SKILL - Autonomiczna Księgowa KSeF Agent (PL)

**Wersja:** 2.1.3
**Data:** 2026-02-09
**Stan prawny na dzień:** 8 lutego 2026

**ZASTRZEŻENIE:** Niniejszy dokument stanowi specyfikację kompetencji agenta AI i nie jest oficjalnym stanowiskiem Ministerstwa Finansów. Przedstawione informacje mogą ulec zmianie. Przed podjęciem decyzji biznesowych lub prawnych zaleca się konsultację z wykwalifikowanym doradcą podatkowym.

**⚠️ BEZPIECZEŃSTWO:** Wszystkie przykłady kodu mają charakter edukacyjny. Przed użyciem w produkcji: przeprowadź security review, używaj dedykowanych narzędzi, waliduj wszystkie wejścia, stosuj principle of least privilege.

---

## 🎯 Mission Statement

Jestem autonomiczną księgową-agentem specjalizującą się w kompleksowej obsłudze Krajowego Systemu e-Faktur (KSeF). Działam w środowisku KSeF 2.0 ze strukturą FA(3). Potrafię wykonywać zadania księgowe związane z fakturowaniem elektronicznym w Polsce, wspierając użytkowników w zachowaniu zgodności z obowiązującymi przepisami.

---

## 📅 Stan Prawny (Skrót)

**UWAGA:** Harmonogram wdrożenia KSeF oraz szczegóły przepisów mogą ulec zmianie.

**📄 Szczegóły prawne:** `{baseDir}/ksef-legal-status.md`

### Kluczowe Daty (planowane)
- **1 lutego 2026** - KSeF 2.0 produkcja, FA(3) obowiązkowa
- **1 kwietnia 2026** - obowiązek wystawiania dla firm ≤200 mln PLN
- **1 stycznia 2027** - obowiązek wystawiania dla mikroprzedsiębiorców
- **31 grudnia 2026** - planowany koniec grace period (brak kar)

### Środowisko Techniczne
```
DEMO:       https://ksef-demo.mf.gov.pl
PRODUKCJA:  https://ksef.mf.gov.pl
API DOCS:   https://ksef.mf.gov.pl/api/docs
```

**Wymagania:**
- Struktura: FA(3) ver. 1-0E
- Format: XML zgodny ze schematem
- Walidacja: automatyczna przy przyjęciu

---

## 🎓 Core Competencies

### 1. Obsługa KSeF 2.0 API

**Potrafię:**
- ✅ Wystawiać faktury FA(3) przez API
- ✅ Pobierać faktury zakupowe automatycznie
- ✅ Zarządzać sesjami i tokenami
- ✅ Obsługiwać tryb Offline24 (awaryjny)
- ✅ Pobierać UPO (Urzędowe Poświadczenie Odbioru)

**Quick Reference:**
```http
# Inicjalizacja sesji
POST /api/online/Session/InitToken
{"context": {"token": "YOUR_TOKEN"}}

# Wysłanie faktury
POST /api/online/Invoice/Send
Authorization: SessionToken {token}
[FA(3) XML Content]

# Sprawdzenie statusu
GET /api/online/Invoice/Status/{ReferenceNumber}

# Pobieranie faktur zakupowych
POST /api/online/Query/Invoice/Sync
{"queryCriteria": {"type": "range", ...}}
```

**📄 Pełna dokumentacja API:** `{baseDir}/ksef-api-reference.md`

---

### 2. Struktura FA(3)

**Znam różnice FA(3) vs FA(2):**
- Załączniki do faktur
- Nowy typ kontrahenta: PRACOWNIK
- Rozszerzone formaty konta bankowego
- Limit 50 000 pozycji w korekcie
- Identyfikatory JST i grup VAT

**UWAGA:** Przykłady XML mają charakter poglądowy.

**📄 Przykłady FA(3):** `{baseDir}/ksef-fa3-examples.md`

---

### 3. Automatyczne Księgowanie

**Workflow Sprzedaży:**
```
Dane → Generuj FA(3) → Wyślij KSeF → Pobierz nr KSeF → Księguj
Wn 300 (Rozrachunki) | Ma 700 (Sprzedaż) + Ma 220 (VAT należny)
```

**Workflow Zakupów:**
```
Odpytuj KSeF → Pobierz XML → Klasyfikuj AI → Księguj
Wn 400-500 (Koszty) + Wn 221 (VAT) | Ma 201 (Rozrachunki)
```

**📄 Szczegółowe przepływy księgowe:** `{baseDir}/ksef-accounting-workflows.md`

---

### 4. Klasyfikacja Kosztów (Wspomagana AI)

**UWAGA:** AI pełni rolę wspierającą, nie zastępuje osądu księgowego. Wskaźniki dokładności są celami projektowymi.

**Algorytm (wysokopoziomowy):**
1. Sprawdź historię z kontrahentem (confidence > 0.9)
2. Dopasuj słowa kluczowe
3. Model ML (Random Forest / Neural Network)
4. Jeśli confidence < 0.8 → flaguj do review

**Typowe kategorie:**
- 400-406: Usługi obce (transport, IT, prawne, marketing, księgowe)
- 500-502: Materiały, energia, biuro

**📄 Szczegóły klasyfikacji AI:** `{baseDir}/ksef-ai-features.md#klasyfikacja`

---

### 5. Dopasowywanie Płatności

**Scoring (wysokopoziomowy):**
- Dokładna kwota (+/- 0.01 PLN): +40 pkt
- NIP w tytule: +30 pkt
- Numer faktury: +20 pkt
- Data w zakresie (±7 dni): +10 pkt
- Numer KSeF: +25 pkt

**Auto-match jeśli score ≥ 70**

**📄 Szczegóły algorytmu:** `{baseDir}/ksef-accounting-workflows.md#dopasowywanie-platnosci`

---

### 6. Mechanizm Podzielonej Płatności (MPP)

**Warunki (zgodnie z obecnymi przepisami):**
- Faktury >15 000 PLN brutto
- Towary z załącznika 15 do ustawy VAT

**Obsługa:** 2 przelewy (netto + VAT na osobne konta)

**📄 Szczegóły MPP:** `{baseDir}/ksef-accounting-workflows.md#mpp`

---

### 7. Rejestry VAT i JPK_V7

**Potrafię generować:**
- ✅ Rejestr sprzedaży (Excel/PDF)
- ✅ Rejestr zakupów (Excel/PDF)
- ✅ JPK_V7M (miesięczny XML)
- ✅ JPK_V7K (kwartalny XML)

**UWAGA:** Przykłady XML mają charakter poglądowy. **📄 Przykłady JPK_V7:** `{baseDir}/ksef-jpk-examples.md`

---

### 8. Faktury Korygujące

**Proces w KSeF 2.0:**
```mermaid
graph LR
    A[Potrzeba korekty] --> B[Pobierz oryginał z KSeF]
    B --> C["Utwórz FA(3) korekty"]
    C --> D[Powiąż z nr KSeF oryginału]
    D --> E[Wyślij do KSeF]
    E --> F[Księguj storno/różnicowe]
```

**Metody księgowania:**
- Storno oryginału + nowa wartość
- Metoda różnicowa

**📄 Szczegóły korekt:** `{baseDir}/ksef-accounting-workflows.md#korekty`

---

### 9. Compliance i Bezpieczeństwo

**Biała Lista VAT:**
- ✅ Automatyczna weryfikacja kontrahenta przed każdą płatnością
- ✅ Sprawdzanie statusu VAT (czynny/nieczynny)
- ✅ Weryfikacja konta bankowego na białej liście
- ✅ Blokada płatności jeśli weryfikacja negatywna

**Bezpieczeństwo danych:**
- ✅ Szyfrowane przechowywanie tokenów (Fernet/Vault)
- ✅ Audit trail wszystkich operacji
- ✅ Strategia backup 3-2-1
- ✅ Disaster recovery (sync z KSeF)

**📄 Szczegóły compliance:** `{baseDir}/ksef-security-compliance.md`

---

### 10. Wykrywanie Anomalii i Fraudu (AI)

**UWAGA:** AI wykrywa potencjalne anomalie wymagające weryfikacji. Nie podejmuje wiążących decyzji.

**Detekcja:**
- ✅ Nietypowe kwoty (Isolation Forest)
- ✅ Phishing invoices (podobna nazwa, inne konto)
- ✅ VAT carousel (cykle transakcji)
- ✅ Anomalie czasowe (weekend, noc)

**Działanie:** Flagowanie do manual review + alert HIGH

**📄 Szczegóły fraud detection:** `{baseDir}/ksef-ai-features.md#fraud-detection`

---

### 11. Predykcja Cash Flow (AI)

**UWAGA:** Predykcje mają charakter szacunkowy, wspierają planowanie finansowe.

**Model predykcyjny (Random Forest):**
- Historia płatności kontrahenta
- Kwota faktury
- Termin płatności
- Miesiąc / koniec kwartału

**Wykorzystanie:** Prognoza miesięcznych przychodów/wydatków

**📄 Szczegóły predykcji:** `{baseDir}/ksef-ai-features.md#cash-flow`

---

### 12. Integracje Zewnętrzne

**UWAGA:** Przykłady mają charakter koncepcyjny. Wymagają dostosowania do konkretnych wersji API.

**Obsługiwane systemy:**
- ✅ Bankowość (PSD2 API) - pobieranie transakcji, planowanie płatności
- ✅ ERP (SAP, Comarch, inne) - sync faktur, mapowanie kontrahentów
- ✅ CRM (Salesforce, HubSpot) - generowanie faktur z opportunities
- ✅ Własne API - REST endpoints dla systemów zewnętrznych

**📄 Szczegóły integracji:** `{baseDir}/ksef-integrations.md`

---

### 13. KPIs i Monitoring

**Typowe metryki:**
- Uptime systemu
- Czas przetwarzania faktury
- Sukces API KSeF
- Wskaźnik auto-klasyfikacji
- Wskaźnik auto-matchingu płatności
- Wykryte anomalie
- Alerty fraud

**📄 Przykładowy dashboard:** `{baseDir}/ksef-monitoring.md`

---

## 🚨 Troubleshooting (Quick Reference)

### Faktura odrzucona (400/422)
**Przyczyny:** Nieprawidłowy XML/NIP/data/brak pól
**Rozwiązanie:** Sprawdź encoding UTF-8, waliduj schemat FA(3), weryfikuj NIP

### Timeout API
**Przyczyny:** Awaria KSeF / problem sieciowy / godziny szczytu
**Rozwiązanie:** Sprawdź status KSeF, testuj sieć, retry z backoff

### Nie można dopasować płatności
**Przyczyny:** Niezgodna kwota / brak danych / split payment
**Rozwiązanie:** Rozszerzone wyszukiwanie (±2%, ±14 dni), sprawdź MPP

**📄 Pełny troubleshooting guide:** `{baseDir}/ksef-troubleshooting.md`

---

## 📚 Zasoby i Dokumentacja

### Oficjalne
- Portal KSeF: https://ksef.podatki.gov.pl
- Demo: https://ksef-demo.mf.gov.pl
- Produkcja: https://ksef.mf.gov.pl
- Biała Lista VAT: https://wl-api.mf.gov.pl

### Repozytoria CIRFMF
- ksef-docs: https://github.com/CIRFMF/ksef-docs
- ksef-client-java: https://github.com/CIRFMF/ksef-client-java
- ksef-client-csharp: https://github.com/CIRFMF/ksef-client-csharp
- ksef-latarnia: https://github.com/CIRFMF/ksef-latarnia

### Dokumentacja wewnętrzna (dla ludzi)
1. [Stan prawny i harmonogram](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-legal-status.md)
2. [API Reference](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-api-reference.md)
3. [Przykłady FA(3)](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-fa3-examples.md)
4. [Przepływy księgowe](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-accounting-workflows.md)
5. [Funkcje AI](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-ai-features.md)
6. [Integracje](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-integrations.md)
7. [Security & Compliance](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-security-compliance.md)
8. [Troubleshooting](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-troubleshooting.md)
9. [Monitoring](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-monitoring.md)

### Dokumentacja wewnętrzna (dla agentów AI)
1. `{baseDir}/ksef-legal-status.md` - Stan prawny i harmonogram
2. `{baseDir}/ksef-api-reference.md` - API Reference
3. `{baseDir}/ksef-fa3-examples.md` - Przykłady FA(3)
4. `{baseDir}/ksef-accounting-workflows.md` - Przepływy księgowe
5. `{baseDir}/ksef-ai-features.md` - Funkcje AI
6. `{baseDir}/ksef-integrations.md` - Integracje
7. `{baseDir}/ksef-security-compliance.md` - Security & Compliance
8. `{baseDir}/ksef-troubleshooting.md` - Troubleshooting
9. `{baseDir}/ksef-monitoring.md` - Monitoring

---

## 🔄 Historia Wersji

**v2.1.3 (9 lutego 2026)**
- Zmiana wszystkich relatywnych linków markdown na absolutne (GitHub)
- Poprawka kompatybilności z clawhub.ai

**v2.1 (9 lutego 2026)**
- Refactor do struktury progressive disclosure (główny plik ~400 linii)
- Wydzielenie szczegółów do osobnych dokumentów referencyjnych
- Zachowanie esencji kompetencji w głównym pliku

**v2.0 (8 lutego 2026)**
- Dodane zastrzeżenia prawne i techniczne
- Złagodzenie twardych deklaracji AI/ML
- Oznaczenie przykładów jako poglądowe

**v1.0 (1 stycznia 2026)**
- Pierwsza wersja dokumentu

---

## ⚡ Quick Start

### Dla nowych użytkowników:
1. Przeczytaj [Stan prawny](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-legal-status.md) - sprawdź czy obowiązek dotyczy Ciebie
2. Zapoznaj się z [API Reference](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-api-reference.md) - podstawy integracji
3. Zobacz [Przykłady FA(3)](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-fa3-examples.md) - struktura faktur

### Dla integratorów:
1. [API Reference](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-api-reference.md) - pełna dokumentacja endpointów
2. [Integracje](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-integrations.md) - przykłady dla ERP/CRM/Bank
3. [Security & Compliance](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-security-compliance.md) - wymagania bezpieczeństwa

### Dla księgowych:
1. [Przepływy księgowe](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-accounting-workflows.md) - automatyzacja
2. [Funkcje AI](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-ai-features.md) - klasyfikacja i matching
3. [Monitoring](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-monitoring.md) - KPIs i dashboardy

---

## 📞 Wsparcie

**Problemy techniczne:** Sprawdź [Troubleshooting](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-troubleshooting.md)
**Pytania prawne:** Konsultacja z doradcą podatkowym
**Zgłoszenia:** github.com/CIRFMF

---

**ZASTRZEŻENIE KOŃCOWE:**

Niniejszy dokument stanowi specyfikację kompetencji agenta AI wspierającego obsługę KSeF. Wszystkie informacje odzwierciedlają stan wiedzy na dzień sporządzenia i mogą nie być aktualne. Dokument nie stanowi porady prawnej ani podatkowej. Przed wdrożeniem zaleca się:
- Konsultację z doradcą podatkowym
- Weryfikację aktualnego stanu prawnego
- Testy w środowisku demonstracyjnym
- Przegląd bezpieczeństwa i zgodności z RODO

**Licencja:** MIT
**Opracowanie:** Autonomous Accounting AI Team
**Źródło:** github.com/CIRFMF
