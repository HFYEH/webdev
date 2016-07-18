---
layout: post
title: 'jQuery memo'
date: 2015-05-25 12:02
comments: true
categories: 
---
jQuery make it easy to:
    1. **find** elements in and HTML document
    2. **change** HTML content
    3. **listen** to what a user does and react accrodingly
    4. **animate** content on the apge
    5. **talk** over the network to fetch new content

`$("h1");`	找 h1 element
`$("h1").text();`	顯示h1 element裡的內容
`$("h1").text("blah");`	改變h1 element裡的內容
`$("li").length;`	計算 li 數目

`$(document).ready(function(){});`	讀完DOM之後才會開始做裡面的事
function 是 even handler

## Selector
`$("ol li");`	Descendant selector
`$("ol > li");`	Child selector
`$("hi, h2");`	Multiple selector
`$("ol li:first");`	CSS like pesudo selector (filter)
`$("ol li:last");`
`$("ol li:odd");`
`$("ol li:even");`
## Traversing
`$("ol").find("li");`	速度比 selector 快
`$("li").first().next();`	選第二個 li
`$("li").first().next().prev();`	選第一個 li
`$("li").first().parent();`	選擇其 parent
`$("ol").children("li");`	選擇 ol 的 direct children,而且只選 li

## Manipulating the DOM
`var message = $("<span>1-555-jquery-air</span>");`	產生一個 DOM 並存入 message
`$('.vacation').append('<p>Append</p>');`	在 vacation class 的 direct children 新增一個 DOM p 在最後
`$('.vacation').prepend('<p>Append</p>');`	在 vacation class 的 direct children 新增一個 DOM p 在最前
`$('.vacation').before('<p>Append</p>');`	在 vacation class 之前新增一個 DOM p 在最前
`$('.vacation').after('<p>Append</p>');`	在 vacation class 之後新增一個 DOM p 在最前
`$('button').remove();`	移除 DOM button

## Acting on interaction
```
$(document).ready(function(){

	$("button").on('click', function(){
		Manipulating the DOM
	});

});
```

## This DOM
`$(this).remove;`	this 指向觸發 (trigger) 這個事件的 DOM,因此此例中只有陔 DOM 會被移除
`$(this).closest('.vacation');`	找其最接近的 .vacation 祖先

## Refactor
`var vacation = $(this).closest('.vacation');`	簡化 code
`vacation.remove();` = `$(this).closest('.vacation').remove`

## Tackling the HTML
`.data(<name>);`	HTML5中有data attribute,可使用此方法處理這些data attributes,"name"為其鍵值
`<p data-price='100'>` 使用 `.data('price');`

`$(".vacation").on('click','button', function(){});`	只對 vacation 裡的 button click 反應,button也可以置換成 class or id 和其它的 DOM,注意這裡被觸發的是button不是.vacation!!

