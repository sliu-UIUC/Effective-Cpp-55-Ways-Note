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



```diff
- Things to Remember
```
* 
