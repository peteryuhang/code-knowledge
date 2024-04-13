![](/assets/the-progmatic-programmer/preface_the_pragmatic_programmer.png)

## CH 1. A Pragmatic Philosophy

- Broken window theory: Neglect accelerates the rot faster
- Your knowledge Portfolio:
  - Continue investing is the key
  - The process of learning will expand your thinking, new way for doing thing
  - Don't let challenge stop there
  - Critically analyze what you read and hear
- Communicate:
  - If you don't listen to them, they won't listen to you
  - Encourage people to talk by asking questions
  - Sometimes all it takes is the simple question "Is this a good time to talk about..."
  - ![](./assets/the-progmatic-programmer/wisdom_acrostic_understanding_an_audience.png)

## CH 2. A Pragmatic Approach

### The Evils of Duplication

- **DRY principle (Don't Repeat Yourself)**: Every piece of knowledge must have a single unambiguous, authoritative representation within a system
  - Make it easy to reuse
  - Short cuts make for long delays

  - eg. duplicate code:

  ```cpp
  class Line {
    public:
      Point start;
      Point end;
      // The length is defined by the start and end points: change one of the points and the length changes
      // better to make the length a calculated field: double length() { return start.distanceTo(end); }
      double length;
  };
  ```
  - Where possible, always use accessor functions to read and write the attributes of objects. It will make it easier to add functionality, such as caching, in the future

### Orthogonality

- **Orthogomality** in programming: operations change just one thing without affecting others