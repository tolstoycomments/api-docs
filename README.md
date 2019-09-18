# Tolstoy Comment Api
## Вставка кода на сайт
Образец кода для вставки на сайт можно взять после прохождения регистрации на сайте [tolstoycomments.com](https://tolstoycomments.com/)
## Основное Api
Для доступа к настройкам виджета:
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
### Проверка состояние виджета
```javascript
/// javascript
window.tolstoycomments.vidget.isopen(); // return type bool
```
### Инициализовать виджет
по умолчанию, при загрузке страницы, вызывается инициализация виджета
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
В случае если страница чата совпадает с текущей открытой обновление не произойдет.
```javascript
/// javascript
window.tolstoycomments.vidget.navfirst();
```
### Перейти на страницу чата
В случае если страница чата совпадает с текущей открытой обновление не произойдет.
```javascript
/// javascript
window.tolstoycomments.vidget.nav({
	url: url, // url: string - не обязательный, по умолчанию: document.location.href
	title: title, // title: string - не обязательный, по умолчанию: document.title
	identity: identity // identity: string - не обязательный, по умолчанию: MD5(document.location.href)
});
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
или
<span class="tolstoycomments-cc" data-identity="[Любая произвольная строка]"></span>
```
Пример счетчика с текстом `Нет комментариев` по умолчанию:
```html
/// html
<span class="tolstoycomments-cc" data-url="[ПОЛНАЯ ССЫЛКА НА СТАТЬЮ]">Нет комментариев</span>
или
<span class="tolstoycomments-cc" data-identity="[Любая произвольная строка]"></span>
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
Так будет выглядеть инициализация виджета с выводом счетчика комментариев, но без вывода самого виджета
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
## Комментарии под статьей (стандартный вид)
По умолчанию виджет выводится в том месте куда был вставлен код. Если код вставки был видоизменен или отформатирован или перенесен в отдельный файл, виджет вставится отдельным окном. Это происходит поскольку становится невозможно найти место вставки кода виджета в DOM дереве страницы сайта.
Виджет можно вставить в произвольное место с помощью специального тега с классом:
```html
/// html
/* ... текст статьи */
<div class="tolstoycomments-feed"></div>
```
В параметрах инициализации виджета нужно добавить такое же имя класса как и в коде выше:
```html
/// html
<script type="text/javascript">
    window.tolstoycomments.config = { 
        desktop_class: 'tolstoycomments-feed'
    };
</script>
```
*Примечание: виджет будет загружаться в указанное место только если указать соответствующие настройки в панели администрирования в разделе дизайн*
## Полный список всех параметров инициализации виджета
Указан весь список параметров инициализации виджета с параметрами по умолчанию
```html
/// html
<script type="text/javascript">
    window.tolstoycomments.config = { 
	main: false, // если задать true то выведить список чатов
	identity: null, // identity текущей страницы
	url: NormolizeURL(document.location.href), // url текущий страницы, по умолчанию используется встроенная функция
	title: LoadTitle(window), // заголовок текущей страницы, по умолчанию используется встроенная функция LoadTitle
	visible: true, // загружать виджет комментариев
	comment_class: "", // класс элементов для вывода кол-ва комментариев в статьях
	comment_render: val => { // функция рендеринга кол-ва комментариев
		// this - текущий DOM элемент
		return val.toString(); 
	},
	comment_button_text: "Обсудить", // на мобильных устройствах при выборе дизайна с вызовом виджета по кнопке
	desktop_class: "tolstoycomments-feed", // класс элемента в который будет отрендерин виджет при встроенном в сайт дизайне
	success: () => {}, // callback функция для полчения доступа к методов виджета объекта tolstoycomments.vidget
	scroll_border_top: 0, // отступ сверху при отображении виджета встроенным в статью
	scroll_border_bottom: 0, // отступ снизу при отображении виджета встроенным в статью
	sso: null,
	mobile: IsMobile() // текущая платформа на которой был загружен виджет, по умолчанию используется встроенная функция
    };
</script>
```
Тексты встроенных функций используемые в инициализации виджета.
```javascript
/// javascript
function NormolizeURL(url) {
	let u = url,
		h = u.indexOf("#"),
		q = u.indexOf("?");
	if (h != -1) {
		u = u.substr(0, h);
	}
	if (q != -1) {
		u = u.substr(0, q);
	}
	return u;
}
function LoadTitle(win) {
	var titles = win.document.getElementsByTagName("title");
	if (titles.length > 0) {
		return trim(titles[0].innerText).substr(0, 255);
	} else {
		return "";
	}
}
function IsMobile() {
	return /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(window.navigator.userAgent);
}
```
## Встроенные события виджета
```javascript
/// javascript
window.tolstoycomments.vidget.on(event, callback); // подписываемся на событие
window.tolstoycomments.vidget.off(event, callback); // отписываемся от события
```
Поддерживаемые типы событий:
1. `ready` - вызывается после загрузки фрейма и до его показа
1. `domready` - вызывается после первого показа фрейма
1. `open` - событие открытия виджета
1. `close` - событие закрытия виджета
### Экспорт комментариев через API
Для выгрузки комментариев с сайта [tolstoycomments.com](https://tolstoycomments.com/) необходимо получить ключ доступа к API в одноименном разделе панели администрирования. В этом разделе необходимо сгенерировать новый ключ доступа к API.

SITE_ID задан в разделе Код - последний параметр скрипта инициализации виджета.
```html
/// html
...(window,document,'script','tolstoycomments','[SITE_ID]');</script>
```
### API получения списка комментариев
Все запросы к API нужно формировать на домен https://api.tolstoycomments.com
Для получения списка комментариев нужно сделать GET запрос:

`/api/export/[API_KEY]/site/[SITE_ID]/comment` - вернет 100 последних комментариев с сайта.

Для получения следующих 100 комментариев нужно подставить значение из поля comment_last_id в новый запрос к API:

`/api/export/[API_KEY]/site/[SITE_ID]/comment/[COMMENT_LAST_ID]` - вернет 100 последних комментариев с сайта которые были написаны раньше комментария COMMENT_LAST_ID

Выполняя запросы к API циклично можно получить все комментарии написаные за все время на вашем сайте.

Структура ответа сервера:
```json5
/// json
{
	"query": {
		"site_id": 0, // переданный в заголовке ваш id сайта
		"skip": null // переданное значение параметра COMMENT_LAST_ID
	},
	"data": {
		"count": 41, // кол-во комментариев в выгрузке
		"comment_last_id": null, // id последнего комментария для выгрузки следующей страницы, если null то значит это последняя страница
		"comments": [
			{
				"id": 186, // id комментария (формат - число)
				"message": "Текст сообщения", // текст сообщения (формат - текст)
				"ip": "", // ip адрес автора комментария  (формат - текст)
				"datеtime": "2017-11-27T10:06:08+00:00", // дата написания (формат - текст)
				"rating": 1, // значение рейтинга (формат - число)
				"attaches": [], // прикрепленный материал (формат - json)
				"visible": true, // true - комментарий опубликован на сайте, false - скрыт/удален/помечен как спам (формат - bool)
				"user": {
					"id": 0, // id пользователя (формат - число)
					"nick": "help", // ник (формат - текст)
					"name": "help", // имя на сайте (формат - текст)
					"email": "help@tolstoycomments.com", // email (формат - текст)
					"phone": "", // телефон (формат - текст)
					"avatar": null // ссылка на аватар (формат - текст)
				},
				"chat": {
					"id": 0, // id чата (формат - число)
					"url": "http://tolstoycomments.com/", // ссылка на страницу чата (формат - текст)
					"title": "Tolstoy comments - современные чаты на твоем сайте" // title чата (формат - текст)
				}
			}
		]
	}
}
```
## SSO авторизация
Для авторизации виджета нужно передать токен авторизации в параметре `sso`:
```html
/// html
<script type="text/javascript">
    window.tolstoycomments.config = { 
        sso: 'ТОКЕН ДЛЯ АВТОРИЗАЦИИ'
    };
</script>
```
Ниже представлены примеры формирования токена для авторизации с использованием различных языков программирования.
### Node.js
```node
/// Node.js
var arr = {
	id: "id0", // произвольная уникальная строка (50 знаков максимум) - обязательный параметр
	nick: "Иванов Иван", // имя пользователя (50 знаков максимум) - обязательный параметр
	email: "temp@temp.temp", // почта (250 знаков максимум) - необязательный параметр
	avatar: "https://static.tolstoycomments.com/ui/ac/b1/fa/acb1faad-2fad-441a-b789-da57f5317399.png" // ссылка на аватар - необязательный параметр
};
var key = "КЛЮЧ ДОСТУПА К API ИЗ НАСТРОЕК САЙТА";

var userdata = window.btoa(unescape(encodeURIComponent(JSON.stringify(arr))));
var microtime = Date.now();
var signtext = userdata + key + microtime.toString();
var sign = md5(signtext);
var sso = userdata + " " + sign + " " + microtime;
```
### PHP
```php
/// PHP
<?php
    $arr = array(
        'id' => 'id0',
        'nick' => 'Иванов Иван',
        'email' => 'temp@temp.temp',
        'avatar' => 'https://static.tolstoycomments.com/ui/ac/b1/fa/acb1faad-2fad-441a-b789-da57f5317399.png'
    );
    $key = "КЛЮЧ ДОСТУПА К API ИЗ НАСТРОЕК САЙТА";
    $userdata = base64_encode(json_encode($arr));
    $timestamp = round(microtime(true) * 1000);
    $sign = md5($userdata . $key . $timestamp);
    echo "$userdata $sign $timestamp";
?>
```
### Ruby
```ruby
content = Base64.strict_encode64(
  {
        id: 'id0',
      nick: 'Иванов Иван',
     email: 'temp@temp.temp',
    avatar: 'https://static.tolstoycomments.com/ui/ac/b1/fa/acb1faad-2fad-441a-b789-da57f5317399.png'
  }.to_json
)
timestamp = Time.now.to_i * 1_000
sign      = Digest::MD5.hexdigest("#{content}#{token}#{timestamp}")

[content, sign, timestamp].join(" ")
```
