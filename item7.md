# Item 7： Declare destructors virtual in polymorphic base classes. 
Sometimes we just want to access certain object without worrying about the details of it, so a `factory function`, which is a function that returns a base
class pointer to a newly-created derived class object - can be used to return a pointer to certain object. For instance: 

```C++
class TimeKeeper {
  public: 
    TimeKeeper();
    ~TimeKeeper();

}

TimeKeeper* getTimeKeeper();      // returns a pointer to a dynamically allocated object of a class derived from TimeKeeper
```
objects returned by `getTimeKeeper` are stored in heap, so its important that they need to be properly deleted after use to avoid memory leak: 

```C++
TimeKeeper *ptk = getTimeKeeper();     // get dynamically allocated object 
                                       // from TimeKeeper hierarchy
...                                    // use it 
delete ptk;                            // release it to avoid resource leak
```


```diff
- Things to Remember
```
* 
