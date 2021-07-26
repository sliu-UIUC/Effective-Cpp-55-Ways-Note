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
Suppose we call ```C++ BuyTransaction b;```

```diff
- Things to Remember
```
* Don't call virtual functions during construction or destruction, because such calls will never go to a more derived class than that of the currently executing constructor or destructor.
