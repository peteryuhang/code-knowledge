
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
