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
TextBlock's operator[]s can be used like: 
```C++
TextBlock tb("Hello");        
std::cout<<tb[0];             // calls non-const TextBlock::operator[]
const TextBlock ctb("Hello");
std::cout<<ctb[0];            // calls const TextBlock::operator[]
```
A more realistic reference-to-const example: 
```C++
void print(const TextBlock& ctb) {  // in this function, ctb is const
  std::cout<<ctb[0];                // calls const TextBlock::operator[]
  ...
}
```
```diff
+ It's never legal to modify the return value of a function that returns a build-in type. 
+ Even if it were legal, the fact that C++ returns objects by value means that a copy of value is modified, not the value itself. 
```
## Bitwise constness vs Logical constness
The book goes in length about those two. Essentially, bitwise corstness is what is expected by the C++ compiler that not even a single bit can be modified in a const member function. That way it is easier to detect violation. Unfortunately, there are still some ways where C++ compiler would consider it as bitwise constant even though it is modified (for instance, an object that contains only a pointer would be considered bitwise constant). That leads to counterintuitive behaviors where one can invoke only const member functions and still change its value. 

Logical constness is another notion stating some of the bits in a const member function can be modified only in ways that clients cannot detect: 
```C++
class CTextBlock {
  public: 
    ...
    std::size_t length() const; 
    
  private: 
    char *pText; 
    std::size_t textLength;           // last calculated length of textblock
    bool lengthIsValid;               // whether length is currently valid
};

std::size_t CTextBlock:::length() const {
  if(!lengthIsValid) {
    textLength = std::strlen(pText);  // ERROR! Can't assign to textLength
    lengthIsValid = true;             // and lengthIsValid in a constmember function
  }
}
```
The obove code would make sense for a logical constness but it won't compile because C++ insists on bitwise constness and it's not. To bypass that, we need to use `mutable` keyword. `mutable` frees non-static data members from the constraints of bitwise constness: 
```C++
class CTextBlock {
  public: 
    ...
    std::size_t length() const; 
    
  private: 
    char *pText; 
    mutable std::size_t textLength;           // these data members may always be modified, even in const member functions
    mutable bool lengthIsValid;              
};

std::size_t CTextBlock:::length() const {
  if(!lengthIsValid) {
    textLength = std::strlen(pText);  // Now the function is OK
    lengthIsValid = true;            
  }
}
```

## Avoiding Duplication in const and Non-const Member Functions
`mutable` doesnt solve all const-related difficulties. For instance: 
```C++
class TextBlock {
public: 
  ...
  const char& operator[](std::size_t position) const {
    ...                           // do bounds checking
    ...                           // log access data
    ...                           // verify data integrity
    return text[position];
  }

  char& operator[](std::size_t position) {
    ...                           // do bounds checking
    ...                           // log access data
    ...                           // verify data integrity
    return text[position];
  }
private: 
  std::string text;
}
```
The above essentially create duplicate codes that increases compilation time, maintenance effort and code-bloat headache. Even though one might remove all the logic for boundary checking, log access data, etc, into a separate member function, it would still have duplicate calls to the function and the duplicated return statement code. 

```diff
- Things to Remember
```
* 
