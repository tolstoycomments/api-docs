# Tolstoy Comment Api
## Вставка кода на сайт
Образец кода для вставки на сайт можно взять после прохождения регистрации на сайте [tolstoycomments.com](http://panel.tolstoycomments.com/)
### Доступ к методам виджета
```typescript
window.tolstoycomments.vidget
```
### Открыть виджет
```typescript
window.tolstoycomments.vidget.open(): void;
```
### Закрыть виджет
```typescript
window.tolstoycomments.vidget.close(): void;
```
### Инициализовать виджет
по умолчанию, при загрузке страници, вызывается инициализация виджета
```typescript
window.tolstoycomments.vidget.init(): void;
```
### Удалить виджет
```typescript
window.tolstoycomments.vidget.destroy(): void;
```
### Смена чата
```typescript
// url - не обязательный аргумент, по умолчанию: document.location.href
// title - не обязательный аргумент, по умолчанию: document.title
window.tolstoycomments.vidget.nav(url: string, title: string): void;
```
### Запрос количества коментариев
```typescript
// url - url адрес или массив строк url адресов по которым
// нужно сделать подсчет кол-ва каментов
// callback - функция обратного вызова после получения
// ответа от сервера с данными
// в качестве аргумента в нее передается массив чисел с 
// количеством комментариев по каждой ссылке в том порядке, 
// в каком были переданы ссылки массива url адресов.
// если был передан только один url то в callback будет 
// возврат массива с одним значением
window.tolstoycomments.vidget.countcomment(url: string[] | string, callback: (count: number[]) => void): void
```
