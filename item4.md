# Item 4: Make sure that objects are initialized before they're used

Always initialize variables when declaring! For example, Uninitialized `int` variables are sometimes guaranteed to be initialized to zero, but sometimes not. Reading unitialized values thus can yield undefined behavior, and in some platforms the act of reading uninitialized values can halt your program. 

```C++
int x = 0;                             // manual initialization of an int
const char *text = "A C-style string"; // manual initialization of a pointer

double d;                              // initialization by reading from an input stream 
std::cin >> d;

```
