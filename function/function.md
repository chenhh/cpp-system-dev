# function

## call by value

當你利用call by value的方法去傳值時，是使用複製變數的方式將參數傳入函式，因此傳入端與函數中的記憶體是分開的，不會互相干擾，但也需要兩個記憶體去儲存他們，如果傳入的變數所佔據的記憶體較大時\(如圖片或是大型矩陣等\)，應使用`const pointer`或是`const reference`減少不必要的複製行為。

```cpp
#include <iostream>

void swap(int x, int y) {
  // call by value
	std::cout << "(swap) address of x = " << &x << std::endl;
	std::cout << "(swap) address of y = " << &y << std::endl;
	
  int temp = x;
  x = y;
  y = temp;
}

int main() {
  int x = 3, y = 2;
  swap(x, y);
  std::cout << "x = " << x << std::endl;
	std::cout << "y = " << y << std::endl;
	std::cout << "(main) address of x = " << &x << std::endl;
	std::cout << "(main) address of y = " << &y << std::endl;
    
  return 0;
}

/*
	call by value, the values does not swap
	在swap函式的x,y的address與main中不同
	(swap) address of x = 0x7ffc6aeb473c
	(swap) address of y = 0x7ffc6aeb4738
	x = 3
	y = 2
	(main) address of x = 0x7ffc6aeb4760
	(main) address of y = 0x7ffc6aeb4764
*/
```

