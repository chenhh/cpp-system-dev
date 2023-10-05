# function

## call by value

當使用call by value的方法去傳變數值時，是使用複製變數的方式將參數傳入函式，因此傳入端與函數中的記憶體是分開的，不會互相干擾，但也需要兩倍記憶體去儲存他們。

如果傳入的變數所佔據的記憶體空間較大時(如圖片或是大型矩陣等)，應使用`const pointer`或是`const reference`減少不必要的複製行為。

```cpp
#include <iostream>

struct Point{
    int x;
    int y;
};

void swap(Point p1, Point p2){
  // call by value
	std::cout << "(swap) address of p1 = " 
	          << &p1 << std::endl;
	std::cout << "(swap) address of p2 = " 
	          << &p2 << std::endl;

    Point temp = p1;
    p1 = p2;
    p2 = temp;
}

int main(){
    Point p1{1,2}, p2{3,4};
    swap(p1, p2);
    std::cout << "(main) address of p1 = " 
              << &p1 << std::endl;
  	std::cout << "(main) address of p2 = " 
	            << &p2 << std::endl;
    std::cout<< "p1 = (" << p1.x << "," 
             << p1.y << ")" << std::endl 
             << "p2 = (" << p2.x << "," 
             << p2.y << ")" << std::endl;
    return 0;
}

/*
	因為是call by value, Point傳入swap是用copy的方式
	(swap) address of p1 = 0x7ffff6648d08
	(swap) address of p2 = 0x7ffff6648d00
	(main) address of p1 = 0x7ffff6648d38
	(main) address of p2 = 0x7ffff6648d40
	p1, p2的值並沒有被交換
	p1 = (1,2)
	p2 = (3,4)
*/
```

## call by reference

函數使用reference時，是將參數取別名，而非複製記憶體，因此傳入的變數和在函數中的變數是指向相同的記憶體空間。

使用by value與by reference將變數放入函數時，都是放置變數儲存的數值，而非變數的記憶體地址。

```cpp
void swap(Point& p1, Point& p2){
    // call by reference
    std::cout << "(swap) address of p1 = " 
	          << &p1 << std::endl;
    std::cout << "(swap) address of p2 = " 
	          << &p2 << std::endl;

    Point temp = p1;
    p1 = p2;
    p2 = temp;
}
```

得到的結果如下

```cpp
/*
	變數為別名而不是複製記憶體，
	指向相同的記憶體區塊
	(swap) address of p1 = 0x7ffe3150f048
	(swap) address of p2 = 0x7ffe3150f050
	(main) address of p1 = 0x7ffe3150f048
	(main) address of p2 = 0x7ffe3150f050
	p1 = (3,4)
	p2 = (1,2)
*/
```

## call by address

在C語言中，因為沒有call by reference，因此若在函數中需要修改變數時，必須使用call by address的方式將變數傳入。

而在C++中，儘量使用call by reference的方式將參數傳入函數以增加程式的可讀性。

```cpp
#include <iostream>

struct Point{
    int x;
    int y;
};

void swap(Point* p1, Point* p2){
  // call by address
	std::cout << "(swap) address of pointer p1 = "
			  << p1 << std::endl;
	std::cout << "(swap) address of pointer p2 = "
	     	  << p2 << std::endl;

	// 此處建立暫存物件時，可使用std::move避免拷貝
    Point temp = *p1;	
    *p1 = *p2;
    *p2 = temp;
}      

int main(){
    Point p1{1,2}, p2{3,4};
    swap(&p1, &p2);
    std::cout << "(main) address of p1 = "
              << &p1 << std::endl;
        std::cout << "(main) address of p2 = "
                    << &p2 << std::endl;
    std::cout<< "p1 = (" << p1.x << ","
             << p1.y << ")" << std::endl
             << "p2 = (" << p2.x << ","
             << p2.y << ")" << std::endl;
    return 0;
}

/*
	使用call by adress時，傳入參數要使用變數的地址而非變數值
	(swap) address of pointer p1 = 0x7ffd00341398
	(swap) address of pointer p2 = 0x7ffd003413a0
	(main) address of p1 = 0x7ffd00341398
	(main) address of p2 = 0x7ffd003413a0
	p1 = (3,4)
	p2 = (1,2)
*/
```
