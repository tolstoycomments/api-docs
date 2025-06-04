# Tolstoy Comment Api Documentation
*Read this in other languages: [Русский](README.md), [English](README.en.md)*

This documentation is intended for those who have already registered [tolstoycomments.com](https://tolstoycomments.com/auth/reg) and have access to the administration panel. JavaScript basic knowledge is also required to work with the documentation.

The documentation highlights:
1. How to add the comment widget to the website.
2. How to get the comment counter for the page.
3. How to display the comment counter for every news at the home page.
4. Miniwidgets: the most commented posts, the most rated comments, the last comments.
5. How to add the widget call button to the website.

## Table of contents
+ [Introduction](#introduction)
+ [Widget initialization](#init)
+ [Comment widget connection](#connect-comment)
+ [Comment linkage by URL and Identity](#init-identity)
+ [Comment counter](#counter-comment)
+ [Miniwidgets](#miniwidget)
+ [Personal style of the comment counter](#comment-render)
+ [User-defined class for the widget output](#desktop-class)
+ [Widget formats](#widget-format)
+ [Work methods with widget](#api)
    - [Methods - `widget`](#api-widget-method)
    - [Events - `widget`](#api-widget-event)
    - [Methods - `comment`](#api-comment-method)
    - [Methods - `miniwidgets`](#api-miniwidgets-method)
+ [Full list of all widget initialization parameters](#init-params)
+ [SSO authorization](#sso)
    - [Node.js](#sso-node)
    - [PHP](#sso-php)
    - [Ruby](#sso-ruby)
+ [Have questions?](#help)

## <a name="introduction"></a>Introduction
Tolstoy Comment widget consists of several parts:
- basic widget with comments;
- comment counter;
- extra miniwidgets.

## <a name="init"></a>Widget initialization
The basic initialization script loads api for the components’ creation. Meantime it doesn’t create a comment widget or other components. That’s why it is possible to connect the basic code to all the pages of your website. The code insertion place is irrelevant, but if you insert the basic script inside the \<HEAD\> tag, the widget will load quicker.

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
`SITE_ID` - your website identifier.

After the basic code was initialized it is possible to pass different parameters to the widget. There are two ways to initialize the parameters: 
1. by `window.tolstoycomments.push`;
Example:
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
2. by `window.tolstoycomments.config = { }` variable.
Example: 
```html
/// html
<script type="text/javascript">
    window.tolstoycomments.config = {
        visible: true
    };
</script>
```
We recommend the first variant with `window.tolstoycomments.push`.

Example of settings rewrite passed by `window.tolstoycomments.push`:
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
In this example the `visible: false` parameter will be passed to the widget because it is lower in the script.
> The parameters passed by `window["tolstoycomments"].push` will always rewrite those passed by `window.tolstoycomments.config`.

## <a name="connect-comment"></a> Comment widget connection
By default the basic initialization code is loaded with the `visible: false` parameter. To output the comment widget at the page right after its loading it’s necessary to pass the `visible: true` parameter in the initialization settings:
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
The tag with the `tolstoycomments-feed` class is in the settings by default. In the section devoted to all the initialization parameters you will find information on how to change the class. In the Tolstoy Comments administration panel you can change the widget design settings. 

## <a name="init-identity"></a>Comment linkage by URL and Identity
identity: a user-defined line that functions as a website page identifier (a number of the article in the base or a full link).
url: a link to the website page which will be used in the widget to get to the current website page. By default this is a 'document.location.href' value.

title: a website page title. By default this is a title from document.title.
```html
/// html
/* ... the widget basic code */
<script type="text/javascript">
    window["tolstoycomments"] = window["tolstoycomments"] || [];
    window["tolstoycomments"].push({
        action: "init",
        values: {
            identity: "Any user-defined line",
            url: "http://youdomen.com/youurl",
            title: "Page title"
        }
    });
</script>
```

## <a name="counter-comment"></a>Comment counter
To output the comment counter at the page you should call the code below. By default the comment counter component is off. 
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
To output the comment counter you should create html element with the class passed by the `comment_class` variable. Example:
```html
/// html
<span class="tolstoycomments-cc" data-url="https://google.com/"></span>
or
<span class="tolstoycomments-cc" data-identity="CUSTOM ID"></span>
```
Example of the comment counter with the text “No comments for now” by default:
```html
/// html
<span class="tolstoycomments-cc" data-url="https://google.com/">No comments for now</span>
or
<span class="tolstoycomments-cc" data-identity="CUSTOM ID">No comments for now</span>
```
In the `data-url` parameter you should pass the full link to the article for which it is necessary to output the number of comments. 

If you need to output the comment counter at the home page or at any section without outputting the widget itself it is enough to connect the widget basic code and the comment counter component code.
Example:
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

## <a name="miniwidget"></a>Miniwidgets
Miniwidgets make it possible to show the hottest posts at the website. You can get the miniwidget code from the section **Miniwidgets** of the administration panel. Example of the miniwidget code:
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
            title: "The most commented posts"
        }
    });
</script>
<!-- /Tolstoy Comments MiniWidget -->
```
`id` - element identifier to output the miniwidget

`key` - client key to output the miniwidget

`type` - type of the widget
Possible values: 
- `chattop` - chat list in descending order of the number of comments,
- `commenttop` - comment list in descending order of the rating
- `commentlast` - comment list in the order of publishing

`take` - number of the output posts (from 1 to 100)

`interval` - sampling interval from the current date: `day`, `week`, `month` or `year`

`title` - widget title

You can output as many widgets as you want at the page. It is just important to give them different id.

## <a name="comment-render"></a>Personal style of the comment counter
To create a personal style of the comment counter there is a `comment_render` function. Example of this function by default:

```html
/// html
/* ... the widget basic code */
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
The `comment_render` function is called after getting the number of comments from the widget server. The `val` variable conveys the number of comments. The `this` variable inside the function conveys the html-element where the output goes on. The `comment_render` function may be called without return, in this case you should describe the output logics by yourself.  
Example of the personal style of the comment counter:
```html
/// html
/* ... the widget basic code */
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
The result:
* 0 comments
* 1 comment
* 2 comments
* 5 comments

## <a name="desktop-class"></a>User-defined class for the widget output
The tag with the `tolstoycomments-feed` class is in the settings by default. You can change the class title giving the new title in the `desktop_class` parameter. 
Example:
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
## <a name="widget-format"></a>Widget formats
You can output the comment widget in different formats:
+ in the desktop version of the website
    - integrated into the article with floating height
    - integrated into the article with fixed height
    - in a separate window
+ in the mobile version of the website
    - integrated into the article with floating height
    - the “Discuss” button is integrated into the article - by clicking this button the widget opens full screen on top of the website.
    - the widget is fixed to the screen bottom - by clicking it opens full screen on top of the website.

You can manage the widget format by default with the help of the “Design” section of the administration panel.
```html
/// html
<div class="CUSTOM_CLASS_NAME"></div>
<script type="text/javascript">
    window["tolstoycomments"] = window["tolstoycomments"] || [];
    window["tolstoycomments"].push({
        action: "init",
        values: {
            // 0 - the widget is fixed to the screen edge,
            // 1 - integrated into the article with fixed height,
            // 2 - integrated into the article with floating height
            show_classic_desktop: 0,
            // 0 - the widget is fixed to the screen bottom, 
            // 1 - the “Discuss” button is integrated into the article, 
            // 2 - integrated into the article with floating height
            show_classic_mobile: 0,
            mobile: IsMobile() // true/false
        }
    });
</script>
```
By the initialization parameters you can redefine the widget output format and set your personal function for platform defining. You can get the current text of the IsMobile function in the section   [Full list of all widget initialization parameters](#init-params)

## <a name="api"></a>Work methods with widget
This section intends for the finer tuning of the widget integration.
The widget begins its work after the connection of the script from the Initialization section. (the *code* section of the administration panel).
After the initialization script loading the `success` method-function is called from the initialization script. Example:
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
The initialization script connects the basic components:
- `tolstoycomments.widget` - basic widget with comments;
- `tolstoycomments.comment` - comment counter;
- `tolstoycomments.miniwidgets` - miniwidgets.
### <a name="api-widget-method"></a>Methods - `widget`
- `tolstoycomments.widget.init()` - create widget with comments
- `tolstoycomments.widget.destroy()` - delete widget with comments
- `tolstoycomments.widget.open()` - open widget
- `tolstoycomments.widget.close()` - close widget
- `tolstoycomments.widget.isopen()` - return status of the widget (open/closed)
- `tolstoycomments.widget.navfirst()` - page will run with the settings passed into 'tolstoycomments.config'. In case the chat page matches the current page it won’t update.
```javascript
tolstoycomments.widget.nav({
    url: url, // url: string - facultative, by default: document.location.href
    title: title, // title: string - facultative, by default: document.title
    identity: identity // identity: string - facultative, by default: MD5(document.location.href)
})
```
- `tolstoycomments.widget.main()` - go to all chats page
- `tolstoycomments.widget.auth()` - go to the login page
### <a name="api-widget-event"></a>Events - `widget`
- `tolstoycomments.widget.on(event, callback)` - subscribe to event
- `tolstoycomments.widget.off(event, callback)` - unsubscribe from event

Supported types of the events:
- `ready` - is called after the widget with comment was loaded and before it was displayed
- `domready` - is called after the first display of the widget with comments
- `open` - the event of the widget opening
- `close` - the event of the widget closing
### <a name="api-comment-method"></a>Methods - `comment`
- `tolstoycomments.comment.update()` - update the number of comments in all the blocks with the `comment_class` class at the website page. This method suits the websites with loadable content, when you need to load the list of posts and to show the actual number of comments for them.
### <a name="api-miniwidgets-method"></a>Methods - `miniwidgets`
- `tolstoycomments.miniwidgets.create(obj)` - create miniwidget. The `obj` variable is equivalent to the list of parameters during the widget initialization.
```javascript
var obj = {
    id: "tolstoycomments-chattop",
    key: "KEY",
    type: "chattop",
    take: 5,
    interval: "month",
    title: "The most commented posts"
}
```
- `tolstoycomments.miniwidgets.destroy(id)` - delete miniwidget

## <a name="init-params"></a>Full list of all widget initialization parameters
This is the full list of all widget initialization parameters with default parameters
```html
/// html
<script type="text/javascript">
    window["tolstoycomments"] = window["tolstoycomments"] || [];
    window["tolstoycomments"].push({
        action: "init",
        values: {
            // if set true then show chat list
            main: false, 
            // identity of the current page
            identity: null, 
            // url of the current page, by default the integrated function is used
            url: NormolizeURL(document.location.href), 
            // title of the current page, by default the integrated function LoadTitle is used 
            title: LoadTitle(window), 
            // load the comment widget, by default the widget is hidden
            visible: false, 
            // class of elements to output number of comments for the articles
            comment_class: "", 
            // function of rendering number of comments, this - the current DOM element
            comment_render: CommentRender, 
            // на мобильных устройствах при выборе дизайна с вызовом виджета по кнопке
            comment_button_text: "Discuss", 
            // class of element in which the widget will be rendered with design integrated into the website
            desktop_class: "tolstoycomments-feed", 
            // callback function which is called after the initialization script loaded
            success: () => {}, 
            
            // top margin for the widget integrated into the article
            // this parameter is necessary if the website has a fixed top menu
            // and the widget is integrated into the article
            scroll_border_top: 0, 
            // bottom margin for the widget integrated into the article
            scroll_border_bottom: 0, 
            // SSO security token
            sso: null,
            // current platform the widget was loaded to 
            // By default the integrated function is used
            mobile: IsMobile(),
            // customization of the widget color theme
            // by default the CreateTheme function is used (described below) 
            // which uses the color variable from the administration panel
            // example of changing the link color
            // theme: { colorLink: 'red' }
            theme: null, // CreateTheme(color)
            // widget localization
	    lang: GetLang(), // "ar", "ru", "en", "uk", "tr", "pl" are supported
        }
    });
</script>
```
Texts of integrated functions used in the widget initialization.
```javascript
/// javascript
function CreateTheme(color) { // color: 0-360 number
    return {
        colorBlack: "#000",
        colorWrite: "#fff",
        colorLink: `hsl(${color}, 44%, 52%)`,
        colorLinkHover: `hsl(${color}, 44%, 40%)`,
        colorLinkNoActive: `hsl(${color}, 26%, 36%)`,
        colorLinkDark: `hsl(${color}, 44%, 76%)`,
        colorCommentBgDefault: `hsl(${color}, 44%, 93%)`,
        colorCommentSelect: `hsl(${color}, 43%, 85%)`,
        colorCommentFocus: `hsl(${color}, 42%, 72%`,
        colorBrend: `hsl(${color}, 45%, 71%)`,
        colorRed: "#ef4c47"
    };
}
function CommentRender(val) {
    return val.toString(); // this - the current DOM element
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

## Integrated events of the widget
```javascript
/// javascript
window.tolstoycomments.widget.on(event, callback); // subscribe to event window.tolstoycomments.widget.off(event, callback); // unsubscribe from event
```
Supported types of the events:
- `ready` - is called after the frame was loaded and before it was displayed
- `domready` - is called after the first display of the frame
- `open` - the event of the widget opening
- `close` - the event of the widget closing

## <a name="sso"></a>SSO authorization
To authorize the widget you need to pass the authorization token in the `sso` parameter:
```html
/// html
<script type="text/javascript">
    window["tolstoycomments"] = window["tolstoycomments"] || [];
    window["tolstoycomments"].push({
        action: "init",
        values: {
            sso: "AUTHORIZATION TOKEN"
        }
    });
</script>
```
Examples of the authorization token forming with the usage of different coding languages.
### <a name="sso-node"></a>Node.js
```node
/// Node.js
var arr = {
    id: "id0", // unique user-defined line (no more than 50 characters) - required parameter
    nick: "John Doe", // user name (no more than 50 characters) - required parameter
    email: "temp@temp.temp", // email address (no more than 250 characters) - optional parameter
    avatar: "https://static.tolstoycomments.com/ui/ac/b1/fa/acb1faad-2fad-441a-b789-da57f5317399.png" // link to avatar - optional parameter
};
var key = "ACCESS KEY TO API FROM WEBSITE SETTINGS";

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
        'nick' => 'John Doe',
        'email' => 'temp@temp.temp',
        'avatar' => 'https://static.tolstoycomments.com/ui/ac/b1/fa/acb1faad-2fad-441a-b789-da57f5317399.png'
    );
    $key = "ACCESS KEY TO API FROM WEBSITE SETTINGS";
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
      nick: 'John Doe',
     email: 'temp@temp.temp',
    avatar: 'https://static.tolstoycomments.com/ui/ac/b1/fa/acb1faad-2fad-441a-b789-da57f5317399.png'
  }.to_json
)
timestamp = Time.now.to_i * 1_000
sign      = Digest::MD5.hexdigest("#{content}#{token}#{timestamp}")

[content, sign, timestamp].join(" ")
```

## <a name="help"></a>Have questions?
If you have questions please message <help@tolstoycomments.com> or write at the landing page of the widget [tolstoycomments.com](https://tolstoycomments.com/)
