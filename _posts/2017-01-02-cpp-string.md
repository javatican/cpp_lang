---
layout:	post
title:	C++ Array and string literal
date:	2017-01-02
categories: cpp_lang
--- 
### C++ array

C++語言的陣列在初始化時, 使用`initializer list`的語法(亦即`{ }語法`):

```
int r1[]={1, 2, 3, 4, 5};
```

當使用`initializer list`語法, 陣列長度可以省略不指定. 其長度將等於`initializer list`內的元素個數.
但是一旦指定長度, 則長度不可以低於`initializer list`內的元素個數. 否則C++ compiler會顯示錯誤, 但是C compiler只給警示.

```
//error in C++ compiler, but warning in C compiler
int r2[5] = {1, 2, 3, 4, 5, 6};	\\ C++ error: too many initializers for ‘int [5]’ or
				\\ C warning: excess elements in array initializer
``` 

若長度多於`initializer list`內的元素個數, 則超過的部份, 元素值將設為0.

```
int r3[5] = { 1, 2, 3}; //r3[3],r3[4]皆為0
``` 

若為char陣列, 在initializer list語法中須多加一個結束的`空字元`(ASCII碼為0), 這也是C語言中儲存字元陣列(字串)的方式, 稱為`zero-terminated array of char`也稱為`C-style string`. 

```
char c1[] = {'h', 'e', 'l', 'p', 0};
```

C也提供`string literal`(字元定字)的方式來初始化字元陣列. `string literal`會自動在字串最後面加上一個`空字元`.

```
char c2[] = "help"; //sizeof(c2) == 5
```

一些字串函式, 例如`strcpy()`需要這個結束的`空字元`才能夠運作. 

### string literal

`string literal`在C++語言中的型態為`const char array`, 但是在C語言中僅僅是`char array`. 因此以下在C語言中是合法的

```
char * xp ="help";
```

但是C++語言會有`deprecated conversion from string constant to ‘char*’`的警示. 必須改為

```
const char * xp ="help";
```

`string literal`是immutable的資料. 所以即使在C語言中僅僅是`char array`的型態, 企圖去改變裡面的字元內容將產生錯誤

```
char * xp ="help";
xp[0]='H'; //error
```

**若要改變字元陣列中的資料, 必須先將`string literal`指定到一個non-const的字元陣列中, 再去改變內容**

```
char xp[]="help";
xp[0]='H'; //ok
```

`string literal`放置在`靜態配置`的記憶體空間, 因此可以當作函數回傳值, 不會因為函數執行完畢而被回收, 例如

```
const char* error_message(){
	return "login failed";
}
```

:sweat_smile:

