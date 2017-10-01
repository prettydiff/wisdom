# Web Accessibility - Introduction
This guide is merely an introduction.  By no means should this document be taken as exhaustive or any form of compliance.

## Contents


## What is Accessibility?
Accessibility is the process and validation of providing near equivalent access to content and media for all persons regardless of physical or cognitive barriers.

### Categories of Disability
The common disability categories commonly addressed by accessibility are:

* **auditory** - Auditory disabilities include any impairment related to physical reception of information by the ears, such as: deafness, frequency loss, tinnitis.
* **cognitive** - Cognitive disabilities are a blanket category that convers learning disabilities or any impairment in the understanding or retaining of information.
* **motor-coordination** - Motor-coordination impairment covers any limitation upon physical access by means of self-propulsion of the body with a certain degree of precision, such as: Parkinson's disease, epilepsy, muscular dystrophy.
* **speech and language** - Speech limitations can be due to physical constraints, such as damage to the jaw or throat, but may be due to a cognitive limitation, such as stroke or apraxia, or stuttering.
* **visual** - Visual disabilities include any impairment related to physical reception of information by the eyes, such as: blindness, color blindness, impairment of visual focus.

### Disabilities and the Web
Unless we are in the business of creating or publishing moving or interactive media web developers are primarily concerned with visual, cognitive, and motor-coordination.  Auditory disabilities are not a priority concern unless music or video is presented.  Speech is generally not a priority concern unless voice recognition software is provided.

### You Should Care, Because It's The Law
In accordance with various federal laws visual, cognitive, and motor-coordination limitations are priority concerns.  The legal statutes and associated regulatory bodies vary by business industry, geography, and relationship to user.  As a general concern always make a best faith effort to produce the highest quality product possible.

At this time most legal bodies and regulatory agencies look to the [Web Content Accessibility Guidelines 2.0 (WCAG)](https://www.w3.org/TR/WCAG20/) level AA conformance for determining acceptance or rejection of accessibility.  I urge everybody to glance through the WCAG document to gain a familiarity on basic technical concepts.

Generally speaking, the commons threats from accessibility violations are class-action law suits and regulatory fines.  Class action law suits can be very expensive, such as the case in National Federation of the Blind v Target Corp that resulted in a $6,000,000 award plus $3,738,864 in court costs. Target was also required to rewrite their online shopping technologies to avoid additional fines and lawsuits.  Target's online shopping experience is now a strategic partner with the National Federation of the Blind as a strong example of online accessibility.

An example of regulatory law is the travel industry's 1986 Air Carrier Access Act that was [updated in 2013](https://www.regulations.gov/document?D=DOT-OST-2011-0177-0111) to supersede prior weak judicial review.  This update required that all air carriers performing business within the US, regardless of national origin, would have accessible websites and kiosks in conformance with WCAG 2.0 Level AA.  Violators risk fines and closing of business services within the jurisdiction of the US Department of Transportation.

### Accessibility is not Internationalization
Accessibility does not include internationalization/localization which are the means to convert artifacts and customs to cultural and geographic preferences.  Various different locations of the world specify unique preferences for format of date/time, currency, and perhaps even spelling of common words.  These differences and all associated conformance do not fall under the umbrella of accessibility.  As general usability guidance, however, formats should always be specified to ensure users read, understand, and respond exactly as the application commands.

## Accessibility in Development (All Hands on Deck)
Development teams are beholden, as subject matter experts, to creating products that appropriately account for and solve accessibility concerns.  Development teams are more than just UI developers writing HTML.  Business requirements that fail to account for accessibility, or create ambiguities, must be considered faulty or incomplete.  Such requirements must be pushed back for refinement before work can start.  Accessibility conflicts that arise in developer code must be considered defects and rejected by any receiving party and blocked from publication to end users.

In order for accessibility to be achievable everybody must be aware of common defects.  By everybody I mean all software developers on the team, business analysts, QA, and even the primary stake-holders.  Accessibility violations in the software are defects in much the same way as a broken tail-light on a car and like-wise fail inspection while violating the law.

In order to think about accessibility developers must first be generally aware of how human impairments impact access to information, how impaired persons contend with this, simplified validation tools, and what assistive technologies are commonly available.  At a high level, all associated team members need this level of awareness.  Only front-end developers need to have knowledge regarding technical implementation of associated code.

It is important for all team members to be aware of accessibility in this manner because it changes how they perceive their own work and where they fit inio a corrective process.  When accessibility becomes a front-end developer only responsibility it is rarely applied and never enforced.

## A Simplified Examination of Common Impairments

### Visual
Visual impairments range from focus disorders, complete blindness, slow motor response of muscles around the eye, color blindness, and various other factors.  The most common solution for vision related impairments are screeen reader applications that read text aloud as speech and interact with websites only using a keyboard.  Some common screen readers:

* [JAWS](http://www.freedomscientific.com/downloads/jaws)
* [NVDA](https://www.nvaccess.org/download/)
* [Apple VoiceOver](https://www.nvaccess.org/download/)
* [Google ChromeVox](http://www.chromevox.com)

The most important factor for screen reader compatibility is well written HTML.  Well written HTML means content is present in the appropriate order from top to bottom and left to right.  All content is well described with valid HTML where tags are appropriately structured with their required attributes.  Screen readers navigate within a page by hyperlinks, headers, ARIA, and various other semantic structures the screen readers make available as shortcuts.

As an experiment try navigating around the website using only a keyboard.  Do you get lost?  Can you effectively and quickly navigate between various sections of content or form controls?

If this is too easy turn on a screen reader and turn your monitors off.  Can you navigate through a single page app without getting lost?  Does the page content make sense?

These experiments aren't hypothetical draconian challenges.  This is common usage for a larger than expected portion of the general population.

### Cognitive
Cognitive suggests factors related to understandability, retention, and relational concepts.  In this regard solving for many cognitive disorders is generally referred to as *usability*.  Solving for many of these problems means ensuring text content is well ordered and is simple to read.  Please keep in mind the average reading level in the US is at a 9th grade level, which means writing for a master's level education erodes access to more than half the population.

The most common cognitive limitations are dyslexia and dysgraphia which dramatically vary by population according to the complexity of the written language.  Languages without an alphabet tend to have higher incidents of these disorders while languages with syllabaries tend to have fewer.  These users may make use of screen readers even though they aren't visually impaired, which suggests perhaps a larger percent of the population may use screen readers than are actually visually impaired.

### Motor-Coordination
People with degraded fine-motor control will often choose to navigate a page using only a keyboard or touch screen.  A mouse is diffecult and frustrating to use when a user cannot keep their hand still.  If these users accidentally click the wrong form control or accidentally navigate to different area do they lose information or become stuck?

## Summary
It is important to remember the purpose of accounting for accessibility is to reduce and eliminate discrimination.  In many cases this isn't charity or kindness.  It is compliance with the law.  As technology experts, it is our job to ensure we develop products that are easy to use and quickly solve the needs of our users.  If we go out of our way to frustrate and challenge our uses out of complacency they well take their business else where.