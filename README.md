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
> Параметры переданные через `window["tolstoycomments"].push` всегда будут перезаписывать параметры переданные через `window.tolstoycomments.config`.
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
Здесь в виджет будет передан `visible: false`.

## Подключение виджета с комментариями
Чтобы вывести виджет с комментариями на странице сайта сразу после ее загрузки нужно разместить следующий код:
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
По умолчанию основной код инициализации загружается с параметром `visible: false`. Чтобы виджет с комментариями отобрахился на странице нужно передать в настройках инициализации `visible: true`. Тег с классом `tolstoycomments-feed` задан в настройках по умолчанию. Название класса можно изменить, задав имя нужного класса в параметре `desktop_class`.
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
