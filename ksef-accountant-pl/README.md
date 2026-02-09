# KSeF Autonomous Accountant Skill (Polish)

**Skill dla autonomicznego agenta AI wspierającego obsługę Krajowego Systemu e-Faktur (KSeF) w Polsce.**

**🤖 Dla Agentów AI:** Zobacz [SKILL.md](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/SKILL.md) - to twój punkt wejścia.

**👤 Dla Ludzi:** Jesteś we właściwym miejscu - ten README zawiera historię wersji, harmonogram wdrożenia i przegląd dokumentacji.

---

## 📋 Opis

Kompleksowa wiedza i kompetencje do obsługi:
- KSeF 2.0 API (FA(3) struktura)
- Automatyczne księgowanie faktur
- Klasyfikacja kosztów (AI/ML)
- Dopasowywanie płatności
- Wykrywanie anomalii i fraudu
- Predykcja cash flow
- Integracje (ERP, CRM, Banking)
- Compliance (Biała Lista VAT, RODO)

## 📅 Harmonogram Wdrożenia KSeF

**UWAGA:** Harmonogram wdrożenia KSeF oraz szczegóły przepisów mogą ulec zmianie.

### Kluczowe Daty (planowane)
- **1 lutego 2026** - KSeF 2.0 produkcja, FA(3) obowiązkowa (dla firm >200 mln PLN obrotu w 2024)
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

**📄 Pełne szczegóły prawne:** [ksef-legal-status.md](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-legal-status.md)

## 📚 Dokumentacja

Szczegółowa dokumentacja (pliki referencyjne):
- [Stan prawny i harmonogram](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-legal-status.md)
- [API Reference](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-api-reference.md)
- [Przykłady FA(3)](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-fa3-examples.md)
- [Przepływy księgowe](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-accounting-workflows.md)
- [Funkcje AI](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-ai-features.md)
- [Security & Compliance](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-security-compliance.md)
- [Troubleshooting](https://github.com/alexwoo-awso/skill/blob/main/ksef-accountant-pl/ksef-troubleshooting.md)

## 📊 Struktura

Wszystkie pliki w root (flat hierarchy dla clawhub.ai):

```
├── SKILL.md                         (główny plik ~400 linii)
├── ksef-legal-status.md            (stan prawny, harmonogram)
├── ksef-api-reference.md           (API endpoints)
├── ksef-fa3-examples.md            (przykłady XML)
├── ksef-accounting-workflows.md    (przepływy księgowe)
├── ksef-ai-features.md             (AI/ML)
├── ksef-security-compliance.md     (bezpieczeństwo)
├── ksef-troubleshooting.md         (troubleshooting)
├── SECURITY.md                     (security policy)
└── README.md                       (ten plik)
```

## 🌍 Język

**Ta wersja:** Polski (dokumenty w języku polskim)

**English version:** Available at https://clawhub.ai/alexwoo-awso/ksef-accountant-en

## 📝 Wersja

**2.2.0** (9 lutego 2026) - Uporządkowanie struktury

### Historia Wersji

**v2.2.0 (9 lutego 2026)**
- Przeniesienie treści dla ludzi do README.md
- SKILL.md teraz czysty entrypoint dla agentów AI
- Przeniesienie harmonogramu wdrożenia i historii wersji do README
- Zmiana referencji {baseDir} na relatywne linki markdown (./plik.md)
- Rozszerzone zastrzeżenie bezpieczeństwa dla kompatybilności z VirusTotal
- Poprawka wykrywania referencji plików dla importu clawhub.ai

**v2.1.5 (9 lutego 2026)**
- Zmiana referencji {baseDir} na relatywne linki markdown
- Rozszerzone zastrzeżenie bezpieczeństwa
- Poprawka wykrywania plików clawhub.ai

**v2.1.4 (9 lutego 2026)**
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

## 📜 Licencja

MIT

## 🔗 Zasoby

- Portal KSeF: https://ksef.podatki.gov.pl
- CIRFMF GitHub: https://github.com/CIRFMF
- clawhub.ai: https://clawhub.ai/skills/ksef-accountant-pl

---

**UWAGA:** Dokument ma charakter specyfikacji kompetencji agenta AI i nie stanowi porady prawnej ani podatkowej. Przed wdrożeniem zaleca się konsultację z wykwalifikowanym doradcą podatkowym.
