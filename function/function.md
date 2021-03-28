# function

## call by value

```cpp
#include <iostream>

void swap(int x, int y) {
    // call by value
    int temp = x;
    x = y;
    y = temp;
}

int main() {
    int x = 3, y = 2;
    swap(x, y);
    std::cout << "x = " << x << std::endl <<
        "y = " << y << std::endl;
    //call by value, the values does not swap
    // x = 3, y = 2
    return 0;
}
```

