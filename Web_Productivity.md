# Productivity for Web Developers

## Definition

Productivity is simply the ability to decrease the expenses required to accomplish a required business task.  From an economic perspective qualities such as labor expenses, duration, tools, talent deficits, and so forth can all be classified as expenses.  Ultimately this means all discussions of productivity are ultimately subjects of business management.  The most simple means to calculate expense is something like:

    var a = number of people assigned to a given effort,
        b = average cost per person,
        c = total duration;
    a * b * c = initial productivity baseline

The more people assigned to a given effort the more expensive that effort becomes.  The longer it takes to complete that effort the more expensive it becomes.  Productivity is calculated from the inverse comparison of various measurements of expense for roughly equivalent efforts.  If expenses going down for similar tasks this is an indication that productivity has increased.  If for example, one developer is capable of completing a task that normally requires three developers that one developer is 3 times more productive or a third of the expense to the business objective.

## Business Imperative

If businesses are able to increase productivity they are able to increase the frequency of task completion without hiring additional personnel.  This translates to an increased competitive stance in the marketplace without increasing expenses, which allows for increased revenue generation that can be reinvested into the business, and so forth.  More important than the economic considerations are the potential business implications.  Increased productivity allows for deeper penetration of the target market, consideration of market diversification, and yields second and third order effects that translates into superior brand performance.

Increasing internal productivity within a business is an absolute good, but it is not absolutely achievable.  There are various qualities that are always competing for attention and may limit or defeat productivity.  The qualities can be things like:

* unclear requirements
* developer regression
* rapidly changing business direction
* shifting market segments
* fear and uncertainty

### Identify Areas of Improvement

While some productivity limiting qualities are clearly known many are not known and challenging to identify.  The most direct way to identify obstructions and potential gains of productivity is to evaluate individuals doing their jobs directly.  Ask basic questions of the observations:

* Are developers spending half the day surfing the web instead of writing code?
* If so what is preventing the developers from doing work?
* How many times must developers repeat a given task before the task is complete?
* What barriers are in place between writing code, testing that code, and proving the results to stake holders?
* Is there an increased need for documentation, tool configuration, internal processes, and so forth that makes doing the job harder and slower?

Many times a business leader can simply ask these questions to developers directly and may be surprised by the directness of the answers.  Typically developers fall into three categories in response to limited productivity.  Senior developers who are confident in their performance potential will generally view productivity barriers as institutional problems that are forced upon them.  These senior developers are happy to discuss productivity problems and will generally willing to propose radical solutions that others might find drastic.  New developers will generally not have an immediate opinion on the matter and are generally flexible and accepting of changes.  Developers who view themselves as imposters or are utterly reliant upon a specific tool, language, or configuration will generally fear any change.  Imposters may find increases in productivity threatening.  Business leaders need to account for imposters when evaluating productivity.

### Train Your Team

Most developers lack the initiative to learn foundational skills on their own.  It is up to the technology leadership in the business to stress and mandate certain levels of technical quality.  Many businesses are completely aware that required training is completely missing.  This can sometimes be due to a toxic development culture, lack of clear technical direction, or other indirect qualities.  More commonly businesses refrain from direct technology training because that is time and money not spent achieving the business's primary objectives to increase revenue.  There is also fear that senior developers are highly valued assets in the market place and investments in training may result in lower employee retention.  These concerns are valid, but must be evaluated against the need for more competitive product development.

## Developer Productivity

### Self-Imposed Barriers

The most direct and important actions to influence developer productivity is to eliminate barriers.  If developers are required to use a series of tools that require a build process between code authoring and execution then developers are idle for the duration of that build process.  That lost time is an expense that must be multiplied by the total number of builds per day from all developers.  For example if a build process takes 30 seconds to execute, but a developer runs that build task 20 times a day that is a loss of 10 minutes of productivity.  If there are 30 developers working on the platform that is a loss of 5 developer hours per day from something that only takes 30 seconds of time.

Many of these barriers are self-imposed, which is to say that developers deliberately put obstacles in their way to make the technologies easier to organize and understand.  While these add steps constitute a known and precisely measurable deterrent to productivity the most common counter-argument is that the developer would be harmed and impaired without the added help, which they believe to be a far greater impediment to productivity.  This is not a technology problem, as many might believe, but rather a training and hiring problem.

When developers lack certain foundational skills they may compensate through the addition of tools to bridge these gaps in their competencies.  Developers who are hiding behind tools can become very creative in their excuses to avoid accepting responsibility for gaps in their qualifications.  It is the responsibility of technology leadership to recognize these excuses and provide positive mentorship.  It is important that technology leadership not continue to enable these behaviors.  Enablement is how bad developers remain bad developers and harm the entirety of their team through apathy, negative defensiveness, and low quality results.

When evaluating for productivity the business must have sufficient technical capabilities to discern the differences between a tool or process that is required directly by a business task versus a tool or process that is required by a developer to accomplish that task.  It is helpful to determine to what degree the given developer can distinguish between those two facets.  Look for terms such as *easier* or *quicker* and be sure to critically examine those terms to determine if the developer is attempting to make things easier for themselves first or for somebody else and at whose expense.

### Institutional barriers

Institutional barriers are qualities enforced by the organization that directly interfere with potential productivity improvements.  These can be things like processes and configurations for required applications.  These things may exist for good reasons, such as prevention of security violations and ensure product stability.  Sometimes these barriers are present because the organization does not trust the technologies which they have been forced to accept, such as Java developers being forced to write web technologies where they lack experience.  These barriers may also occur due to poor planning and an incomplete understand of software architecture.

### Developer Imperative

While there is a clear business interest to increase productivity the implications to the individual developer are more direct and profound.  A developer who is able to accomplish an effort three times faster than their developer peers is a more productive developer.  This means in between task assignments that developer has additional time for independent research, education, and experimentation.  This unstructured time allows the more productive developer to grow their confidence and capabilities to become more productive still.  It also allows productive developers the opportunity and confidence to solve ever large problems, which increases the marketability when shopping for other future employers or competing for that promotion with the current employer.

### Become a Productive Developer

1. Learn the standard technologies.  This cannot be stressed enough.  A developer that is reliant upon extra tools will always be less productive than a developer who is comfortable with solving the same problem in the absence of those extra tools.
2. Reduce and eliminate barriers.  If unit tests are in your build effort move them out to a separate effort.  Continue to reduce and eliminate tasks from the build effort so that its execution duration continues to fall until the build effort can be removed entirely.
3. Solve hard problems early.  For example consider cross-browser irregularities.  A developer who has spent large blocks of time in the past fighting these dragons will find them easy to solve and diagnose.  A developer who is always reliant upon a library or tool to solve these problems will be lost when once that tool is no longer adequate.
4. Document solutions to hard and common problems.  This won't save the documenting developer any time, but it will save substantial time for all their teammates.  This increases overall team productivity.
5. Push back on unclear requirements.  Do not accept tasks that require large amounts of guessing, or simply guess and present the solution pending better requirements.
6. Avoid bad advise.  If a hard decision is required and the technical guidance violates basic technology principles independent test things and present the results of the tests as evidence to more senior decision makers.  Accepting bad advise will gradually destroy productivity over time like a snowball rolling down a hill.
