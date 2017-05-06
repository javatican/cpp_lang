---
layout:	post
title:	C++ array and pointer
date:	2017-01-04
categories: cpp_lang
--- 
 
C++語言的陣列與指標關係十分密切, 陣列名稱可以當作一個指標來使用, 指向它的第一個元素位址. 並且陣列名稱可以`隱含轉型(implicit cast)`成指標, 因此對於像`strlen()`函數, 

```
size_t strlen ( const char * str )
```

其宣告的參數型態為`char*`, 我們實際上可以傳入一`char陣列`, 利用其`隱含轉型`的動作來達到轉換.

但是`char*`無法轉換成`char陣列`. 

```
char c[] = "EltonJohn";
char∗ cp = c; // implicit conversion of char[] to char*
strlen(cp);
strlen(c); // implicit conversion of char[] to char*
c = cp; //error: incompatible types in assignment of ‘char*’ to ‘char [10]’
```

也因為陣列會`隱含轉型`成為指標, 若是將陣列作為參數傳入給函數, 函數將會以指標的形式接收到陣列的起始位址, 因此無法像一般變數作為參數時, 做陣列內容複製的動作.  

這也意謂**陣列傳入函數時, 函數只抓到陣列的起始位址, 陣列的長度資訊將會遺失**. 所以函數必須透過一些方式來得到陣列的長度, 例如**char陣列, 函數將透過`空字元`來作為陣列結束的符號.** 如果是**其他型態的陣列, 則必須將長度資訊利用參數方式傳入給函數**, 如此函數才會知道陣列的長度.

```
void test(char c[]){
	for (int i = 0; c[i]!=0; ++i){
		std::cout<<c[i]<<std::endl;
	}
}
```

上面的參數雖然宣告為`char陣列`, 但是實際上還是一個指標, 跟宣告成`char*`沒有不一樣, 反正陣列長度資訊已經遺失了. 但是因為字元陣列會有結束的`空字元`, 所以可以利用`c[i]!=0`來判斷字元陣列是否已經到達結束位址.

上述test()也可以利用指標語法來使用陣列內容

```
void test2(char* c){
    for(char* i=c; *i!=0; i++){
        std::cout<<*i<<std::endl;
    }
}
```

或者更簡單

```
void test3(char* c){
    for(;*c!=0; c++){
        std::cout<<*c<<std::endl;
    }
}
```

如果是字元除外的陣列, 則必須在傳入陣列的同時, 也傳入陣列長度

```
void test4(int* i, int size){
    for(int m=0; m<size; m++){
         std::cout<<*(i+m)<<std::endl;  
    }
}
```


:sweat_smile:

