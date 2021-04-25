# Writing for Simplicity
Simplicity means few in both expression and approach as well as in steps of a process or a relationship.  Simplification is the action of making fewer.  Neither of these terms suggest any degree of effort perhaps described as *easy*.

## Definitions
* **simple** - *adjective*,
   - Less in size, scope, or scale
   - Few in quantity
* **simplify** - *verb*,
   - To make less, as in more comprehensible or more familiar
   - To make fewer, as in to reduce a quantity
* **complex** - *adjective*,
   - More in size, scope, or scale
   - Many in quantity
* **complicate** - *verb*,
   - To make more, as in larger than comprehensible or more distant from familiarity
   - To make more, as in to increase a quantity
* **easy** - *adjective, adverb*,
   - Of ease, which is of less immediate effort and closer to immediate gratification
* **hard** - *adjective, adverb*,
   - Of challenge, which requires increased effort or investment with less confidence of a desired goal or end state
* **bias** - *noun*,
   - A direction of thought not qualified with evidence
   - A preference
* **objectivity** - *noun*,
   - A state of equally weighted distribution, balance
   - A desire for evidence over a choice or preference
* **insecurity** - *noun*,
   - Self-doubt
   - A priority of fear of failure or reprisal over a risk/reward analysis
* **confidence** - *noun*,
   - A trust or belief in the self or a path of thought
   - A desire to pursue a course, risk/reward, compared to potential for failure or reprisal

## Simple Is Not Easy
1. Simple requires greater effort, which is the opposite of easy.
2. Simple, as in fewer, is often not immediately clear.

Many people claim to desire or quest for simplicity when really they more precisely desire a lower investment of time, understanding, or effort.  The greatest misconception is to equate the terms *simple* and *easy* but those terms are completely unaligned.  Something simple may be easy, but without frequent practice or some other prior investment this is rarely true.  Perhaps the greatest reason for such a misalignment between these terms are potential unknowns that arise only during execution.  As those potential unknowns increase in quantity complexity increases moving a product, action, or service away from both simplicity and easiness.

Another great misconception or barrier to simplicity is bias and insecurity.  Bias occurs when a person a values a choice of options more than the result of a given choice in a given situation.  Bias is problematic in that it is a state of preference without regard for evidence or possibly hostile to evidence.  Insecurity is a behavior that limits or prematurely disqualifies a person's available list of solution choices to a given problem.  Insecurity is not the same as a bias but is problematic for the same reasons with the same unintended consequences.

