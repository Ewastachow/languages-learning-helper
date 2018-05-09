

# Languages Learning Helper

Aplikacja służąca do tworzenia własnych zestawów fiszek.

## Informacje ogólne

Głównym założeniem projektu jest synchronizacja bazy lokalnej IndexedDB (która umożliwa użytkownikom korzystanie z aplikacji, gdy są offline) z bazą zdalną. Dokładne działanie aplikacji jest opisane w dalszej części.

Podfolder `backend` zawiera kod napisany w Javie, natomiast w podfolderze `frontend` znajduje się kod w JavaScript (w projekcie wykorzystano bibliotekę React oraz Redux do zarządzania
stanem aplikacji i akcjami).

Pracując nad projektem, aby przyspieszyć pracę, zastosowano `hot reloading` - gdy aplikacja uruchamiana jest na serwerze developerskim, zmiany w plikach frontendowych
oraz w stylach przeładowywują się autoamtycznie bez odświeżania przeglądarki.


## Uruchamianie aplikacji w trybie developerskim (hot reloading)

#### Wymagania
* node 6.0+
* yarn (by możliwe było uruchomienie webpack dev server)
* stworzenie bazy danych MySQL (skrypt w głównym folderze projektu) oraz konfiguracja w pliku `backend/src/main/resources/application.properties`

Uruchomienie metody głównej w klasie FlashcardsManagerApplication spowoduje, że aplikacja będzie dostępna pod adresem:
 
``
http://localhost:3000/
`` 

## Uruchomianie aplikacji w trybie produkcyjnym

Aby uruchomić aplikację w trybie produkcyjnym należy wygenerować plik jar:

``
$ ./gradlew assemble
``

Plik wygeneruje się do folderu `backend/build/libs`, następnie należy go uruchomić komendą:

``
$ java -jar flashcards-0.0.1-SNAPSHOT.jar --spring.profiles.active=production
``

Aplikacja zostanie uruchomiona pod adresem:

``
http://localhost:8080/
`` 

## Uruchomianie aplikacji w trybie produkcyjnym

Aby uruchomić aplikację w trybie produkcyjnym należy wygenerować plik jar:

``
$ ./gradlew assemble
``

Plik wygeneruje się do folderu `backend/build/libs`, następnie należy go uruchomić komendą:

``
$ java -jar flashcards-0.0.1-SNAPSHOT.jar --spring.profiles.active=production
``

Aplikacja zostanie uruchomiona pod adresem:

``
http://localhost:8080/
`` 

## Dokumentacja

