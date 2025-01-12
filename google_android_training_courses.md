
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
