# function

## call by value

當使用call by value的方法去傳變數值時，是使用複製變數的方式將參數傳入函式，因此傳入端與函數中的記憶體是分開的，不會互相干擾，但也需要兩倍記憶體去儲存他們。

如果傳入的變數所佔據的記憶體空間較大時\(如圖片或是大型矩陣等\)，應使用`const pointer`或是`const reference`減少不必要的複製行為。

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

