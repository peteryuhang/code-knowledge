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

- **Orthogonality** in programming: Two or more things are orthogonal if changes in one do not affect any of the others
- Benefits
  1. Increased productivity
    - Changes are localized
    - Promotes reuse
    - Get more functionality per unit effort by combining orthogonal components
  2. Reduced risk
    - Diseased sections of code are isolated
    - The resulting system is less fragile
    - Better tested
    - Won't be tightly tied to a particular thing (eg. vendor, product, or platform)
- Orthogonality is also important for projects team
  - When teams are organized with lots of overlap, members are confused about responsibilities
- Design
  - Layer system is powerful way to design orthogonal systems
    - Each layer uses only the abstractions provided by the layers below it
  - Ask yourself "If I dramatically change the requirements behind a particular function, how many modules are affected"
    - In an orthogonal system, the answer should be "one"
  - Ask yourself how decoupled your design is from changes in the real world
    - Don't rely on the properties of things you can't control (eg. don't use the telephone as customer id)
- Toolkits and Libararies
  - When you bring in a toolkit, ask yourself whether it imposes changes on your code that shouldn’t be there
    - Isolate the imposed change will make it easier (eg. EJB transaction related logic, AOP logging)
- Code
  - Keep your code decoupled
    - eg. If you need to change an object’s state, get the object to do it for you
  - Avoid global data
    - eg. Singleton pattern
    - Your code is easier to understand and maintain if you explicitly pass any required context into your modules
  - Avoid similar functions
    - Can check strategy pattern for a better implementation
- Testing
  - Module level (or unit) testing is considerably easier to specify and perform than integration testing
  - Building unit tests is itself an interesting test of orthogonality
  - Bug fixing is also a good time to assess the orthogonality of the system as a whole
- Documentation
  - The axes are content and presentation
  - With truly orthogonal documentation, you should be able to change the appearance dramatically without changing the content
- Orthogonality is closely related to the **DRY** principle