---
layout: post
title: 'form_for and simple_form_for'
date: 2016-01-12 15:03
comments: true
categories: 
---
此篇比較form_for和simple_form_for

product是instance variable
title是product的attribute

form tag
1. novalidate="novalidate"功用不明
2. 自帶一個 .new_product，使用simple_form則會多一個 .simple_form和一個#new_product

csrf token
1. 這兩個form寫在同一個erb檔，同個頁面下csrf_token是一樣的
2. 如果要用AJAX，[讀這篇](https://ruby-china.org/topics/21821)

欄位
1. form_for很單純，label就是給<label for="product_title"，而input欄位會給id="product_title"，作為label指向的地方，還有name="product[title]"，發http進到rails後，會變成 params[:product][:title] = 輸入值
2. simple_form_for會在每個欄位自動加div.form-group，還有.string.optional
3. simple_form_for在label多了.control-label，用途不清楚
4. simple_form_for在欄位多了.form-control，用途不清楚






```erb simple_form_for
<%= simple_form_for [:admin, @product] do |f| %>
  <%= f.input :title %>

  <div class="form-action">
    <%= f.submit "Submit", data: { disable_with: "Submitting..." } %>
  </div>
<% end %>
```
產生的html
```html
<form novalidate="novalidate" class="simple_form new_product" id="new_product" action="/admin/products" accept-charset="UTF-8" method="post">
  <input name="utf8" type="hidden" value="✓">
  <input type="hidden" name="authenticity_token" value="ucgBdaq0OQasbf5wDVw7Au0q5mj8lCq0YOgf5yU/LB9PN6Rbj1iGVpK6zusViHJgdj6vLUjBD9BckwQt8f30sQ==">
  
    <div class="form-group string optional product_title">
    	<label class="string optional control-label" for="product_title">Title</label>
      <input class="string optional form-control" type="text" name="product[title]" id="product_title"></div>

  <div class="form-action">
    <input type="submit" name="commit" value="Submit" data-disable-with="Submitting...">
  </div>
</form>
```




```erb form_for
<%= form_for [:admin, @product] do |f| %>

<div class="form-group">
  <%= f.label "Title" %>
  <%= f.text_field :title %>
</div>

<div class="form-action">
  <%= f.submit "Submit", data: {disable_with: "Submitting..."} %>
</div>

<% end %>
```
產生的html
```html
<form class="new_product" id="new_product" action="/admin/products" accept-charset="UTF-8" method="post">
<input name="utf8" type="hidden" value="✓">
<input type="hidden" name="authenticity_token" value="ucgBdaq0OQasbf5wDVw7Au0q5mj8lCq0YOgf5yU/LB9PN6Rbj1iGVpK6zusViHJgdj6vLUjBD9BckwQt8f30sQ==">

<div class="form-group">
  <label for="product_Title">Title</label>
  <input type="text" name="product[title]" id="product_title">
</div>

<div class="form-action">
  <input type="submit" name="commit" value="Submit" data-disable-with="Submitting...">
</div>

</form>
```