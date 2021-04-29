# Item 3: Use const whenever possible
`const` specifies a semantic constraint that a particular object should not be modified, and will be enforced by the compiler. It allows you to communicate to both compilers and other programmers that certain values should remain invariant. 

`const` is versatile. It can be used at global or namespace scope, for objects declared static at file function or block scope. Inside classes, it can be used for both static and non-static data types. For pointers, it can specify whether the pointer itself or the data it points to is constant, both, or neither: 

```C++
char greeting[] = "Hello"; 
char *p = greeting;             // non-const pointer,
                                // non-const data
                          
const char *p = greeting;       // non-const pointer,
                                // const data
char* const p = greeting;       // const pointer, 
                                // non-const data
const char* const p = greeting; // const pointer, 
                                // const data

```
```diff
+ If const appears to the left of *, what's pointed to is constant; appears to the right of *, the pointer is constant. 
```

```C++
void f1(const Widget *pw);     // f1 takes a pointer to a 
                               // constant Widget object 
                               
void f2(Widget const *pw);     // so does f2
```

## STL Iterators

Iterators are modeled on pointers and acts like a T* pointer. Declaring an iterator const is like declaring a pointer const (i.e., T* const): the iterator isn't allowed to point to something different, but the thing it points to may be modified. If you want an iterator that points to something that can't be modified (const T* analogue), you want a `const_iterator`: 

```C++
std::vector<int> v; 
...
const std::vector<int>::iterator iter = v.begin();   // iter acts like a T* const
*iter = 10;                                          // OK, changes what iter points to
++iter;                                              // ERROR! iter is const

std::vector<int>::const_iterator cIter = v.begin();  // cIter acts like const T*
*cIter = 10;                                         // ERROR! *cIter is const 
++cIter;                                             // fine, changes cIter
```

## const Member Functions
Identify which member functions may be invoked on const objects. Such member functions are important because: 
* They make the interface of a class easier to understand. It's important to know which functions may modify an object and which may not. 
* They make it possible to work with const objects. One of the most fundamental way to improve C++ program's performance is to pass objects by reference-to-const, a technique only viable if there are const member functions with which to manipulate the resulting const-qualified objects. 

```diff
+ In C++, member functions differ only in whether their constness can be overloaded. 
```

```C++
class TextBlock {
public: 
  ...
  const char& operator[](std::size_t position) const {   // operator[] for const objects
    return text[position]; 
  }                         

  char& operator[](std::size_t position) {               // operator[] for non-const objects
    return text[position];
  }
  
private:
  std::string text; 
}

```


```diff
- Things to Remember
```
* 
