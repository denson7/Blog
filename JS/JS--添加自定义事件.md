## 自定义事件
```javascript
const triggerEvent = (el, eventType, detail) =>
  el.dispatchEvent(new CustomEvent(eventType, { detail }));

// 事例
triggerEvent(document.getElementById('myId'), 'click');
triggerEvent(document.getElementById('myId'), 'click', { username: 'bob' });
```
自定义事件的函数有 Event、CustomEvent 和 dispatchEvent。
```javascript
// 向 window派发一个resize内置事件
window.dispatchEvent(new Event('resize'));


// 直接自定义事件，使用 Event 构造函数：
var event = new Event('build');
var elem = document.querySelector('#id');
// 监听事件
elem.addEventListener('build', function (e) { ... }, false);
// 触发事件.
elem.dispatchEvent(event);
```
CustomEvent 可以创建一个更高度自定义事件，还可以附带一些数据，具体用法如下
```javascript
var myEvent = new CustomEvent(eventname, options);
// 其中 options 可以是：
{
  detail: {
  ...
  },
  bubbles: true,    //是否冒泡
  cancelable: false //是否取消默认事件
}
```
其中 detail 可以存放一些初始化的信息，可以在触发的时候调用。其他属性就是定义该事件是否具有冒泡等等功能。

内置的事件会由浏览器根据某些操作进行触发，自定义的事件就需要人工触发。 dispatchEvent 函数就是用来触发某个事件：
```javascript
element.dispatchEvent(customEvent);
```
```javascript
// add an appropriate event listener
obj.addEventListener("cat", function(e) { process(e.detail) });

// create and dispatch the event
var event = new CustomEvent("cat", {"detail":{"hazcheeseburger":true}});
obj.dispatchEvent(event);
// 使用自定义事件需要注意兼容性问题，而使用 jQuery 就简单多了：

// 绑定自定义事件
$(element).on('myCustomEvent', function(){});

// 触发事件
$(element).trigger('myCustomEvent');
// 此外，你还可以在触发自定义事件时传递更多参数信息：

$( "p" ).on( "myCustomEvent", function( event, myName ) {
  $( this ).text( myName + ", hi there!" );
});
$( "button" ).click(function () {
  $( "p" ).trigger( "myCustomEvent", [ "John" ] );
});
```