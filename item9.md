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

The reason is that because base class constructors execute before derived class constructors, derived class data members have not been initialized when base class constructors run. If virtual functions were to be called during base class construction went down to derived classes, the derived class functions would almost certainly refer to local data members, but these data members would not yet have been initialized. This is a more general issue, besides virtual functions resolving to the base class, the parts of the language that use runtime type information (e.g., `dynamic_cast` and `typeid`) treat the object as a base class type. In this example, while the `Transaction` constructor is running to initialize the base class part of a `BuyTransaction` object, the object is of type `Transaction`.

```diff
- Things to Remember
```
* Don't call virtual functions during construction or destruction, because such calls will never go to a more derived class than that of the currently executing constructor or destructor.
