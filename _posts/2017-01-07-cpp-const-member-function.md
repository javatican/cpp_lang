---
layout:	post
title:	C++ const member function 
date:	2017-01-06
categories: cpp_lang
--- 
  
C++語言的const宣告使用的非常頻繁. 對於使用像是Java語言的人來說, C++ const是比較陌生的一個主題.
基本上C++希望設計函式庫的人, 能盡量多使用const, 像是參數, 回傳值, 甚至成員函數本身, 如果不會改變物件的內容, 
都應該使用const, 將設計者的想法或意圖表達清楚.

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

`non-const operator[]()`的回傳值宣告為`char&`. 這裡不能使用`char`, 因為回傳單一個char, 是所謂的`rvalue`, 是不能放在指定運算子的左邊. 因此`tb[0]='H'`就不合法. 相對的`char&`是所謂的lvalue, 可以放在指定運算子的左邊.
基本上一個資料, **若是可以取得它的記憶體位址(identifiable location in memory), 便是lvalue, 否則就是rvalue.**

:sweat_smile:
