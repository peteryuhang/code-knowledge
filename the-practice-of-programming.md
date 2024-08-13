![](/assets/the-practice-of-programming/preface.png)

## Ch 1. Style

- The purpose of style is to make the code easy to read for yourself and others, and good style is crucial to good programming
- The principles of programming style are based on common sense guided by experience, not on arbitrary rules and prescriptions

### Names

- **Use descriptive names for globals, short names for locals**
- Programmers are often encouraged to use long variable names regardless of context. That is a mistake: clarity is often achieved through brevity

```c
// bad
for (theElementIndex = 0 ; theElementIndex < number0fElements; theElementIndex++)
  elementArray[theElementIndex] = theElementIndex;

// good
for (i = 0; i < nelems; i++)
  elem[i] = i ;
```

- **Be consistent**. Give related things related names that show their relationship and high-light their difference

```java
// bad: Queues can only be accessed from a variable of type UserQueue, member names do not need to mention "queue" at all
class UserQueue {
  int noOfItemsInQ, frontOiTheQueue, queuecapacity;
  public int noOfUsersInQueue() {...}
}

// good
class UserQueue {
  int nitems, front, capacity;
  public int nusers() {...}
}
```

- **Use active names for functions**
- Functions that return a boolean (true or false) value should be named so that the return value is unambiguous

```c
// bad: Does not indicate which value is true and which is false
if (checkoctal(c)) ...

// good
if (isoctal(c)) ...
```

- **Be accurate**. A misleading name can result in mystifying bugs
- It's easy for a sensible name to disguise a broken implementation

```c
// bad
#define isoctal(c) ((c) >= '0' && (c) <= '8')

// good
#define isoctal(c) ((c) >= '0' && (c) <= '7')
```
