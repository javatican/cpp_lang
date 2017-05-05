---
layout:	post
title:	void* 差異
date:	2017-05-05
categories: cpp_lang
---
void* 在C++語言是所有指標型態的super type. 指向一個不知道型態物件的指標. 
在C語言中void* 可以被implicit cast(隱含轉型或自動轉型)成其他型態的指標, 但是C++語言則必須使用explicit cast.
例如C語言的malloc()函數, 回傳型態為void*. 所以以下敘述在C語言中可以compile

``` 
int* xp = malloc(sizeof(int)*5); 
```

但到了C++語言則必須做手動轉型

``` 
int* xp = (int*) malloc(sizeof(int)*5); 
```

:sweat_smile:

