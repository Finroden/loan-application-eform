# loan-application-eform

Elektroniczny formularz wniosku kredytowego dla pracownika banku: walidacja pól, sekcje
warunkowe, zablokowane klauzule prawne i podgląd dokumentu na żywo. Wynik to XML zgodny ze
schematem [correspondence-engine](https://github.com/Finroden/correspondence-engine), z którego powstaje PDF.

> **Status:** w budowie. Postęp widać w [Issues](../../issues) i [Pull Requests](../../pulls).

## Zakres

- [ ] Formularz wniosku z walidacją PESEL, NIP, IBAN, kwot i dat
- [ ] Sekcje warunkowe (np. współkredytobiorca, klient indywidualny / firma)
- [ ] Zablokowane klauzule prawne i edytowalne fragmenty z limitem znaków
- [ ] Podgląd dokumentu na żywo
- [ ] Eksport do XML zgodnego z XSD silnika korespondencji → PDF
- [ ] Alternatywnie: wypełnialny formularz PDF (AcroForm)
- [ ] Dostępność: obsługa klawiaturą, etykiety i komunikaty błędów dla czytników ekranu

## Technologie

Next.js · React · TypeScript · Tailwind CSS · zod · Vitest · XML / XSD

## Uruchomienie

Instrukcja pojawi się razem z pierwszą działającą wersją.

## Dane

Aplikacja niczego nie zapisuje. Wszystkie dane przykładowe są fikcyjne, a „Bank Przykładowy S.A.” nie istnieje.
