
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
