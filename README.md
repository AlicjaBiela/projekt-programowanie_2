# Projekt: Przewidywanie kosztów medycznych pacjentów (Regresja)

### 1. Opis danych i źródło
Zbiór danych pochodzi z platformy Kaggle (`Medical Cost Personal Datasets`). Zawiera informacje o pacjentach i ich rocznych kosztach ubezpieczenia medycznego. Do analizy wybrałam cechy numeryczne:
* **age** (wiek)
* **bmi** (indeks masy ciała)
* **children** (liczba dzieci)
* **smoker** (status palacza: przekształcony na postać binarną 1 - tak, 0 - nie)
* **charges** (zmienna docelowa - roczny koszt leczenia w USD)

### 2. Cel analizy i hipotezy badawcze
Celem projektu jest zbudowanie modelu regresyjnego potrafiącego prognozować koszty medyczne na podstawie cech fizycznych i nawyków pacjenta.
* **Hipoteza 1:** Status palacza wykazuje najsilniejszą korelację dodatnią z kosztami leczenia.
* **Hipoteza 2:** Model Drzewa Decyzyjnego lepiej poradzi sobie z problemem niż Regresja Liniowa ze względu na nieliniowe skoki kosztów (np. połączenie wysokiego BMI i palenia).

### 3. Wyniki analizy korelacji i czyszczenia danych
* Ewentualne braki danych zostały zabezpieczone metodą uzupełniania wartością średnią (`fillna`).
* Analiza macierzy korelacji wykazała, że zmienna `smoker` jest najsilniej skorelowana z kosztami leczenia (współczynnik korelacji wynosi aż **0.79**), co w pełni potwierdza Hipotezę 1.

### 4. Porównanie modeli (Metryka RMSE)
Dane zostały podzielone w stosunku 80% (trening) do 20% (test). Zaimplementowano dwa modele, uzyskując następujące błędy RMSE:
* **Regresja Liniowa:** $5829.38
* **Drzewo Decyzyjne (max_depth=4):** $4592.76

Za pomocą algorytmu **GridSearchCV** sprawdzono optymalną głębokość drzewa (w przedziale 2-10). Narzędzie wskazało, że głębokość `max_depth: 4` jest optymalna. 

**Wniosek:** Drzewo Decyzyjne okazało się znacznie lepszym modelem (niższy błąd RMSE o ponad 1200 dolarów), ponieważ skuteczniej interpretuje skokowe, nieliniowe zależności w danych pacjentów.

### 5. Praktyczna symulacja (Predykcja)
Model pomyślnie przeszedł testy logiczne. Przy identycznych parametrach bazowych (wiek: 22 lata, BMI: 23.0, brak dzieci), model prognozuje:
* Dla pacjenta **niepalącego**: $2757.95
* Dla pacjenta **palącego**: $16174.13
