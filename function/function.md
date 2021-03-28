# function

## call by value

原生類別

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