### Spis treści
+ [Opis funkcjonalności](#functionality)
+ [Progres pracy](#progress)
+ [Proponowane technologie](#technologies)
+ [Narzędzia](#tools)
+ [Dokumentacja API](#docAPI)
  + [Użytkownik](#user)
    + [Rejestracja użytkownika](#register)
    + [Logowanie](#login)
    + [Wylogowywanie](#logout)
  + [Zestawy fiszek](#flashcardSets)
    + [Tworzenie zestawu](#create)
    + [Edycja zestawu](#update)
    + [Usuwanie zestawu](#delete)
    + [Zestaw](#set)
    + [Lista wszystkich zestawów](#sets)
    + [Synchronizacja](#synchronize)
+ [Przykładowy Test](#test)
+ [Diagramy](#diagrams)

<a name="functionality"/>

## Opis funkcjonalności

Aplikacja, którą zamierzamy stworzyć będzie służyć do szybkiego przyswajania słówek w języku obcym.

* Użytkownik po zalogowaniu będzie mógł stworzyć własny zestaw fiszek, który następnie będzie przeglądał w losowej kolejności (z jednej strony będzie widział polskie słówko, a po odwróceniu takiej fiszki ukaże mu się słówko angielskie).

* Aby proces nauki był jeszcze lepszy, chcemy zaangażować nie tylko zmysł wzroku użytkownika, ale również słuchu, więc po odwróceniu fiszki będzie można usłyszeć wymowę danego zwrotu.

* Słówka, które użytkownik będzie uważał za przyswojone będzie mógł zaznaczyć w trybie edycji zestawu, aby więcej mu się nie pokazywały w trybie przeglądania.

* Kolejną funkcjonalnością, którą chcemy zrealizować jest możliwość pracy użytkownika w trybie offline. Gdy osoba korzystająca z naszej aplikacji straci połączenie z internetem dalej będzie mogła dodawać, usuwać i edytować zestawy fiszek, a wszystkie zmiany lokalne zsynchronizują się z bazą na serwerze po ponownym połączeniu z siecią.

* Chcemy również obsłużyć problem konfliktów (np. gdy jeden zestaw będzie edytowany równocześnie przez dwie osoby). Gdy takie konflikty wystąpią, użytkownik będzie zobowiązany wybrać wersję, która aktualnie powinna znaleźć się w bazie zdalnej na serwerze.

<a name="progress"/>

## Progres pracy
### Milestone 1
 * [x] Wstępna dokumentacja projektu
 * [x] Utworzenie bazy danych MySql
### Milestone 2
 * [x] [BACKEND] Dodawanie użytkownika
 * [x] [FRONTEND] Formularz rejestracji użytkownika
 * [x] [BACKEND] Autentykacja użytkownika przy pobieraniu zasobów
 * [x] [FRONTEND] Formularz logowania użytkownika
 * [x] [BACKEND] Pobieranie wszystkich zestawów fiszek
 * [x] [FRONTEND] Widok wszystkich zestawów fiszek (strona główna po zalogowaniu)
 * [x] [BACKEND] Pobieranie zestawu fiszek po identyfikatorze
 * [x] [FRONTEND] Widok zestawu fiszek umożliwiający ich naukę (odwracanie fiszki na drugą stronę, zaangażowanie zmysłu słuchu)
### Milestone 3
 * [x] [BACKEND] Dodawanie zestawu fiszek
 * [x] [FRONTEND] Formularz dodawania zestawu fiszek
 * [X] [TESTY] Przygotowanie środowiska testerskiego (osobna baza)
 * [X] [TESTY] Podstawowe testy zrealizowanych funcjonalności (*happy paths*)
### Milestone 4
 * [ ] [BACKEND] Edycja zestawu fiszek
 * [ ] [FRONTEND] Formularz edycji zestawu fiszek
 * [ ] [BACKEND] Usuwanie zestawu fiszek
 * [ ] [FRONTEND] Przycisk do usuwanie zestawu fiszek
 * [ ] [BACKEND] Obsługa synchronizacji zestawów edytowanych w tym samym czasie, implementacja Optimistic Offline Lock
 * [ ] [FRONTEND] Odpowiednie komunikaty w celu informacji użytkownika o braku możliwości zapisu zmian, gdy niezgodność wersji
 * [ ]  [FRONTEND] Możliwość korzystania z aplikacji offline
 * [ ]  [TESTY] Przetestowanie aplikacji - testy integracyjne
 * [ ]  Kompletna dokumentacja

<a name="technologies"/>

## Proponowane technologie

* Backend naszej aplikacji planujemy napisać w `Java 8` przy pomocy `Spring-Boot` w formie usługi typu `REST JSON API`

* Frontend natomiast korzystając z biblioteki `React` oraz `Redux` (do zarządzania stanem aplikacji i akcjami).

* Frontend i backend będą się komunikować za pomocą protokołu HTTP.

* Pracując nad projektem, aby przyspieszyć pracę, chcemy zastosować *hot reloading* - gdy aplikacja będzie uruchamiana na serwerze developerskim `Webpack Dev Server`, zmiany w kodzie JavaScript i stylach będą powodować automatyczne przeładowanie aplikacji bez odświeżania przeglądarki.

* Korzystając z interfejsu serwera baz danych AGH, stworzymy bazę `MySQL`.

* Aby umożliwić użytkownikowi pracę offline użyjemy bazy lokalnej w przeglądarce - `IndexedDB`.

<a name="tools"/>

## Narzędzia

*  `Intellij` - zintegrowane środowisko programistyczne

* `Github` - repozytorium kodu, umożliwia współtworzenie projektu ze wsparciem rozwiązywania
konfliktów (git)

* `Trello` - lista zadań do zrobienia, podział pomiędzy osoby oraz przypisanie priorytetów


## Dokumentacja API

<a name="user"/>

### Użytkownik

<a name="register"/>

#### Rejestracja użytkownika

*URL:*  `/api/register`

*Metoda:* `POST`

Użytkownik przesyła swoje dane (imię, nazwisko, login, hasło, email) w celu rejestracji konta. Jeśli jakieś pole jest puste, lub hasło np. za krótkie zwracany jest komunikat o błędzie.


*Przykład:*

```javascript
  axios.post('/api/register', {
      firstName: 'Fred',
      lastName: 'Flintstone',
      username: 'Fred41',
      email: 'fredflintstone@yahoo.com',
      password: 'IloveWilma123'
   }) .then(response => {
    console.log(response);
   }) .catch(error => {
    console.log(error);
   });
```

*Odpowiedź:*

Kod odpowiedzi HTTP: `200 OK`

```javascript
{
  "username": "Fred41",
  "firstName": "Fred",
  "lastName": "Flintstone",
  "email": "fredflintstone@yahoo.com",
  "role": "USER"
}
```
*Zwracane błędy:*

Warunek: Przesyłane dane nie spełniają kryteriów (np. hasło ma mniej niż 8 znaków, nie zawiera dużej litery i cyfry), lub obiekt JSON nie zawiera wszystkich wymaganych pól

Kod odpowiedzi HTTP: `400 Bad Request`

```javascript
{
  "messageKey": "userData.error.badRequest"
}
```
Warunek: Przesyłany username już istnieje

Kod odpowiedzi HTTP: `400 Bad Request`

```javascript
{
  "messageKey": "register.error.usernameExists"
}
```

Warunek: Przesyłany email już istnieje

Kod odpowiedzi HTTP: `400 Bad Request`

```javascript
{
  "messageKey": "register.error.emailExists"
}
```

<a name="login"/>

#### Logowanie

*URL:*  `/api/session`

*Metoda:* `POST`

Użytkownik przesyła swój login i hasło w celu zalogowania się do aplikacji, jeśli hasło lub login są nieprawidłowe wyświetla się komunikat o błędzie.
Jeśli użytkownik jest zautentykowany zwracany jest mu token.

*Przykład:*

```javascript
  axios.get('/api/session')
   .then(response => {
    console.log(response);
   }) .catch(error => {
    console.log(error);
   });
```

*Odpowiedź:*

Kod odpowiedzi HTTP: `200 OK`

```javascript
{
  "username": "Fred41",
  "token": "6D55B805C77A21230EA9269091FFBE75",
  "authenticated": true
}
```

<a name="logout"/>

#### Wylogowywanie

*URL:*  `/api/logout`

 *Metoda:* `DELETE`

Użytkownik przerywa swoją sesję - zostaje wylogowany z aplikacji.

*Przykład:*

```javascript
  axios.delete('/api/logout')
   .then(response => {
    console.log(response);
   }) .catch(error => {
    console.log(error);
   });
```

<a name="flashcardSets"/>

### Zestawy fiszek

<a name="create"/>

#### Tworzenie zestawu

*URL:*  `/api/flashcards`

 *Metoda:* `POST`

Użytkownik tworzy nowy zestaw przesyłając obiekt z nazwą zestawu oraz listą fiszek.

*Przykład:*

```javascript
  axios.post('/api/flashcards', {
    owner: 'Fred41',
    name: 'Animals',
    flashcards: [
     {  
       frontSide: 'cat',
       backSide: 'kot'
     },
     {
       frontSide: 'dog',
       backSide: 'pies'
     }
   ]}
   ).then(response => {
    console.log(response);
   }) .catch(error => {
    console.log(error);
   });
```

*Odpowiedź:*

Kod odpowiedzi HTTP: `200 OK`
```javascript
{
  "setId": 2,
  "version": 0,
  "owner": "Fred41",
  "name": "Animals",
  "lastModified": 1517603827715,
  "flashcards": [
    {
      "flashcardId": 13,
      "frontSide": "cat",
      "backSide": "kot"
    },
    {
      "flashcardId": 14,
      "frontSide": "dog",
      "backSide": "pies"
    }
  ]
}
```

*Zwracane błędy:*

Warunek: Obiekt JSON nie zawiera wszystkich wymaganych pól

Kod odpowiedzi HTTP: `400 Bad Request`

```javascript
{
  "messageKey": "flashcards.error.badRequest"
}
```

<a name="update"/>

#### Edycja zestawu

*URL:*  `/api/flashcards`

*Metoda:* `PUT`

Użytkownik przesyła obiekt z nazwą zestawu, identyfikatora zestawu oraz listą fiszek.
Użytkownik ma możliwość zmiany nazwy zestawu oraz listy fiszek.

<a name="delete"/>

#### Usuwanie zestawu

*URL:*  `/api/flashcards/{setId}`

*Metoda:* `DELETE`

<a name="set"/>

Użytkownik w ścieżce podaje zestaw fiszek, który chce usunąć. Jeżeli zestaw nie istnieje zostanie zwrócony komunikat o błędzie.

#### Zestaw

*URL:*  `/api/flashcards/{setId}`

*Metoda:* `GET`

Użytkownik w ścieżce podaje zestaw fiszek, który chce pobrać - w odpowiedzi dostaje nazwę zestawu wraz z całą listą fiszek. Jeżeli zestaw nie istnieje zostanie zwrócony komunikat o błędzie.

*Przykład:*
```javascript
  axios.get('/api/flashcards/2')
   .then(response => {
    console.log(response);
   }) .catch(error => {
    console.log(error);
   });
```

*Odpowiedź:*

Kod odpowiedzi HTTP: `200 OK`

```javascript
{
  "setId": 2,
  "version": 1,
  "owner": "Fred41",
  "name": "Animals",
  "lastModified": 1517604771815,
  "flashcards": [
    {
      "flashcardId": 15,
      "frontSide": "fish",
      "backSide": "ryba"
    }
  ]
}
```

*Zwracane błędy:*

Warunek: Set o podanym id nie istnieje

Kod odpowiedzi HTTP: `404 Not Found`

<a name="sets"/>

#### Lista wszystkich zestawów

*URL:*  `/api/flashcards`

*Metoda:* `GET`

Użytkownikowi zostaje zwrócona lista wszystkich zestawów fiszek.

*Przykład:*
```javascript
  axios.get('/api/flashcards')
   .then(response => {
    console.log(response);
   }) .catch(error => {
    console.log(error);
   });
```
*Odpowiedź:*

Kod odpowiedzi HTTP: `200 OK`

```javascript
{
  "flashcardSets": [
    {
      "setId": 2,
      "version": 1,
      "owner": "Fred41",
      "name": "Animals",            
      "lastModified": 1517604771815,
      "flashcards": [
        {
          "flashcardId": 15,
          "frontSide": "fish",
          "backSide": "ryba"
        }
      ]
    },
    {
      "setId": 3,
      "version": 1,
      "owner": "Fred41",
      "name": "Numbers",
      "lastModified": 1517431161000,
      "flashcards": [
        {
          "flashcardId": 5,
          "frontSide": "one",
          "backSide": "jeden"
        }
      ]
    }
  ],
  "version": 1517602995656
}
```

<a name="synchronize"/>

#### Synchronizacja

*URL:*  `/api/synchronize`

*Metoda:* `POST`

Użytkownik synchronizuje lokajną bazę - przesyła obiekt wszystkich lokalnych zestawów, w odpowiedzi dostaje listę zestawów, w których wystąpił konflikt - wersja przesłana w zapytania różni się od wersji w bazie.


<a name="test"/>

## Przykładowy test

```groovy
class GeneralAuthenticationTest extends AbstractMvcSpec {

  def "unauthenticated users cannot get resource"() {
    when:
    def res = get("/api/flashcards")

    then:
    res.status == HttpStatus.FORBIDDEN
  }

  @WithMockUser
  def "authenticated users can get resource"() {
    when:
    def res = get("/api/flashcards")

    then:
    res.status == HttpStatus.OK
  }
}
```


## Diagramy

### Schemat baz danych
![alt text](https://image.ibb.co/eZu9AS/Zrzut_ekranu_2018_03_29_o_10_50_37.png)

### Rozwiązywanie konfliktów
![alt text](https://image.ibb.co/cBRSZ7/optimistic.png)
