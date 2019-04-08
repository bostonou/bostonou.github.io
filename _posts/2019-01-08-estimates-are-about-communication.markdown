---
title: Estimates Are About Communication
tags: estimates design hiring-career
---

When people asks for estimates, it often feels like they're forcing us to commit before we're ready. But if we think through why they're asking, we can approach estimating with less pressure and contention.

Estimates provide the following:

* Communication about what's needed by the business
* Communication about the time and resources required to fulfill the need
* Communication about the risks involved in fulfilling the need

### What's Needed By The Business

One key thing is that estimates are for communication *in both directions*. For too long, I treated estimates as managers simply telling me what to do and me explaining how long it would take.

One of my favorite business books is [*The Advantage* by Patrick Lencioni][advantage]. He says that the vast majority of businesses have more than enough good information to succeed. They fail because they do not have a culture that encourages or allows the free flow of that information (what he calls Organizational Health).

Estimates are one way to free up the flow of information. When clients and managers ask us for estimates, it's our job to figure out what they really need:

* What benefit are they expecting from this feature?
* Why wouldn't some other feature provide the same benefit?
* How does this fit into a larger plan?
* Is it clear what they're giving up in order to get this feature?

There are plenty of other questions and variations, but the key thing is to work towards a shared understanding. When we sit down to finally write code, we want to make sure we understand how the code is impacting the people we work for.

People say to me all the time, "I have an idea for an app. How much will it cost to build?" My standard, immediate response is $250k, and the number goes up every time someone asks. How can I estimate something as vague as "an idea"?

I'm not guessing as to how long it will take or how much it will cost. I'm communicating the magnitude of what they're asking for because, in many cases, people have wildly inaccurate expectations around the difficulty and cost of building software. So when I say $250k, what I'm really saying is, "If you don't have significant capital/energy to invest in this and it's not immediately clear that this idea is worth that capital, you don't want to do this."

The general response is, "What!? I just want something to do X and Y and it's going to cost $250k?" And with that we've broken through the assumed idea of "I need an app" and gotten to what they really need. And in many cases, people's problems are solved with something as simple as a group text or regularly scheduled email.

We can take a similar approach in meetings around estimates, not necessarily with default answers, but with an attempt to understand what businesses really need when they ask us, "How long will it take to..."

### What Time And Resources Are Required

This is the point that most people focus on, and generally the focus is on figuring out some range of numbers. In a future post, I'll dive into how to do that better, but here it's important to recognize that we can provide more than just a dollar amount or range of time. We should also provide some explanation of our confidence in our numbers.

Certain areas of a project or feature are bound to overlap with work we've done before, along with other areas that have some degree of newness and uncertainty. Our estimates should be less like "Project X will take three to six months" and more like "We're confident Part One will take two months, but we think Part Two could be anywhere from one to four months." The first estimate is given as a take-it-or-leave-it, with the responsibility on stakeholders to dive in and break the timeline apart. What often happens is that there's a discussion (argument) that ends with some averaging of the time range. The second estimate provides options; maybe we just do Part One, maybe we check-in at two months to revisit the problem, or maybe we schedule time to further investigate Part Two before making a decision.

### What Risks Are Involved

There are multiple lenses to view risk through that should be part of the estimate. Perhaps the most obvious is the risk to the estimated time and cost. This plays a part in determining our confidence levels; in the areas of low confidence, we should call out the risks we see that lower our confidence. Risks of this type can include key personnel availability, using unknown libraries, switching technology, browser quirks, etc.

Another way to view risk is as risk to the business. One company I worked for asked for a new api integration with a large supplier. To them, it was just another feature request with nothing out of the ordinary. But the integration they wanted would now change data instead of just reading data, which opened up several new risks. A bug in business logic could destroy margins. The supplier might have different api limits for changes, and if we broke their terms we could put the whole company at risk. It was my responsibility to communicate these risks as part of the estimate. "This will only take a week" is significantly different than "This will only take a week but puts our relationship with $BigCo at risk."

---
<br />
Estimating doesn't have to be a contentious activity where business people feel like they can't get a straight answer and developers feel like they're being asked to make and commit to impossible predictions. Clients and managers are better served and developers feel less pressure when we focus on communication with a goal of shared understanding.

[advantage]: https://www.amazon.com/Advantage-Organizational-Everything-Business-Lencioni-ebook/dp/B006ORWT3Y/ref=tmm_kin_swatch_0?_encoding=UTF8&qid=&sr=
