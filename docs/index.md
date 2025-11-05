# Dokumentacja techniczna projektu **Mini-CMS**

# 1. Opis ogólny

Lekki przykład aplikacji CMS zbudowany przy użyciu Node.js, Express i EJS.
Pozwala na przeglądanie listy artykułów, podgląd pojedynczego artykułu oraz
tworzenie nowych wpisów zapisywanych w pliku JSON (`/data/articles.json`).

# 2. Schemat zależności

 - `routes/` --> `controllers/`
 - `controllers/` --> `services/`
 - `services/` --> `models/`
 - `models/` --> data[(articles.json)]
 - `controllers/` --> `views/`
 - `public/` --> pliki statyczne

---

# 3. Struktura projektu

```
Mini-CMS-main/ 
├── data/
│   └── articles.json
├── public/
│   └── css/
│       └── main.css
├── src/
│   ├── app.js
│   ├── server.js
│   ├── controllers/
│   │   └── articlesController.js
│   ├── models/
│   │   └── storage.js
│   ├── routes/
│   │   └── articles.js
│   ├── services/
│   │   └── articleService.js
│   └── views/
│       ├── articles/
│       │   ├── error.ejs
│       │   ├── list.ejs
│       │   ├── new.ejs
│       │   └── show.ejs
│       └── partials/
│           ├── footer.ejs
│           └── header.ejs
```

---

# 4. Główne moduły aplikacji

## `src/app.js`

**Plik konfiguracyjny aplikacji Express:**

 - ustawia silnik szablonów EJS,

 - wskazuje katalog z plikami widoków,

 - dodaje middleware express.static('public') dla zasobów statycznych,

 - rejestruje ścieżki z routes/articles.



## `src/server.js`

**Uruchamia serwer HTTP:**

 - importuje aplikację z app.js,

 - nasłuchuje na porcie (domyślnie 3000),

 - wyświetla komunikat w konsoli po starcie serwera

## `src/routes/articles.js`

**Definiuje wszystkie trasy związane z artykułami:**

*GET /articles* --> lista artykułów

*GET /articles/new* --> formularz dodania

*POST /articles* --> zapis nowego artykułu

*GET /articles/:id* --> wyświetlenie konkretnego artykułu

# 5. Funkcje.

## Funkcje pobierania, wyszukiwania, tworzenia, zapisu:

### `getAllArticles() `

 - Funkcja asynchronicznie wczytuje i zwraca wszystkie artykuły.

 **Przykład użycia:**
```js 
    const articles = await articleService.getAllArticles();
    res.render('articles/list', {Title: "Artykuły", articles });
```

### `getArticleBySlug(data)`

 - Funkcja asynchronicznie wyszukuje i zwraca artykuł o podanym slugu spośród wszystkich wczytanych artykułów.

```js 
    const slug = req.params.slug;
    const article = await articleService.getArticlesBySlug(slug);
```

### `createArticle(data)`

 - Funkcja asynchronicznie tworzy nowy artykuł z unikalnym slugiem, dodaje go na początek listy artykułów i zapisuje zmiany.

 ```js 
    const article = await articleService.createArticle({ title, content, author });
        res.status(300).redirect(`/articles/${article.slug}`);
```

## Funkcje odczytu i zapisu:

### `readArticles()`

 - odczytuje wszystkie artykuły z pliku JSON.

**Przykład użycia:**
```js 
    const articles = await readArticles();
    return articles.find(article);
```

### `writeArticles(data)`

 - zapisuje nową zawartość pliku JSON.

**Przykład użycia:**

```js
const articles = await readArticles();
articles.unshift(newArticle);
await writeArticles(articles);
```

**W tej warstwie znajdują się wyłącznie operacje na plikach**.

## Funkcje obsługi artykułów

### `index(req, res)`

 - wczytuje wszystkie artykuły i renderuje listę.

**Przykład użycia**

```js
const router = express.Router();
const controllers = require('../controllers/articlesController');
router.get('/', controllers.index);
```

### `show(req, res)`

 - wyszukuje artykuł po slug i wyświetla go, a jeśli nie istnieje, pokazuje stronę błędu 404.

```js
const router = express.Router();
const controllers = require('../controllers/articlesController');
router.get('/articles/:slug', controllers.show);
```

### `create(req, res)`

 - sprawdza poprawność danych z formularza, tworzy nowy artykuł i przekierowuje do jego strony lub pokazuje błędy.

```js
const router = express.Router();
const controllers = require('../controllers/articlesController');
router.post('/articles', controllers.create);
```

### `newForm(req, res)`

- renderuje formularz do dodania nowego artykułu.

```js
const router = express.Router();
const controllers = require('../controllers/articlesController');
router.get('/articles/new', controllers.newForm);
```

# 6. Widoki

**Każdy widok renderowany jest przy użyciu EJS.**

| Plik | Opis |
|------|------|
| `list.ejs` | Lista artykułów |
| `show.ejs` | Szczegóły artykułu |
| `new.ejs` | Formularz dodawania artykułu |
| `error.ejs` | Strona błędu |
| `footer.ejs` | Stopka strony |
| `header.ejs` | Nagłówek strony |

---

### Przykład widoku `list.ejs`

```js
<%- include('../partials/header') %>

<h1>Lista artykułów</h1>
<ul>
  <% articles.forEach(a => { %>
    <li><a href="/articles/<%= a.id %>"><%= a.title %></a></li>
  <% }) %>
</ul>

<a href="/articles/new">Dodaj nowy artykuł</a>

<%- include('../partials/footer') %>
```

---

Dokumentacja: Miłosz Kulczycki

