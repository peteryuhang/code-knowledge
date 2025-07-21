
[Training Courses](https://developer.android.com/courses)

## Android Basics with Compose

### Introduction to programming in Kotlin

- Variable initial value:

```kt
// val name: data_type = initial_value
val count: Int = 2
```

- Type inference(The Kotlin compiler looks at the data type of the initial value, and assumes that you want the variable to hold data of that type):
  - If initial value not been given, type must been specified

```kt
// val name = initial_value
val count = 2
```

- `val` keyword define a variable that is read-only
- `var` keyword define a variable is mutable or changeable
- Use `+` to concatenate strings together, `Int` and `Boolean` can also been concatenated to string in this way

- Function format:

```kt
// fun name(parameters): return_type {}
fun birthdayGreeting(name: String, age: Int): String {
    val nameGreeting = "Happy Birthday, $name!"
    val ageGreeting = "You are now 5 years old!"
    return "$nameGreeting\n$ageGreeting"
}
```

- Named arguments(let you reorder the arguments without affecting the output):

```kt
// both ways are same
println(birthdayGreeting(name = "Rex", age = 2))
println(birthdayGreeting(age = 2, name = "Rex"))
```

- Default arguments(lets you omit the argument when you call a function):

```kt
fun birthdayGreeting(name: String = "Rover", age: Int): String {
  return "Happy Birthday, $name! You are now $age years old!"
}
println(birthdayGreeting(age = 5))
println(birthdayGreeting("Rex", 2))
```

### Setup Android Studio

- `onCreate()` function is the entry point to Android app and calls other functions to build the user interface
- `setContent()` function within the `onCreate()` function is used to define your layout through composable functions
- **Composable** annotation tells the Kotlin compiler that this function is used by Jetpack Compose to generate the UI
  - `@Composable` functions can't return anything
- A Modifier is used to augment or decorate a composable
- [How to connect your Android device](https://developer.android.com/codelabs/basic-android-kotlin-compose-connect-device?continue=https%3A%2F%2Fdeveloper.android.com%2Fcourses%2Fpathways%2Fandroid-basics-compose-unit-1-pathway-2%23codelab-https%3A%2F%2Fdeveloper.android.com%2Fcodelabs%2Fbasic-android-kotlin-compose-connect-device#0)

### Build a Basic Layout

- A composable function:
  - Describes some part of your UI
  - Doesn't return anything
  - Takes some input and generates what's shown on the screen

- By default, the **SP** unit is the same size as the **DP** unit, but it resizes based on the user's preferred text size under phone settings
- [Add image & resource to Android App](https://developer.android.com/codelabs/basic-android-kotlin-compose-add-images?continue=https%3A%2F%2Fdeveloper.android.com%2Fcourses%2Fpathways%2Fandroid-basics-compose-unit-1-pathway-3%23codelab-https%3A%2F%2Fdeveloper.android.com%2Fcodelabs%2Fbasic-android-kotlin-compose-add-images#1)


```kt
class MainActivity : ComponentActivity() {
  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContent {
      HappyBirthdayTheme {
        // A surface container using the 'background' color from the theme
        Surface(
          modifier = Modifier.fillMaxSize(),
          color = MaterialTheme.colorScheme.background
        ) {
          GreetingImage(
            stringResource(R.string.happy_birthday_text),
            stringResource(R.string.signature_text)
          )
        }
      }
    }
  }
}

@Composable
fun GreetingText(message: String, from: String, modifier: Modifier = Modifier) {
    // Create a column so that texts don't overlap
    Column(
      verticalArrangement = Arrangement.Center,
      modifier = modifier
    ) {
      Text(
        text = message,
        fontSize = 100.sp,
        lineHeight = 116.sp,
        textAlign = TextAlign.Center,
        modifier = Modifier.padding(top = 16.dp)
      )
      Text(
        text = from,
        fontSize = 36.sp,
        modifier = Modifier
            .padding(top = 16.dp)
            .padding(end = 16.dp)
            .align(alignment = Alignment.End)

      )
    }
}

@Composable
fun GreetingImage(message: String, from: String, modifier: Modifier = Modifier) {
    // Create a box to overlap image and texts
    Box(modifier) {
      Image(
        painter = painterResource(id = R.drawable.androidparty),
        contentDescription = null,
        contentScale = ContentScale.Crop,
        alpha = 0.5F
      )
      GreetingText(
        message = message,
        from = from,
        modifier = Modifier
            .fillMaxSize()
            .padding(8.dp)
      )
    }
}

@Preview(showBackground = false)
@Composable
private fun BirthdayCardPreview() {
    HappyBirthdayTheme {
      GreetingImage(
        stringResource(R.string.happy_birthday_text),
        stringResource(R.string.signature_text)
      )
    }
}
```

### Kotlin Fundamentals

#### Conditional Statements

- `when` statement, eg:

```kt
fun main() {
  val trafficLightColor = "Black"

  when (trafficLightColor) {
    "Red" -> println("Stop")
    "Yellow" -> println("Slow")
    "Green" -> println("Go")
    else -> println("Invalid traffic-light color")
  }
}

fun main() {
  val x = 3

  when (x) {
    2, 3, 5, 7 -> println("x is a prime number between 1 and 10.")
    3 -> println("x is a prime number between 1 and 10.")
    5 -> println("x is a prime number between 1 and 10.")
    7 -> println("x is a prime number between 1 and 10.")
    else -> println("x isn't a prime number between 1 and 10.")
  }
}
```

- You can write more complex conditions in when conditionals with the comma (,), in ranges, and the `is` keyword

```kt
fun main() {
  val x: Any = 20

  when (x) {
    2, 3, 5, 7 -> println("x is a prime number between 1 and 10.")
    in 1..10 -> println("x is a number between 1 and 10, but not a prime number.")
    is Int -> println("x is an integer number, but not between 1 and 10.")
    else -> println("x isn't an integer number.")
  }
}
```

- It's recommended to use the `when` conditional to replace an `if/else` conditional when there are more than two branches

- Condition can be used as statement:

```kt
fun main() {
  val trafficLightColor = "Black"

  val message = 
    if (trafficLightColor == "Red") "Stop"
    else if (trafficLightColor == "Yellow") "Slow"
    else if (trafficLightColor == "Green") "Go"
    else "Invalid traffic-light color"

  println(message)
}

fun main() {
  val trafficLightColor = "Amber"

  val message = when(trafficLightColor) {
    "Red" -> "Stop"
    "Yellow", "Amber" -> "Slow"
    "Green" -> "Go"
    else -> "Invalid traffic-light color"
  }
  println(message)
}
```

#### Nullability

- In Kotlin, there's a distinction between **nullable** and **non-nullable** types:
  - Nullable types are variables that can hold `null`
  - Non-null types are variables that can't hold `null`

- To declare nullable variables in Kotlin, you need to add a `?` operator to the end of the type, eg

```kt
fun main() {
  var favoriteActor: String? = "Sandra Oh"
  favoriteActor = null
}
```

- Use the `?.` safe call operator to access methods or properties of nullable variables, eg.
  - This operator allows safer access to nullable variables because the Kotlin compiler stops any attempt of member access to null references and returns null for the member accessed

```kt
fun main() {
  var favoriteActor: String? = "Sandra Oh"
  println(favoriteActor?.length)
}
```

- Use the `!!` not-null assertion operator to access methods or properties of nullable variables
  - It means that you assert that the value of the variable isn't null, regardless of whether it is or isn't
  - This operator may result in a `NullPointerException` error being thrown if the nullable variable is indeed null

```kt
fun main() {
  var favoriteActor: String? = "Sandra Oh"
  println(favoriteActor!!.length)
}
```

- Null check can be combine with `if/else` statement:

```kt
fun main() {
  var favoriteActor: String? = "Sandra Oh"

  val lengthOfName = if (favoriteActor != null) {
    favoriteActor.length
  } else {
    0
  }

  println("The number of characters in your favorite actor's name is $lengthOfName.")
}
```

- You can convert a nullable variable to a non-nullable type with `if/else` expressions

- With the `?:` Elvis operator, you can add a default value when the `?.` safe-call operator returns null, eg

```kt
fun main() {
  var favoriteActor: String? = "Sandra Oh"

  val lengthOfName = favoriteActor?.length ?: 0

  println("The number of characters in your favorite actor's name is $lengthOfName.")
}
```

#### Classes and Objects

- Class basic example:

```kt
class SmartDevice {
  // The variables of val type are read-only variables, so they don't have set() functions
  val name = "Android TV"
  val category = "Entertainment"
  var deviceStatus = "online"
  var speakerVolume = 2
  // When you don't define the getter and setter function for a property, the Kotlin compiler internally creates the functions
  //  get() = field  
  //  set(value) {
  //      field = value    
  //  }

  fun turnOn() {
    println("Smart device is turned on.")
  }

  fun turnOff() {
    println("Smart device is turned off.")
  }
}

fun main() {
  val smartTvDevice = SmartDevice()
  println("Device name is: ${smartTvDevice.name}")
  smartTvDevice.turnOn()
  smartTvDevice.turnOff()
}
```

- Kotlin properties use a backing field to hold a value in memory
  - A backing field is basically a class variable defined internally in the properties
  - A backing field is scoped to a property, which means that you can only access it through the `get()` or `set()` property functions
  - Don't use the property name to get or set a value, otherwise code enters an endless loop

- Constructor syntax:

```kt
// default constructor, can be omitted
class SmartDevice constructor() {
  // ...
}

// constructor with parameters
class SmartDevice(val name: String, val category: String) {
  // ..
}

// instantiate the object
SmartDevice("Android TV", "Entertainment")
SmartDevice(name = "Android TV", category = "Entertainment")
```

- Two type of constructor in Kotlin
  - **Primary constructor**: 
    - A class can have only one
    - Defined as part of the class header
    - Can be a default or parameterized
    - Doesn't have body, doesn't contain any code
  - **Secondary constructor**:
    - A class can have multiple
    - If the class has a primary constructor, each secondary constructor needs to initialize the primary constructor

![](/assets/google-android-training-courses/kotlin_class_constructor_syntax.png)

- eg. Secondary constructor in the convert statusCode parameter to string representation

```kt
class SmartDevice(val name: String, val category: String) {
  var deviceStatus = "online"

  constructor(name: String, category: String, statusCode: Int) : this(name, category) {
    deviceStatus = when (statusCode) {
      0 -> "offline"
      1 -> "online"
      else -> "unknown"
    }
  }
  ...
}
```

- In Kotlin, all the classes are final by default, which means that you can't extend them, so you have to define the relationships between them
- The `open` keyword informs the compiler that this class is extendable, so now other classes can extend it

```kt
// Create a SmartTvDevice subclass that extends the SmartDevice superclass:
class SmartTvDevice(deviceName: String, deviceCategory: String) :
  SmartDevice(name = deviceName, category = deviceCategory) {
}
// The constructor definition for SmartTvDevice doesn't specify whether the properties are mutable or immutable
// This means that the deviceName and deviceCategory parameters are merely constructor parameters instead of class properties
// You won't be able to use them in the class, but simply pass them to the superclass constructor
```

- Override superclass methods or properties from subclasses, eg.

```kt
open class SmartDevice(val name: String, val category: String) {
  var deviceStatus = "online"

  open val deviceType = "unknown"

  open fun turnOn() {
    // function body
  }

  open fun turnOff() {
    // function body
  }
}

class SmartLightDevice(deviceName: String, deviceCategory: String) :
  SmartDevice(name = deviceName, category = deviceCategory) {

  override val deviceType = "Smart TV"

  override fun turnOn() {
    // ...
  }

  override fun turnOff() {
    // Reuse superclass code in subclasses with the super keyword
    super.turnOff()
  }
}
```

- Kotlin provides four visibility modifiers:
  - `public`: Default visibility modifier. Makes the declaration accessible everywhere
  - `private`: Makes the declaration accessible in the same class or source file
  - `protected`: Makes the declaration accessible in subclasses
  - `internal`: Makes the declaration accessible in the same module

- eg.

```kt
// properties and methods eg
open class SmartDevice(val name: String, val category: String) {
  var deviceStatus = "online"
    protected set
  
  protected fun nextChannel() {
    channelNumber++
    println("Channel number increased to $channelNumber.")
  } 
}

// constructor eg
open class SmartDevice protected constructor (val name: String, val category: String) {
  // ...
}

// class eg
internal open class SmartDevice(val name: String, val category: String) {
  // ...
}
```

- **Property delegates**. A property delegate lets you reuse the getter and setter code in multiple classes, eg:

```kt
import kotlin.properties.ReadWriteProperty
import kotlin.reflect.KProperty

open class SmartDevice(val name: String, val category: String) {
  // ...
}

class SmartTvDevice(deviceName: String, deviceCategory: String) :
  SmartDevice(name = deviceName, category = deviceCategory) {

  override val deviceType = "Smart TV"

  private var speakerVolume by RangeRegulator(initialValue = 2, minValue = 0, maxValue = 100)

  private var channelNumber by RangeRegulator(initialValue = 1, minValue = 0, maxValue = 200)

  // ...
}

class RangeRegulator(
  initialValue: Int,
  private val minValue: Int,
  private val maxValue: Int
) : ReadWriteProperty<Any?, Int> {  // Implement the ReadOnlyProperty interface for the val type
  var fieldData = initialValue

  // The KProperty is an interface that represents a declared property and lets you access the metadata on a delegated property
  override fun getValue(thisRef: Any?, property: KProperty<*>): Int {
    return fieldData
  }

  override fun setValue(thisRef: Any?, property: KProperty<*>, value: Int) {
    if (value in minValue..maxValue) {
      fieldData = value
    }
  }
}

fun main() {
  // ...
}
```

#### Function Types & Lambda Expressions

- Refer to a function as a value, you need to use the function reference operator `::`:

```kt
fun main() {
  val trickFunction = ::trick
}

fun trick() {
  println("No treats!")
}
```

- We can define function with a lambda expression:

```kt
fun main() {
  val trickFunction = trick
  trick()
}

val trick = {
  println("No treats!")
}
```

- Function types consist of a set of parentheses that contain an optional parameter list, the `->` symbol, and a return type

```kt
val treat: () -> Unit = {
  println("Have a treat!")
}
```

- Function can be used like other data type:

```kt
fun trickOrTreat(isTrick: Boolean, extraTreat: (Int) -> String): () -> Unit {
  if (isTrick) {
    return trick
  } else {
    println(extraTreat(5))
    return treat
  }
}

val trick = {
  println("No treats!")
}

val treat = {
  println("Have a treat!")
}
```

- Return can't be used in lambda expressions. Instead, the result of the last expression in the function becomes the return value:

```kt
// For multiple parameters, parameters are given names in the order that they occur
val coins: (Int) -> String = { quantity ->
  "$quantity quarters"
}
```

- Like other data types, function types can be declared as nullable:

```kt
fun trickOrTreat(isTrick: Boolean, extraTreat: ((Int) -> String)?): () -> Unit {  }
```

- Lambda expressions provide a variety of ways to make your code more concise:

```kt
// When a function has a single parameter and you don't provide a name, use it name instead
val coins: (Int) -> String = {
  "$it quarters"
}

// Can be passed directly in the call
val treatFunction = trickOrTreat(false, { "$it quarters" })

// You can place the lambda expression after the closing parenthesis to call the function
// when a function type is the last parameter of a function
// This makes your code more readable because it separates the lambda expression from the other parameters
// but doesn't change what the code does.
val treatFunction = trickOrTreat(false) { "$it quarters" }
```

- Kotlin provides several useful **higher-order** functions, which you can take advantage of with your newfound knowledge of lambdas
  - When a function returns a function or takes a function as an argument, it's called a **higher-order** function

- The `repeat()` function is a concise way to express a for loop with functions:

```kt
// for (iteration in start..end) {// ...} => repeat(times) {iteration -> // ...}
// repeat(times: Int, action: (Int) -> Unit)
fun main() {
  val treatFunction = trickOrTreat(false) { "$it quarters" }
  repeat(4) {
    treatFunction()
  }
}
```

### Add a Button to an App

- Composables are stateless by default, but it can store an object in memory using the `remember` composable
- `mutableStateof()` function returns an observable
  - When the value of observable variable changes, a recomposition is triggered, the value reflected, and the UI refreshes

```kt
@Preview
@Composable
fun DiceRollerApp() {
  DiceWithButtonAndImage(modifier = Modifier
    .fillMaxSize()                       // The layout fills the entire screen
    // The wrapContentSize() method specifies that the available space should
    // at least be as large as the components inside of it
    .wrapContentSize(Alignment.Center)
  )
}

@Composable
fun DiceWithButtonAndImage(modifier: Modifier = Modifier) {
  Column (
    modifier = modifier,
    horizontalAlignment = Alignment.CenterHorizontally
  ) {
    var result by remember {
      mutableStateOf(1)
    }
    val imageResource = when (result) {
      1 -> R.drawable.dice_1
      2 -> R.drawable.dice_2
      3 -> R.drawable.dice_3
      4 -> R.drawable.dice_4
      5 -> R.drawable.dice_5
      else -> R.drawable.dice_6
    }
    Image(
      painter = painterResource(id = imageResource),
      // descriptions to their respective UI components to increase accessibility
      contentDescription = result.toString()
    )
    Spacer(modifier = Modifier.height(16.dp))
    Button(
      onClick = { result = (1..6).random() }
    ) {
      Text(stringResource(id = R.string.roll))
    }
  }
}
```

- [Use the debugger in Android Studio](https://developer.android.com/codelabs/basic-android-kotlin-compose-intro-debugger?continue=https%3A%2F%2Fdeveloper.android.com%2Fcourses%2Fpathways%2Fandroid-basics-compose-unit-2-pathway-2%23codelab-https%3A%2F%2Fdeveloper.android.com%2Fcodelabs%2Fbasic-android-kotlin-compose-intro-debugger#3)

- Useful features in debuggers:
  - **Step Into**: Enter the call function and step by step
  - **Step Over**: Moves the execution to the next line of code (execute the call function instead enter)
  - **Step Out**: Does the opposite of the Step Into (execute the remaining part and return to outer function)

### Interacting with UI and State

#### State in Compose

- State in an app is any value that can change over time
- The only way to modify the Composition is through recomposition
  - Compose needs to know what state to track so that it can schedule the recomposition when it receives an update
  - Composable functions can store an object across recompositions with the `remember`

```kt
@Composable
fun EditNumberField(modifier: Modifier = Modifier) {
  // Use remember { }, the change survives the recomposition and state not re-initialized to ""
  // by is a Kotlin property delegation
  // The default getter and setter functions for the amountInput property are delegated to the remember class's getter and setter functions, respectively
  var amountInput by remember { mutableStateOf("") }

  TextField(
    // Compose keeps track of each composable that reads state value properties and triggers a recomposition when its value changes
    value = amountInput,
    // The onValueChange callback is triggered when the text box's input changes. In the lambda expression, the it variable contains the new value
    onValueChange = { amountInput = it },
    label = { Text(stringResource(R.string.bill_amount)) },
    // This condenses the text box to a single, horizontally scrollable line from multiple lines
    singleLine = true,
    keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Number),
    modifier = modifier
  )
}
```

- You should hoist the state when you need to:
  - **Share the state with multiple composable functions**
  - **Create a stateless composable that can be reused in your app**
- A stateless composable is a composable ​​that doesn't store its own state. It displays whatever state it's given as input arguments
  - Composable functions can be made stateless by extracting state from them

- eg. we can make previous composable stateless by extracting and hoisting state:

```kt
@Composable
fun EditNumberField(
  value: String,
  onValueChange: (String) -> Unit,
  modifier: Modifier = Modifier
) {
  TextField(
    label = { Text(stringResource(R.string.bill_amount)) },
    singleLine = true,
    keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Number),
    value = value,
    onValueChange = onValueChange,
    modifier = modifier
  )
}
```

#### Write automated tests

- Type of automated tests:
  - **Local tests**
    - Test functions, classes, and properties
    - Run in a development environment without the need for a device or emulator
  - Instrumentation tests
    - UI test
    - Test parts of an app that depend on the Android API, and its platform APIs and services
    - UI tests launch an app or part of an app, simulate user interactions, and check whether the app reacted appropriately
    - When run an instrumentation test on Android, the test code is actually built into its own Android Application Package (APK) like a regular Android app

##### Local test

- `@VisibleForTesting` annotation makes the method public, but indicates to others that it's only public for testing purposes
- Few thing about writing test:
  - Write automated tests in the form of methods
  - Annotate the method with the `@Test` (`org.junit.Test`) annotation
    - This lets the compiler know that the method is a test method and runs the method accordingly
  - Ensure that the name clearly describes what the test tests for and what the expected result is
  - Test methods only execute a set of instructions to assert that an app's UI or logic functions correctly
  - Tests typically end with an assertion, which is used to ensure that a given condition is met

- Common assertions in `JUnit` lib:
  - assertEquals()
  - assertNotEquals()
  - assertTrue()
  - assertFalse()
  - assertNull()
  - assertNotNull()
  - assertThat()

- eg.

```kt
class TipCalculatorTests {
  @Test
  fun calculateTip_20PercentNoRoundup() {
    val amount = 10.00
    val tipPercent = 20.00
    val expectedTip = NumberFormat.getCurrencyInstance().format(2)
    val actualTip = calculateTip(amount = amount, tipPercent = tipPercent, false)
    assertEquals(expectedTip, actualTip)
  }
}
```

##### Instrumentation test

- UI content must be set before the test
- eg.

```kt
class TipUITests {
  @get:Rule
  val composeTestRule = createComposeRule()

  @Test
  fun calculate_20_percent_tip() {
    // set the UI content
    composeTestRule.setContent {
      TipTimeTheme {
        TipTimeLayout()
      }
    }

    // Testing with example
    composeTestRule.onNodeWithText("Bill Amount").performTextInput("10")
    composeTestRule.onNodeWithText("Tip Percentage").performTextInput("20")
    val expectedTip = NumberFormat.getCurrencyInstance().format(2)
    composeTestRule.onNodeWithText("Tip Amount: $expectedTip").assertExists(
      "No node with this text was found."
    )
  }
}
```

### More Kotlin fundamentals

#### Generic data type

- Generic allow a data type to specify an unknown placeholder data type that can be used with its properties and methods

- eg. 

```kt
class Question<T>(
  val questionText: String,
  val answer: T,
  val difficulty: String
)
```

#### Enum constant

- Use an **enum constant**, which makes the maintain more easy

```kt
enum class Difficulty {
  EASY, MEDIUM, HARD
}

class Question<T>(
  val questionText: String,
  val answer: T,
  val difficulty: Difficulty
)
```

#### Data class

- Defining a class as a **data class** allows the Kotlin compiler to make certain assumptions, and to automatically implement some methods
  - eg. `toString()`, `hashCode()`, `equals()`, `copy()`
  - Data class only contains data
  - A data class cannot be abstract, open, sealed, or inner

```kt
data class Question<T>(
  val questionText: String,
  val answer: T,
  val difficulty: Difficulty
)
```

#### Singleton object

- Define a singleton object
  - A singleton object can't have a constructor as you can't create instances directly

- eg.

```kt
object StudentProgress {
  var total: Int = 10
  var answered: Int = 3
}

fun main() {
  println("${StudentProgress.answered} of ${StudentProgress.total} answered.")
}
```

- Define a singleton object inside another class using a companion object
  -  A companion object allows you to access its properties and methods from inside the class

```kt
class Quiz {
  val question1 = Question<String>("Quoth the raven ___", "nevermore", Difficulty.MEDIUM)
  val question2 = Question<Boolean>("The sky is green. True or false", false, Difficulty.EASY)
  val question3 = Question<Int>("How many days are there between full moons?", 28, Difficulty.HARD)

  companion object StudentProgress {
    var total: Int = 10
    var answered: Int = 3
  }
}

fun main() {
  println("${Quiz.answered} of ${Quiz.total} answered.")
}
```

#### Extend classes

- Define an extension property

```kt
val Quiz.StudentProgress.progressText: String
  get() = "${answered} of ${total} answered"
```

- Define an extension function

```kt
fun Quiz.StudentProgress.printProgressBar() {
  repeat(Quiz.answered) { print("▓") }
  repeat(Quiz.total - Quiz.answered) { print("▒") }
  println()
  println(Quiz.progressText)
}
```

- Having the option of extension properties and methods gives you more options to expose your code to other developers

#### Interface

- Interfaces allow for variation in the behavior of classes that extend them. It's up to each class to provide the implementation

```kt
interface ProgressPrintable {
  val progressText: String
  fun printProgressBar()
}

class Quiz : ProgressPrintable {
  override val progressText: String
    get() = "${answered} of ${total} answered"
  
  override fun printProgressBar() {
    repeat(Quiz.answered) { print("▓") }
    repeat(Quiz.total - Quiz.answered) { print("▒") }
    println()
    println(progressText)
  }
}
```

#### Scope functions

- **Scope functions** allow you to concisely access properties and methods from a class without having to repeatedly access the variable name

- The `let()` function allows you to refer to an object in a lambda expression using the identifier it, instead of the object's actual name

```kt
fun printQuiz() {
  question1.let {
    println(it.questionText)
    println(it.answer)
    println(it.difficulty)
  }
  println()
  question2.let {
    println(it.questionText)
    println(it.answer)
    println(it.difficulty)
  }
}
```

- The `apply()` function is an extension function that can be called on an object using dot notation, no need the variable

```kt
Quiz().apply {
  printQuiz()
}
```

#### Collections

##### Arrays

- eg.

```kt
val rockPlanets = arrayOf<String>("Mercury", "Venus", "Earth", "Mars")
val gasPlanets = arrayOf("Jupiter", "Saturn", "Uranus", "Neptune")

val solarSystem = rockPlanets + gasPlanets
```

- If you want to make an array larger than it already is, you need to create a new array

##### Lists

- **List** is an interface that defines properties and methods related to a read-only ordered collection of items
- **MutableList** extends the List interface by defining methods to modify a list, such as adding and removing elements

- eg.

```kt
// =============================== List =====================================

val solarSystem = listOf("Mercury", "Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune")
println(solarSystem.size) // 8
println(solarSystem[2])  // "Earth"
println(solarSystem.get(3)) // "Mars"
println(solarSystem.indexOf("Earth"))  // 2
println(solarSystem.indexOf("Pluto"))  //  -1

// Iterate
for (planet in solarSystem) {
  println(planet)
}

// =============================== MutableList =====================================

val solarSystem = mutableListOf("Mercury", "Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune")
solarSystem.add("Pluto") // adds to the end of the list
solarSystem.add(3, "Theia") // adds to the certain position
solarSystem[3] = "Future Moon"
solarSystem.removeAt(9)
solarSystem.remove("Future Moon") // only remove one
println(solarSystem.contains("Pluto"))
println("Future Moon" in solarSystem)
```

##### Sets

- eg.

```kt
val solarSystem = mutableSetOf("Mercury", "Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune")
println(solarSystem.size)
solarSystem.add("Pluto")
println(solarSystem.contains("Pluto"))
solarSystem.remove("Pluto")
```

##### Map

- eg.

```kt
val solarSystem = mutableMapOf(
  "Mercury" to 0,
  "Venus" to 0,
  "Earth" to 1,
  "Mars" to 2,
  "Jupiter" to 79,
  "Saturn" to 82,
  "Uranus" to 27,
  "Neptune" to 14
)
println(solarSystem.size)
solarSystem["Pluto"] = 5
solarSystem.put("Jupiter", 1)
solarSystem.remove("Pluto")
```

##### Higher-order functions

- **Higher-order functions**: functions that take other functions as parameters and/or return a function
  - eg. `repeat()`
  - Higher-order functions can let collections usage more concise

###### forEach()

- The `forEach()` function executes the function passed as a parameter once for each item in the collection
  - `forEach(action: (T) -> Unit)`

- eg.

```kt
class Cookie(
  val name: String,
  val softBaked: Boolean,
  val hasFilling: Boolean,
  val price: Double
)

val cookies = listOf(
  Cookie(
    name = "Chocolate Chip",
    softBaked = false,
    hasFilling = false,
    price = 1.69
  ),
  Cookie(
    name = "Banana Walnut", 
    softBaked = true, 
    hasFilling = false, 
    price = 1.49
  )
)

fun main() {
  cookies.forEach {
    println("Menu item: ${it.name}")
  }
}
```

###### map()

- The `map()` function lets you transform a collection into a new collection with the same number of elements

```kt
val fullMenu = cookies.map {
  "${it.name} - $${it.price}"
}
println("Full menu:")
fullMenu.forEach {
  println(it)
}
```

###### filter()

- The `filter()` function lets you create a subset of a collection

```kt
val softBakedMenu = cookies.filter {
  it.softBaked
}
println("Soft cookies:")
softBakedMenu.forEach {
  println("${it.name} - $${it.price}")
}
```

###### groupBy()

- The `groupBy()` function can be used to turn a list into a map, based on a function
  - Each unique return value of the function becomes a key in the resulting map
  - The values for each key are all the items in the collection that produced that unique return value

```kt
val groupedMenu = cookies.groupBy { it.softBaked }
val softBakedMenu = groupedMenu[true] ?: listOf()
val crunchyMenu = groupedMenu[false] ?: listOf()

println("Soft cookies:")
softBakedMenu.forEach {
  println("${it.name} - $${it.price}")
}

println("Crunchy cookies:")
crunchyMenu.forEach {
  println("${it.name} - $${it.price}")
}
```

###### fold()

- The `fold()` function is used to generate a single value from a collection
  - Sometimes called `reduce()`

```kt
val totalPrice = cookies.fold(0.0) {total, cookie ->
  total + cookie.price
}

println("Total price: $${totalPrice}")
```

###### sortedBy()

- `sortedBy()` lets you specify a lambda that returns the property you'd like to sort by

```kt
val alphabeticalMenu = cookies.sortedBy {
  it.name
}
```

### Build a scrollable list

- Sample code:

```kt
@Composable
fun AffirmationsApp() {
  val layoutDirection = LocalLayoutDirection.current
  // This composable will set the padding for the AffirmationsList composable
  Surface(
    modifier = Modifier
      .fillMaxWidth()
      .statusBarsPadding()
      .padding(
        // translate a LayoutDirection object into padding
        start = WindowInsets.safeDrawing.asPaddingValues().calculateStartPadding(layoutDirection),
        end = WindowInsets.safeDrawing.asPaddingValues().calculateEndPadding(layoutDirection)
      )
  ) {
    AffirmationList(
      affirmationList = Datasource().loadAffirmations()
    )
  }
}

@Composable
fun AffirmationCard(
  affirmation: Affirmation,
  // It is a best practice to pass a modifier to every composable and set it to a default value
  modifier: Modifier = Modifier
) {
  Card(modifier = modifier) {
    Column {
      Image(
        painter = painterResource(affirmation.imageResourceId),
        contentDescription = stringResource(affirmation.stringResourceId),
        modifier = Modifier.fillMaxWidth().height(194.dp),
        // A contentScale determines how the image should be scaled and displayed
        contentScale = ContentScale.Crop
      )
      Text(
        text = LocalContext.current.getString(affirmation.stringResourceId),
        modifier = Modifier.padding(16.dp),
        style = MaterialTheme.typography.headlineSmall
      )
    }
  }
}

@Composable
fun AffirmationList(
  affirmationList: List<Affirmation>,
  modifier: Modifier = Modifier
) {
  // A Column can only hold a predefined, or fixed, number of composables
  // A LazyColumn can add content on demand, which makes it good for long lists and particularly when the length of the list is unknown
  // A LazyColumn also provides scrolling by default, without additional code
  LazyColumn(modifier = modifier) {
    // The items() method is how you add items to the LazyColumn
    items(affirmationList) { affirmation ->
      AffirmationCard(
        affirmation = affirmation,
        modifier = Modifier.padding(8.dp)
      )
    }
  }
}
```

### Build beautiful apps

#### Color

- Color represented in Android system:

![](/assets/google-android-training-courses/color_represent_system.png)

- [Material Theme Builder](https://material-foundation.github.io/material-theme-builder/) to create a custom color scheme
  - The **primary** colors are used for key components across the UI
  - The **secondary** colors are used for less prominent components in the UI
  - The **tertiary** colors are used for contrasting accents that can be used to balance primary and secondary colors or bring heightened attention to an element, such as an input field
  - The **on** color elements appear **on top** of other colors in the palette, and are primarily applied to text, iconography, and strokes. In our color palette, we have an **onSurface** color, which appears on top of the **surface** color, and an **onPrimary** color, which appears on top of the primary color

- We don't need to explicitly assign a color to a component, it is automatically mapped to a color slot when you set the color theme in your app
  - `MaterialTheme` components are automatically mapped to color slots
  - Other key components across the UI like Floating Action Buttons also default to the Primary color

#### Shape

- `RoundedCornerShape` describe a rectangle with rounded corners, it defines how round the corners are
- eg.

```kt
Image(
  modifier = modifier
    .size(dimensionResource(R.dimen.image_size))
    .padding(dimensionResource(R.dimen.padding_small))
    // Clip the image into a circle
    .clip(MaterialTheme.shapes.small),
    // Crops the image to fit
  contentScale = ContentScale.Crop,
  painter = painterResource(dogIcon),
  contentDescription = null
)
```

- A **Card** is a surface that can contain a single composable and contains **options for decoration**

#### Typography

- [Material Design Type Scale](https://m3.material.io/styles/typography/type-scale-tokens) includes fifteen font styles that are supported by the type system

- Reusable categories of text:
  - **Display**
    - As the largest text on the screen, display styles are reserved for short, important text or numerals. They work best on large screens
  - **Headline**
    - Headlines are best-suited for short, high-emphasis text on smaller screens. These styles can be good for marking primary passages of text or important regions of content
  - **Title**
    - Titles are smaller than headline styles, and should be used for medium-emphasis text that remains relatively short
  - **Body**
    - Body styles are used for longer passages of text in your app
  - **Label**
    - Label styles are smaller, utilitarian styles, used for things like the text inside components or for very small text in the content body, such as captions

- **Fonts**:
  - The Android platform provides a variety of fonts, but you may want to customize your app with a font not provided by default
  - Custom fonts can add personality and be used for branding
  - Custom fonts can be searched on [here](https://fonts.google.com/)

#### Top bar

- A **Scaffold** is a layout that provides slots for various components and screen elements, such as an Image, Row, or Column
  - A Scaffold also provides a slot for a TopAppBar

- There are four different types of **TopAppBar**: center, small, medium and large

- eg.
```kt
@Composable
fun WoofTopAppBar(modifier: Modifier = Modifier) {
  CenterAlignedTopAppBar(
    title = {
      Row(
        verticalAlignment = Alignment.CenterVertically
      ) {
        Image(
          modifier = Modifier
              .size(dimensionResource(id = R.dimen.image_size))
              .padding(dimensionResource(id = R.dimen.padding_small)),
          painter = painterResource(R.drawable.ic_woof_logo),
          contentDescription = null
        )
        Text(
          text = stringResource(R.string.app_name),
          style = MaterialTheme.typography.displayLarge
        )
      }
    },
    modifier = modifier
  )
}

@Composable
fun WoofApp() {
  Scaffold(
    topBar = {
      WoofTopAppBar()
    }
  ) { it ->
    LazyColumn(contentPadding = it) {
      items(dogs) {
        DogItem(
          dog = it,
          modifier = Modifier.padding(dimensionResource(R.dimen.padding_small))
        )
      }
    }
  }
}
```

#### Animation

- **Icon** design often reduces the level of detail to the minimum required to be familiar to a user
  - Material Design provides a [number of icons](https://fonts.google.com/icons), arranged in common categories, for most of your needs
  - Add gradle dependency: `implementation("androidx.compose.material:material-icons-extended")`

- **Animations** can add visual cues that notify users about what's going on in your app
  - [Spring animation](https://developer.android.com/develop/ui/compose/animation/introduction#spring) is a physics-based animation driven by a spring force
    - Damping ratio: The bounciness of the spring
    - Stiffness level: How fast the spring moves toward the end
  - The **animate*AsState()** functions are one of the simplest animation APIs in Compose for animating a single value
    - You only provide the end value (or target value), and the API starts animation from the current value to the specified end value
    - Beside built-in data type, can easily add support for other data types using `animateValueAsState()` that takes a generic type
    - We can also consider change the color by using `animateColorAsState()`

- eg.

```kt
@Composable
private fun DogItemButton(
  expanded: Boolean,
  onClick: () -> Unit,
  modifier: Modifier = Modifier
) {
  IconButton(
    onClick = onClick,
    modifier = modifier
  ) {
    Icon(
      imageVector = if (expanded) Icons.Filled.ExpandLess else Icons.Filled.ExpandMore,
      contentDescription = stringResource(R.string.expand_button_content_description),
      tint = MaterialTheme.colorScheme.secondary
    )
  }
}

@Composable
fun DogHobby(
  @StringRes dogHobby: Int,
  modifier: Modifier = Modifier
) {
  Column(
    modifier = modifier
  ) {
    Text(
      text = stringResource(R.string.about),
      style = MaterialTheme.typography.labelSmall
    )
    Text(
      text = stringResource(dogHobby),
      style = MaterialTheme.typography.bodyLarge
    )
  }
}

@Composable
fun DogItem(
  dog: Dog,
  modifier: Modifier = Modifier
) {
  var expanded by remember { mutableStateOf(false) }
  val color by animateColorAsState(
    targetValue = if (expanded) MaterialTheme.colorScheme.tertiaryContainer
    else MaterialTheme.colorScheme.primaryContainer,
  )
  Card(modifier = modifier) {
    Column(
      modifier = modifier
        .animateContentSize (
          animationSpec = spring(
            dampingRatio = Spring.DampingRatioMediumBouncy,
            stiffness = Spring.StiffnessMedium
          )
        )
        .background(color = color)
    ) {
      Row(
        modifier = modifier
          .fillMaxWidth()
          .padding(dimensionResource(R.dimen.padding_small))
      ) {
        DogIcon(dog.imageResourceId)
        DogInformation(dog.name, dog.age)
        // Modifier.weight() sets the UI element's width/height proportionally to the element's weight, relative to its weighted siblings
        // The spacer is the only weighted child element in the row, it causes the spacer to fill the space remaining in the row
        Spacer(modifier = Modifier.weight(1f))
        DogItemButton(
          expanded = expanded,
          onClick = { expanded = !expanded }
        )
      }
      if (expanded) {
        DogHobby(
          dog.hobbies,
          modifier = Modifier.padding(
            start = dimensionResource(R.dimen.padding_medium),
            top = dimensionResource(R.dimen.padding_small),
            end = dimensionResource(R.dimen.padding_medium),
            bottom = dimensionResource(R.dimen.padding_small)
          )
        )
      }
    }
  }
}
```

### Navigation and Architecture

#### Stages of the Activity lifecycle

- **Activity Lifecycle** on android app:

![](/assets/google-android-training-courses/activity_lifecycle.png)

- Android invokes these callbacks when the activity moves from one state to another
  - Can override those methods in your own activities to perform tasks in response to those lifecycle state changes

- Example steps
  1. `onCreate()`:
    - Do any one-time initializations for activity
    - Called once
    - After this, activity considered created
    - Must immediately call `super.onCreate()`, same is true for other lifecycle callback methods
  2. `onStart()`:
    - Just after `onCreate`
    - After run, activity is visible on the screen
    - Can be called many times by the system
    - Paired with a corresponding `onStop()`
    - If the user starts your app and then returns to the device's home screen, the activity is stopped and is no longer visible on screen
  3. `onResume()`:
    - Brings the app to the foreground, and the user is now able to interact with it
  4. `onPause()`:
    - After called, the app no longer has focus, user can interact with the app
    - Need to keep lightweight
  5. `onStop()`:
    - After called, the app no longer on screen
  6. `onDestroy()`:
    - Nullify, close, or destroy objects that the activity may have been using so that they don't continue to use resources, like memory
    - Called once
  7. `onRestart()`:
    - Put code that you only want to call if your activity is not being started for the first time

- Can use `import android.util.Log` and **Logcat** tool to debugging
- Rotate screen:
  - The activity is shut down (`onDestroy()` been called) and re-created, the activity re-starts with default values

- **Lifecycle of a composable**:
  - Composable functions have their own lifecycle that is independent of the Activity lifecycle
    - Enters the Composition, recomposing 0 or more times, and then leaving the Composition
  - When the state of your app changes, a **recomposition** is scheduled
  - Recomposition is when Compose re-executes the composable functions whose state might have changed and creates an updated UI
  - **The only way to create or update a Composition is by its initial composition and subsequent recompositions**
  - To indicate to Compose that it should track an object's state, the object needs to be of type **State** or **MutableState**
    - eg.
    ```kt
    // mutableStateOf for recomposition triggered
    // remember for retain its updated value & reuse its value during recompositions
    var revenue by remember { mutableStateOf(0) }
    ```
    - For Compose to retain the state during a configuration change, you must use rememberSaveable
  - To save values during recompositions, you need to use **remember**
  - Use **rememberSaveable** to save values during recompositions AND **configuration changes**

#### ViewModel and State in Compose

- Architectural principles:
  - **Separation of concerns**:
    - App is divided into classes of functions, each with separate responsibilities
  - **Driving UI from a model**:
    - Should drive your UI from a model, preferably a persistent model
    - Models are components responsible for handling the data for an app
    - Models are independent from the UI elements and app components, so they're unaffected by the app lifecycle

![](/assets/google-android-training-courses/view_model.png)

- The **ViewModel** component holds and exposes the state the UI consumes
- **UI state** is application data transformed by ViewModel
- ViewModel objects are not destroyed
  - The app automatically retains ViewModel objects during configuration changes so that the data they hold is immediately available after the recomposition
- **unidirectional data flow (UDF)** is a design pattern in which state flows down and events flow up
  - Decouple composables that display state in the UI from the parts of storing and changing state

- `UI = UI Elements + UI State`
  - UI is what the user sees, UI state is what the app says they should see

- **StateFlow**: Data holder observable flow that emits the current and new state updates
  - Works well with classes that must maintain an observable immutable state
  - `value` property reflects the current state value
  - `MutableStateFlow` helps update the state and send to the flow

- Anatomy of **alert dialog**:

![](/assets/google-android-training-courses/alert_dialog_anatomy.png)

- For detail code and example, can check `Unscramble` app


#### Unit tests for ViewModel

- Use the `testImplementation` configuration in gradle to add test only dependencies
  - eg. `testImplementation("junit:junit:4.13.2")` for junit test module

- Compose **BOM** is the recommended way to manage Compose library versions
  - BOM lets you manage all of your Compose library versions by specifying only the BOM's version
  - eg. `implementation platform("androidx.compose:compose-bom:2023.06.01")`
  - BOM with the compose testing libraries(instrumented tests) eg. `androidTestImplementation(platform("androidx.compose:compose-bom:2023.06.01"))`
  - Compose BOM is only for Compose libraries

##### Test strategy

- At a very basic level, you can categorize the tests in three scenarios
  1. Success path
    - Also known as happy path
    - Easy to create an exhaustive list of scenarios
  2. Error path
    - Check how the app responds to error conditions or invalid user input
    - Quite challenging to determine all the possible error
  3. Boundary case
    - Focuses on testing boundary conditions in the app

- Good unit test properties:
  1. Focused
    - Focus on testing a unit, such as a piece of code (eg. class or function)
    - Narrow and focus on validating the correctness of individual pieces of code, rather than multiple pieces of code at the same time
  2. Understandable
    - At a glance, a developer should be able to immediately understand the intention behind the test
  3. Deterministic
    - Should consistently pass or fail w/o changing the code
    - Run any number of times, should yield the same result
  4. Self-contained
    - Does not require any human interaction or setup and runs in isolation

- Test function naming strategy: `thingUnderTest_TriggerOfTest_ResultOfTest`
  - eg. `gameViewModel_CorrectWordGuessed_ScoreUpdatedAndErrorFlagUnset`

- Test methods are executed in isolation to avoid unexpected side effects from mutable test instance state
  - By default, before each test method is executed, JUnit creates a new instance of the test class
  - Each instance has its own copy of the all the corresponding test class property

- In Android studio, we can run test with coverage to help checking
  - Use the code coverage as a tool to find the parts of code that were not executed by your tests, rather than a tool to measure your code's quality

#### Navigation in Compose

- Navigation component has three main parts:
  1. **NavController**: Responsible for navigating between destinations—that is, the screens in your app
  2. **NavGraph**: Maps composable destinations to navigate to
  3. **NavHost**: Composable acting as a container for displaying the current destination of the NavGraph

- Because the routes is finite number, we can define app's route using an **enum class**

- A benefit of using a NavHost to handle your app's navigation is that navigation logic is kept separate from individual UI
  - This option avoids some of the major drawbacks of passing the navController as a parameter

#### UI Testing

- UI tests are contained in their own source sets called androidTest
  - `androidTestImplementation` keyword to declare the dependency in the app module's `build.gradle.kts` file

- eg. testing for navigation

```kt
class CupcakeScreenNavigationTest {
  @get:Rule
  val composeTestRule = createAndroidComposeRule<ComponentActivity>()

  // the lateinit keyword is used to declare a property that can be initialized after the object has been declared
  private lateinit var navController: TestNavHostController

  // When a method is annotated with @Before, it runs before every method annotated with @Test
  @Before
  fun setupCupcakeNavHost() {
    composeTestRule.setContent {
      navController = TestNavHostController(LocalContext.current).apply {
        navigatorProvider.addNavigator(ComposeNavigator())
      }
      CupcakeApp(navController = navController)
    }
  }
}
```

### Kotlin Coroutines

#### Synchronous Code

- examples:

```kt
import kotlinx.coroutines.*

fun main() {
  // runBlocking() is synchronous; it will not return until all work within its lambda block is completed
  runBlocking {
    println("Weather forecast")
    // Suspend function 'delay' should be called only from a coroutine or another suspend function
    delay(1000)
    println("Sunny")
  }
}
```

- A suspend function can only be called from a coroutine or another suspend function:
  - A suspending function is like a regular function, but it can be suspended and resumed again later
  - A **suspension point** is the place within the function where execution of the function can suspend
    - Once execution resumes, it picks up where it last left off in the code and proceeds with the rest of the function

```kt
fun main() {
  val time = measureTimeMillis {
    runBlocking {
      println("Weather forecast")
      printForecast()
      printTemperature()
    }
  }
}

// Add the suspend modifier before the fun keyword to make it a suspending function
suspend fun printForecast() {
  delay(1000) // suspending function
  println("Sunny")
}

suspend fun printTemperature() {
  delay(1000)
  println("30\u00b0C")
}
```

#### Asynchronous Code

- Coroutines in Kotlin follow a key concept called **structured concurrency**
  - Code is sequential by default and cooperates with an underlying event loop, unless you explicitly ask for concurrent execution (e.g. using `launch()`)

- eg. The output is the same but will be faster than before

```kt
import kotlinx.coroutines.*

fun main() {
  runBlocking {
    println("Weather forecast")
    launch {
      printForecast()
    }
    launch {
      printTemperature()
    }
  }
}

suspend fun printForecast() {
  delay(1000)
  println("Sunny")
}

suspend fun printTemperature() {
  delay(1000)
  println("30\u00b0C")
} 
```

- The 2 functions run concurrently because they are in separate coroutines:

![](/assets/google-android-training-courses/weather_example_corotine.png)

- `launch()`'s nature is "fire and forget"  

- Use the `async()` function from the coroutines library if you care about when the coroutine finishes and need a return value from it
  - The `async()` function returns an object of type **Deferred**
  - A promise that the result will be in there when it's ready
  - You can access the result on the Deferred object using `await()`

- Can think `async()` as producer and the `await()` as consumer

```kt
import kotlinx.coroutines.*

fun main() {
  runBlocking {
    println("Weather forecast")
    val forecast: Deferred<String> = async {
      getForecast()
    }
    val temperature: Deferred<String> = async {
      getTemperature()
    }
    println("${forecast.await()} ${temperature.await()}")
    println("Have a good day!")
  }
}

suspend fun getForecast(): String {
  delay(1000)
  return "Sunny"
}

suspend fun getTemperature(): String {
  delay(1000)
  return "30\u00b0C"
}
```

- **Parallel Decomposition**: 
  1. Taking a problem and breaking it into smaller subtasks that can be solved in parallel
  2. When the results of the subtasks are ready, you can combine them into a final result

```kt
import kotlinx.coroutines.*

fun main() {
  runBlocking {
    println("Weather forecast")
    println(getWeatherReport())
    println("Have a good day!")
  }
}

// coroutineScope{} creates a local scope
// The coroutines launched within this scope are grouped together within this scope
suspend fun getWeatherReport() = coroutineScope {
  val forecast = async { getForecast() }
  val temperature = async { getTemperature() }
  "${forecast.await()} ${temperature.await()}"
}

suspend fun getForecast(): String {
  delay(1000)
  return "Sunny"
}

suspend fun getTemperature(): String {
  delay(1000)
  return "30\u00b0C"
}
```

- With `coroutineScope()`, even though the function is internally doing work concurrently, it appears to the caller as a synchronous operation because coroutineScope won't return until all work is done
  - The key insight here for **structured concurrency** is that you can take multiple concurrent operations and put it into a single synchronous operation, where concurrency is an **implementation detail**
  - The only requirement on the calling code is to be in a suspend function or coroutine

#### Exceptions and cancellation

- There is a parent-child relationship among coroutines
  - You can launch a coroutine (known as the child) from another coroutine (parent)
  - As you launch more coroutines from those coroutines, you can build up a whole hierarchy of coroutines

- When one of the child coroutines fails with an exception, it gets propagated upwards

eg.

```kt
fun main() {
  runBlocking {
    println("Weather forecast")
    println(getWeatherReport())
    println("Have a good day!")
  }
}

suspend fun getWeatherReport() = coroutineScope {
  val forecast = async { getForecast() }
  val temperature = async {
    try {
      getTemperature()
    } catch (e: AssertionError) {
      println("Caught exception $e")
      "{ No temperature found }"
    }
  }

  "${forecast.await()} ${temperature.await()}"
}

suspend fun getForecast(): String {
  delay(1000)
  return "Sunny"
}

suspend fun getTemperature(): String {
  delay(500)
  throw AssertionError("Temperature is invalid")
  return "30\u00b0C"
}

// Running result:
//    Weather forecast
//    Caught exception java.lang.AssertionError: Temperature is invalid
//    Sunny { No temperature found }
//    Have a good day!
```

- **Cancellation**
  - Mostly **user-driven** when an event has caused the app to cancel work that it started

- eg. 

```kt
suspend fun getWeatherReport() = coroutineScope {
  val forecast = async { getForecast() }
  val temperature = async { getTemperature() }

  delay(200)
  temperature.cancel()

  "${forecast.await()}"
}
```

- A coroutine can be cancelled, but it won't affect other coroutines in the same scope and the parent coroutine will not be cancelled

#### Coroutine concepts

##### Job

- When you launch a coroutine with the `launch()` function, it returns an instance of **Job**
  - The Job holds a handle, or reference, to the coroutine, so you can manage its lifecycle

```kt
val job = launch { ... }
```

- The **Deferred object** that is returned from a coroutine started with the `async()` function is a Job as well, and it holds the future result of the coroutine

- With a job, you can check if it's **active**, **cancelled**, or **completed**:
  - Completed: Coroutine and any coroutines that it launched have completed all of their work

- When a coroutine launches another coroutine, the job that returns from the new coroutine is called the child of the original parent job
  - If a parent job gets cancelled, then its child jobs also get cancelled
  - When a child job is canceled using `job.cancel()`, it terminates, but it does not cancel its parent
  - If a job fails with an exception, it cancels its parent with that exception (known as propagating the error upwards)

![](/assets/google-android-training-courses/parent_child_relationship.png)

##### CoroutineScope

- `launch()` and `async()` are extension functions on `CoroutineScope`
  - `CoroutineScope` is declared as an interface and it contains a `CoroutineContext` as a variable
  - The `launch()` and `async()` functions create a new child coroutine within that scope and the child also inherits the context from the scope

- A `CoroutineScope` is tied to a lifecycle, which sets bounds on how long the coroutines within that scope will live (see example above)

- The `CoroutineContext` provides information about the context in which the coroutine will be running in
  - The `CoroutineContext` is essentially a map that stores elements where each element has a unique key, eg.
    - name: name of the coroutine to uniquely identify it (default be "coroutine")
    - job: controls the lifecycle of the coroutine (default no parent job)
    - dispatcher: dispatches the work to the appropriate thread (default be `Dispatchers.Default`)
    - exception handler: handles exceptions thrown by the code executed in the coroutine (default be no exception handler)

  - Each of the elements in a context can be appended together with the `+` operator. For example, one `CoroutineContext` could be defined as follows:
    - `Job() + Dispatchers.Main + exceptionHandler`

  - Can override any elements that were inherited from the parent context by passing in arguments to the `launch()` or `async()` functions for the parts of the context that you want to be different, eg.

  ```kt
  scope.launch(Dispatchers.Default) {
    ...
  }
  ```

- Thread:
  - A regular function **blocks** the calling thread until its work is completed
  - Asynchronous function to perform **non-blocking** work because it returns before its work is completed
  - We need to move any long-running work items off the **main thread** and handle it in a different thread

- Coroutines use **dispatchers** to determine the thread to use for its execution, some built-in dispatchers:
  - **Dispatchers.Main**:
    - Use this dispatcher to run a coroutine on the main Android thread
    - Primarily for handling UI updates and interactions, and performing quick work
  - **Dispatchers.IO**:
    - This dispatcher is optimized to perform disk or network I/O outside of the main thread
  - **Dispatchers.Default**:
    - Default dispatcher used when calling `launch()` and `async()`
    - Use this dispatcher to perform computationally-intensive work outside of the main thread

eg. you can switch the dispatcher by modifying the context that is used for the coroutine

```kt
import kotlinx.coroutines.*

fun main() {
  runBlocking {
    println("${Thread.currentThread().name} - runBlocking function")
    launch {
      println("${Thread.currentThread().name} - launch function")
      withContext(Dispatchers.Default) {
        println("${Thread.currentThread().name} - withContext function")
        delay(1000)
        println("10 results found.")
      }
      println("${Thread.currentThread().name} - end of launch function")
    }
    println("Loading...")
  }
}

// Print Result:
//    main @coroutine#1 - runBlocking function
//    Loading...
//    main @coroutine#2 - launch function
//    DefaultDispatcher-worker-1 @coroutine#2 - withContext function
//    10 results found.
//    main @coroutine#2 - end of launch function
```

### Coroutines in Android Studio

- eg. 

```kt
suspend fun run() {
  while (currentProgress < maxProgress) {
    delay(progressDelayMillis)
    currentProgress += progressIncrement
  }
}
```

![](/assets/google-android-training-courses/example_suspend_fun_in_android_studio.png)

- The coroutine suspends (**but doesn't block**) the execution after calling the `delay()` function with the desired interval value

- To call suspend functions safely from inside a composable, you need to use the `LaunchedEffect()` composable
  - `LaunchedEffect()` will take care the **Dispatcher** thing also

- The unit of this **hierarchy** is referred to as a coroutine scope
  - Coroutine scopes should always be associated with a lifecycle
  - You cannot call a suspend function from a function which is not marked suspend
  - suspend functions only been called by coroutine builders, such as launch. These builders are, in turn, tied to a **CoroutineScop**

- eg.

```kt
LaunchedEffect(playerOne, playerTwo) {
  // The scope inherits its coroutineContext from the LaunchedEffect() scope
  // The scope returns as soon as the given block and all its children coroutines are complete
  coroutineScope {
    launch { playerOne.run() }
    launch { playerTwo.run() }
  }
  raceInProgress = false
}
```

![](/assets/google-android-training-courses/example_coroutineScope.png)

- When write unit testing for coroutines, `runTest` coroutine builder need to be used
  - eg. `testImplementation("org.jetbrains.kotlinx:kotlinx-coroutines-test:1.6.4")`

```kt
@Test
fun raceParticipant_RaceStarted_ProgressUpdated() = runTest {
  val expectedProgress = 1
  // You can directly call the raceParticipant.run() in the runtTest builder,
  // but the default test implementation ignores the call to delay()
  launch { raceParticipant.run() }

  // The advanceTimeBy() function helps to reduce the test execution time
  advanceTimeBy(raceParticipant.progressDelayMillis)

  // Since advanceTimeBy() doesn't run the task scheduled at the given duration,
  // need to call the runCurrent() to executes any pending tasks at the current time
  runCurrent()

  assertEquals(expectedProgress, raceParticipant.currentProgress)
}
```

### Get Data from the internet

- **ViewModel** with **Retrofit**:

![](/assets/google-android-training-courses/web_service_with_retrofit.png)

- [Doc of Retrofit](https://square.github.io/retrofit/)

![](/assets/google-android-training-courses/retrofit_responsibility.png)

- A **viewModelScope** is the built-in coroutine scope defined for each ViewModel in your app
  - Any coroutine launched in this scope is automatically canceled if the ViewModel is cleared

- `kotlinx.serialization` provides sets of libraries that convert a JSON string into Kotlin object
  - There is a community developed third party library that works with Retrofit, [Kotlin Serialization Converter](https://github.com/JakeWharton/retrofit2-kotlinx-serialization-converter#kotlin-serialization-converter)

- To enable your app to make connections to the internet, add the `android.permission.INTERNET` permission in the Android manifest

- Retrofit service example:

```kt
private const val BASE_URL =
    "https://android-kotlin-fun-mars-server.appspot.com"

private val retrofit = Retrofit.Builder()
  .addConverterFactory(Json.asConverterFactory("application/json".toMediaType()))
  .baseUrl(BASE_URL)
  .build()

interface MarsApiService {
  // Retrofit appends the endpoint photos to the base URL
  @GET("photos")
  suspend fun getPhotos(): List<MarsPhoto>
}

object MarsApi {
  // lazily initialized retrofit object property
  // it is initialized at its first usage
  val retrofitService : MarsApiService by lazy {
    retrofit.create(MarsApiService::class.java)
  }
}
```

- Serialization example:

```kt
@Serializable
data class MarsPhoto(
  val id: String,

  @SerialName(value = "img_src")
  val imgSrc: String
)
```

### Repository and DI

#### Repository

- **Android recommended architecture** states that an app should have at least a **UI layer** and a **data layer**

![](/assets/google-android-training-courses/ui_layer_and_data_layer.png)

- The data layer is made up of one or more **repositories**. Repositories themselves contain zero or more **data sources**
  - Best practices require the app to have a repository for each type of data source your app uses

- General concept of **repository**:
  - Exposes data to the rest of the app
  - Centralizes changes to data
  - Resolves conflicts between multiple data sources
  - Abstracts sources of data from the rest of the app
  - Contains business logic

- Repository classes are named after the data they're responsible for
  - The repository naming convention is type of `data + Repository`

- eg.

![](/assets/google-android-training-courses/repo_with_view_model.png)

#### DI (Dependency Injection)

- When a class requires another class, the required class is called a **dependency**

- To make the code more flexible and adaptable, a class **must not** instantiate the objects it depends on
  - The objects it depends on must be instantiated **outside the class and then passed in**

- Passing in the required objects is called **dependency injection (DI)**. It is also known as [inversion of control](https://en.wikipedia.org/wiki/Inversion_of_control)
  - DI is when a dependency is provided at runtime instead of being hardcoded into the calling class

- DI
  - Helps with the reusability of code
  - Makes refactoring easier
  - Helps with testing (eg. testing the network calling code)

- A [container](https://developer.android.com/training/dependency-injection/manual#dependencies-container) is an object that contains the dependencies that the app requires
  - These dependencies are used across the whole application, so they need to be in a common place that all activities can use

- Android framework does not allow a **ViewModel** to be passed values in the constructor when created, we implement a `ViewModelProvider.Factory` object, which lets us get around this limitation

```kt
companion object {
  val Factory: ViewModelProvider.Factory = viewModelFactory {
    initializer {
      val application = (this[APPLICATION_KEY] as MarsPhotosApplication)
      val marsPhotosRepository = application.container.marsPhotosRepository
      MarsViewModel(marsPhotosRepository = marsPhotosRepository)
    }
  }
}
```

##### Testing Example

- By passing fake classes for dependencies, you can control exactly what the dependency returns, eg.

```kt
class NetworkMarsRepositoryTest {
  // The coroutine test library provides the runTest() function
  // The function takes the method that you passed in the lambda and runs it from TestScope, which inherits from CoroutineScope
  @Test
  fun networkMarsPhotosRepository_getMarsPhotos_verifyPhotoList() = runTest{ 
    val repository = NetworkMarsPhotosRepository(
      marsApiService = FakeMarsApiService()
    )
    assertEquals(FakeDataSource.photosList, repository.getMarsPhotos())
  }
}
```

- This approach ensures that the code you are testing doesn't depend on untested code or APIs that could change or have unforeseen problems

- eg. for main dispatcher unavailable in testing environment

```kt
class MarsViewModelTest {
  // Main dispatcher is only available in a UI context
  // If code under a local unit test references the Main dispatcher, an exception (like the one above) is thrown when the unit tests are run
  @get:Rule
  val testDispatcher = TestDispatcherRule()

  @Test
  fun marsViewModel_getMarsPhotos_verifyMarsUiStateSuccess() = runTest {
    val marsViewModel = MarsViewModel(
      marsPhotosRepository = FakeNetworkMarsPhotosRepository()
    )
    assertEquals(
      MarsUiState.Success(
        "Success: ${FakeDataSource.photosList.size} Mars " + "photos retrieved"
      ),
      marsViewModel.marsUiState
    )
  }
}

class TestDispatcherRule(
  // The UnconfinedTestDispatcher specifies that tasks must not be executed in any particular order
  val testDispatcher: TestDispatcher = UnconfinedTestDispatcher(),
): TestWatcher() { // The TestWatcher class enables you to take actions on different execution phases of a test
  override fun starting(description: Description?) {
    Dispatchers.setMain(testDispatcher)
  }

  override fun finished(description: Description?) {
    Dispatchers.resetMain()
  }
}
```

### Display images from the internet

- The image has to be **downloaded**, **internally stored(cached)**, and **decoded** from its compressed format to an image that Android can use
  - Can rely on [Coil](https://coil-kt.github.io/coil/)

- **Coil** basically needs two things:
  1. The **URL** of the image you want to load and display
  2. An **AsyncImage** composable to actually display that image

- eg.

```kt
@Composable
fun MarsPhotoCard(photos: MarsPhoto, modifier: Modifier = Modifier) {
  Card(
    modifier = modifier,
    elevation = CardDefaults.cardElevation(defaultElevation = 8.dp)
  ) {
    AsyncImage(
      model = ImageRequest.Builder(context = LocalContext.current)
        .data(photos.imgSrc)
        .crossfade(true)
        .build(),
      contentDescription = stringResource(R.string.mars_photo),
      contentScale = ContentScale.Crop,
      error = painterResource(R.drawable.ic_broken_image),
      placeholder = painterResource(R.drawable.loading_img),
      modifier = Modifier.fillMaxWidth()
    )
  }
}

@Composable
fun PhotosGridScreen(
  photos: List<MarsPhoto>,
  modifier: Modifier = Modifier,
  contentPadding: PaddingValues = PaddingValues(0.dp),
) {
  LazyVerticalGrid(
    columns = GridCells.Adaptive(150.dp),
    modifier = modifier.padding(horizontal = 4.dp),
    contentPadding = contentPadding,
  ) {
    items(items = photos, key = { photo -> photo.id }) { photo ->
      MarsPhotoCard(
        photo,
        modifier = modifier
          .padding(4.dp)
          .fillMaxWidth()
          .aspectRatio(1.5f)
      )
    }
  }
}
```

### Persist Data with Room

- [Room](https://developer.android.com/training/data-storage/room) is a persistence library that's part of Android [Jetpack](https://developer.android.com/jetpack/androidx/explorer?case=data)

- Room is an abstraction layer on top of a [SQLite](https://developer.android.com/training/data-storage/sqlite) database
  - Room simplifies the chores of database setup, configuration, and interactions with the app
  - Room also provides compile-time checks of SQLite statements

- Room is the data source in the image below:

![](/assets/google-android-training-courses/ui_layer_and_data_layer.png)

- Room components:
  - [Room entities](https://developer.android.com/training/data-storage/room/defining-data)
    - Represent tables in your app's database
    - Use to update the data stored in rows in tables and to create new rows for insertion
  - [Room DAOs](https://developer.android.com/training/data-storage/room/accessing-data)
    - Provide methods that your app uses to retrieve, update, insert, and delete data in the database
  - [Room Database](https://developer.android.com/reference/kotlin/androidx/room/Database)
    - Provides your app with instances of the DAOs associated with that database

![](/assets/google-android-training-courses/room_components.png)

#### Item Entity

- An **Entity class** defines a table, and each instance of this class represents a **row** in the database table

- The `@Entity` annotation marks a class as a database Entity class
  - For each Entity class, the app creates a database table to hold the items
  - Each field of the Entity is represented as a column in the database, unless denoted otherwise

- eg.

```kt
/**
 * Entity data class represents a single row in the database.
 */
@Entity(tableName = "items")
data class Item(
  @PrimaryKey(autoGenerate = true)
  val id: Int = 0,
  val name: String,
  val price: Double,
  val quantity: Int
)
```

#### Item DAO

- The **Data Access Object (DAO)** is a pattern you can use to separate the **persistence layer** from the rest of the **application** by providing an abstract interface

- The Room library provides convenience annotations, such as `@Insert`, `@Delete`, and `@Update`, for defining methods that perform simple inserts, deletes, and updates without requiring you to write a SQL statement
  - If you need to define more complex operations for insert, delete, update, or if you need to query the data in the database, use a `@Query` annotation instead

- eg.

```kt
@Dao
interface ItemDao {
  // The database operations can take a long time to execute, so they need to run on a separate thread
  // The argument onConflict tells the Room what to do in case of a conflict
  @Insert(onConflict = OnConflictStrategy.IGNORE)
  suspend fun insert(item: Item)

  @Update
  suspend fun update(item: Item)

  @Delete
  suspend fun delete(item: Item)

  // :id uses the colon notation in the query to reference arguments in the function
  // With Flow as the return type, you receive notification whenever the data in the database changes
  // The Room keeps this Flow updated for you, which means you only need to explicitly get the data once
  // Because of the Flow return type, Room also runs the query on the background thread
  // You don't need to explicitly make it a suspend function and call it inside a coroutine scope.
  @Query("SELECT * FROM items WHERE id = :id")
  fun getItem(id: Int): Flow<Item>

  @Query("SELECT * FROM items ORDER BY name ASC")
  fun getAllItems(): Flow<Item>
}
```

#### Database Instance

- The **Database class** provides your app with **instances of the DAOs** you define

- eg.

```kt
// Whenever you change the schema of the database table, you have to increase the version number
// Set exportSchema to false so as not to keep schema version history backups
@Database(entities = [Item::class], version = 1, exportSchema = false)
abstract class InventoryDatabase : RoomDatabase() {
  abstract fun itemDao() : ItemDao

  // Allows access to the methods to create or get the database and uses the class name as the qualifier
  companion object {
    // The value of a volatile variable is never cached, and all reads and writes are to and from the main memory
    // These features help ensure the value of Instance is always up to date and is the same for all execution threads
    // Changes made by one thread to Instance are immediately visible to all other threads
    @Volatile
    private var Instance: InventoryDatabase? = null

    fun getDatabase(context: Context): InventoryDatabase {
      // makes sure the database only gets initialized once
      return Instance ?: synchronized(this) {
        Room.databaseBuilder(context, InventoryDatabase::class.java, "item_database")  
          .fallbackToDestructiveMigration()
          .build()
          .also { Instance = it }
      }
    }
  }
}
```

### Read and update data with Room

- Getting data from a flow is called collecting from a flow:
  - **Lifecycle change** causes recomposition and collecting from your Flow all over again
  - You want the values to be cached as state so that existing data isn't lost between lifecycle events
  - Flows should be canceled if there's no observers left, such as after a composable's lifecycle ends
  - The recommended way to expose a Flow from a `ViewModel` is with a `StateFlow`
    - Using a StateFlow allows the data to be saved and observed, regardless of the UI lifecycle
    - To convert a Flow to a StateFlow, you use the `stateIn` operator

- eg.

```kt
/**
 * ViewModel to retrieve all items in the Room database.
 */
class HomeViewModel(itemsRepository: ItemsRepository) : ViewModel() {
  val homeUiState: StateFlow<HomeUiState> =
    itemsRepository.getAllItemsStream().map { HomeUiState(it) }
      .stateIn(
        scope = viewModelScope,
        started = SharingStarted.WhileSubscribed(TIMEOUT_MILLIS),
        initialValue = HomeUiState(),
      )

  companion object {
    private const val TIMEOUT_MILLIS = 5_000L
  }
}
```

### Preferences DataStore

- The DataStore Jetpack Component is a great way to store small and simple data sets with low overhead

- Preferences DataStore stores **key-value pairs**

- eg.

```kt
class UserPreferencesRepository(
  private val dataStore: DataStore<Preferences>
) {
  private companion object {
    val IS_LINEAR_LAYOUT = booleanPreferencesKey("is_linear_layout")
    const val TAG = "UserPreferencesRepo"
  }

  var isLinearLayout: Flow<Boolean> = dataStore.data
    .catch {
      if (it is IOException) {
        Log.e(TAG, "Error reading preferences.", it)
        emit(emptyPreferences())
      } else {
        throw it
      }
    }.map { preferences ->
      preferences[IS_LINEAR_LAYOUT] ?: true
    }

  suspend fun saveLayoutPreference(isLinearLayout: Boolean) {
    dataStore.edit { preferences ->
      preferences[IS_LINEAR_LAYOUT] = isLinearLayout
    }
  }
}
```

### WorkManager


