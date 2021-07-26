# Item 8： Prevent exceptions from leaving destructors
Destructors can throw exceptions as well, which can lead to various problems. For instance, consider the following code: 

```C++
class Widget {
public: 
  ...
  ~Widget(){...}            // asume this might throw an exception 
};

void doSomething()
{
  std::vector<Widget> v;
  ...
}                            // v is automatically destroyed here

}

```

When `v` gets destroyed, it is responsible for destroying all the Widgets it contains. Suppose the first one element in `v` throws an exception. The other `Widget`s wil still have to be destroyed (otherwise the resources they hold would be leaked). Suppose another one later on also throws an exception. Since C++ can only handle one exception at any given time and there are two simultaneously active exceptions, program will either terminate or yield undefined behavior, depending on the precise situation. In this case, it is undefined behavior. 

If exceptions must be thrown in destructor, the destructor should: 
* **Terminate the program**: typically by calling `abort()`
```C++
~destructor() {
  try {something} 
  catch(...) {
    make log entry about the specific failure; 
    std::abort();
  }
}
```
The advantage is that aborting the program earlier can prevent the propagation of exception in destructor that could lead to undefined behavior. Thus it may forestall undefined behavior. 
* **Swallow the exception**
```C++
~destructor() {
  try {something} 
  catch(...) {
    make log entry about the specific failure;
  }
}
```
This is generally a bad practice because it suppresses important failed information. Sometimes this is prefered: for it to be a viable option, the program must be able to reliably continue execution even after an error has been encountered and ignored. 

A better way is to design the class interface so that a specialized function can be provided for its clients to have an opportunity to react to problems that may arise. A function other than destructor can be used to handle that exception, or it can keep track of certain condition and let destructor handle it later on if the condition is not meet. 

```diff
- Things to Remember
```
* Destructors should never emit exception. If functions called in a destructor may throw, the destructor should catch any exceptions, then swallow them or terminate the program. 
* If class clients need to be able to react to exceptions thrown during an operation, the class should provide a regular (i.e., non-destructor) function that performs the operation. 
