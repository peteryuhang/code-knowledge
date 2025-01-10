
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