There are a couple of factors necessary to achieve simplicity.  First, the total effort required is irrelevant and must be discarded from consideration as a bias.  Second, simplicity is often not immediately achieved even after extensive planning.  The increased effort required to simplify an effort is referred to as *refactoring*.  Third, simplicity cannot be achieved without a total consideration of a unit and its various parts, which means accounting for dependencies, processes, assets, and so forth.  It also means accounting for the various layers comprising a solution, see [Law of Leaky Abstractions](https://en.wikipedia.org/wiki/Leaky_abstraction).  Each additional layer, or abstraction, likely increases easiness but they also increase complexity, as in quantity.

The final consideration of simplicity and complexity are the quantity of steps in a given process or relationship.  A simple approach has fewer steps than a more complex result, but bear in mind the Law of Leaky Abstractions in that a given process or relationship may comprise more steps than they might appear pending a deeper investigation.

## Less Is More
The advantage of simplicity is fewer parts to maintain and test.  This reduces risk, increases speed of maintenance, reduces potential defects, potentially increases product performance, and increases product quality.

## Refactoring
The best way to reduce complexity in any code base is to eliminate redundance where possible.  This often requires refactoring and may require architectural rework if two similar functions/libraries have different input and/or output.  In these cases it is helpful to have comprehensive coverage from test automation.  TypeScript interfaces are also incredibly helpful.

## Simplicity in JavaScript Code Conventions
This section of the document will provide unpopular suggests that lower complexity according to the definition of that term.

### addEventListener
Don't use this.  *addEventListener* allows assignment of multiple event handlers to a given element's event via a listener.  A more simple approach is to assign event handlers directly to an element's event property.  This eliminates the need for a listener and imposes simplicity without option because only one handler can be directly assigned to a given property at any time.  Example: `myButton.onclick = aClickFunction`.

### this
The *this* keyword is the pronoun of JavaScript, which means its value is not immediately clear by reading the code.  That means flow control must be evaluate to determine the value of *this*.  Worse is that the scope of *this* is different depending upon the context of its scope.  In an arrow function *this* is lexically scoped, while in other contexts *this* points to the caller of the function on which *this* is contained.  More simple than *this* are explicitly named variables that are assigned to exactly what you need because these can be directly evaluated by a person by reading the code.

### bind, call, and apply
These are convenience methods introduced with ES5.  Their purpose is to manually alter the scope of a function's *this* value and optionally inject side effects into a function.  If you are willing to completely abandon use of *this* then you don't need these either.

### Named Functions
A function name is an identifier immediately following the *function* keyword.  In JavaScript functions do not require names so long as they have some form of identity, such as [IIFE](https://developer.mozilla.org/en-US/docs/Glossary/IIFE) or assignment to a variable.  Without a function name a function is anonymous.  Naming functions with unique names offers several advantages from performance profilers, error stack traces, and identity.  Anonymous functions have no internal identity.  Named functions, on the other hand, have identity.  This means the logic within a function is aware of their containing function(s) by function name without resolving assignment through the scope chain outside the given function.  [Arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) take great pains to eliminate many of the complex conventions allowed by other functions, but they are also always anonymous.

### querySelector
At first blush the [querySelector](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelector) convention appears to reduce complexity because they provide a single method using a single convention to access many different things from the DOM.  Unfortunately, this is a case of confusing easiness for simplicity because there are precision limitations on what query selectors can access and they only allow reading from the DOM.  A query selector works by parsing a string that resembles a CSS selector into a series of instructions for accessing elements in page similar to other standard DOM methods.  Since query selectors can only read from the page any changes to the DOM require use of those other DOM methods anyways.  The simplicity of query selectors is lost when attempting to access particular areas of a page with great precision as this requires are more complex selector string instruction as input.  Even more limiting is that query selectors only allow access to page elements, where other DOM methods can allow access to any aspect of a page such as element attributes, text, comments, and various other things.  Due to those limitations a more simple approach is to use single convention without such limitations that would require use of additional conventions.

### Classes
Classes exist to provide [polyinstantiation](https://en.wikipedia.org/wiki/Polyinstantiation).  That word means many instances and many is a synonym for complex.  The idea is to create a data model of containing logic and properties than can be cloned and extended with additional logic and properties as necessary.  This is the basis of inheritance in object oriented programming.  Every time a function is called it executes a new instance of its internal logic without need to clone and without an option to extend its logic dynamically.  If simplicity is a goal then just use functions instead of classes.

### Promises and Async Functions
Promises and async functions exist as conventions for handling asynchronous logic.  Opinions on the simplicity of these conventions are arguable either way.  On one hand they do reduce complexity by separating asynchronous logic from other synchronous logic which lowers the relationship between a delayed event and the logic handling the result.  On the other hand they are separating asynchronous logic from the flow control in which that logic is actually invoked.  You don't always need to dive through the flow control to resolve every defect, but when you do these conventions apply additional steps in evaluation of the code through this separation.  These conventions also require minor additional syntax that regular callback functions do not.

### Implicit Comparisons
Implicit comparisons in JavaScript are generally a form of convenience but are generally a bad idea in such a loosely typed language.  An example of an implicit comparison is: `if (myBook) {`.  That code sample evaluates whether myBook resolves to a truthy value as opposed to an explicit comparison: `if (myBook === true)` or `if (myBook !== null)` or `if (myBook.length < 0)`.  The immediate benefit of an explicit comparison is a loss of confusion.  You know exactly what the expression says without having to guess.  An implicit comparison is risky and a poor practice for the same reason operators `!=` and `==` are considered a poor practice.

