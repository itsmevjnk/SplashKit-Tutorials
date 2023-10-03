In programming, especially when it comes to tasks encountered in the SIT102 unit, it is not uncommon that some data needs to be converted into a string. In fact, the most common case that you might find is when you want to print a number to the terminal - you might try something like this:

```c++
int x = 42;
write_line("x = " + x);
```

You will eventually realise that either this does not compile, or it does not behave as expected. This is because `"x = "` is a string, and `x` is an integer, and they cannot be added quite easily. Remember, an `int` cannot be casted into a `string` by itself, and vice versa - and this applies to many other data types too. We need a solution.

# `std::to_string`
This neat function is the solution for the above problem; as the name suggests, it converts a number into a string. This can be seen by [the list of its overloads](https://en.cppreference.com/w/cpp/string/basic_string/to_string):

```c++
std::string to_string(int value);
std::string to_string(long value);
std::string to_string(long long value);
std::string to_string(unsigned value);
std::string to_string(unsigned long value);
std::string to_string(unsigned long long value);
std::string to_string(float value);
std::string to_string(double value);
std::string to_string(long double value);
```

*(Oh, and a bit of a protip: that `std::` part can be a bit of a clutter in the long run, so if you use the function frequently, you might want to add `using namespace std;` to the start of your code after the `#include` lines so you do not have to type that out.)*

So, instead of the obviously-not-working solution above, you can print the value of `x` using this:
```c++
int x = 42;
write_line("x = " + to_string(x));
```

That should result in the following output:

```
x = 42
```

Similarly, if `x` is a `float` or a `double`, we can do the same thing:

```c++
double x = 42.5;
write_line("x = " + to_string(x));
```

which results in:

```
x = 42.500000
```

Note the trailing zeroes - `to_string` uses a fixed precision of six decimal digits.

# What about other data types?
To be honest, it depends on the data type you are talking about. While there are no functions available to print your custom `enum`s or `struct`s (but you can implement them yourself - see the next section), if you want to print a SplashKit data type (such as a 2D point or a rectangle struct), the library has got you covered with its own set of `to_string` functions. For your reference, here are some of the more common ones that you may use in the SIT102 unit:

##  [`color_to_string`](https://splashkit.io/api/color/#color-to-string)
This function converts a colour struct (which is used in graphics procedures - remember [this](https://splashkit.io/articles/guides/tags/starter/get-started-drawing/)?) into a representative string.

```c++
string color_to_string(color c);
```

For example:

```c++
color tan = color_tan();
write_line(color_to_string(tan));
```

This results in the colour's hex string:

```
#d2b48cff
```

But *wait* - said some of you with a background in design - *what's up with the `ff` at the end?* Colours in SplashKit are stored with their alpha values - that is, their opacity. The `ff` that you can see above is the colour's opacity level, which is maximum as a default for all of SplashKit's defined colours (except `color_transparent`).

## [`point_to_string`](https://splashkit.io/api/geometry/#point-to-string)
This function converts a 2D point struct (which is used for [line drawing](https://splashkit.io/api/graphics/#draw-line-point-to-point), and most notably, sprite location; see [`center_point`](https://splashkit.io/api/sprites/#center-point) and the Game Project tasks for more detail).

```c++
string point_to_string(const point_2d &pt);
```

For example (from the 5.3C Game Project Part 1 task):

```c++
draw_text("LOCATION: " + point_to_string(center_point(player.player_sprite)), COLOR_YELLOW, 0, 20, option_to_screen());
```

This results in the following text on the window:

```
LOCATION: Pt @241.043090:329.227824
```

where the first number (`241.043090`) is the sprite's X coordinate, and the second number (`329.227824`) is the sprite's Y coordinate.

## Other functions
Along with the two functions introduced above, SplashKit also provides string description functions for other data types:
 - [`string line_to_string(const line &ln);`](https://splashkit.io/api/geometry/#line-to-string)
 - [`string rectangle_to_string(const rectangle &rect);`](https://splashkit.io/api/geometry/#rectangle-to-string)
 - [`string triangle_to_string(const triangle &tri);`](https://splashkit.io/api/geometry/#triangle-to-string)
 - [`string json_to_string(json j);`](https://splashkit.io/api/json/#json-to-string)
 - [`string http_response_to_string(http_response response);`](https://splashkit.io/api/networking/#http-response-to-string)
 - [`string matrix_to_string(const matrix_2d &matrix);`](https://splashkit.io/api/physics/#matrix-to-string)
 - [`string vector_to_string(const vector_2d &v);`](https://splashkit.io/api/physics/#vector-to-string) (not to be confused with the C++ `std::vector` type)

# Create your own `to_string`
It is actually surprisingly easy to write a function that creates a string description of your custom struct or enum.

## `to_string` for enums
Simply test the supplied enum for each possible value and return a corresponding string. For example:

```c++
enum weekday {
	MONDAY,
	TUESDAY,
	WEDNESDAY,
	THURSDAY,
	FRIDAY,
	SATURDAY,
	SUNDAY
};

string weekday_to_string(weekday d) {
	switch(d) {
		case MONDAY: return "Monday";
		case TUESDAY: return "Tuesday";
		case WEDNESDAY: return "Wednesday";
		case THURSDAY: return "Thursday";
		case FRIDAY: return "Friday";
		case SATURDAY: return "Saturday";
		case SUNDAY: return "Sunday";
	}
}
```

## `to_string` for structs
To create a string description for a struct, you can simply convert each member of the struct into strings, then concatenate them to create one descriptive string. For example:

```c++
struct knight {
	string name;
	int age;
};

string knight_to_string(const knight &k) {
	return k.name + ", aged " + to_string(k.age);
}
```

Note how we specify the input parameter with `const` and `&`. This function must not modify the input struct, so we enforce this with the `const` specifier, and the `&` symbol specifies that the struct is passed by reference, which is more efficient than passing by value.