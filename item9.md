# Item 9: Never call virtual functions during construction or destruction

Consider the following example.

```C++
class Transaction {                         // Base class for all transactions
public: 
  Transaction();
  virtual void logTransaction() const = 0;  // make type-dependent log entry
  ...
};

Transaction::Transaction() {                // implementation of basic class constructor
  ...
  logTransaction();                         // as a final action, log this transaction
}

class BuyTransaction: public Transaction {  // derived class
public: 
  virtual void logTransaction() const;      // how to log transactions of this type
  ...
};

class SellTransaction: public Transaction { // derived class
public: 
  virtual void logTransaction() const;      // how to log transactions of this type
  ...
}
```
Suppose the following code is executed: 
```C++
BuyTransaction b;
```
Before its contructor was called, the `Transaction` constructor is called, because base class parts of derived class objects are constructed earlier. However, in this case the version of `logTransction()` being called at the last line of `BuyTransaction` is actually the one from `Transaction`, the base class. That is, virtual functions never go down into derived class, and the object behaves as if it were of the base type. 

The reason is that because base class constructors execute before derived class constructors, derived class data members have not been initialized when base class constructors run. If virtual functions were to be called during base class construction went down to derived classes, the derived class functions would almost certainly refer to local data members, but these data members would not yet have been initialized. This is a more general issue, besides virtual functions resolving to the base class, the parts of the language that use runtime type information (e.g., `dynamic_cast` and `typeid`) treat the object as a base class type. In this example, while the `Transaction` constructor is running to initialize the base class part of a `BuyTransaction` object, the object is of type `Transaction`, because `BuyTransaction` object has not been initialized.

The same reasoning applies during destruction. Once derived class destructor has run, the object's derived class data members assume undefined value, so C++ treat them as no longer exist; Upon entry to the base class destructor, the object becomes a base class object.

In the example, `Transaction` constructor made a direct call to a virtual function, a clear violation that many compilers will issue warnings. Because the `logTransaction` function is pure virtual in `Transaction`, the program will have problem before runtime and would not link: the linker can not find the necessary implementation of `Transaction::logTransaction`. 

It would be good software engineering to avoid code replication by putting the common initialization code, including the call to `logTransaction`, into a private non-virtual initialization function like `init`: 

```C++
class Transaction {
public: 
  Transaction()
  {init(); }                  // call to non-virtual
  
  virtual void logTransaction() const = 0; 
  ...
private: 
  void init() {
    ...
    logTransaction();        // ... that calls a virtual!
  }
};
```
the above code is conceptually the same as the earlier version, but it typically compile and link successfully. In this case, becuase `logTransaction` is pure virtual in `Transaction`, most runtime systems will abort the program when the pure virtual is called (typically issuing a message to that effect). However, if `logTransaction` were not pure virtual, that version would be called, and the program would trot along and lead to undefined behavior. The only way to avoid this problem is to make sure that none of the constructor and destructor call virtual functions on the object being created or destroyed and that all the functions they call obey the same constraint. 

One way to ensure the proper version of `logTransaction` is called each time an object is created is to turn `logTransaction` into a non-virtual function, then require that derived class contructors pass the necessary log information to the transaction constructor. Then function can safely call the non-virtual `logTransaction`: 
```C++
class Transaction {
public: 
  explicit Transaction(const std::string& logInfo);
  void logTransaction(const std::string& logInfo) const;  // now a non-virtual func
  
  ...
};

Transaction::Transaction(const std::string& logInfo) {
  ...
  logTransaction(logInfo);                               // now a non-virtual call
}

class BuyTransaction: public Transaction {
public: 
  BuyTransaction(parameters)
  : Transaction(createLogString(parameters))            // pass log info to base class constructor
  {...}
  ...
private: 
  static std::string createLogString(parameters);  
}
```
Since you can't use virtual functions to call down from base classes during construction, you can compensate by having derived classes pass necessary information up to base class constructors instead. 

```diff
- Things to Remember
```
* Don't call virtual functions during construction or destruction, because such calls will never go to a more derived class than that of the currently executing constructor or destructor.
