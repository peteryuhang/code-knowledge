![](/assets/the-practice-of-programming/preface.png)

## Ch 1. Style

- The purpose of style is to make the code easy to read for yourself and others, and good style is crucial to good programming
- The principles of programming style are based on common sense guided by experience, not on arbitrary rules and prescriptions
- If you think about style as you write code originally, and if you take the time to revise and improve it, you will develop good habits

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

### Function Macros

- With modern machines and compilers, the drawbacks of function macros outweigh their benefits
- **Avoid function macros**

```c
// bad: A parameter that appears more than once in the definition might be evaluated more than once
#define isupper(c) ((c) >= 'A' && (c) <= 'Z')
while (isupper(c = getchar())) // Each time an input character is greater than or equal to A, it will be discarded and another character read to be tested against Z
  ...

while ((c = getchar()) != EOF && isupper(c)) // correct
  ...

// bad: This will perform the square root computation twice as often as necessary
#define ROUND_TO_INT(x) ((int) ((x)+(((x)>O) ? O.5: -0.5)))
  ...
size = ROUND_TO_INT(sqrt(dx*dx + dy*dy));
```

- **Parenthesize the macro body and arguments**
  - Macros work by textual substitution: the parameters in the definitionare replaced by the arguments of the call and the result replaces the original call, as text

```c
// incorrect
#define square(x) (x) * (x)
1/square(x) // equal to 1/(x) * (x)

// correct
#define square(x) ((x) * (x))
```

- If an operation is expensive or common enough to be wrapped up. use a function

### Magic Numbers

- Magic tiumbers are the constants, array sizes, character positions, conversion factors, and other literal numeric values that appear in programs

- **Give names to magic numbers**

```c
// bad
fac = lim / 20; /* set scale factor */
if (fac < 1)
  fac = 1;
/* generate histogram */
for (i = 0, col = 0; i < 27; i++, j++) {
  col += 3;
  k = 21 - (let[i] / fac);
  star = (let[i] == 0) ? ' ' : '*';
  for (j = k; j < 22; j++)
    draw(j, col, star);
}
draw(23, 2, ' '); /* label x axis */
for (i = 'A'; i <= 'Z'; i++)
  printf("%c ", i );

// good
enum {
  MINROW    = 1,                /* top edge */
  MINCOL    = 1,                /* left edge */
  MAXROW    = 24,               /* bottom edge (<=) */
  MAXCOL    = 80,               /* right edge (<=) */
  LABELROW  = 1,                /* position of labels */
  NLET      = 26,               /* size of alphabet */
  HEIGHT    = MAXROW - 4,       /* height of bars */
  WIDTH     = (MAXCOL-1)/NLET   /* width of bars */
};
  ...
fac = (lim + HEIGHT-1) / HEIGHT;  /* set scale factor */
if (fac < 1)
  fac = 1;
for (i= 0; i < NLET; i++) {
  if (let[i] == 0)
    continue;
  for (j = HEIGHT - let[i]/fac; j < HEIGHT; j++)
    draw(j+1 + LABELROW, (i+l)*WIDTH, '*') ;
}
draw(MAXROW-1, MINCOL+l, ' '); /* label x axis */
for(i = 'A'; i <= 'Z'; i++)
  printf("%c ",i);
```

- **Define numbers as constant, not macros**
  - Macros are a dangerous way to program because they change the lexical structure of the program underfoot

- **Use character constants, not integers**

```c
// bad
if (c >= 65 && c <= 90)
  ...

// improved
if (c >= 'A' && c <= 'Z')
  ...

// best
if (isupper(c))
  ...
```

- A related issue is that the number 0 appears often in programs, in many contexts
  - The compiler will convert the number into the appropriate type, but it helps the reader to understand the role of each 0 if the type is explicit

```c
// bad
str = 0;
name[i] = O;
x = 0;

// good
str = NULL;
name[i] = '\0';
x = 0.0;
```

- **Use the language to calculate the size of an object**
- `sizeof(array[0])` may be better than `sizeof(int)` because it's one less thing to change if the type of the array changes

```c
// A convenient way to avoid inventing names for the numbers that determine array sizes
char buf[1024];
fgets(buf, sizeof(buf), stdin);

// The array size is set in only one place; the rest of the code does not change if the size does
// In fact the computation is done as the program is being compiled
// This is an appropriate use for a macro because it does something that a function cannot: compute the size of an array from its declaration
#define NELEMS(array) (sizeof(array) / sizeof(array[0]))
double dbuf[100];
for (i = 0;i < NELEMS(dbuf); i++)
  ...
```

### Comments

- The best comments aid the understanding of a program by briefly pointing out salient details or by providing a lager-scale view of the proceedings
- **Don't belabor the obvious**
  - Comments should add something that is not immediately evident from the code, or collect into one place information that is spread through the source
- **Comment functions and global data**, eg.

