<!-- cspell: words automaticity, Gottman -->

# JavaScript Made Simple
Anything that can be written in JavaScript can be written with only 4 ingredients: data structures, primitives, functions, and APIs.
The problem with writing code then is not the language or an instance of code so much as the assumptions a developer places upon that code.

## Unnecessary Complexity
Many developers, often due to poor education and insufficient experience, attempt to inject conventions and syntax into their code they don't need.
For example developers coming from a Java or C++ background and without formal training in JavaScript will attempt to write Java or C++ conventions into JavaScript, because they lack the necessary guidance to know otherwise.
This results in dramatically increased code size, increased tech debt, poorer performance, and greater reliance upon external layers of abstraction that limit freedom of expression.

There are two key actions that eliminates more than 90% of unnecessary complexity in JavaScript:

1) Eliminate use of keyword `this` and all conventions that either make use of it or are reliant upon it.
2) Practice systems of organization.

### this
The keyword `this` introduces tremendous complexity and often does not do what most developers believe it actually does.
The only scope mechanism in JavaScript is lexical scope.
In the case of arrow functions `this` is lexically scoped, which means it always resolves to the containing function.
In all other cases `this` refers to the prior step in the call stack, which for methods is the object on which the method function is attached, or in the case of other functions refers to that which called the function.

A great many exceptionally poor decisions in the design of applications results directly from confusion reasoning about the conventions in place.
There exists only confusion when attempting to determine the value of `this`, because in any instance of code the value of `this` is never explicit.
It will always require research or guessing.

