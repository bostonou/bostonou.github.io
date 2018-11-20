---
title: Wash Your Hands
---

I was listening to an [interview with Seth Goden][sethg] where he discussed what it means to be a professional. The example he used was surgeons preparing for surgery. Surgeons are expected to scrub their hands every time before surgery. Part of being a professional surgeon is going through the hand washing procedure.

How could washing your hands be described as a "procedure" or as something that requires "proper technique" and a "systematic approach"? Check out [this description of hand scrubbing][hand-scrub]. It has:

* more than 10 steps
* is timed
* expected setup beforehand
* expected support from teammates
* rules to be observed
* formal guidelines and recommended practices from professional organizations

The amount of work that goes into hand washing is way more than I would have ever thought. And this happens for every single surgery.

Part of being a professional surgeon is going through this process. Every. Single. Time.

Under a tight deadline?

Doesn't matter, wash your hands.

Think your hands are probably clean enough?

Doesn't matter, wash your hands. 

It's just a quick, minor surgery?

Doesn't matter, wash your hands.

## Professional Developers

For those of us that call ourselves or aspire to be professional developers, what is our equivalent of washing our hands? What do we always do, without exception, before we start typing?


Surgeons wash their hands to ensure cleaningless; what do developers need to be sure of?

I suggest that we need to be sure we've thought properly about a problem. Specifically, we need to discipline ourselves to **think and design before *and* apart from typing**.

And I think a solution is very simple: **we need to write more**.

The best way I've learned was really modeled well by my friend and old coworker [Tim Pote][timp]. He put together a simple template that we filled out whenever we started working on a problem. If you were starting something new (feature, architecture, rewrite, etc), it was expected that you'd fill out the template.

The template asked some simple questions that encouraged us to think through problems.

## Simple Template

### Problem

What is the problem you're solving? Not what is the feature, not what are you going to do. What is the problem, why is it necessary, who is it for?

Attempting to answer just this question provides so much value. You should be able to clearly articulate:

* what the issue is
* who is having the issue
* who would benefit if this issue was solved
* what the benefit would be

If you can't answer all of those questions, you're probably solving a non-problem. At best, you're trying to solve an actual problem but don't have enough understanding to provide a real solution.

In practice, this step is often very hard because the problem is ambiguous and hard to piece apart. But often, the process of working through this step helps clarify the *actual* problem.

### Goals

What goals, if achieved, would solve the problem?

The difference between the Problem and Goals are important. Tim provided further clarification:

> It’s important to note the distinction between problems and goals. Broadly
speaking, a problem impacts people _outside_ of the team. A goal impacts people _inside_ the team. This is not _strictly_ true, however, if we’re addressing problems for people on the team, we should explicitly state that. This distinction forces us to tie our solutions to people’s needs while still acknowledging that we can only address people’s needs to varying degrees.

For example, a business owner may want to understand how much they should spend on advertising. This may translate to a variety of goals such as, "allow users to determine the life time value of a customer," and, "create a workflow for determining profit."

### Design Approach

How will you go about achieving the goals? What's required? Why this approach?

### Alternate Designs

It’s not a tradeoff if you don’t have more than one option ([paraphrasing Rich Hickey][rich]). The first design may be the best, but there should be a discussion of tradeoffs and why one approach is better than the other.

### Constraints

What constraints surround the problem and proposed solution? Besides forcing us to think through the limits around the problem and solution, it helps to have this documented for the future when we're trying to understand why certain decisions were made.

### Risks

Thinking about risk is especially useful for communicating digestable information to stakeholders, helping them to make better decisions.

### Timeline

We found that, once we thought through designs, we were able to provide surprisingly accurate estimates. Even when we were significantly off in estimates, stakeholders weren't totally caught off guard if the risks were communicated.

## The REPL & Exploration

The point is not to set a hard rule of no typing until the design is complete. The REPL is a great tool for exploration, and we'd be foolish to throw it away. The key is to be clear and disciplined about what we are doing. Exploring, testing out ideas, etc. at the REPL **should be treated as input to the design process. It should not be considered part of the final solution**, which is the mistake often made. This requires discipline because code, once written, has the habit of hanging around and creeping into production.

## Summary

We weren't perfect when we started, and I'm still not disciplined enough in my practice. But this approach is amazingly helpful. Even when write ups were done only for individual work, with no intention of sharing, it made solving problems much easier.

Truthfully, this is hard. But the work has to be done, and in fact we do most of it already. The problem is we make implicit decisions (e.g. we accept risks without acknowledging them) and we simply "design" as we go.

Here's the secret: **By the time we sit down to type, most of the hard work should already be done.**

The exact sections and breakdowns aren't vital, but a process for systematically thinking through problems is required. Anything less, and I think we're just pretending when we call ourselves professional developers.

---
<br/> 
*Bonus: This approach is useful for thinking in general. Here's my thought process for this blog post.*

### Problem

Developers waste time solving problems that don't need to be solved and creating brittle solutions. If we were disciplined to think first, we'd provide better solutions for actual problems.

### Goals

* Effectively communicate the need for professional discipline.
* Provide an approach to force thinking about design before and apart from typing.

### Design Approach

Use the analogy of surgeons washing their hands:

* Professionals
* A good example of something we all do, yet not closely approaching the rigor of professionals

### Alternate Designs

I could use other professions like pilots. Hand washing is probably more relatable (and I cheated: I wrote most of the post before I wrote this section).

### Constraints

* I'm not a surgeon, so I'm depending on second-hand information
* A single blog post that is short enough for people to read

### Risks

It's just a blog so the price of failure is small.

**Risk:** My resources for surgeon behavior are wrong

**Mitigation:** Check a few different sources

**Risk:** Poor writing and communication skills

**Mitigation:** Have a few peers review my writing

### Timeline

I can knock this out in one sitting. Depending on feedback, could extend for a few days.

*Thanks to [Tim Pote][timp], [Caleb Phillips][calp], [Jack Mocherman][jackm], and [Joe Kane][joek] for reading rough drafts and providing valuable feedback.*

[sethg]: https://tim.blog/2018/11/05/the-tim-ferriss-show-transcripts-seth-godin/
[hand-scrub]: https://www.infectioncontroltoday.com/hand-hygiene/how-perform-surgical-hand-scrubs
[timp]: https://twitter.com/potetm
[rich]: https://github.com/matthiasn/talk-transcripts/blob/master/Hickey_Rich/HammockDrivenDev-mostly-text.md
[calp]: https://twitter.com/_calebp
[jackm]: https://twitter.com/jack_mocherman
[joek]: https://github.com/josephkane
