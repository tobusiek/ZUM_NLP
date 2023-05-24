# PROJEKT ZALICZENIOWY
## Analiza sentymentu przy pomocy NLP

### Autorzy:
- Miłosz Wójcik – s26587
- Karol Manarczyk – s26592
- Szymon Jaśkowski – s26549

<br>

### Etap 1
**Branch:** *main* <br>
**Plik:** *scraper.ipynb* <br>
**Dane:** zebranie komentarzy i postów z reddita dotyczących tagu *‘musk’*, zapisane jako *musk.csv* <br>
**Wstępne czyszczenie danych:** *cleaning_data.ipynb*, zapisane jako *musk_clean.csv*

**Branch:** *miszo* <br>
**Plik:** *cleaning_data_w2v_sentiment.ipynb* <br>
Pomoc przy czyszczeniu danych, przygotowanie bigramów i zastąpienie nimi fraz w tekście, budowa słownika przy pomocy Word2Vec (model również zapisany w branchu), utworzenie klastrów przy pomocy metody k-mean, stworzenie pliku *sentiment_dictionary.csv* na potrzeby uczenia nienadzorowanego przy użyciu tf-idf. 

**Plik:** *tfidf.ipynb* <br>
Przy użyciu danych wyczyszczonych i słownika – uczenie nienadzorowane i nadanie w ten sposób etykiet do wszystkich zdań w zbiorze danych, później używane we wszystkich kolejnych etapach. Arbitralnie zadecydowana granica między wiadomościami pozytywnymi i negatywnymi – estymata sentymentu na poziomie -100, w przeciwnym wypadku dane byłyby bardzo przechylone w stronę komentarzy negatywnych – ponad 90% do niecałych 10% pozytywnych.

<br>

### Etap 2
**Branch:** *Classic_machine_learning* <br>
**Plik:** *classic_algorithms.ipynb* <br>
Dane zostały wczytane i podzielone na dwa zbiory: treningowe i testowe. Oba zbiory zostały zapisane jako *'bag of words'*. Dopasowanie zostało dokonane na zbiorze treningowym. Wykorzystane zostały następujące modele: naive Bayes, drzewo decyzyjne, las losowy, SVM. Najlepiej wypadł model naiwnego Bayesa zarówno pod względem dokładności, jak i wyniku f1. Zaraz za nim uplasował się las losowy. Na ostatnim miejscu znalazło się drzewo decyzyjne. Najlepszy z modeli osiągnął dokładność o wysokości 85,5% oraz wynik f1 w wysokości 0,85. Wyniki poszczególnych modeli umieszczone zostały w pliku results/results_classic_ml.csv. W samym notatniku pod wywołaniem każdego z modeli znajduje się: raport klasyfikacji, macierz pomyłek oraz wykres zawierający krzywą ROC.

<br>

### Etap 3
**Branch:** *basic_neural_networks* <br>
**Plik:** *basic_neural_networks.ipynb* <br>
Wczytanie oetykietowanych danych z pliku *labeled.csv*, ograniczenie zbioru (problemy z pamięcią) do wszystkich aktualnie posiadanych obserwacji z klasy o sentymencie negatywnym (5824 rekordy) i 10 tysięcy obserwacji z klasy o sentymencie pozytywnym. Rozdzielenie danych na zbiory treningowe, walidacyjne i testowe w proporcjach 70/20/10%. Wyliczenie wag klas ze względu na niezbalansowane dane - uwzględnienie wag podczas uczenia. Wytrenowanie prostych modeli z warstwami gęstymi, a następnie kilku z warstwami rekurencyjnymi. Modele z warstwami gęstymi uczone na danych wczytanych jako *‘bag of words’* - użycie CountVectorizer. Modele z rekurencyjnymi na danych w formacie potokenizowanej, z post-paddingiem. W obu przypadkach ograniczenie liczby słów branych pod uwagę do 5 tysięcy. Użyty callback early stopping, monitorujący minimalną wartość wyniku funkcji straty na zbiorze walidacyjnym. Najlepiej spisał się model z dwukierunkową warstwą rekurencyjną o wymiarze embeddingu równym 96 i rozmiarze wynikowym warstwy równym 128 ze zwiększonym wskaźnikiem dropoutu - wynik: 93% dokładności oraz 96% pełności na zbiorze testowym. Modele zostały zapisane na branchu.

<br>

### Etap 4
**Branch:** *miszo* <br>
**Plik:** *bert.ipynb* <br>
Uczenie oparte na pretrenowanym modelu *distilbert-base-uncased*, z batchem wielkości 128 i ograniczeniem tekstu do 256 tokenów długości, wyższe wartości znacząco wydłużały okres uczenia. Modelowi zajmowało ok. 8-10 minut na uczenie jednej epoki, więc ograniczone zostało to do 5 epok, z widocznym szybkim przetrenowaniem modelu (rosnąca wartość funkcji straty na zbiorze walidacyjnym już od 2 epoki), a niepoprawiającą się dokładnością, od początku osiągającą ponad 90%. Test na zdaniu "I hate elon musk he is an idiot" zwrócił poprawną etykietę negatywną, co można uznać jako dodatkowy sukces w uczeniu modelu.

**Plik:** *zero_shot.ipynb* <br>
Drugi model językowy – *bart-large-mnli*, z zadaniem zero-shot-classification, dodatkowo wykorzystany model flair - TextClassifier > en-sentiment. Po krótkim przygotowaniu danych do nauki, modelowi zajęło ok. 90 minut przetworzenie całości danych, a zwrócone wyniki były dość niezadowalające, osiągając 37% dokładności, co można równie dobrze odczytać jako 63% dokładności dla odwróconych etykiet 🙂 Wiązało się to prawdopodobnie z założonym odgórnie progiem >-100 oddzielającym dwie etykiety w uczeniu nienadzorowanym, większość etykiet powinna jednak być tam negatywna.

**Wnioski z etapu 4** – Bert dostosowuje się do nadanych etykiet dużo łatwiej niż drugi wykorzystany model, który jednak nadal stara się dość subiektywnie podchodzić do oceny sentymentu.
