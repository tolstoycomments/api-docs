# Документация по Tolstoy Comment Api
Эта документация расчитана на то, что вы уже прошли регистрацию на сайте [tolstoycomments.com](https://tolstoycomments.com/) и у вас уже доступ к личному кабинету. Так же для работы с документацией потребуется базовое знание основ JavaScript.
Основные вопросы на которые будут даны ответы в этой документации:
1. Как вставить виджет комментариев на сайт.
2. Как разместить счетчик комментариев под статьёй.
3. Как вывести счетчик комментариев на против каждой новости на главной и в разделе новостей странице.
4. Как вставить виджет с самыми обсуждаемыми статьями на сайте.
5. Как кстативить кнопку на сайт с вызовом виджета.

## Введение
Виджет Tolstoy Comment состоит из нескольких компонентов: 
- основной виджет с комментариями;
- счетчика кол-ва комментариев;
- дополнительных мини-виджетов.

## Инициализация
Основной скрипт инициализации загружает api для создания компонентов. При этом он не создает виджета с комментариями и других компонентов. Поэтому можно подключить основной код на все страницы вашего сайта. Место вставки кода особого значения не имеет, однако, если разместить основной скрипт внутри тега \<HEAD\> виджет будет загружаться быстрее.
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
		j.src = "//test.tolstoycomments.com/sitejs/app.js?i=" + l + "&x=" + x + "&t=" + w[l].t;
		f.parentNode.insertBefore(j, f);
	})(window, document, "script", "tolstoycomments", "SITE_ID");
</script>
<!-- /Tolstoy Comments Init -->
```
`SITE_ID` - идентификатор вашего сайта

После инициализации основго кода, можно делать перечу различных параметров в виджет. Есть два способа инициализации параметров:
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
> Параметры переданные через `window["tolstoycomments"].push` всегда будут перезаписывать параметры переданные через `window.tolstoycomments.config`.

## Подключение виджета с комментариями
По умолчанию основной код инициализации загружается с параметром `visible: false`. Чтобы виджет с комментариями отобрахился на странице сразу после ее загрузки, нужно передать в настройках инициализации `visible: true`:
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
Тег с классом `tolstoycomments-feed` задан в настройках по умолчанию. Как изменить класс можно найти в разделе всех параметров инициализации. Как будет выглядеть виджет на странице сайта, вы можете задать на странице дизайна в панели администрирования.

## Счетчик комментариев
Чтобы сделать вывод счетчика комментариев на странице, нужно вызвать код ниже. По умолчанию компонент счетчика комментариев выключен.
```html
/// html
<script type="text/javascript">
	window["tolstoycomments"] = window["tolstoycomments"] || [];
	window["tolstoycomments"].push({
		action: "init",
		values: {
			comment_class: "tolstoycomments-cc"
		}
	});
</script>
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

## Счетчик комментариев в собственном стиле
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
Функция `comment_render` вызывается после получения числа коментариев с сервера виджета. В переменной `val` передается число комментариев. В переменной `this` внутри функции будет передан html-элемент, в который происходит вывод. Функцию `comment_render` можно вызывать без return, в этом случае вы сами должны описать локигу вывода.
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

## Задание произвольного класса для вывода виджета
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

## Остались вопросы?
Если у вас есть вопросы, их можно задать, отправив по почте <help@tolstoycomments.com> или написать в виджете на странице лендинга [tolstoycomments.com](https://tolstoycomments.com/)
