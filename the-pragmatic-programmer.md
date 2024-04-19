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

### Reversibility

- There are no final decisions
- Hide a third-party product behind a well-defined, abstract interface

### Tracer Bullets

- Use tracer bullets to find the target
- Tracer code approach: Initially simply achieved an end-to-end connection among the components of your system
- Beneifits:
  - Users get to see something working early
  - Developers build a structure to work in
  - You have an integration platform
  - You have something to demonstrate
  - You have a better feel for progress
- Tracer bullets might bot always hit target, you then adjust your aim until they're on target
- Think of prototyping as the reconnaissance and intelligence gathering that takes place before a single tracer bullet is fired

### Prototypes and Post-it Notes

- We build software prototypes to analyze and expose risk, and to offer chances for correction at a greatly reduced cost
- Prototypes no need to be code-based, other format also ok, eg. post-it notes, index card, workflow, application logic, drawing...
- If you find yourself cannot give up the details, then you need to ask yourself if you are really building a prototype at all
  - Can consider switching to **tracer bullet** instead
- You can prototype anything you are not sure, not comfortable with, haven't tried before, or very critical to the final system
- Prototyping is a learning experience, its value is in the lessons learned
- Some specific area in the architectural prototype:
  - Are the responsibilities of the major components well defined and appropriate?
  - Are the collaborations between major components well defined?
  - Is coupling minimized?
  - Can you identify potential sources of duplication?
  - Are interface definitions and constraints acceptable?
  - Does every module have an access path to the data it needs during execution? Does it have that access when it needs it? (most valuable)

### Domain Languages

- You can design a mini-language which let you program close to the problem domain
- eg. the word below can be translate to a language

```
Listen for transactions defined by ABC Regulation 12.3 on a set of X.25 lines, translate them to XYZ Company’s format 43B, retransmit them on the satellite uplink, and store for future analysis


From X25LINE1 (Format=ABC123) {
  Put TELSTAR1 (Format=XYZ43B);
  Store DB;
}
```

- By coding at a higher level of abstraction, you are free to concentrate on solving domain problems, and can ignore petty implementation details
- Each people/user has their own problem domain, can generate mini-environments and languages for all of them
- To implement a mini-language, first using a notation such as **BNF** to define the syntax and have grammer specified
  - There’s another way of implementing a mini-language: extend an existing one
- Implemented languages can be used in 2 different ways:
  - **Data Languages**: produce some form of data structure, often used to represent configuration information
  - **Imperative languages**: the language is actually executed, more like an interface to help connect systems/components, eg. screen scraping
  ```
  locate prompt "SSN:"
  type "%s" social_security_number
  type enter
  waitfor keyboardunlock
  if text_at(10,14) is "INVALID SSN" return bad_ssn
  if text_at(10,14) is "DUPLICATE SSN" return dup_ssn
  # etc...
  ```
    - It is common to embed high-level imperative languages directly into your application, so that they execute when your code runs

### Estimating

- Estimate to avoid surprises
- Choose the units of your answer to reflect the accuracy you intend to convey:
  | Duration | Register ID|
  |:-----|:------|
  | 1-15 days | days |
  | 3-8 weeks | weeks |
  | 8-30 weeks | months |
  | 30+ weeks | think hard before giving an estimate |
- Before you get too committed to model building, cast around for someone who’s been in a similar situation in the past
- From your understanding of the question being asked, build a rough and ready bare-bones mental model
  - Often, the process of building the model leads to discoveries of underlying patterns and processes that weren’t apparent on the surface
- Once you have a model, you can decompose it into components
  - You’ll need to discover the mathematical rules that describe how these components interact
  - You’ll find that each component will typically have parameters that affect how it contributes to the overall model
  - At this stage, simply identify each parameter
- Once you have the parameters broken out, you can go through and assign each one a value
  - The trick is to work out which parameters have the most impact on the result, and concentrate on getting them about right
  - Typically, parameters whose values are added into a result are less significant than those that are multiplied or divid
- Run multiple calculations, varying the values of the critical parameters, until you work out which ones really drive the model
- When an estimate turns out wrong, don’t just shrug and walk away. Find out why it differed from your guess
  - Next time will be better
- The only way to determine the timetable for a project is by gaining experience on that same project
- Repeating the following steps to refine the estimate:
  - Check requirement
  - Analyze risk
  - Design, implement, inegrate
  - Validate with the users