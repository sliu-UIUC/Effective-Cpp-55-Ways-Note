# Item 2: Prefer consts, enums, and inlines to #defines.

Prefer the compiler to the preprocessor because **#define** may be treated as if it;s not part of the language per se. 

For instance:  
```C++
#define ASPECT_RATIO 1.653
```
the symbolic name ASPECT_RATIO may never be seen by compilers. It might be removed by preprocessor before the source code ever gets to a compiler. As a result, it may not get entered into the source table, which can be confusing if you get an error during compilation involving the use of the constant, because the error message may refer to the **value**(1.653) rather than the name ASPECT_RATIO. This is especially confusing when defined in a header file you did't write. Not only you have no idea where that comes from, it's extremely time-confusing to debug. This problem can also crop up in a symbolic debugger because the name may not be in the symbol table.

A nice alternative is to replace the macro with a constant: 
```C++
const double AspectRatio = 1.653;    //uppercase names are usually for macros, hence the name change
```
Advantages are: 
1. A language constant like this is ensured to be seen by compilers and entered to symbol tables. 
2. In the case of a floating point constant, use of constant may yield smaller code than using **#define**. **With macro**, **the preprocessor's blind substitution** of the macro name ASPECT_RATIO with 1.653 could result in **multiple copies** of 1.653 in your code. **With the use of constant, only 1 copy will be generated**.

Two special cases when replacing **#define**:
1. **Defining constant pointers**: because constant definitions are typically put in header file, where many different source files will include them, pointer should be declared const in addition to the value it points to. 
Ex: to define a constant char*-based string in header file
```C++
const char * const authorName = "Scott Meyers";
//or 
const std::string authorName("Scott Meyers"); //generally prefered compare to the char* based progenitors
```

2. **Make class-specific constants 'static'** this is to **limit the scope** of a constant to a class, and make it a member and ensure there is **at most one copy** of the constant. Ex: 
```C++ 
class GamePlayer {
private: 
  static const int NumTurns = 5;   //constant declaration
  int scores[NumTurns];            //use of constant
  '''
}
```
Note: 
  * 1a: 
