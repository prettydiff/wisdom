# Object Oriented Programming
Written 18 August 2023.
It seems many people advocate for object oriented programming (OOP) without knowing what it is or what problem it solves.

## History
The language [Simula 67](https://en.wikipedia.org/wiki/Simula) invented the concept of OOP in 1967 by a team of Norwegian engineers.
The prior popular programming language paradigm, called [procedural](https://en.wikipedia.org/wiki/Procedural_programming), expressed instructions into groups called procedures.
Each procedure wrote to memory and access to that memory, called *procedure calls*, created the means of an expression that allowed instruction reuse per instruction group.
OOP achieved a more thorough memory reuse scheme through a group of instructions, called an *object*.
A developer then clones and extends the objects as necessary through a process called [poly-instantiation](https://en.wikipedia.org/wiki/Polyinstantiation).
Poly-instantiation allows for the cloning of objects, called *inheritance*, but the cloned object shares the same memory as the object from which it was cloned.

The only purpose of these programming paradigms, procedural and OOP, involved code reuse versus memory conservation.
Looking at the [history cost of memory](https://jcmit.net/memoryprice.htm) 1mb costed about $734,000 in 1967 when Simula 67 was invented.

In 1979 a Danish academic started work on C language with classes.
This did not work and so he started over with a new language named [C++](https://en.wikipedia.org/wiki/C%2B%2B) that he published with his PhD dissertation in 1982.
The language became quite popular and for a while became the de facto programming language of both university computer science departments and corporate software production, thus becoming the normative language of institutional education.
Even by 1985 the cost of memory was still around $880 per 1mb.

In 1995 Sun Microsystems released the [Java](https://en.wikipedia.org/wiki/Java_(programming_language)).
Java inherited many ideas and idiomatic patterns/styles directly from C++.
One critical distinction between Java and C++ centers around memory management.
C++ uses memory pointers to set and free memory in a manner very similar to C language, but Java uses a more automated process called [garbage collection](https://en.wikipedia.org/wiki/Garbage_collection_(computer_science)).
With garbage collection memory allocation occurs automatically as an application executes by lower level code interpreting the given language and that lower level interpreter frees the memory when the application no longer needs it.
Later in 1995 another garbage collected language named JavaScript was invented.
The cost of memory was down to about $31 per 1mb.

Java was invented to solve several business problems.
One of these problems included the concept of *write once, run anywhere*, which describes a single programming language environment that executes in an identical way on different operating systems.
Another business solution included making use of the conventions popularized, and later institutionalized, by C++ but with less learning effort and greater access to extensions by only package distribution channels.

## Language Style
The design decision to leverage the popularity of C++ also illustrates the single greatest difference between Java and JavaScript.
JavaScript is inherently an OOP language due to its internal definitions and type system, however the JavaScript language takes no position on stylistic conventions of developers.
This allows JavaScript to achieve a designation named multi-paradigm, while Java maintains a single paradigm style imposing a single expressive C++ like system for writing application code.
At 1995 the cost of memory became cheap enough that developers were free to deviate from restrictive memory conserving conventions.
At ths time of this writing memory costs about $0.0015 per 1mb, or $15 per 1gb.

OOP was created to allow a greater degree of programming expressiveness with a greater conservation of memory.
In modern programming the technical purpose of OOP, memory conservation, is absent from most high level languages and replaced by automated conventions.
The high costs of memory have also largely disappeared.
If [UltraRAM](https://ultraram.tech/) becomes a commercial reality computers will cease to have memory all together.

## Institutions
The reason OOP remains popular is due solely to institutional social conventions since both the economic and technical motives are long gone.
OOP is still the primary means of computer science education in universities.
Most of the modern legacy applications in commercial use within the last 40 years make use of OOP.

The popularity of OOP largely exists as a result of a broken feedback loop.
Universities continue to teach OOP because industry says that is what they want.
Industry claims to want OOP because that is what the universities teach and training developers cost too much.

Many people will argument that universities primary teach OOP because industry has OOP applications that need to be maintained, but this is faulty logic.
My university continues to teach [COBOL](https://en.wikipedia.org/wiki/COBOL), a language from 1955, because there are still companies that execute COBOL applications.
That is true, but COBOL is not the primary language of education of any computer science program even where it is taught.
The primary reason universities principally teach OOP is because that is the language concept most familiar to the university educators, a deeply institutional environment.

In industry then there are only two reasons to continue the practice of writing OOP style code.
First, is to maintain legacy applications.
Second, is for social compatibility with older developers who only know OOP programming paradigm.

## Future
It appears the future of programming is [functional programming](https://en.wikipedia.org/wiki/Functional_programming) paradigm.
In difference to the Wikipedia link I provide for function programming it is frequently a [declarative](https://en.wikipedia.org/wiki/Declarative_programming) programming style, but not inherently so and may be exceedingly [imperative](https://en.wikipedia.org/wiki/Imperative_programming).
For example a program that makes explicit use of event handling in the control flow of its logic and is designed solely around that consideration would be extremely imperative.

Functions are yet another form of instruction grouping like procedures and objects.
Functions and procedures differ from objects in that they both execute instructions directly opposed to just grouping instructions into a common point of reference.
Unlike procedures functions may optionally receive input and always return output.
SQL databases illustrate the distinction between functions and procedures because they allow both the concept of stored procedures and functions as separate types of artifacts.

Some people consider functional programming superior to OOP because the concept of poly-instantiation is wildly complex where object inheritance results in many vaguely similar artifacts.
Functional programming also achieves a substantial reduction in code volume because the code execution point and grouping mechanism are one in the same.
Also there is less need for syntactical decoration in functional programming where variable value assignment is explicit opposed to the implicit nature of a pronoun-like reference referring to artifacts in the call stack or scope chain for value assignment.

## JavaScript and OOP
In JavaScript don't use OOP conventions.
OOP conventions greatly increase the code size, which increases the time to maintenance (also known as tech debt), and also increases the complexity of both organizational conventions and syntax.
More explicitly never use the keyword *this* or anything that either makes use of or is reliant upon keyword *this*.

In JavaScript OOP is far more challenging than in other languages.
This is because in modern times OOP only exists due to social institutions which teach a C++ class based inheritance scheme.
In JavaScript the inheritance mechanism is quite different using a concept called [prototypes](https://en.wikipedia.org/wiki/Prototype-based_programming), which come from the language [Scheme](https://en.wikipedia.org/wiki/Scheme_(programming_language)).
Also the keyword *this* is procedural, which refers to the prior step in the call stack.
In functions that means *this* refers to that which called the function, which could be a different function.
In methods *this* refers to that object on which the method is attached.
In arrow functions, however, *this* is lexical, as opposed to procedural, so it always refers to the function containing the given arrow function.

Many developers commonly get OOP wrong in JavaScript, and since the language is multi-paradigm just do use OOP, particularly *this*.
Since the value of *this* is never clear or predictable by developers just reading the code many developers will apply unnecessary syntax decoration further increasing code system and reducing both clarity and predictability.
So, just don't do OOP in the language.
I promise, you will thank me later.

The only two times use of OOP cannot be avoided in JavaScript is to extend OOP code you didn't write or to extend internal features of the language directly.