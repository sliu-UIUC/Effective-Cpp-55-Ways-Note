# Item 2: Prefer consts, enums, and inlines to #defines.

Prefer the compiler to the preprocessor because **#define** may be treated as if it;s not part of the language per se. 

For instance: 
```C++
#define ASPECT_RATIO 1.653
```
