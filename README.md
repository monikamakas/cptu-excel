# CPTU → Excel

Aplikacja webowa: wrzucasz kartę CPTU (PDF z wektorowymi wykresami, np. z programu
CPT-Star), a apka odczytuje krzywe qc, fs, u2, Rf(qc) i tworzy gotowy plik Excel
z danymi i wykresami (głębokość jako wartość ujemna, więc wykres wygląda tak samo
jak na karcie PDF).

## Jak to wystawić "na świat" (jednorazowo, ~10 minut)

### 1. Wrzuć te pliki na GitHub
1. Wejdź na [github.com/new](https://github.com/new)
2. Nazwa repozytorium np. `cptu-excel`, ustaw jako **Public** (Streamlit Cloud w darmowej
   wersji wymaga publicznego repo albo połączenia z prywatnym przez Twoje konto)
3. Kliknij **uploading an existing file** i wrzuć wszystkie pliki z tego folderu:
   - `app.py`
   - `extractor.py`
   - `excel_export.py`
   - `requirements.txt`
4. Kliknij **Commit changes**

### 2. Wystaw apkę na Streamlit Community Cloud
1. Wejdź na [share.streamlit.io](https://share.streamlit.io) i zaloguj się przez GitHub
2. Kliknij **New app**
3. Wybierz repozytorium `cptu-excel`, branch `main`, plik główny: `app.py`
4. Kliknij **Deploy**

Po 1-2 minutach dostaniesz link w stylu `https://cptu-excel-xxxxx.streamlit.app` —
ten link możesz wysłać każdemu, kto ma korzystać z apki. Apka jest publiczna
(każdy z linkiem może jej użyć), ale nikt bez linku jej nie znajdzie sam.

### Aktualizacje
Jeśli coś poprawimy w kodzie — wystarczy podmienić pliki na GitHubie (Commit changes),
Streamlit Cloud sam przeładuje apkę w ciągu kilku sekund.

## Jak to działa (skrót techniczny)

- `extractor.py` — czyta PDF na poziomie wektorowym (biblioteka `pdfminer.six`),
  automatycznie znajduje 4 panele wykresu, kalibruje osie na podstawie podpisanych
  liczb i wyciąga punkty krzywych po kolorze linii (qc=niebieski, fs=czerwony,
  u2=bordowy, Rf(qc)=czarny).
- `excel_export.py` — buduje plik `.xlsx` (openpyxl): arkusz z danymi + arkusz
  z prawdziwymi wykresami Excela (typu XY/Scatter, głębokość na osi Y jako wartość
  ujemna — dzięki temu wykres od razu wygląda jak na karcie PDF, bez ręcznego
  odwracania osi).
- `app.py` — interfejs Streamlit: upload, podgląd danych/wykresu, pobranie Excela.

## Ograniczenia (na dziś)

- Działa dla kart PDF z **wektorowymi** wykresami (nie skan/zdjęcie).
- Zakłada standardowy układ 4 paneli: qc, fs, u2, Rf(qc) w tej kolejności
  (tak jak generuje CPT-Star). Inny układ paneli może wymagać dostosowania.
- Piki, które wychodzą poza skalę wykresu na oryginale (i są tam podpisane ręcznie
  liczbą, np. "17.290"), zostaną w danych ucięte do granicy skali — to rzadkie
  przypadki, warto je zerknąć wzrokowo przy analizie wyników.
