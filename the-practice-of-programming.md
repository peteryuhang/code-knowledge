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

### Expressions and Statements

- Indent to show structure. A consistent indentation style is the lowest-energy way to make a program's structure self-evident

```c
// bad
for(n++;n<100;field[n++]='\0');
*i = '\0'; return('\n');

// improve in structure, but still bad
for(n++; n < 100; field[n++] = '\0')
  ;
*i = '\0';
return('\n');

// better: the loop takes a more conventional form and is thus easier to grasp
for (n++; n < 100; n++)
  field[n] = '\0';
*i = '\0';
return '\n';
```

- **Use the natural form for expressions**. Write expressions as you might speak them aloud
- Conditional expressions that include negations are always hard to understand:

```c
// bad
if (!(block-id < actblks) || !(block-id >= unblocks))
  ...

// good
if ((block-id >= actblks) || (blockkid < unblocks))
  ...
```

- **Parenthesize to resolve ambiguity**

```c
// bad: not clear
leap_year = y % 4 == 0 && y % 100 != 0 || y % 400 == 0;

// good: Removed some of the blanks: grouping the operands of higher-precedence operators helps the reader to see the structure more quickly
leap_year = ((y%4 == 0) && (y%100 != 0)) || (y%400 == 0);
```

- **Break up complex expressions**

```c
// bad
*x += (*xp=(2*k < (n-m) ? c[k+1] : d[k--]));

// good: It's easier to grasp when broken into several pieces
if (2*k < n-m)
  *xp = c[k+1];
else
  *xp = d[k--];
*x += *xp;
```

- **Be clear**. The goal is to write clear code, not clever code

```c
// bad
subkey = subkey >> (bitoff - ((bitoff >> 3) << 3));

// improved: easy to grasp
subkey = subkey >> (bitoff & 0x7);

// continue improved: be clear
subkey >>= bitoff & 0x7
```

```c
// bad
child=(!LC&&!RC)?0:(!LC?RC:LC);

// good
if (LC == 0 && RC == 0)
  child = 0;
else if (LC == 0)
  child = RC;
else
  child = LC;
```

- Clarity is not the same as brevity. Often the clearer code will be shorter, as in the bit-shifting example, but it can also be longer, as in the conditional expression recast as an if-else. The proper criterion is ease of understanding

- **Be careful with side effects**

```c
// bad
str[i++] = str[i++] = ' ';

// good
str[i++] = ' ';
str[i++] = ' ';

// bad: If i is initially 3, the array element might be set to 3 or 4
array[i++] = i;

// bad: Part of the expression modifies yr and another part uses it. All the arguments to scanf are evaluated before the routine is called
scanf("%d %d", &yr, &profit[yr]);

// good(fix)
scanf("%d", &yr);
scanf("%d", &profit[yr]);
```

### Consistency and Idioms

- **Use a consistent indentation and brace style**
- Programmers have always argued about the layout of programs, but the specific style is much less important than its consistent application
- If you work on a program you didn't write, preserve the style you find there

- **Use idioms for consistency**
  - A central part of learning any language is developing a familiarity with its idioms

```c
// bad
i = O;
while (i <= n-1)
  array[i++] = 1.0;

// bad
for (i = 0; i < n; )
  array[i++] = 1.0;

// bad
for (i = n; --i >= 0; )
  array[i] = 1.0;

// good
for (i = 0; i < n; i++)
  array[i] = 1.0;
```

- **Use `else-ifs` for multi-way decisions**

```c
// bad
if (argc == 3)
  if ((fin = fopen(argv[1] , "r")) != NULL)
    if ((fout = fopen(argv[2], "w")) != NULL) {
      while ((c = getc(fin)) != EOF)
        putc(c, fout);
      fclose(fin);
      fclose(fout);
    } else
      printf("Can't open output file %s\n", argv[2]);
  else
    printf("Can't open input file %s\n", argv[1]);
else
  printf("Usage: cp inputfile outputfile\n");

// good: Changing the order in which the decisions are made leads to a clearer version
// in which we have also corrected the resource leak in the original
if (argc != 3)
  printf("Usage: cp inputfile outputfile\n");
else if ((fin = fopen(argv[1] , "r")) == NULL)
  printf("Can't open input file %s\n", argv[1]);
else if ((fin = fopen(argv[2] , "w")) == NULL)
  printf("Can't open output file %s\n", argv[2]);
else {
  while ((c = getc(fin)) != EOF)
    putc(c, fout);
  fclose(fin);
  fclose(fout);
}
```
