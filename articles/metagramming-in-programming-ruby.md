# Metaprogramming in Programming Ruby

本篇文章是Programming Ruby第三部份關於meataprogramming的讀書筆記，幫助整理思路。直接進Metaprogramming Ruby感覺比較吃力，有一些觀念是看了這章後才豁然開朗的。如果讀者讀Metaprogramming Ruby碰壁，可以考慮先讀Programming Ruby的這一章。

## Object and Class

Ruby中萬物皆物件（除了block），所謂的Class也只是class Class自己的實例而已。但物件和類別的內容是不同的。

Object包含一些***flag***，***實例變數***，和關聯到的***類別***。

Class因為也是Object，所以包含上面所有，再加上一些***（實例）方法***和關聯到的***父類***。
