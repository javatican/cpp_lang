---
layout:	post
title:	C++ Array no built-in copy operation
date:	2017-01-03
categories: cpp_lang
--- 
 
C++語言的陣列沒有提供複製語法, 因此無法根據一個陣列來初始化另一個陣列, 也因此不支援陣列指定(array assignement)以及陣列參數以值傳入(pass-by-value)的機制.

```
int i1[5] = {1, 2, 3, 4, 5};
int i2[5] = i1; //error: array must be initialized with a brace-enclosed initializer
int i3[5] = {11, 12, 13, 14, 15};
i1 = i3; //error: invalid array assignment
``` 

實際上陣列僅提供`initializer list`語法(亦即`{ }語法`)來做初始化. 如果是字元陣列則可以使用`string literal`語法(亦即`" "語法`).
:sweat_smile:

