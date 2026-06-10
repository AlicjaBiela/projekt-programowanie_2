# Przewidywanie rocznych kosztów medycznych pacjentów (Zadanie Regresji)

## 1. Opis danych i źródło
Zbiór danych pochodzi z platformy Kaggle (*Medical Cost Personal Datasets*). Zawiera on informacje o profilach pacjentów oraz ich rocznych kosztach ubezpieczenia medycznego w USA. W celu budowy modeli regresyjnych przeanalizowano następujące cechy:
* **age** – wiek pacjenta (numeryczna),
* **bmi** – wskaźnik masy ciała (numeryczna),
* **children** – liczba dzieci/osób na utrzymaniu (numeryczna),
* **smoker** – status palacza (zmienna kategoryczna, przekształcona w procesie preprocessingu na postać binarną: `1` – tak, `0` – nie),
* **charges** – (zmienna docelowa / target) roczny koszt leczenia wyrażony w USD.

*Uwaga: Pozostałe zmienne kategoryczne (płeć, region) zostały pominięte w celu uproszczenia struktury wejściowej i skupienia się na cechach o najsilniejszym wpływie korelacyjnym.*

## 2. Cel analizy i hipotezy badawcze
Głównym celem projektu jest zbudowanie i optymalizacja modelu uczenia maszynowego, który na podstawie cech biologicznych oraz nawyków pacjenta będzie w stanie precyzyjnie prognozować roczne koszty medyczne.

W projekcie postawiono trzy hipotezy badawcze:
* **Hipoteza 1:** Status palacza wykazuje najsilniejszą dodatnią korelację z kosztami leczenia spośród wszystkich dostępnych cech.
* **Hipoteza 2:** Model Drzewa Decyzyjnego osiągnie lepszą jakość predykcji (niższy błąd RMSE) niż model Regresji Liniowej ze względu na występowanie nieliniowych skoków wartości w danych.
* **Hipoteza 3:** Wpływ wskaźnika BMI na koszty nie jest prostoliniowy – ekstremalnie wysokie koszty generowane są głównie przy współwystępowaniu wysokiego BMI oraz statusu palacza (efekt synergii dwóch negatywnych czynników).

## 3. Eksploracja (EDA) i czyszczenie danych
* **Zabezpieczenie braków danych:** Analiza zbioru wykazała brak pustych wartości (zbiór kompletny). W kodzie zastosowano jednak defensywne programowanie i zabezpieczono potok przetwarzania metodą uzupełniania ewentualnych braków wartością średnią (`.fillna()`).
* **Weryfikacja Hipotezy 1:** Analiza macierzy korelacji Pearsona wykazała, że zmienna `smoker` jest skorelowana ze zmienną docelową `charges` na poziomie aż **0.79**. Jest to zdecydowanie najwyższy wynik (dla porównania: wiek to 0.30, a BMI 0.20), co **w pełni potwierdza Hipotezę 1**.
* **Weryfikacja Hipotezy 3:** Wykres rozrzutu (Scatterplot) jednoznacznie zobrazował nieliniową interakcję: u pacjentów niepalących wzrost BMI podnosi koszty w sposób łagodny, natomiast u pacjentów palących przekroczenie progu BMI $\ge$ 30 powoduje gwałtowny, skokowy wzrost kosztów leczenia (od ok. 20 000\$ do ponad 40 000\$). **Hipoteza 3 została potwierdzona wizualnie**.

## 4. Porównanie modeli (Metryka RMSE)
Zbiór danych został podzielony na część treningową (80%) i testową (20%). Do rozwiązania zadania regresji zaimplementowano dwa algorytmy. Dla modelu drzewa decyzyjnego przeprowadzono automatyczny dobór hiperparametrów przy użyciu `GridSearchCV` (badany zakres głębokości: 2-10). Narzędzie wskazało `max_depth=4` jako wartość optymalną.

Wyniki oceny modeli na zbiorze testowym za pomocą metryki RMSE (Root Mean Squared Error):

| Model | RMSE |
| :--- | :--- |
| **Regresja Liniowa** | \$5829.38 |
| **Drzewo Decyzyjne (`max_depth=4`)** | **\$4592.76** |

**Wniosek:** Model Drzewa Decyzyjnego okazał się znacznie skuteczniejszy – zmniejszył błąd prognozy o **ponad \$1236**. Potwierdza to **Hipotezę 2** – algorytmy drzewiaste znacznie lepiej radzą sobie z odwzorowaniem skokowych i nieliniowych zależności zachodzących pomiędzy paleniem, BMI a końcowym kosztem ubezpieczenia.

## 5. Praktyczna symulacja (Predykcja)
W celu ostatecznej walidacji logicznej działania ulepszonego modelu drzewa decyzyjnego, przeprowadzono symulację dla profilu młodego pacjenta o identycznych parametrach bazowych (wiek: 22 lata, BMI: 23.0 [waga prawidłowa], liczba dzieci: 0):

* **Prognozowany roczny koszt dla pacjenta NIEPALĄCEGO:** \$2757.95
* **Prognozowany roczny koszt dla pacjenta PALĄCEGO:** \$16174.13

Drastyczna, niemal sześciokrotna różnica w wycenie dla tego samego profilu biologicznego udowadnia, że model poprawnie zinterpretował kluczowe reguły decyzyjne i posiada wysoką użyteczność aplikacyjną.
