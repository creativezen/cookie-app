# COOKIE APP

## для работы с Cookies

### Структура проекта

```
/mvc-cookie-example
|-- index.html
|-- /js
|   |-- cookieApp.js
|   |-- controller.js
|   |-- model.js
|   |-- view.js
```

### 1. `index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MVC Cookie Example</title>
</head>
<body>
    <h1>MVC Cookie Example</h1>
    <button id="setCookie">Set Cookie</button>
    <button id="getCookie">Get Cookie</button>
    <button id="deleteCookie">Delete Cookie</button>
    <div id="cookieResult"></div>

    <script src="js/model.js"></script>
    <script src="js/view.js"></script>
    <script src="js/controller.js"></script>
    <script src="js/cookieApp.js"></script>
</body>
</html>
```

### 2. `model.js`

```javascript
class CookieModel {
    setCookie(name, value, options = {}) {
        options = {
            path: '/',
            ...options
        };

        if (options.expires instanceof Date) {
            options.expires = options.expires.toUTCString();
        }

        let updatedCookie = encodeURIComponent(name) + "=" + encodeURIComponent(value);

        for (let optionKey in options) {
            updatedCookie += "; " + optionKey;
            let optionValue = options[optionKey];
            if (optionValue !== true) {
                updatedCookie += "=" + optionValue;
            }
        }

        document.cookie = updatedCookie;
    }

    getCookie(name) {
        let matches = document.cookie.match(new RegExp(
            "(?:^|; )" + name.replace(/([.$?*|{}()[\]\\/+^])/g, '\\$1') + "=([^;]*)"
        ));
        return matches ? decodeURIComponent(matches[1]) : undefined;
    }

    deleteCookie(name) {
        this.setCookie(name, "", {
            'max-age': -1
        });
    }
}
```

### 3. `view.js`

```javascript
class CookieView {
    constructor() {
        this.resultElement = document.getElementById('cookieResult');
    }

    displayCookie(value) {
        this.resultElement.textContent = `Cookie value: ${value}`;
    }

    displayMessage(message) {
        this.resultElement.textContent = message;
    }
}
```

### 4. `controller.js`

```javascript
class CookieController {
    constructor(model, view) {
        this.model = model;
        this.view = view;

        document.getElementById('setCookie').addEventListener('click', () => this.setCookie());
        document.getElementById('getCookie').addEventListener('click', () => this.getCookie());
        document.getElementById('deleteCookie').addEventListener('click', () => this.deleteCookie());
    }

    setCookie() {
        const name = 'userToken';
        const value = 'abc123';
        const options = { maxAge: 3600, secure: true };
        this.model.setCookie(name, value, options);
        this.view.displayMessage('Cookie set successfully!');
    }

    getCookie() {
        const name = 'userToken';
        const value = this.model.getCookie(name);
        if (value) {
            this.view.displayCookie(value);
        } else {
            this.view.displayMessage('Cookie not found!');
        }
    }

    deleteCookie() {
        const name = 'userToken';
        this.model.deleteCookie(name);
        this.view.displayMessage('Cookie deleted successfully!');
    }
}
```

### 5. `cookieApp.js`

```javascript
const model = new CookieModel();
const view = new CookieView();
const controller = new CookieController(model, view);
```

### Описание работы

1. **Model (`model.js`)**: 
    - Содержит методы для работы с куками: `setCookie`, `getCookie`, `deleteCookie`.
    - Эти методы непосредственно взаимодействуют с `document.cookie`.

2. **View (`view.js`)**:
    - Отвечает за отображение информации на странице.
    - Содержит методы `displayCookie` и `displayMessage` для вывода значения куки или сообщения пользователю.

3. **Controller (`controller.js`)**:
    - Связывает Model и View.
    - Обрабатывает события, возникающие на странице (нажатия кнопок).
    - Вызывает соответствующие методы Model для работы с куками и обновляет View для отображения результата.

4. **App (`cookieApp.js`)**:
    - Инициализирует Model, View и Controller.
    - Связывает их вместе для работы приложения.

### Пример использования

1. **Установка куки**:
    - Нажмите кнопку "Set Cookie".
    - В консоли будет выведено сообщение "Cookie set successfully!".

2. **Чтение куки**:
    - Нажмите кнопку "Get Cookie".
    - Если кука существует, её значение будет отображено на странице.
    - Если кука не найдена, будет выведено сообщение "Cookie not found!".

3. **Удаление куки**:
    - Нажмите кнопку "Delete Cookie".
    - В консоли будет выведено сообщение "Cookie deleted successfully!".

### Заключение

Данное решение демонстрирует принципы работы с куками в контексте архитектуры MVC. Model отвечает за бизнес-логику, View за отображение, а Controller связывает их вместе, обрабатывая пользовательский ввод и обновляя состояние приложения.