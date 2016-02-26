10 Domains of Web Development
===

This list is inspired by the [CISSP Domains of Information Security](https://www.isc2.org/cissp-domains/default.aspx).  The CISSP domains provide a clear path towards areas of study in the assessment and management of various security concerns.  The primary advantage to this form of knowledge organization is in communicating a set of decision-making skills appropriate to a single area of concerns where those concerns together to encompass the required general body of knowledge.

The 10 domains are as follows:

* Accessibility
* Architecture
* Data
* Events and Interaction
* Internationalization / Localization
* Presentation
* Security
* Semantics
* Transmission
* Understandability / Content

Accessibility
---

Accessibility (a11y) is the means of intentionally providing content, services, and products to audiences in a way that is anti-discriminatory.  The primary concentration is to ensure equivalent access to users with disabilities who may require the use of assistive technologies.  Improvements to accessibility benefits everybody by ensuring content is better organized, easier to understand, and easier to access.  The five primary disability categories of concern by web technologies are: auditory, cognitive, epileptic, motor reflex, and visual.

Most information on the web is conveyed by a plurality of formats expressed as text.  Due to this auditory assistance is typically only required with media that contains an auditory signal such as video or a sound clip.  Auditory assistance can be accomplished by providing text captions embedded into a video allowing the conveyed speech to be read visually by the audience as it is spoken and conveyed in the video or through providing a text equivalent of the conveyed speech.

Cognitive impairment typically represents a wide diversity of behavioral, intellectual capacity, and neurologic conditions that impact greater potential for fluid comprehension and retention of information according to a common person standard.  The most common ways to reduce cognitive barriers are to reduce educational requirements to use or consume content and to ensure content is well organized and properly described.

Photo-sensitive epileptic seizures can be triggered through repeated flash or rapidly changing visual impulses.  The standard guidance from WCAG 2.0 is to ensure stark contrasts of colors or visual flickers occur at a rate of slower than 3 times per second.

Motor reflex disorders impacts the body's finer coordination and dexterity.  Missing or impaired extremities and digits, though not an indication of degraded motor control, is included in this category as the result is an impairment of access by means of physical interaction.  The primary considerations for conformance is proper order of keyboard focus state, ensuring the focused element is clearly visible, and that interactive controls space apart enough to ease access by mouse users who have less steady hands.

Most accessibility efforts are invested towards vision impaired users.  Vision impairment includes limited vision, blurring, color blindness, partial blindness, and complete blindness.  Blind users typically use text reading applications to read textual content and descriptive meta data aloud by a synthesize voice.  Different text readers will navigate the content of a web page in various different ways.  The order of content, page structure, description of content, and order of headings is extremely important.  This demands a strict understanding of HTML, semantics, and description.  An additional technology, Accessible Rich Internet Applications (ARIA), is provided to allow additional description and navigation for text readers.  A secondary visual concern is to ensure color contrast meets the standards specified by the WCAG.

* [WCAG 2.0](https://www.w3.org/TR/WCAG20/)
* [Understanding WCAG 2.0](https://www.w3.org/TR/UNDERSTANDING-WCAG20/)
* [ARIA](https://www.w3.org/TR/wai-aria/)
* [Accessibility Tools](https://github.com/prettydiff/a11y-tools)

Architecture
---

Architecture is the means of organizing and planning technical requirements, business requirements, and available resources into a product, service, or offering as envisioned by identified stakeholders.  This is typically severely under represented as merely a means of organizing software components.  A more accurate characterization are these terms applied broadly: *planning*, *organization*, and *execution*.  The primary objective is all about making basic decisions early and documenting them so their influences upon creativity and direction are known and intentional.  Dwight D. Eisenhower once said:

> Plans are worthless, but planning is everything.

In the web it is common to apply a popular JavaScript framework and assume architecture is largely complete, which often bears devastating and unforeseeable long term consequences to the health of a given application.  The immediate problem is that applying an architecture in a box instant solution appears to solve the problem of planning.  This is insufficient as technology planning is more than the mere binding of modules.  Successful planning is a continuous process that will include the success metrics identified by the project stakeholders, the key objectives specified by the business requirements, and an inventory of available technical capabilities before shopping for additional tools. A successful architecture is not a rushed technology tool to solve an immediate technical concern but rather a well considered business plan applied to a technology as closely as possible and frequently reevaluated.

It is safe to assume if a given technology architecture is separated from business input the needs of the business will grow independently of the technology without hope of resolution.  This distancing will eventually result in a critical impasse where business decisions are invariable constrained by limitations of the software system or where the software system must be replaced by a separate system at incredible expense.

* [Separation of Concerns](https://en.wikipedia.org/wiki/Separation_of_concerns)
* [System Architecture](https://en.wikipedia.org/wiki/Systems_architecture)
* [Planning](https://en.wikipedia.org/wiki/Planning)
* [The Operations Process, Army](http://armypubs.army.mil/doctrine/DR_pubs/dr_a/pdf/adp5_0.pdf)
* [Simplicity](https://en.wikipedia.org/wiki/Simplicity)
* [Design Methods](https://en.wikipedia.org/wiki/Design_methods)
* [Requirements Analysis](https://en.wikipedia.org/wiki/Requirements_analysis)

Data
---

Data is atomic particles of primitives or symbols that convey an informational asset directly.  In the context of software design data is typically represented as *types* as supported and defined by a given language or system.  In the specific context of transmission data is conveyed in various structures, such as: XML, JSON, YAML, and various similar formats.  When data is organized properly into a structure that is self-descriptive and easily understood by the consuming humans and executing automation systems it becomes *information* as defined by the DIKW model of shared wisdom.

In addition to understanding what data is and how it is conveyed another important characteristic is the means by which data is consumed by a web application.  The most common of such means are: DOM, query selectors, JSON JavaScript methods, DOMParser, and XPath.  These methods of retrieving informational assets directly into an application are typically referred to as an Application Programming Interface (API).

The most common and preferred means of formatting data for the web is JSON format due to its minimal syntax, terseness, and primitive nature.  In most cases intelligent data structures are not demanded by most current web applications.  In this case data is structured in a way that is manually defined and populated by primitive data facets.  This structure can be transferred across the wire as demanded and easily consumed by the receiving application provided the structure of the data is already known to the receiving application.  One limitation of this approach is that data structure definitions cannot be dynamically adjusted to meet the demands of changing applications.  Another limitation is that since the data structure provided by JSON is primitive meta-data descriptions, and thus context, cannot be reasoned from the data in any sort of automated means.

* [DIKW Pyramid](https://en.wikipedia.org/wiki/DIKW_Pyramid)
* [Semantic Web](https://www.w3.org/standards/semanticweb/)
* [DOM](https://www.w3.org/DOM/)
* [The DOM Explained, Quick and Simple](http://prettydiff.com/guide/unrelated_dom.xhtml)
* [JSON](http://json.org/)
* [XML](https://www.w3.org/XML/)
* [XML Schema](https://www.w3.org/XML/Schema)
* [DOMParser](https://developer.mozilla.org/en-US/docs/Web/API/DOMParser)

Events and Interaction
---

Generally speaking events can refer to any point in code where a request is made of an external resource in an asynchronous way.  An event occurs when a given single threaded language defers execution to an external resource This can include a variety of things including user initiated actions, timers, transmission requests, and so forth.  From most web applications events occur almost exclusively in JavaScript code or things which interface with JavaScript code.  It could also refer to changes in data or modifications to data structures that are not resident within the execution context of the currently running application.

It is important to understand the so called *event loop* and how it has architectural implications far beyond user interaction.  The event loops is a means of explaining a model of asynchronous execution in a single threaded language.

When assigning user interactions the common best practice approach is to use *addEventListener*.  This is a feature it allow binding of events to DOM elements that is considered more safe than the older DOM *on-event* assignment.  The term safe merely suggests that numerous event handlers can be assigned to a single event without overriding other handlers already assigned to the given event.  There are serious limitations in this approach, however.  Numerous handlers bound to a given event can make the process of JavaScript garbage collection more complex which and drastically slow an application execution speed.  It also masks serious security and privacy concerns. Third party code sourced into a page from an unrelated source, such as from an advertisement, can assign functions to track user interactions and sensitive data or perform malicious results.  In the older DOM on-event assignments only a single handler can be associated with an event, which requires application maintainers to properly manage event allocation or risk exposing errors to their users, but such errors would be more readily identifiable without malicious implications.

* [Concurrency model and Event Loop in JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop)
* [DOM on-event handlers](https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Event_handlers)
* [addEventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)
* [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
* [Using and Declaring Callbacks](https://developer.mozilla.org/en-US/docs/Mozilla/js-ctypes/Using_js-ctypes/Declaring_and_Using_Callbacks)
* [Understaning callback functions in JavaScript](http://recurial.com/programming/understanding-callback-functions-in-javascript/)
* [WindowTimers](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers)
* [Node Events](https://nodejs.org/api/events.html)

Internationalization / Localization
---

Internationalization (i18n) is the means of a software application's ability to adapt content, presentation, and data facets to the conventions and customs of a regional or geo-political locale.  Such changes may include changes to currency symbols, data/time formats, presentation of numbers, and different human written language.  Localization is process of defining and adding the various definitions for a given locale to an internationalization process.

* [Localization and internationalization, what's the difference?](http://stackoverflow.com/questions/506743/localization-and-internationalization-whats-the-difference)
* [Internationalization](https://en.wikipedia.org/wiki/Internationalization_and_localization)
* [Unicode](http://unicode.org/)
* [W3C Internationalization Activity](https://www.w3.org/International/technique-index)
* [Internationalized Resource Identifier](http://tools.ietf.org/html/rfc3987)

Presentation
---

Presentation is the means of supplementally enhancing a given communication to intentionally alter how an audience perceives the communication without manipulating or interfering with the communication.  Typically presentation refers to a visual enhancement, but may also refer to certain interactive experiences and certain limited adjustments of media, such as modifications to play back speed.

The primary purpose of presentation is to alter human perceptions and behaviors when consuming content.  This can means something as benign as making content easier to understand and consume by window dressing its appearance.  It can also seek to incite deliberate cognitive emotions for the express purpose of manipulating opinions and emotions.

The most common application of presentation is through CSS code.  It is important to understand how presentation can touch accessibility, security, and understandability in both positive and negative ways to ensure a given software application reaches a wider audience in a non-malicious and anti-discriminatory ways.

To reduce risks to accessibility, internationalization, semantics, and other important considers it is imperative that presentation code be fully separated from data, content, architectural code, and all manners of data structures.  Separated CSS is easier to maintain and alter without needing to change the enhanced resource.

* [W3C - Cascading Style Sheets](https://www.w3.org/Style/CSS/)
* [Accessibility Features of CSS](https://www.w3.org/TR/CSS-access)
* [Responsive Design](https://en.wikipedia.org/wiki/Responsive_web_design)
* [Google, Mobile Friendly Test](https://www.google.com/webmasters/tools/mobile-friendly/)
* [Separation of Presentation and Content](https://en.wikipedia.org/wiki/Separation_of_presentation_and_content)

Security
---

Security is the process of identifying risks and progressively reducing, eliminating, or isolating them.  Risks are any interference to a provided business activity that disrupts, defaces, or denies that activity.  Risks are not always known qualities and are frequently unknown.  Threats are identified vectors known to cause some amount of risk.

Security threats can be something as obvious as a malicious agent actively seeking to destroy a given service or product.  Security threats can also be accidents and minor unintentional behavior, such as a small software bug that is exposed from a combination of features not anticipated by the software authors.

The iSC(2) organization has identified several areas of security concern of which many are not connected to any malicious behavior.  It is the job of a security manager to reduce all risks where possible without regard for malicious intent.  This can mean preventing legal civil suits, reducing software defects, reducing unauthorized entry, reviewing software architecture, enforcing data sensitivity, and so forth.

Software violations can occur anywhere from accidentally and poorly written content that damages a brand to a complete denial of service from a malicious criminal.  In software the most common threats occur from users exposing and engaging software defects.  Frequently this behavior is not malicious and may even be completely unintentional.  The most dangerous web software vulnerabilities typically occur from malicious individuals finding and abuse software with poor user security.

The most destructive threat vector are referred to as the *insider threat*.  Typically this refers to employees of a provided organization, but it includes anybody who provided deliberate access to confidential materials.  Again, this sector is most commonly experiences without any negative intent, such as a developer releasing code that causes a defect in production.

It is necessary to be aware of common security threats to the development of web applications and ensure these threats are eliminated or mitigated.  Many such problems can be addressed through policies and automated controls, but sometimes manual intervention is required to sufficiently reduce a threat to acceptable levels.

* [Systems Security Certified Practitioner](https://www.isc2.org/sscp/default.aspx)
* [Online Web Application Security Project](https://www.owasp.org/)
* [Payment Card Industry, Data Security Standard](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-1.pdf)
* [Internet Security](https://en.wikipedia.org/wiki/Internet_security)
* [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)
* [HTTP access control](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS)
* [Cross-Origin Resource Sharing](https://www.w3.org/TR/cors/)
* [List of useful HTTP headers](https://www.owasp.org/index.php/List_of_useful_HTTP_headers)

Semantics
---

The ability to extend data is referred to as *semantics* and means of discovering more precise definitions from parts of a larger whole are referred to as *context*. Understanding how to implement these concepts allows automation tools to extend, mutation, selectively store, and transfer content independent of manual human intervention.  This is the foundation of the data-oriented approach to artificial intelligence.  The web platform has a natural advantage with regard to artificial intelligence because it is highly distributed.  Various applications operating remotely doing different automated tasks are capable of benefiting from shared data generated by other unrelated systems so long as such data remains available.  This is the focus of research referred to as the *Semantic Web*.

Semantics is the means of intentionally describing content with meta data.  Application of semantics is essential to the success of accessibility and search engine optimization (SEO).  For example, Google's backlinks strategy would be considered a semantic technology approach.  When considered as part of the architectural process semantics can result in more meaningful content that is communicated directly, which has second and third order consequences to the business from the resulting influences upon user behavior.

A meta-data description could be structured wherein a piece of meta-data extends the definition of an enclosed meta-data facet, which means content is described and the definition of that description is extended.  This is referred to as *structured meta-data* and is exemplified by lists in HTML when used appropriately.

Structured meta-data is the cornerstone of the RDF series of technologies and is a huge influence upon the design of XML Schema language.  The advantage of an RDF similar technology is that the relations between data facets influences how data-facets are described and extended, which means the relations between nodes is just as important as their definition.  This phenomenon is somewhat observed when walking the DOM on well written HTML.

The primary difference between an RDF system and the DOM is explicitness and state.  In an RDF like system the data graph is saved explicitly so that it can be queried as a database system.  The W3C defined such a query language called SPARQL.

Semantics isn't limited to local extensions of data.  The web is a highly distributed system and the semantic web is an evolution of that distribution.  The primary design goal of the semantic web is to allow additional data, supplementation, and new data definitions through hyperlinking.  The idea is that by making data openly available in a standardized and commonly consumed format such data is available to a variety of applications each with their own reasoning upon that data.  The ability to gather disparate reasoning capabilities and combine those capabilities in new ways is the very definition of shared knowledge according to the DIKW model of wisdom.  This is the foundation behind reasoning based artificial intelligence, and a form of this approach is commonly referred to as heuristics.

Sometimes data needs to be segmented opposed to extended. This can be accomplished by namespacing.  The concept of namespacing allows data to be segmented into a named identifier that can then be independently extended or segmented further.  There is a formal definition of this concept in XML by the W3C, but this concept can apply generally as a data architectural convention.  Namespacing is commonly used to segment data and functions in JavaScript, for instance.

* [DIKW Pyramid](https://en.wikipedia.org/wiki/DIKW_Pyramid)
* [Semantic Web](https://www.w3.org/standards/semanticweb/)
* [RDF](https://www.w3.org/RDF/)
* [SPARQL](https://www.w3.org/TR/sparql11-query/)
* [Heuristics](https://en.wikipedia.org/wiki/Heuristic)
* [Search Engine Optimization](https://en.wikipedia.org/wiki/Search_engine_optimization)
* [Google provides backlink tool for site owners](https://www.mattcutts.com/blog/google-provides-backlink-tool-for-site-owners/)
* [XML Namespaces](https://www.w3.org/TR/REC-xml-names/)

Transmission
---

Transmission is the means of moving data from one location to another. The primary transmission technology of the web is HTTP an application layer protocol that rides over the TCP transmission stack.  HTTP almost exclusively operates against an addressing scheme called URL, which is the more common form of URI technology.

Transmissions can occur in a single instance or in a plurality.  They can be static (human initiated) or dynamic (application initiated).  They be synchronous (blocking) or asynchronous.  The web is a highly distributed system and transmission capabilities are the primary enabling technology behind that distribution.

Because transmission capabilities are so profoundly essential there is a great amount of flexibility in where and when they may occur in a given application.  This great flexibility also suggests a wide opportunity for security exploitation.  To solve for some of this web browsers employ a cross-origin request limitation that prevents transmissions to or from third party domains unless a *CORS* rule exists to explicitly allow the transmission.

* [TCP](https://en.wikipedia.org/wiki/Transmission_Control_Protocol)
* [HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol)
* [HTTP/2](https://en.wikipedia.org/wiki/HTTP/2)
* [SMTP](https://en.wikipedia.org/wiki/Simple_Mail_Transfer_Protocol)
* [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)
* [URI](http://tools.ietf.org/html/rfc3986)
* [HTTP access control](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS)
* [Cross-Origin Resource Sharing](https://www.w3.org/TR/cors/)
* [List of useful HTTP headers](https://www.owasp.org/index.php/List_of_useful_HTTP_headers)
* [xmlHttpRequest Object](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest?redirectlocale=en-US&redirectslug=DOM%2FXMLHttpRequest)
* [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)

Understandability / Content
---

There is no greater opportunity to win users and influence behaviors, such as increasing retail conversion, than through the effectiveness of a well written communication.  The potential to write details that concise, explicit, and accurate goes further to demonstrate precision and care than any other single factor.  In additional to winning external users well written communication also has extremely important considerations for internal users and developers with regard to documentation, logging, release notes and so forth.

The words expressed in a purely textual communication also have security and accessibility implications.  For instance false states on a commercial website could result in a law suit.  The quality of text and information that is externally available is the single most important factor to search engine ranking.

Users respond differently to different messaging.  This is a testable factor through A/B testing and thus suggests behavioral implications that not immediately known.  This form of behavioral analysis is called *understandability*.  There are several different strategies to accomplish this, that is to make the product easier to understand.  In order to make a product or service more understandable it is important to empathize with users.  It is challenging to have an idea of what the audience doesn't know, but identifying gaps in the audience's understanding is what can almost instantly transform a weak product into an amazing product.  The technical qualities are the substance of content, the order in which content is provided, the presentation and description of that content, and the means of interacting with that content.

Eliminating barriers to understandability in your audience is a challenging problem, but eroding barriers where feedback and sharing are important is a greater challenge.  Fortunately there is a science dedicated to this subject called *knowledge management*.  In knowledge management the primary problem to solve is that a technical expert is valuable for their ability to solve a given problem, but would be substantially more valuable to the organization if they could document and train others in their expertise.  This requires sharing of skills and capabilities gained through years of experience and hard work that most people find incredibly challenging to share in a way that makes sense to less experienced people.  This isn't just a challenge of finding the right words or substance to describe a topic.  It is a challenge of being a super-influencer as necessary to overcome behavioral and cultural barriers and defenses an audience is likely unaware they have raised.

* [Writing for the Web](http://www.usability.gov/how-to-and-tools/methods/writing-for-the-web.html)
* [The Inverted Pyramid: a Great Format for Web Pages](https://writingtoinfluence.wordpress.com/tag/web-writing/)
* [Social media “toolkit” has more influence than traditional news release](https://writingtoinfluence.wordpress.com/category/writing-to-influence/page/2/)
* [6 Steps To Influence Others With Your Business Writing](http://www.forbes.com/sites/allbusiness/2013/07/01/6-steps-to-influence-others-with-your-business-writing/#650821509fcb)
* [Good Documentation and Quality Management Principles](http://apps.who.int/prequal/trainingresources/pq_pres/stakeholders_2011/presentations/Day_2/Good_Documentation_Practices.pdf)
* [Understandability](https://en.wikipedia.org/wiki/Understanding)
* [Content Integration](https://www.nngroup.com/articles/content-integration/)
* [What is KM? Knowledge Management Explained](http://www.kmworld.com/Articles/Editorial/What-Is-.../What-is-KM-Knowledge-Management-Explained-82405.aspx)