```c
struct State { /* prefix +suffix list */
  char *pref[NPREF]; /* prefix words */
  Suffix *suf;       /* list of suffixes */
  State *next;       /* next i n hash table */
};

// random: return an integer in the range [O...r-1]
int random(int r) {
  return (int)(Math.floor(Math.random()*r));
}

/*
 * idct: Scaled integer implementation of
 * Inverse two dimensional 8x8 Discrete Cosine Transform,
 * Chen-Wang algorithm (IEEE ASSP-32, pp 803-816, Aug 1984)
 *
 * 32-bit integer arithmetic (8-bit coefficients)
 * 11 multiplies, 29 adds per DCT
 *
 * Coefficients extended to 12 bits for
 * IEEE 1180-1990 compliance
 */
static void idct(int b[8*8]) { ... }
```

- **Don't comment bad code, rewrite it**

- **Don't contradict the code**
  - Most comments agree with the code when they are written, but as bugs are fixed and the program evolves, the comments are often left in their original form, resulting in disagreement with the code
  - Comments should not only agee with code,they should support it

```c
// bad & confused
time(&now) ;
strcpy(date, ctime(&now));
/* get rid of trailing newline character copied from ctime */
i = O;
while(date[i] >= ' ')
  i++;
date[i] = '\0';

// improved
time(&now);
strcpy(date, ctime(&now));
/* get rid of trailing newline character copied from ctime */
for (i= 0; date[i] != '\n'; i++)
  ;
date[i] = '\0';

// better: made more direct
time(&now) ;
strcpy(date, ctime(&now)) ;
/* ctime() puts newline at end of string; delete it */
date[strlen(date)-1] = '\0' ;
```

- Good code needs fewer comments than bad code

## Ch 2. Algorithms and Data Structures

### Searching

- **Sequential search** - linear search

```c
int lookup(char *word, char *array[]) {
  int i;
  for (i = 0; array[i] != NULL; i++)
    if (strcmp(word, array[i]) == 0)
      return i;

  return -1;
}
```

- **Binary search**: the table must be sorted, and we must know how long the table is

```c
int lookup(char *name, Nameval tab[], int ntab) {
  int low, high, mid, cmp;

  low = 0;
  high = ntab - 1;
  while (low <= high) {
    mid = (low + high) / 2;
    cmp = strcmp(name, tab[mid].name);
    if (cmp < 0)
      high = mid - 1;
    else if (cmp > 0)
      low = mid + 1;
    else /* found match */
      return mid;
  }
  return -1;
}
```

### Sorting

- Quick sort:

```c
/* quicksort: sort v[O]. .v[n-11 into increasing order */
void quicksort(int v[], int n) {
  int i,last;
  if (n <= 1) /* nothing to do */
    return;
  swap(v, 0, rand() % n); /* move pivot elem to v[O] */
  last = 0:
  for (i = 1; i < n; i++) /* partition */
    if (v[i] < v[0])
      swap(v, ++last, i);
  swap(v, 0, last);       /* restore pivot */
  quicksort(v, last);     /* recursively sort */
  quicksort(v+last+l, n-last-1); /* each part */
}

/* swap: interchange v[i] and v[j] */
void swap(int v[], int i,int j) {
  int temp;
  temp = v[i];
  v[i] = v[j];
  v[j] = temp;
}
```

### Libraries

- Function `scmp` to cast the arguments and call `strcmp` to do the comparison

```c
/* scmp: string compare of *p1 and *p2 */
int scmp(const void *p1, const void *p2) {
  char *v1, *v2;
  v1 = *(char **) p1;
  v2 = *(char **) p2;
  return strcmp(v1, v2);
}
```

- Write comparison function as `return v1-v2;` will resulting overflow, which would produce an incorrect answer.
- Direct comparison is longer but safe

```c
if (v1 < v2)
  return -1;
else if (v1 == v2)
  return 0;
else
  return 1;
```

### Growing Arrays

- The following code defines a growable array of Nameval items; new items are added at the end of the array, which is grown as necessary to make room

```c
typedef struct Nameval Nameval;
struct Nameval {
  char *name;
  int value;
};

struct NVtab {
  int nval;          /* current number of values */
  int max;           /* allocated number of values */
  Nameval tnameval;  /* array of name-value pairs */
} nvtab;

enum  { NVINIT = 1, NVGROW = 2 };

/* addname: add new name and value to nvtab */
int addname(Nameval newname) {
  Nameval tnvp;
  if (nvtab.nameval == NULL) { /* first time */
    nvtab.nameval = (Name *) malloc(NVINIT * sizeof(Nameval));
    if (nvtab.nameval == NULL)
      return -1;
    nvtab.max = NVINIT;
    nvtab.nval = 0;
  } else if (nvtab.nval >= nvtab.max) { /* grow */
    nvp = (Nameval *) realloc(nvtab.nameval, (NVGROW*nvtab.max) * sizeof(Nameval));
    if (nvp == NULL)
      return -1;
    nvtab.max *= NVGROW;
    nvtab.nameval = nvp;
  }
  nvtab.nameval[nvtab.nval] = newname;
  return nvtab.nval++;
}
```

