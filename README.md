# Документация по Tolstoy Comment Api
*Инструкция на других языках: [Русский](README.md), [English](README.en.md)*

Эта документация расcчитана на то, что вы уже прошли регистрацию на сайте [tolstoycomments.com](https://tolstoycomments.com/auth/reg), и у вас есть доступ к административной панели. Так же для работы с документацией потребуется базовое знание JavaScript.
Основные вопросы, которые освещаются в документации:
1. Как вставить виджет комментариев на сайт.
2. Как получить счетчик комментариев для страницы.
3. Как вывести счетчик комментариев напротив каждой новости на главной.
4. Мини-виджеты: самые обсуждаемые материалы, самые рейтинговые комментарии, последние комментарии.
5. Как вставить кнопку на сайт с вызовом виджета.

## Содержание
+ [Введение](#introduction)
+ [Инициализация виджета](#init)
+ [Подключение виджета с комментариями](#connect-comment)
+ [Сортировка и древовидный вид комментариев](#comment-sort-and-format)
+ [Привязка комментариев по URL и Identity](#init-identity)
+ [Счетчик комментариев](#counter-comment)
+ [Счетчик реакций](#counter-reaction)
+ [Мини-виджеты](#miniwidget)
	- [Мини-виджет / Профиль пользователя](#miniwidget-profile)
	- [Мини-виджет / Реакции](#miniwidget-reaction)
	- [Мини-виджеты / Самые обсуждаемые записи / Последние комментарии / Лучшие комментарии](#miniwidget-default)
+ [Собственный стиль счетчика комментариев](#comment-render)
+ [Задание произвольного класса для вывода виджета](#desktop-class)
+ [Форматы виджета](#widget-format)
+ [Цветовая схема виджета](#theme)
+ [Методы работы с виджетом](#api)
	- [Методы - `widget`](#api-widget-method)
	- [События - `widget`](#api-widget-event)
	- [Методы - `comment`](#api-comment-method)
	- [Методы - `miniwidgets`](#api-miniwidgets-method)
+ [Полный список всех параметров инициализации виджета](#init-params)
+ [SSO авторизация](#sso)
	- [Node.js](#sso-node)
	- [PHP](#sso-php)
	- [Ruby](#sso-ruby)
+ [Остались вопросы?](#help)

## <a name="introduction"></a>Введение
Виджет Tolstoy Comment состоит из нескольких компонентов: 
- основного виджета с комментариями;
- счетчика колличества комментариев;
- дополнительных мини-виджетов.

## <a name="init"></a>Инициализация виджета
Основной скрипт инициализации загружает api для создания компонентов. При этом он не создает виджета с комментариями и других компонентов. Поэтому можно подключить основной код на все страницы вашего сайта. Место вставки кода особого значения не имеет, однако, если разместить основной скрипт внутри тега \<HEAD\>, виджет будет загружаться быстрее.
```html
/// html
<!-- Tolstoy Comments Init -->
<script type="text/javascript">
	!(function(w, d, s, l, x) {
		w[l] = w[l] || [];
		w[l].t = w[l].t || new Date().getTime();
		var f = d.getElementsByTagName(s)[0],
			j = d.createElement(s);
		j.async = !0;
		j.src = "//web.tolstoycomments.com/sitejs/app.js?i=" + l + "&x=" + x + "&t=" + w[l].t;
		f.parentNode.insertBefore(j, f);
	})(window, document, "script", "tolstoycomments", "SITE_ID");
</script>
<!-- /Tolstoy Comments Init -->
```
`SITE_ID` - идентификатор вашего сайта

После инициализации основного кода, можно делать передачу различных параметров в виджет. Есть два способа инициализации параметров:
1. через `window.tolstoycomments.push`;
пример:
```html
/// html
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "init",
		values: {
			visible: true
		}
	});
</script>
```
2. переменной `window.tolstoycomments.config = { }`.
пример: 
```html
/// html
<script type="text/javascript">
	window.tolstoycomments.config = {
		visible: true
	};
</script>
```
Мы рекомендуем использовать первый вариант с `window.tolstoycomments.push`.

Пример перезаписи настроек переданных через `window.tolstoycomments.push`:
```html
/// html
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({ action: "init", values: { visible: true } });
</script>
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({ action: "init", values: { visible: false } });
</script>
```
Здесь в виджет будет передан `visible: false` поскольку оно находится ниже.
> Параметры, переданные через `window["tolstoycomments"].push` всегда будут перезаписывать параметры переданные через `window.tolstoycomments.config`.

## <a name="connect-comment"></a>Подключение виджета с комментариями
По умолчанию основной код инициализации загружается с параметром `visible: false`. Чтобы виджет с комментариями отобразился на странице сразу после ее загрузки, нужно передать в настройках инициализации `visible: true`:
```html
/// html
<!-- Tolstoy Comments Widget -->
<div class="tolstoycomments-feed"></div>
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "init",
		values: {
			visible: true
		}
	});
</script>
<!-- /Tolstoy Comments Widget -->
```
Тег с классом `tolstoycomments-feed` задан в настройках по умолчанию. Как изменить класс, можно найти в разделе всех параметров инициализации. Изменить настройки дизайна виджета вы можете в панели администрирования Tolstoy Comments.

## <a name="comment-sort-and-format"></a>Сортировка комментариев и древовидный вид
По умолчанию комментарии в виджете выводятся без вложенности с сортировкой "старые сверху". Менять вложенность и сортировку для всех страниц виджета можно через панель администрирования виджетом: "Дизайн", "Вложенность сообщений" и "Сортировать сообщения". Переопределить поведение виджета для отдельной страницы можно с помощью параметров инициализации:
```html
/// html
<!-- Tolstoy Comments Widget -->
<div class="tolstoycomments-feed"></div>
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "init",
		values: {
			// "asc" - сначала старые (по умолчанию)
			// "desc" - сначала новые
			// "rating" - по рейтингу
			comment_show_sort: null, // null - сначала старые (по умолчанию)
			// вложенность комментариев
			// "linear" - без вложенности (по умолчанию)
			// "tree" - вложенный формат отображения
			comment_show_format: null, // null - без вложенности (по умолчанию)
		}
	});
</script>
<!-- /Tolstoy Comments Widget -->
```

## <a name="init-identity"></a>Привязка комментариев по URL и Identity
identity: произвольная строка, которая служит идентификатором страницы сайта (это может быть порядковый номер статьи в базе или полная ссылка).

url: ссылка на страницу сайта, которая будет использована в виджете для перехода на текущую страницу сайта. По умолчанию берется значение 'document.location.href'.

title: заголовок страницы сайта. По умолчанию берется заголовок из document.title.
```html
/// html
/* ... основной код виджета */
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "init",
		values: {
			identity: "Любая произвольная строка",
			url: "http://youdomen.com/youurl",
			title: "Заголовок страницы"
		}
	});
</script>
```

## <a name="counter-comment"></a>Счетчик комментариев
Чтобы сделать вывод счетчика комментариев на странице, нужно вызвать код ниже. По умолчанию компонент счетчика комментариев выключен.
```html
/// html
<!-- Tolstoy Comments Comment Counter -->
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "init",
		values: {
			comment_class: "tolstoycomments-cc"
		}
	});
</script>
<!-- /Tolstoy Comments Comment Counter -->
```
Для вывода счетчика комментариев нужно создать html элемент с классом, переданным в переменной `comment_class`. Пример:
```html
/// html
<span class="tolstoycomments-cc" data-url="https://google.com/"></span>
или
<span class="tolstoycomments-cc" data-identity="CUSTOM ID"></span>
```
Пример счетчика с текстом `Нет комментариев` по умолчанию:
```html
/// html
<span class="tolstoycomments-cc" data-url="https://google.com/">Нет комментариев</span>
или
<span class="tolstoycomments-cc" data-identity="CUSTOM ID">Нет комментариев</span>
```
В параметре `data-url` нужно передать полную ссылку на статью, для которой необходимо сделать вывод количества комментариев.

Если нужно вывести счетчик комментариев на главной странице или в разделе, но при этом не выводить сам виджет, то достаточно подключить основной код виджета и код компонента счетчика комментариев.
Пример:
```html
/// html
<!-- Tolstoy Comments Init -->
<script type="text/javascript">
	!(function(w, d, s, l, x) {
		w[l] = w[l] || [];
		w[l].t = w[l].t || new Date().getTime();
		var f = d.getElementsByTagName(s)[0],
			j = d.createElement(s);
		j.async = !0;
		j.src = "//web.tolstoycomments.com/sitejs/app.js?i=" + l + "&x=" + x + "&t=" + w[l].t;
		f.parentNode.insertBefore(j, f);
	})(window, document, "script", "tolstoycomments", "SITE_ID");
</script>
<!-- /Tolstoy Comments Init -->

<!-- Tolstoy Comments Comment Counter -->
<div class="tolstoycomments-feed"></div>
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "init",
		values: {
			comment_class: "tolstoycomments-cc"
		}
	});
</script>
<!-- /Tolstoy Comments Comment Counter -->
```

## <a name="counter-comment"></a>Счетчик реакций
Для вывода счетчика реакций на странице, нужно подключаить параметры инициализации счетчика реакций
```html
/// html
<!-- Tolstoy Comments Reaction Counter -->
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "init",
		values: {
			reaction_class: "tolstoycomments-rc"
		}
	});
</script>
<!-- /Tolstoy Comments Reaction Counter -->
```
Для вывода счетчика реакций нужно создать html элемент с классом, переданным в переменной `reaction_class`. Пример:
```html
/// html
<span class="tolstoycomments-rc" data-type="votes" data-url="https://google.com/"></span>
или
<span class="tolstoycomments-rc" data-type="votes" data-identity="CUSTOM ID"></span>
```
Пример счетчика с текстом `Нет реакций` по умолчанию:
```html
/// html
<span class="tolstoycomments-rc" data-type="votes" data-url="https://google.com/">Нет реакций</span>
или
<span class="tolstoycomments-rc" data-type="votes" data-identity="CUSTOM ID">Нет реакций</span>
```
В параметре `data-url` нужно передать полную ссылку на статью, для которой необходимо сделать вывод количества реакций.

Если нужно вывести счетчик реакций на главной странице или в разделе, но при этом не выводить сам виджет, то достаточно подключить основной код виджета и код счетчика реакций.
Пример:
```html
/// html
<!-- Tolstoy Comments Init -->
<script type="text/javascript">
	!(function(w, d, s, l, x) {
		w[l] = w[l] || [];
		w[l].t = w[l].t || new Date().getTime();
		var f = d.getElementsByTagName(s)[0],
			j = d.createElement(s);
		j.async = !0;
		j.src = "//web.tolstoycomments.com/sitejs/app.js?i=" + l + "&x=" + x + "&t=" + w[l].t;
		f.parentNode.insertBefore(j, f);
	})(window, document, "script", "tolstoycomments", "SITE_ID");
</script>
<!-- /Tolstoy Comments Init -->

<!-- Tolstoy Comments Reaction Counter -->
<div class="tolstoycomments-feed"></div>
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "init",
		values: {
			reaction_class: "tolstoycomments-rc"
		}
	});
</script>
<!-- /Tolstoy Comments Reaction Counter -->
```

## <a name="miniwidget"></a>Мини-виджеты
### <a name="miniwidget-profile"></a>Мини-виджет / Профиль пользователя
Пример реализации профиля пользвоателя можно посмотреть в [нашем блоге](https://blogs.tolstoycomments.com/mw-profile/)
```html
/// html
<!-- Tolstoy Comments Init -->
<script type="text/javascript">
	!(function(w, d, s, l, x) {
		w[l] = w[l] || [];
		w[l].t = w[l].t || new Date().getTime();
		var f = d.getElementsByTagName(s)[0],
			j = d.createElement(s);
		j.async = !0;
		j.src = "//web.tolstoycomments.com/sitejs/app.js?i=" + l + "&x=" + x + "&t=" + w[l].t;
		f.parentNode.insertBefore(j, f);
	})(window, document, "script", "tolstoycomments", "SITE_ID");
</script>
<!-- /Tolstoy Comments Init -->

<!-- Tolstoy Comments EventsWidget -->
<div id="tolstoycomments-profile"></div>
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "eventwidget",
		values: {
			id: "tolstoycomments-profile",
			key: "KEY",
			locale_login: "Войти", // текст кнопки перед авторизацией
			profile: "https://domain.xyz/profile#tc-profile", // ссылка на профиль пользователя
			profile_target_blank: true, // открывать ссылку на профиль пользователя в новой вкладке
			events: "https://domain.xyz/profile#tc-events", // лента уведомлений пользователя
			events_target_blank: true // открывать ссылку на ленту уведомлений пользователя в новой вкладке
		}
	});
</script>
<!-- /Tolstoy Comments EventsWidget -->
```
### <a name="miniwidget-reaction"></a>Мини-виджет / Реакции
Мини-виджет “Реакции” позволяет пользователям оценивать ваши материалы. Доступны два формата виджета реакций: emoji и звезды. Параметры url, title, identity при отсутствии будут браться из кода инициализации виджета комментариев. Если виджет комментариев на странице отсутствует то параметры будут сформированы автоматически по принципу формирования таких праметров для виджета комментариев.
Пример виджета реакций в [нашем блоге](https://blogs.tolstoycomments.com/reactions/):
```html
/// html
<!-- Tolstoy Comments Init -->
<script type="text/javascript">
	!(function(w, d, s, l, x) {
		w[l] = w[l] || [];
		w[l].t = w[l].t || new Date().getTime();
		var f = d.getElementsByTagName(s)[0],
			j = d.createElement(s);
		j.async = !0;
		j.src = "//web.tolstoycomments.com/sitejs/app.js?i=" + l + "&x=" + x + "&t=" + w[l].t;
		f.parentNode.insertBefore(j, f);
	})(window, document, "script", "tolstoycomments", "SITE_ID");
</script>
<!-- /Tolstoy Comments Init -->

<!-- Tolstoy Comments MiniWidget -->
<div id="tolstoycomments-reaction-emoji"></div>
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "reaction",
		values: {
			id: "tolstoycomments-reaction-emoji",
			key: "KEY",
			header: "Оцените материал", // заголовок виджета (null - поле отсутствует)
			description: "Всего голосов: {count}", // описание виджета (null - поле отсутствует); {count} - переменная, заменяется в виджете на число
			type: "emoji",
			emoji: ["1f44d","1f44e"], // коды emoji, допускается использовать от 1 до 8 иконок. (все коды emoji в этом репозитории https://github.com/twitter/twemoji/tree/master/assets/72x72)
			hide_default_values: false, // скрывать оценки до выставления голоса (false - по умолчанию)
			disable_update_values: false, // запретить переголосовывать (false - по умолчанию)
			identity: null, // identity текущей страницы
			url: NormolizeURL(document.location.href), // url текущий страницы, по умолчанию используется встроенная функция
			title: LoadTitle(window), // заголовок текущей страницы, по умолчанию используется встроенная функция LoadTitle
		},
	});
</script>
<!-- /Tolstoy Comments MiniWidget -->
```
Пример виджета со звездами:
```html
/// html
<!-- Tolstoy Comments Init -->
<script type="text/javascript">
	!(function(w, d, s, l, x) {
		w[l] = w[l] || [];
		w[l].t = w[l].t || new Date().getTime();
		var f = d.getElementsByTagName(s)[0],
			j = d.createElement(s);
		j.async = !0;
		j.src = "//web.tolstoycomments.com/sitejs/app.js?i=" + l + "&x=" + x + "&t=" + w[l].t;
		f.parentNode.insertBefore(j, f);
	})(window, document, "script", "tolstoycomments", "SITE_ID");
</script>
<!-- /Tolstoy Comments Init -->

<!-- Tolstoy Comments MiniWidget -->
<div id="tolstoycomments-reaction-stars"></div>
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "reaction",
		values: {
			id: "tolstoycomments-reaction-stars",
			key: "KEY",
			header: "Оцените материал", // заголовок виджета (null - поле отсутствует)
			description: "Всего голосов: {count}. Средняя оценка: {avg}", // описание виджета (null - поле отсутствует); {count} - переменная, заменяется в виджете на число: {avg} - средняя оценка
			type: "stars",
			hide_default_values: false, // скрывать среднюю оценку до выставления голоса (false - по умолчанию)
			disable_update_values: false, // запретить переголосовывать (false - по умолчанию)
			identity: null, // identity текущей страницы
			url: NormolizeURL(document.location.href), // url текущий страницы, по умолчанию используется встроенная функция
			title: LoadTitle(window), // заголовок текущей страницы, по умолчанию используется встроенная функция LoadTitle
		},
	});
</script>
<!-- /Tolstoy Comments MiniWidget -->
```
### <a name="miniwidget-default"></a>Мини-виджеты / Самые обсуждаемые записи / Последние комментарии / Лучшие комментарии
Мини-виджеты позволяют вывести самые горячие материалы на сайте. Получить код минивиджета можно из раздела **Мини-виджеты** панели администрирования. Пример кода мини-виджета:
```html
/// html
<!-- Tolstoy Comments Init -->
<script type="text/javascript">
	!(function(w, d, s, l, x) {
		w[l] = w[l] || [];
		w[l].t = w[l].t || new Date().getTime();
		var f = d.getElementsByTagName(s)[0],
			j = d.createElement(s);
		j.async = !0;
		j.src = "//web.tolstoycomments.com/sitejs/app.js?i=" + l + "&x=" + x + "&t=" + w[l].t;
		f.parentNode.insertBefore(j, f);
	})(window, document, "script", "tolstoycomments", "SITE_ID");
</script>
<!-- /Tolstoy Comments Init -->

<!-- Tolstoy Comments MiniWidget -->
<div id="tolstoycomments-chattop"></div>
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "miniwidget",
		values: {
			id: "tolstoycomments-chattop",
			key: "KEY",
			type: "chattop",
			take: 5,
			interval: "month",
			title: "Самые обсуждаемые записи"
		}
	});
</script>
<!-- /Tolstoy Comments MiniWidget -->
```
`id` - идентификатор элемента для вывода мини-виджета

`key` - клиентский ключ для вывода виджета

`type` - тип виджета. 
Возможные значения: 
- `chattop` - список чатов по убыванию кол-ва комментариев, 
- `commenttop` - список комментариев по убыванию рейтинга, 
- `commentlast` - список комментариев по дате публикации.

`take` - количество выводимых записей (от 1 до 100)

`interval` - интервал выборки от текущей даты: `day`, `week`, `month` или `year`

`title` - заголовок виджета

На странице можно вывести сколько угодно виджетов, главное задавать им всем разный id.

## <a name="comment-render"></a>Собственный стиль счетчика комментариев
Для создания собственного стиля вывода числа комментариев предусмотрена функция `comment_render`. Пример этой функции по умолчанию:
```html
/// html
/* ... основной код виджета */
<script>
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "init",
		values: {
			comment_render: function(val) {
				return val.toString();
			}
		}
	});
</script>
```
Функция `comment_render` вызывается после получения числа комментариев с сервера виджета. В переменной `val` передается число комментариев. В переменной `this` внутри функции будет передан html-элемент, в который происходит вывод. Функцию `comment_render` можно вызывать без return, в этом случае вы сами должны описать логику вывода.
Пример собственного стиля вывода счетчика комментариев:
```html
/// html
/* ... основной код виджета */
<script>
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "init",
		values: {
			comment_render: function(val) {
				this.textContent = val + " комментари" + GetNumEnding(val, "й", "я", "ев");
			}
		}
	});
	var GetNumEnding = function(n, a, b, c) {
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
	};
</script>
```
Результат:
* 0 комментариев
* 1 комментарий
* 2 комментария
* 5 комментариев

## <a name="desktop-class"></a>Задание произвольного класса для вывода виджета
Тег с классом `tolstoycomments-feed` задан в настройках по умолчанию. Название класса можно изменить, задав имя нужного класса в параметре `desktop_class`.
Пример:
```html
/// html
<div class="CUSTOM_CLASS_NAME"></div>
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "init",
		values: {
			desktop_class: 'CUSTOM_CLASS_NAME'
		}
	});
</script>
```
## <a name="widget-format"></a>Форматы виджета
Виджет комментариев можно выводить в различных форматах:
+ на desktop версии сайта
	- встроенный в статью с плавающей высотой
	- встроенный в статью фиксированной высотой
	- отдельным окном
+ на мобильной версии сайта
	- встроенный в статью с плавающей высотой
	- в статью встраивается кнопка "Обсудить" - по клику на кнопку виджет раскрывается во весь экран поверх сайта
	- виджет приклеен к нижней части экрана - по клику раскрывается во весь экран поверх сайта

Управлять форматом виджета по умолчанию можно с помощью раздела *дизайн* панели администрирования.
```html
/// html
<div class="CUSTOM_CLASS_NAME"></div>
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "init",
		values: {
			// 0 - виджет приклеен к краю экрана, 
			// 1 - встроенный в статью фиксированной высотой,
			// 2 - встроенный в статью с плавающей высотой
			show_classic_desktop: 0,
			// 0 - виджет приклеен к нижней части экрана, 
			// 1 - в статью встраивается кнопка "Обсудить", 
			// 2 - встроенный в статью с плавающей высотой
			show_classic_mobile: 0,
			mobile: IsMobile() // true/false
		}
	});
</script>
```
Через параметры инициализации можно переопределить формат вывода виджета и задать собственную функцию определения платформы. Текст текущей функции IsMobile доступен в разделе [Полный список всех параметров инициализации виджета](#init-params)

## <a name="theme"></a>Цветовая схема виджета
Управление цветовой схемой виджета в полном объеме доступна через панель администрирования. Тем не менее в некоторых случаях необходимо если необходимо использовать несколько цветовых решений на разных страницах сайта можно воспользоваться методом задание цветов через через код инициализации:

**Светлая тема по умолчанию**
```html
/// html
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "init",
		values: {
			theme: {
				// white theme
				colorBlackDark: "#333333",
				colorBlackMiddle: "#808080",
				colorBlackLight: "#e6e6e6",
				colorBlack: "#000",
				colorMainDark: "#1D3F49",
				colorMainMiddleDark: "#2C616F",
				colorMain: "#4FA3BA",
				colorMainMiddle: "#93C6D5",
				colorMainLight: "#DBECF1",
				colorAlert: "#EF4C47",
				colorBackground: "#fff",
			},
		},
	});
</script>
```

**Темная тема по умолчанию**
```html
/// html
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "init",
		values: {
			theme: {
				// black
				colorBlackDark: "#fff",
				colorBlackMiddle: "#808080",
				colorBlackLight: "#333333",
				colorBlack: "#fff",
				colorMainDark: "#DBECF1",
				colorMainMiddleDark: "#93C6D5",
				colorMain: "#4FA3BA",
				colorMainMiddle: "#2C616F",
				colorMainLight: "#1D3F49",
				colorAlert: "#EF4C47",
				colorBackground: "#000",
			},
		},
	});
</script>
```

## <a name="api"></a>Методы работы с виджетом
Этот раздел предназначен для более тонкой настройки интеграции виджета.
Виджет начинает работу после подключения скрипта из раздела Инициализация. (раздел *код* из панели администрирования.
После загрузки скрипта инициализации вызывается метод-функция `success` из скрипта инициализации. Пример:
```html
/// html
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "init",
		values: {
			success: function() {
				// your code
			}
		}
	});
</script>
```
Скрипт инициализации подключает основные компоненты:
- `tolstoycomments.widget` - основной виджет с комментариями;
- `tolstoycomments.comment` - счетчик кол-ва комментариев;
- `tolstoycomments.miniwidgets` - мини-виджеты.
### <a name="api-widget-method"></a>Методы - `widget`
- `tolstoycomments.widget.init()` - создать виджет с комментариями
- `tolstoycomments.widget.destroy()` - удалить виджет с комментариями
- `tolstoycomments.widget.open()` - открывает виджет
- `tolstoycomments.widget.close()` - закрыть виджет
- `tolstoycomments.widget.isopen()` - возвращает состояния виджета (открыт/закрыт)
- `tolstoycomments.widget.navfirst()` - страница будет запущена с настройками, переданными в 'tolstoycomments.config'. В случае, если страница чата совпадает с текущей открытой, обновление не произойдет.
- перейти на страницу чата
```javascript
tolstoycomments.widget.nav({
	url: url, // url: string - не обязательный, по умолчанию: document.location.href
	title: title, // title: string - не обязательный, по умолчанию: document.title
	identity: identity // identity: string - не обязательный, по умолчанию: MD5(document.location.href)
})
```
- `tolstoycomments.widget.main()` - перейти на страницу всех чатов
- `tolstoycomments.widget.auth()` - перейти на страницу авторизации
### <a name="api-widget-event"></a>События - `widget`
- `tolstoycomments.widget.on(event, callback)` - подписаться на событие
- `tolstoycomments.widget.off(event, callback)` - отписаться от события

Поддерживаемые типы событий:
- `ready` - вызывается после загрузки виджета с комментариями и до его показа
- `domready` - вызывается после первого показа виджета с комментариями
- `open` - событие открытия виджета
- `close` - событие закрытия виджета
### <a name="api-comment-method"></a>Методы - `comment`
- `tolstoycomments.comment.update()` - обновить кол-во комментариев во всех блоках с классов `comment_class` на странице сайта. Этот метод подойдет для сайтов с подгружаемым содержимым, когда нужно подгрузить список публикаций и показать для них актуальное кол-во комментариев.
### <a name="api-miniwidgets-method"></a>Методы - `miniwidgets`
- `tolstoycomments.miniwidgets.create(obj)` - создать минивиджет. Переменная `obj` аналогична списку параметров при инициализации минивиджета.
```javascript
var obj = {
	id: "tolstoycomments-chattop",
	key: "KEY",
	type: "chattop",
	take: 5,
	interval: "month",
	title: "Самые обсуждаемые записи"
}
```
- `tolstoycomments.miniwidgets.destroy(id)` - удалить минивиджет
## <a name="init-params"></a>Полный список всех параметров инициализации виджета
Указан весь список параметров инициализации виджета с параметрами по умолчанию
```html
/// html
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "init",
		values: {
			// если задать true то выведить список чатов
			main: false, 
			// identity текущей страницы
			identity: null, 
			// url текущий страницы, по умолчанию используется встроенная функция
			url: NormolizeURL(document.location.href), 
			// заголовок текущей страницы, по умолчанию используется встроенная функция LoadTitle
			title: LoadTitle(window), 
			// загружать виджет комментариев, по умолчанию виджет скрыт
			visible: false, 
			// класс элементов для вывода кол-ва комментариев в статьях
			comment_class: "", 
			// функция рендеринга кол-ва комментариев, this - текущий DOM элемент
			comment_render: CommentRender, 
			// класс элементов для вывода кол-ва реакций в статьях
			reaction_class: "", 
			// функция рендеринга кол-ва реакций, this - текущий DOM элемент
			reaction_render: ReactionRender, 
			// на мобильных устройствах при выборе дизайна с вызовом виджета по кнопке
			comment_button_text: "Обсудить", 
			// класс элемента в который будет отрендерин виджет при встроенном в сайт дизайне
			desktop_class: "tolstoycomments-feed", 
			// callback функция которая вызвается после загрузки скрипта инициализации
			success: () => {}, 
			
			// отступ сверху при отображении виджета встроенным в статью
			// этот параметр необходим если на сайте есть фиксированное меню сверху 
			// и виджет установлен как встроенный в статью
			scroll_border_top: 0, 
			// отступ снизу при отображении виджета встроенным в статью
			scroll_border_bottom: 0, 
			// токен sso авторизации
			sso: null,
			// текущая платформа на которой был загружен виджет, 
			// по умолчанию используется встроенная функция
			mobile: IsMobile(),
			// кастомные настойки цветовой схемы виджета
			// по умолчанию цвета берутся из панели администрирования
			// образец передачи произвольной цветовой схемы в виджет 
			// описан в разделе Цветовая схема виджета
			theme: null,
			// локализация виджета
			lang: GetLang(), // в настроящее время поддерживаются локали ru и en
			// сортировка комментариев
			// "asc" - сначала старые (по умолчанию)
			// "desc" - сначала новые
			comment_show_sort: null, // null - сначала старые (по умолчанию)
			// вложенность комментариев
			// "linear" - без вложенности (по умолчанию)
			// "tree" - вложенный формат отображения
			comment_show_format: null, // null - без вложенности (по умолчанию)
			// скрытие из чата раздела со всеми чатами
			hide_all_chats: false, // true - скрывает "все чаты"
		}
	});
</script>
```
Тексты встроенных функций, используемые в инициализации виджета.
```javascript
/// javascript
function CommentRender(val) {
	return val.toString(); // this - текущий DOM элемент
}
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
function GetLang(){
	return window.navigator.language;
}
```

## Встроенные события виджета
```javascript
/// javascript
window.tolstoycomments.widget.on(event, callback); // подписываемся на событие
window.tolstoycomments.widget.off(event, callback); // отписываемся от события
```
Поддерживаемые типы событий:
1. `ready` - вызывается после загрузки фрейма и до его показа
1. `domready` - вызывается после первого показа фрейма
1. `open` - событие открытия виджета
1. `close` - событие закрытия виджета

## <a name="sso"></a>SSO авторизация
Для авторизации виджета нужно передать токен авторизации в параметре `sso`:
```html
/// html
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "init",
		values: {
			sso: "ТОКЕН ДЛЯ АВТОРИЗАЦИИ"
		}
	});
</script>
```
Ниже представлены примеры формирования токена для авторизации с использованием различных языков программирования.
### <a name="sso-node"></a>Node.js
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
### <a name="sso-php"></a>PHP
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
### <a name="sso-ruby"></a>Ruby
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

## <a name="help"></a>Остались вопросы?
Если у вас есть вопросы, их можно задать, отправив по почте <help@tolstoycomments.com> или написать в виджете на странице лендинга [tolstoycomments.com](https://tolstoycomments.com/)
