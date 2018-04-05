# Tolstoy Comment Api
## Вставка кода на сайт
Образец кода для вставки на сайт можно взять после прохождения регистрации на сайте [tolstoycomments.com](http://panel.tolstoycomments.com/)
## Основное Api
Для доступа к настройкам виджена:
```javascript
/// javascript
window.tolstoycomments.vidget
```
### Открыть виджет
```javascript
/// javascript
window.tolstoycomments.vidget.open();
```
### Закрыть виджет
```javascript
/// javascript
window.tolstoycomments.vidget.close();
```
### Инициализовать виджет
по умолчанию, при загрузке страници, вызывается инициализация виджета
```javascript
/// javascript
window.tolstoycomments.vidget.init();
```
### Удалить виджет
```javascript
/// javascript
window.tolstoycomments.vidget.destroy();
```
### Открыть начальную страницу
Страница будет запущена с настройками переданными в window.tolstoycomments.config. 
В случае если виджет свернут он будет развернут.
В случае если страница чата совпадает с текущей открытой обновление не произойдет.
```javascript
/// javascript
window.tolstoycomments.vidget.navfirst();
```
### Перейти на страницу чата
В случае если виджет свернут он будет развернут.
В случае если страница чата совпадает с текущей открытой обновление не произойдет.
```javascript
/// javascript
// url: string - не обязательный, по умолчанию: document.location.href
// title: string - не обязательный, по умолчанию: document.title
// identity: string - не обязательный, по умолчанию: MD5(document.location.href)
window.tolstoycomments.vidget.nav({url: url, title: title, identity: identity});
```
### Перейти на главную страницу чата
```javascript
/// javascript
window.tolstoycomments.vidget.main();
```
### Перейти на страницу авторизации
```javascript
/// javascript
window.tolstoycomments.vidget.auth();
```
## Инициализация
### Инициализация на странице всех чатов
По умолчанию код вызова виджета создает чат для текущей веб страницы.
Чтобы загружать виджет на странице всех чатов нужно разместить следующий код сразу после вставки кода виджета:
```html
/// html
/* ... основной код виджета */
<script>
window.tolstoycomments.config = { main: true };
</script>
```
### Инициализация с указание произвольных данных страницы
identity: произвольная строка которая служит идентификатором страницы сайта (это может быть порядковый номер статьи в базе или полная ссылка). По умолчанию расчитывается как MD5(document.location.href).

url: ссылка на страницу сайта которая будет использована в виджете для перехода на текущую страницу сайта. По умолчанию берется значение document.location.href.

title: заголовок страницы сайта. По умолчанию берется заголовок из document.title.
```html
/// html
/* ... основной код виджета */
<script>
    window.tolstoycomments.config = {
        identity: 'Любая произвольная строка', 
        url: 'http://youdomen.com/youurl',
        title: 'Заголовок страницы'
    };
</script>
```
