# Tolstoy Comment Api
## Вставка кода на сайт
Образец кода для вставки на сайт можно взять после прохождения регистрации на сайте [tolstoycomments.com](https://tolstoycomments.com/)
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
Страница будет запущена с настройками переданными в 'window.tolstoycomments.config'. 
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
### Инициализация с указанием произвольных данных страницы
identity: произвольная строка которая служит идентификатором страницы сайта (это может быть порядковый номер статьи в базе или полная ссылка).

url: ссылка на страницу сайта которая будет использована в виджете для перехода на текущую страницу сайта. По умолчанию берется значение 'document.location.href'.

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
### Счетчик комментариев
Чтобы вывести счетчик комментариев, необходимо чтобы на странице был подключен основной код виджета. В параметрах конфигурации виджета нужно задать имя класс селектора:
```html
/// html
/* ... основной код виджета */
<script>
    window.tolstoycomments.config = {
        comment_class: 'tolstoycomments-cc'
    };
</script>
```
Для вывода счетчика комментариев нужно создать html элемент с именем класс, переданном в переменной `comment_class`. Пример:
```html
/// html
<span class="tolstoycomments-cc" data-url="[ПОЛНАЯ ССЫЛКА НА СТАТЬЮ]"></span>
```
Пример счетчика с текстом `Нет комментариев`:
```html
/// html
<span class="tolstoycomments-cc" data-url="[ПОЛНАЯ ССЫЛКА НА СТАТЬЮ]">Нет комментариев</span>
```
В параметре `data-url` нужно передать полную ссылку на статью для которой необходимо сделать вывод количества комментариев.
(по умолчанию `comment_class` не задан и виджет не будет выводить счетчики комментариев.
### Счетчик комментариев без вывода самого виджета
Для вывода счетчика комментариев на главной странице сайта, в списке рубрик или в списке статей можно отключить вывод виджета и оставить только загрузку счетчика комментариев. Для этого нужно задать `visible: false` в настройках конфигурации:
```html
/// html
/* ... основной код виджета */
<script>
    window.tolstoycomments.config = {
        visible: false
    };
</script>
```
Так будет выглядеть инициализация виджета с выводом счетчика комментариев но без вывода самого виджеа
```html
/// html
/* ... основной код виджета */
<script>
    window.tolstoycomments.config = {
        visible: false,
        comment_class: 'tolstoycomments-cc'
    };
</script>
```
### Счетчик комментариев в собственном стиле
Для создания собственного стиля вывода числа комментариев предусмотрена функция `comment_render`. Пример этой функции по умолчанию:
```html
/// html
/* ... основной код виджета */
<script>
    window.tolstoycomments.config = {
        visible: false,
        comment_class: 'tolstoycomments-cc',
        comment_render: function (val) {
            return val.toString();
        }
    };
</script>
```
Функция `comment_render` вызывается после получения числа коментариев с сервера виджета. В переменной `val` передается число комментариев. В переменной `this` внутри функции будет передан html-элемент в который происходит вывод. Функцию `comment_render` можно вызывать без return в этом случае вы сами должны описать локигу вывода.
Пример собственного стиля вывода счетчика комментариев:
```html
/// html
/* ... основной код виджета */
<script>
    window.tolstoycomments.config = {
        visible: false,
        comment_class: 'tolstoycomments-cc',
        comment_render: function (val) {
            this.textContent = val + " комментари" + GetNumEnding(val, "й", "я", "ев");
        }
    }
    var GetNumEnding = function (n, a, b, c) {
        var v = parseInt(n % 100);
        if (v >= 11 && v <= 19) {
            return c;
        } else {
            v = parseInt(n % 10);
            if (v == 1) {
                return a;
            } else {
                if (v >= 2 && v <= 4) {
                    return b;
                } else {
                    return c;
                }
            }
        }
    }
</script>
```
Результат:
* 0 комментариев
* 1 комментарий
* 2 комментария
* 5 комментариев