So, don't use `this`.
The language does not force that convention upon you, so do not voluntarily punish yourself with it.
Never is there a good reason to do so.
Despite all evidence and logic many developers will continue to reinforce poor practices due to the behaviors of [conservatism](https://en.wikipedia.org/wiki/Conservatism_(belief_revision)) and [anchoring bias](https://en.wikipedia.org/wiki/Anchoring_(cognitive_bias)).
If you suffer from conservatism or anchoring bias please read this [explanation of OOP](./Object_Oriented_Programming.md).

### Organization
I have been writing JavaScript full time for more than 15 years as I write this.
During that time I have found many developers apply systems of organization in even the most simple and immediate instances.
That inability results in reliance upon external things to provide that organization on their behalf.
These external things then become a crutch and that crutch becomes the limits of their capabilities, which means when a problem occurs that cannot be solved by the crutch the problem will remain unsolved.
If that problem contributes to tech debt it will become more expensive over time, which should be expected because in business terms application code is always a cost center.

I use the following scenario to describe this to non-developers:

> Imagine parenting a child around 7 to 9 years old.
> That child possesses the maturity to form complex thoughts and reason through various challenges, but lacks the maturity to plan and apply critical decisions even in the absence of any risk.
> You ask that child to clean their room and the child panics because a colossal mess stands before them and they don't know where to start or how to proceed.
> You remind the child they already know what to do: put dirty clothes in the hamper, pick up trash, make their bed, pick up toys, and finally sweep the floor.
> The child knows of those steps in isolation and has completed them several times in the past, but cannot put them together in a meaningful way.

The behavior in that scenario applies equally to many adults.
Cognitive planning and forming systems of organization comes more gracefully to some people than others.
Psychology explains this behavior of as a form of conscientiousness.
In layman terms the common anecdote describing the behavior goes something like: *cannot see the forest for the trees*.

Strangely, of the big 5 personality index conscientiousness is the least correlated with intelligence by a ratio of as much as -0.27.
That means intelligence alone remains largely unreliable to solve for better organizational capacity and without sufficient practice many highly intelligent people will never perform well at this.
Anybody can build better organizational capacity but doing so requires practice, repetition, and experimentation no differently than improving any other cognitive skill.

## Knowing Your Environment
After mitigating away unnecessary complexity the next step on the journey to simple JavaScript requires an understanding of the environment you are writing for.
The compile target of the web browser is the DOM, and for Node.js it is Node's standard API.
Those inescapable facts will alter how a developer perceives the code they write once those facts become accepted by the developer on an emotional level.
Emotional connections to the work form the key to attaining [automaticity](https://en.wikipedia.org/wiki/Automaticity), which describes how the brain learns to complete large series of low level tasks without cognitive effort.
Muscle memory demonstrates one example of automaticity.
Achieving the apex of skill mastery, [unconscious competence](https://en.wikipedia.org/wiki/Four_stages_of_competence), only occurs through automaticity.

### Negativity
Since many developers get lost in the insanity of unnecessary complexity they become utterly incapable of accepting the nature of their environment.
Developers demonstrate the result of that lost acceptance through various forms of emotional trauma such as insecurity, apprehension, blame/diversion, contempt, stonewalling, and so forth.
People frequently use the term *toxic* to describe such negativity because it harms everything like a cancer slowly creeping across the body.
Psychologist [John Gottman](https://www.gottman.com/blog/the-four-horsemen-recognizing-criticism-contempt-defensiveness-and-stonewalling/) describes that emotional negativity as the ultimate predictor of failure in human relationships, but such failure applies with equivalence to all things.

### Positivity
Together honesty and intimacy comprise positivity, the opposite of negativity.
Honesty describes the capacity for truth, which is yet another cognitive skill that requires continual deliberate practice to improve.
Demonstrations of superior honesty result in advanced moral character that builds trust and inspires confidence, which form the [emotional bedrock of leadership](https://armypubs.army.mil/epubs/DR_pubs/DR_a/ARN20039-ADP_6-22-001-WEB-5.pdf) according to the US Army.
When a person advances their understanding of honesty sufficiently they will alter how they perceive themselves, the world outside themselves, and how those qualities interact.
With respect to authoring code, or any other activity, advanced honesty results in greater abilities to question prior conceived notions, which then allows consideration of possibilities not open to consideration before.

Intimacy is the connection, or link, between points that comprise a relationship such that the primary focus of the link is to maximize positive outcome and mitigate away negativity.
The word intimacy most frequently describes social relationships and even then frequently describes relationships between adults that yields consensual sex, but the word can equally apply to any data points that form a positivity focused connection.
In a purely biological sense the common use of *intimacy* makes sense because pair bonding exists for security and reproduction.
In regards to any activity, such as code authorship, greater intimacy allows for considerations of shorter paths between problems and solutions while simultaneously building greater interests in finding and reinforcing improved directness.

I was recently watching a video about a [divorce attorney](https://www.youtube.com/watch?v=o5z8-9Op2nM&t=1322s) and how he perceived marriage failure after speaking about it with numerous clients.
All, I mean every single one, of the behaviors he describes about marriage failure equally apply to how people perceive any activity where they invest sufficient time to form some kind of emotional connection.
Becoming good at programming requires an absurd amount of time.
The frustrations always came down to lost intimacy that over time eventually resulted in broken relationships on an emotional level well before the legal contract of marriage came into dispute.

### Contagiousness
Both positive and negative emotions deeply effect quality of output upon a thing, whether that thing is a physical product for sale or a pair bonded human relationship.
Since all humans express both positive and negative emotions and since both positive and negative emotions directly effect the quality of relationships between humans those emotions spread like a raging virus between people.
That explains why a deliberate understanding of applied positivity forms the emotional bedrock of leadership, because it allows the person forming that positivity to deliberately influence people around them.

## Measurements
Once knowledge of the environment becomes familiar enough to build an emotional bond a new cognitive enemy takes stage: [bias](https://en.wikipedia.org/wiki/Bias).
Bias exists to reinforce deeply held assertions as necessary to free us from the emotional pain associated with the loss of emotional investment qualifying those assertions.
Such assertions may include [saving face](https://en.wikipedia.org/wiki/Face_(sociological_concept)) or [loss aversion](https://en.wikipedia.org/wiki/Loss_aversion).
The underlying behaviors triggering bias are almost always non-cognitive emotional responses to a stress condition and are frequently associated with social conditioning.
All humans demonstrate bias in various levels and forms.

### An Example of Bias and JavaScript
To observe bias in other people watch their repetition of decisions looking primarily for motivation and evidence.
For example I once had a supervisor attempt to convince me that JavaScript executes slowly and that slowness occurs because JavaScript is a single-threaded language.

First, statements such as *slow* suggest a comparison where one thing is less fast than something else where that something else may not be stated.
In this case the something else was logic executing in a SQL database application, but was in fact not stated.

Secondly, in order for one thing to execute less fast than something else there must be measures of both things executing in similar conditions responding to similar inputs and generating similar outputs.
In this case no such measures existed.

Finally, the statement that JavaScript is single threaded is true, but its incomplete and that incompleteness suggests outcomes that do not exist.
An offered incomplete qualifier demonstrates one or more [logical fallacies](https://en.wikipedia.org/wiki/List_of_fallacies#Formal_fallacies) depending upon motive and deliberation.
The more complete technical answer is that JavaScript is single threaded and multi-call stack where one CPU thread sufficiently executes multiple tasks in sequence without halting with each task occupying memory in isolation.
This multi-call stack nature allows the CPU thread to continue executing a task until it must wait on any external input/output at which point it moves to the next call stack and executes that task until either completion or a wait scenario is encountered there.
This rotation between call stacks is the [event loop](https://en.wikipedia.org/wiki/Event_loop).
I explained this to the supervisor who claimed to have heard of the subject but they did not seem to understand it and thus refused to revise their beliefs accordingly.

In summary JavaScript may or may not be slow, but such statements demand comparative measures as qualifiers.
The absence of evidence resulted in the total reliance upon a [prejudiced](https://en.wikipedia.org/wiki/Prejudice) belief system, which in this case was never qualified and later proved both insignificant and invalid.

### Bias Mitigation
Conduct measures with a goal to challenge deeply held opinions.
The exercise of measuring results in more durable decisions less prone to failure and more open to pivots as new evidence and conditions arise.

## Closing
In order to write clean JavaScript you must simply follow these few steps:

1) Avoid unnecessary conventions sufficiently such that the goal becomes more important than the contributing actions.
2) Know your environment well enough to build an emotional connection.
3) Conduct measurements to identify bias.

Rarely, as in almost never, do I see developers attain mastery of this language, but there are developers that do.
Mastery occurs not necessarily because of talent, but primarily because of refinement.
I also hope these steps may serve more broadly than writing JavaScript or even just programming, because the behaviors and results remain the same irrespective of the task at hand.