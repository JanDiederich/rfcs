- Feature Name: named-non-optional-fixed-order-arguments
- Start Date: 2020-07-01
- RFC PR: https://github.com/rust-lang/rfcs/pull/TBD
- Rust Issue: https://github.com/rust-lang/rust/issues/TBD

# Summary
[summary]: #summary

Introduce named, non-optional, fixed order arguments as the new choice for function signatures.

# Motivation
[motivation]: #motivation

### In a nutshell

In practice code is more read than written [1][2].
A specific example: The designers at Apple looked at many languages as inspiration for their new language: Swift. Rust was one of their influences when designing the language, which is clearly visible for a Swift programmer. While they got rid of many traits of the previous standard Apple programming language (namely Objective-C), they still kept the named arguments, they must have proven themselfs. Opposite to C++ with C there is no compatibility with any other language, named arguments or not, so that couldn't be the deciding factor. The Swift language designers fine tuned after the release of Swift 1.0 still the named arguments till they reached a state they considered good.
The suggestion is to copy the core of that design "back" to Rust to massively improve the readability of the code.

### Most often it is hard to guess the meaning of arguments

If you stumble upon and read a method like `create_point_2d(1.2, 3.4)` the meaning of the arguments is obvious: X and Y. But that's a rare optimium, more often it's something like `calculate_car_arrival_date(1.45, false, true, false)`, which is without searching and reading the documentation not self-documenting at all. While the function name tells a complete enough story about the intention of the function, it's hard to guess what each argument means.

### Big projects lack often enough a good documentation

Big projects lack often a good documentation. There a many idealistic companies out there where a good documentation is manadatory. But the reality is that there are also many projects/companies without any real documentation. Even many popular open source projects don't have them.

### In languages with traditionally long class and method names they often document the code well

Java is a language which has traditionally long, speaking class and method names. So, big open source Java projects have often enough no good detailed documentation, but often enough the class and method names have such well thought through self-documenting names that they alone are a big help. What would still improve it alot, if not only the class and method names would be speaking, but also the argument names.

### Rust follows already that ideology

Rust already follows the path of Java with clear speaking names. Example: `String::from_utf8_unchecked`, which would be named in classic `C` `Str::frm_u8_un`, making the name as short as possible, to safe as much typing work as possible. Rust designers decided against this naming pattern, they even decided against the shorter `CamelCase` and went for `under_score` which means extra characters, longer names and more typing but better readability.

### Adapting the Swift pattern would work flawlessly with already existing code

Swift has the same idea for argument naming as Rust with its variables and constants. Of course lazy/novice/careless programmers can define everything as `let mut`, but it's extra typing work, the syntax tells them that this shouldn't be the norm, and so they are enticed to create constants with writing "let" alone. Swifts argument names _can_ be mandatory like
`func move(from: Point, to: Point)`, but they can also be ommitted like `create_point_2d(_ x: Double, _ y: Double)` where the underscore means that the argument name is never passed.

Since Rust is still not ABI stable [3] all existing libraries must be recompiled anyway if they should be used with newly written code and compilers. So a `rustfix` run could add to all source code files a `#![deprecated_anonymous_arguments]` attribute declaration. So any function appears to the caller as `example_function(_ ex: i64)`.

### Keeping the possibility to still have anonymous arguments

Even Swift recommends to use anonymous arguments where arguments can’t be usefully distinguished. A perfect example for that would be the classic `min(a, b)` function.

# Explanation
[guide-level-explanation]: #guide-level-explanation

`pub fn with_capacity(capacity: usize) -> String` becomes `pub fn with_capacity(_ capacity: usize) -> String`. Called `with_capacity(23)`.

New functions would then ideally don't have that underscore, like `fn read_av1_image(fileName: String, targetWidth: i64, targetHeight: i64)`. Called `read_av1_image(fileName: "Hello World", targetWidth: 32, targetHeight: 32)`

### Migration

Add to rustfix the function to add to all code-files in a directory and their sub-directories the attribute `#![deprecated_anonymous_arguments]`.

# Drawbacks
[drawbacks]: #drawbacks

- Rustfix must be extended
- It's a syntax change affecting all function/method calling.

# Rationale and Alternatives
[alternatives]: #alternatives

- Using anonymous structs. Their big drawback is that they are syntax sugar and not an incentive to writer better, more readable code.

# Unresolved questions
[unresolved]: #unresolved-questions

- Should this be merged with the optional argument feature, which Swift already has?


[1] https://devblogs.microsoft.com/oldnewthing/20070406-00/?p=27343
[2] https://www.goodreads.com/quotes/835238-indeed-the-ratio-of-time-spent-reading-versus-writing-is
[3] https://internals.rust-lang.org/t/a-stable-modular-abi-for-rust/12347/83