## Filtering
`<li class="vacation onsale`>`	假定有一個 DOM
`$(".vacation").filter("onsale");`	選擇 .class 中含有 .onsale 的 DOM

## DOM manipulation
`.addClass(<class>);`	e.x. `$('p').addClass('highlight')`
`.removeClass(<class>);` e.x. `$('.highlight').removeClass('highlight')`

## Slide down
In CSS
`.ticket {display: none;}`
To show it
`.find(ticket).slideDown();`	顯示
`.find(ticket).slideDown();`	隱藏
`.find(ticket).slideToggle();`	顯示加隱藏
`.fadeIn();`
`.fadeOut();`
`.fadeToggle();`

## On() mouse event
`click`,`focusin`,`mousedown`,`mousemove`,`mouseover`,`mouseenter`,`dbclick`,`focusout`,`mouseup`,`mouseout`,`mouseleave`

## On() keyboard event
`keypress`,`keydown`,`keyup`

## jQuery 轉型
`+$('p').data('price');`	將字串轉型為數字

## jQuery 存取 input 欄位
`$('').val()`	取值
`$('').val()`	存值

## FadeToggle
`<a href='#' class='expand'>show comments</a>`
> .expand 觸發 event, 產生 event bubble, 會向上經過所有parent nodes,最後傳至document
> 這會導致頁面拉到最上方

防止瀏覽器預設行為
```
$(document).ready(function(){

	$(".expand").on('click', function(event){
  	event.preventDefault();
		Manipulating the DOM
	});

});
```

防止 bubble up
```
$(document).ready(function(){

	$(".expand").on('click', function(event){
  	event.spotPropagation;
		Manipulating the DOM
	});
});
```

## Changing the Style
Set style
`$(this).css(<attr>,<value>)` or `$(this).css(<obj>);`
`$(this).css({'background-color':'red','boder-color':'blue'});`

不要在 js 內寫 css,寫在CSS裡 (application.js => application.css)
application.css
```
.highlighted {
	background-color: red;
}
.highlighted .price {
	background-color: green;
  display: block;
}
```
application.js
```
$(this).toggleClass("highlighted");
```

## Animation
`$(this).animate(<obj>);`
`$(this).animate({'top','-10px'},time);`	從目前狀態變成設定的 obj 的狀態
default time = 400, 'fast' = 200, 'slow' = 600; time 可以用在 slideToggle, fadeToggle...
同樣,不希望在 js 裡寫 css
application.css
```
.vacation {
	transition: top 0.2;
}
.highlited {
	top: -10px;
}
```

## hasClass
`$(this).hasClass('highlighted');`	檢查是否有 highlighted 類,回傳 bool

## AJAX
```
<li class='confirmation'>
	<h3>Hawaiian Vacation</h3>
	<button>Flight details</button>
	<div class='ticket'>
		<a class='view-boarding-pass'>View Boarding Pass</a>
		<img src='/ticket-14836.png'/>
	</div>
</li>
```
application.js (non-AJAX)
```
$('.confirmation').on('click', 'button', function(){
	$(this).closest('.confirmation').find('.ticket').slideDown();
});
$('.confirmation .view-boarding-pass').on('click', function() {
	$(this).closest('.ticket').find('img').show();
});
```
application.js (AJAX)
`$.ajax(url, <obj>)`
```
$('.confirmation').on('click', 'button', function(){
	$.ajax(url, {
		success: function(response) {		// Run only when the server returns a successful response
			$('.ticket').html(response).slideDown();
		}
	});
});
```
What responded is...
```
<div> ...
<strong>Boarding Pass: </strong>
<a href='#' class='view-boarding-pass'>View Boarding Pass</a>
<img src='ticket.png' alt='Your boarding pass' class='boarding-pass' />
</div>
```
A shorthand method
`.get(url,success)`
```
$('.confirmation').on('click', 'button', function(){```
	$.get('confirmation.html', function(response) {
		$('.ticket').html(response).slideDown();
	});
});
```

AJAX url we like this
`confirmation.html?confNum=1234`	Dynamic url
```
$.ajax('confirmation.html', {
	success: function(response) {
		$('.ticket').html(response).slideDown();
	},
  data: { "confNum": 1234 }		// js object
	}
});
```
If you have data attribute, you can pull it from HTML, replace the js object obove
`data: { "confNum": $(".ticket").data("confNum")`

Some Ajax options
```
$('.confirmation').on('click', 'button', function(){
	$.ajax(url, {
		success: function(response) {		// Run only when the server returns a successful response
			$('.ticket').html(response).slideDown();},
    error: function(request, errorType, errorMessage) {		// Run when time out
			alert('Error: ' + errorType + ' with message: ' + errorMessage);},
    timeout: 3000,
    beforeSend: function() {	// Run before the Ajax request
    	$('.confirmation').addClass('is-loading');},
		}
  	complete: function() {	// Run after both success and error
  		$('.confirmation').removeClass('is-loading');}
	});
});
```
AJAX 內的 AJAX 要指定 target
`$("div").on('click','button',function(){});`




