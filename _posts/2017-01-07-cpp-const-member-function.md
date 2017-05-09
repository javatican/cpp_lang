---
layout:	post
title:	C++ const member function 
date:	2017-01-06
categories: cpp_lang
--- 
  
C++語言的const宣告使用的非常頻繁. 對於使用像是Java語言的人來說, C++ const是比較陌生的一個主題.
基本上C++希望設計函式庫的人, 能盡量多使用const, 像是參數, 回傳值, 甚至成員函數本身, 如果不會改變物件的內容, 都應該使用const, 將設計者的想法或意圖表達清楚. 也可以讓compiler幫忙找出使用上的錯誤. 

舉一個例子來說明:

```
class TextBlock {
    public:
        TextBlock(std::string text){
            this->text=text;
        }
        const char& operator[](std::size_t position) const
            { return text[position]; }
        char& operator[](std::size_t position)
            { return text[position]; }
    private:
        std::string text;
};
```

這個TextBlock類別, 代表了一段文字內容. 定義了`operator[]()` member function(成員函數), 
用來提供`[]運算子`, 以取得某一個索引值位置的字元. 
它提供了兩個版本的`operator[]()` member fuction, 一個是const, 一個是non-const. 

### const member function

`const member function`可以確保函數內不會改變`*this`物件的內容. 
所以當一個const TextBlock物件呼叫`[]運算子`時, 這個`const operator[]()`會被呼叫. 

重點在於`const operator[]()`的回傳值, 我們宣告為`const char&`, 代表回傳的字元是原本字串內容中某個位置字元的參考, 所以**不會做值的複製**, 並且**不能改變這個參考的內容**. 後者是為了能夠符合當初`const operator[]()`函數所做的承諾. 因此我們不能這樣做

```
const TextBlock ctb("World");
std::cout << ctb[0]<<std::endl;
ctb[0]='w'; //error
```

`const operator[]()`回傳值可以宣告成其他形式嗎? 重點在於我們必須確保`*this`物件沒有被改變, 所以除了宣告為`const char&`之外, 可以回傳`char`. 回傳值若為`char`, 會將TextBlock物件中的字串內容中某個位置字元, 做值的複製然後再回傳, 所以當然不會改變`*this`物件, 因此也是可以的. 

### non-const member function

另外一個`operator[]()`成員函數沒有宣告為const, 所以**只有不是const的TextBlock物件可以呼叫它**. 所以這個`non-const operator[]()`提供`[]運算子`的語法**可以讀取或者改變**TextBlock物件的字串內容中某個位置字元的內容.

```
TextBlock tb("hello");
std::cout<<tb[0]<<std::endl;
tb[0]='H'; //ok
```

`non-const operator[]()`的回傳值宣告為`char&`. 這裡不能使用`char`, 因為回傳單一個char, 是所謂的`rvalue`, 是不能放在指定運算子的左邊. 因此`tb[0]='H'`就不合法. 相對的`char&`是所謂的`lvalue`, 可以放在指定運算子的左邊.基本上一個資料, **若是可以取得它的記憶體位址(identifiable location in memory), 便是`lvalue`, 否則就是`rvalue`.**

### bitwise const 

上面範例示範的const member function是使用`bitwise const`的形式, 也就是只要`*this`物件內容沒有改變(從儲存物件的記憶體內容的bit形式來說沒有改變, 所以稱為bitwise const), 就視為const. 我們稍微改寫一下上述的範例, 把它的member variable `string text`換成`char* ptext`

```
class CTextBlock {
    public:
        CTextBlock(char* text){
            this->pText=text;
        }
        const char& operator[](std::size_t position) const
            { return pText[position]; }
        char& operator[](std::size_t position)
            { return pText[position]; }
        
    private:
        char* pText;
};
```