- The function `addname` returns the index of the item just added, or -1 if some error occurred
- Deleting a name can be tricky:

```c
/* delname: remove first matching nameval from nvtab */
int delname(char *name) {
  int i;
  for (i = 0; i < nvtab.nval; i++) {
    if (strcmp(nvtab.nameval[i].name, name) == 0) {
      memmove(nvtab.nameval+i, nvtab.nameval+i+1, (nvtab.nval-(i+1)) * sizeof(Nameval))
      nvtab.nval--;
      return 1;
    }
  }
  return 0;
}
```

- The call to `memmove` squeezes the array by moving the elements down one position
- `memmove` is a standard library routine for copying arbitrary-sized blocks of memory
- The ANSI C standard defines two functions: `memcpy`, which is fast but might overwrite memory if source and destination overlap; and `memmove`, which might be slower but will always be correct

- We would replace the memmove call with the following loop:

```c
int j;
for (j = i; j < nvtab.nval-1; j++)
  nvtab.nameval[j] = nvtab.nameval[j+1];
```

- An alternative to moving the elements of the array is to mark deleted elements as unused. Then to add a new item, first search for an unused slot and grow the vector only if none is found

### Lists

- Structure of **singly-linked list**:

![](/assets/the-practice-of-programming/structure_of_singly_linked_list.png)

- List is better for items change frequently; Array is better for relatively static data

- Rather than defining an explicit List type, the usual way lists are used in C is to start with a type for the elements, and add a pointer that links to the next element

```c
typedef struct Nameval Nameval;
struct Nameval {
  char *name;
  int value;
  Nameval *next; /* in list */
};
```

- Way to construct an item:

```c
/* newitem: create new item from name and value */
Nameval *newitem(char tname, int value) {
  Nameval *newp;
  newp = (Nameval *) emalloc(sizeof(Nameval));
  newp->name = name;
  newp->value = value;
  newp->next = NULL;
  return newp;
}
```

- `emalloc` - calls malloc , and if the allocation fails, it reports the error and exits the program
- The simplest and fastest way to assemble a list is to add each new element to the front

```c
/* addfront: add newp to front of listp */
Nameval *addfront(Nameval *listp, Nameval *newp) {
  newp->next = listp;
  return newp;
}

// usage
nvlist = addfront(nvlist, newitem("smi1ey", Ox263A));
```

- This design works even if the existing list is empty (null) and makes it easy to combine the functions in expressions
- Adding an item to the end of a list is an `O(n)` procedure, since we must walk the list to find the end:

```c
/* addend: add newp to end of listp */
Nameval *addend(Nameval *listp, Nameval *newp) {
  Nameval *p;
  if (listp == NULL)
    return newp;
  for (p = listp; p->next != NULL; p = p->next)
    ;
  p->next = newp;
  return listp;
}
```

- To search for an item with a specific name, follow the next pointers:

```c
/* lookup: sequential search for name in listp */
Nameval *lookup(Nameval *listp, char tname) {
  for ( ; listp != NULL; listp = listp->next)
    if (strcmp(name, lisp->name) == 0)
      return listp;
  return NULL;     /* no match */
}
```

- An alternative is to write one function, `apply`, that walks a list and calls another function for each list element:

```c
/* apply: execute fn for each element of listp */
void apply(Nameval *listp, void (*fn)(Nameval *, void *), void *arg) {
  for (; listp != NULL; listp = listp->next)
    (*fn)(listp, arg); /* call the function */
}
```

- The usage of `apply`, eg. print the elements of a list:

```c
/* printnv: print name and value using format in arg */
void printnv(Nameval *p, void *arg) {
  char *fmt;
  fmt = (char *) arg;
  printf(fmt, p->name, p->value);
}

apply(nvl ist, printnv, "%s: %x\n");
```

- To destroy a list we must use more care:

```c
/* freeall: free all elements of listp */
void freeall(Nameval *ilstp) {
  Nameval *next;
  for ( ; listp != NULL; listp = next) {
    next = listp->next;
    /* assumes name is freed elsewhere */
    free(listp);
  }
}
```

- Notice that `freeall` does not free `listp->name`. It assumes that the name field of each `Nameval` will be freed somewhere else, or was never allocated

- Since memory cannot be used after it has been freed, the below gonna be wrong:

```c
// Wrong
for ( ; listp != NULL; listp = listp->next)
  free(listp);
```

- Deleting a single element from a list is more work than adding one:

```c
/* delitem: delete first "name" from listp */
Nameval *delitem(Nameval *listp, char *name) {
  Nameval *p, *prev;
  prev = NULL;
  for (p = listp; p != NULL; p = p->next) {
    if (strcmp(name, p->name) == 0) {
      if (prev == NULL)
        listp = p->next;
      else
        prev->next = p->next;
      free(p);
      return listp;
    }
    prev = p;
  }
  eprintf("delitem: %s not in list", name);
  return NULL; /* can't get here */
}
```

- Doubly-linked lists require more overhead, but finding the last element and deleting the current element are `O(1)` operations

## Trees


