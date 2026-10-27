# loan-application-eform — elektroniczny formularz wniosku kredytowego

Aplikacja webowa, w której pracownik banku wypełnia wniosek kredytowy: z walidacją pól,
sekcjami warunkowymi, zablokowanymi klauzulami prawnymi i podglądem dokumentu na żywo.
Wynik to XML zgodny ze schematem z `correspondence-engine`, z którego silnik robi PDF.
Koncepcja jak w interaktywnych modułach systemów CCM (Exstream Empower, Quadient Interactive).

Projekt portfolio. Pokazuje digitalizację druków i połączenie umiejętności webowych
z generowaniem dokumentów. Jakość kodu, historia Gita i README są tak samo ważne jak działanie.

## Status

Start projektu: jest dokumentacja, nie ma jeszcze kodu. Pierwsze zadania:
1. Szkielet Next.js + walidatory PESEL, NIP, IBAN z testami.
2. Formularz wniosku z walidacją pól.
3. Sekcje warunkowe (współkredytobiorca, klient indywidualny / firma).
4. Klauzule zablokowane + edytowalne fragmenty z limitem znaków.
5. Podgląd na żywo, eksport XML, integracja z silnikiem PDF (najpierw decyzja, patrz niżej).

Aktualizuj tę sekcję w PR, który kończy etap.

## Stack

- **Next.js 16** (App Router) · React 19 · TypeScript (strict) · Tailwind CSS 4
- **zod**: jeden schemat walidacji dla formularza i serwera
- **Vitest**: testy walidatorów i budowania XML
- Hosting: Vercel

Next.js 16 różni się od wersji z danych treningowych. Przed użyciem API, którego nie jesteś
pewien, przeczytaj dokumentację w `node_modules/next/dist/docs/`.

Każda nowa zależność wymaga mojej zgody. Wersje sprawdzaj w npm, nie zgaduj z pamięci.

## Otwarte decyzje (rozstrzygnij planem, zanim zaczniesz dane zadanie)

1. **Skąd PDF.** Silnik z `correspondence-engine` działa na Javie, a Vercel jej nie uruchomi.
   Opcje do porównania: silnik jako osobna usługa HTTP (np. kontener), wywołanie wydanego JAR-a
   lokalnie / w CI albo alternatywny wynik: wypełnialny PDF (AcroForm) generowany w JS.
2. **Walidacja XSD po stronie tej aplikacji** (np. biblioteka WASM) czy tylko w silniku.

Decyzję zapisz w `docs/DECISIONS.md`, a potem zaktualizuj tę sekcję.

## Komendy

Uzupełnij, gdy powstanie `package.json`. Docelowo:

```bash
npm run dev          # http://localhost:3000
npm run lint
npm run typecheck    # next typegen && tsc --noEmit
npm test             # Vitest
npm run build
```

Instalacja zależności w istniejącym projekcie: `npm ci` (nie zmienia `package-lock.json`).

## Planowana struktura

```
app/                strony i route handlery
components/form/    sekcje formularza, pola, komunikaty błędów
components/preview/ podgląd dokumentu na żywo
lib/validators/     PESEL, NIP, IBAN, kwoty, daty (czyste funkcje + testy)
lib/schema/         schemat zod wniosku (jedno źródło prawdy dla UI i serwera)
lib/xml/            budowanie XML z danych wniosku
schemas/            kopia XSD z correspondence-engine (przypięta do wersji) + plik SOURCE
content/            treść klauzul prawnych (przykładowych)
docs/               DECISIONS.md
```

## Zasady domeny

**Walidacja**
- PESEL: suma kontrolna i poprawna data urodzenia zakodowana w numerze (z uwzględnieniem stulecia).
- NIP: suma kontrolna z wagami. IBAN: format PL (28 znaków) i kontrola mod 97.
- Kwoty i daty po polsku (`1 234,56 zł`, `dd.MM.yyyy`), na wejściu akceptuj przecinek i kropkę.
- Walidatory to czyste funkcje w `lib/validators/`, każdy z testami na poprawne i błędne
  przypadki (także graniczne). Te same reguły działają w przeglądarce i na serwerze.

**Formularz**
- Sekcje warunkowe: ukryta sekcja nie jest walidowana i nie trafia do XML.
- Klauzule prawne są tylko do odczytu. Edytowalne fragmenty mają widoczny licznik i limit znaków.
- Dostępność: każde pole ma etykietę, błąd jest powiązany z polem (`aria-describedby`),
  formularz da się wypełnić samą klawiaturą. Formularze bankowe muszą to spełniać.
- Podgląd aktualizuje się na bieżąco i pokazuje ten sam tekst, który trafi do dokumentu.

**XML i kontrakt z silnikiem**
- XML musi być zgodny z XSD z `correspondence-engine`. Kopia w `schemas/` jest przypięta do
  konkretnej wersji (tag), a plik `schemas/SOURCE` mówi, do której.
- Nie edytuj XSD w tym repo. Potrzebna zmiana schematu = najpierw issue i zmiana w
  `correspondence-engine`, potem aktualizacja kopii tutaj.
- Test: XML zbudowany z przykładowego wniosku przechodzi walidację XSD.

**Dane osobowe**
- Aplikacja niczego nie zapisuje: brak bazy, brak analityki, dane żyją tylko w sesji formularza.
- Nie loguj PESEL-i, NIP-ów, numerów kont ani treści wniosku (także w konsoli i błędach).
- Dane testowe i przykładowe: wyłącznie fikcyjne, z poprawnymi sumami kontrolnymi.
  Bank w treści to „Bank Przykładowy S.A.”, nigdy prawdziwy.

## Konwencje kodu

- Nazwy w kodzie po angielsku. Interfejs, komentarze, README, issues, commity i PR po polsku.
- Domyślnie Server Components. `"use client"` tylko dla interaktywnych części formularza.
- Kolory i odstępy przez tokeny Tailwinda w `app/globals.css`.
- Komentarze wyjaśniają *dlaczego*, nie *co*.

## Workflow (Git)

1. Gałąź domyślna: `main`. Nie pracuj na `main`. Gałąź na zadanie: `feat/…`, `fix/…`,
   `test/…`, `docs/…`, `chore/…`.
2. Każde zadanie wychodzi od issue (wymaganie biznesowe + kryteria akceptacji).
   Etykiety: `feature`, `bug`, `template`, `data`.
3. Przed kodem: krótki plan i czekaj na moje OK.
4. Commity w Conventional Commits z zakresem, po polsku, np. `feat(walidacja): suma kontrolna PESEL`.
5. Pull Request według szablonu, z `Closes #N` i zrzutem ekranu przy zmianach w UI.
   Vercel buduje podgląd dla każdego PR.
6. Merge, tagi i release robię ja.

## Definicja „gotowe”

- `npm run lint`, `npm run typecheck`, `npm test` i `npm run build` przechodzą,
- nowe walidatory i budowanie XML mają testy,
- formularz sprawdzony na telefonie i desktopie, z klawiatury, z błędnymi i poprawnymi danymi,
- w odpowiedzi wypisane, czego nie dało się sprawdzić.

## Granice

- Nie dodawaj zależności bez pytania.
- Nie dodawaj bazy danych, kont użytkowników, analityki ani cookies.
- Nie edytuj `schemas/` (patrz „XML i kontrakt z silnikiem”).
- Nigdy: `git push --force`, przepisywanie historii, push na `main`, tworzenie tagów i release'ów.