從string物件換成char*物件, const的意義也就從string內容不能改變變成char指標位址不能改變. 後者的意義即是只要char指標沒有轉向(指向其他char物件), 就算是符合const的精神. 因此這裡的`const operator[]()`的回傳值型態, 雖然我們定義成`const char&`, 但是實際上也可以宣告為`char&`或`char`. 當然意義完全不同.

1. 宣告成`const char&`

可以確保pText指標所指向的字串物件在透過[]運算子取得某個位置的字元時, 不可以改變值.
所以`ctb[0]='H'`是不合法的.

```
char[] text="hello";
CTextBlock ctb(text);
std::cout<<ctb[0]<<std::endl;
ctb[0]='H'; //error
``` 

注意: 這裡CTextBlock的建構子`CTextBlock(char* text)`, 所要傳入的參數為`char*`型態, 我們不可以直接傳入`string literal`, "hello". 因為`string literal`的型態在C++中是`const char[]`, 不能夠轉型成`char*`, 目前會有`warning: deprecated conversion from string constant to ‘char*’`. 所以我們先產生一個`char[]`陣列, 然後再傳入給CTextBlock建構子.

2. 宣告成`char&`

這樣並沒有違反`const operator[]()`的承諾. 因為我們不會改變pText指標. 但是pText指標所指向的字串物件, 可以透過[]運算子取得某個位置的字元, 並且改變其內容. 所以`ctb[0]='H'`就變成是合法的.

3. 宣告成`char`

pText指標所指向的字串物件, 可以透過[]運算子取得某個位置的字元, 做複製然後回傳. 但是如同前面提及過的, 回傳char, 就變成一種`rvalue`, 是不能放在指定運算子的左邊. 所以`ctb[0]='H'`是不合法的.

所以上面這個使用`char*`作為成員變數的CTextBlock類別範例, 雖然使用`const member function`, 但是基於它是`bitwise const`的形式, 所以除非我們在回傳值型態也宣告為const, 否則是違反我們心中所常識認知的const的意圖的.

### logical const 

所以這裡再說道另一種const的概念叫做`logical const`. 這是從使用者的角度來看物件是否為const, 基於封裝的原則, 常常一些內部的物件狀態, 資料是隱藏起來的, 因此只要讓使用者感覺物件內容沒有改變即可, 一些內部隱藏的資料做一些改變是並不違反const精神的. 這裡可以將允許被改變的成員變數宣告為`mutable`. 例如:

```
class CTextBlock {
    public:
        CTextBlock(char* text){
            this->pText=text;
        }
        const char& operator[](std::size_t position) const
            { return pText[position]; }
        char& operator[](std::size_t position)
            { return pText[position]; }
		//
        std::size_t length() const{
            if(!lengthIsValid){
                textLength = std::strlen(pText); //ok
                lengthIsValid=true; //ok
            }
            return textLength;
        }
    private:
        char* pText;
        mutable std::size_t textLength;
        mutable bool lengthIsValid;
};
```

CTextBlock加上了兩個內部變數textLength, lengthIsValid並且宣告為`mutable`. 所以在const length()函數中, 可以改變這些宣告為mutable的成員變數, 其並不違反const.


### code duplication問題
以上利用overloading所撰寫的const operator[]()與non-const operator[]()基本上內容是重複的, 差異只是在回傳值型態是否為const. 所以可以讓non-const的member function, 呼叫const的member function.

```
const char& operator[](std::size_t position) const{ 
	return pText[position]; 
}

char& operator[](std::size_t position)
{ 
	const_cast<char&>(static_cast<const CTextBlock&>(*this)[position]);
}
```

這裡先用`static_cast`語法將non-const CTextBlock物件轉型成const物件, 然後呼叫const operator[](), 最後再利用`const_cast`語法, 將回傳值型態`const char&`轉回`char&`.


問題是可**否讓const的member function, 呼叫non-const的member function呢?** 這樣做是會有風險的, 因為non-const member function基本上是可能改變`*this`物件狀態的. 以上範例是可以改成這樣的寫法, 但是在基本概念上, 是錯誤的作法.

:sweat_smile:
