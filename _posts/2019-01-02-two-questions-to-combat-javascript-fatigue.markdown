---
title: Two Questions to Combat JavaScript Fatigue
tags: evaluation hiring-career
---

How do you think the average developer would feel if they were given the following technologies to learn?

* jQuery
* React
* Flexbox
* Lodash
* Webpack
* GraphQL
* NodeJS

Does that seem unreasonable or fatigue worthy? Each of those technologies requires significant work to understand and use, but I can almost guarantee that, if you're reading this, you are familiar with every single tech on that list.

The problem with that list is the work required to make it. Why jQuery and not Zepto or MooTools? Why React and not Angular or Elm? Underscore, Ramda, Lodash, or native? Webpack or... lots of stuff. When you add the difficulty of figuring out *what* to learn and the frustration of numerous false starts and dead ends, then, yes, that sounds very fatigue worthy.

The key to combatting JavaScript Fatigue (and the related Fear Of Missing Out, FOMO) is knowing what's worth learning and what's safe to ignore. And to know that, we need to get better at evaluating technology before investing significant time and energy.

## Two Questions For Evaluation

Evaluation is actually pretty simple and just comes down to two questions:

1. What am I being offered?
2. What am I being asked to give up?

We need to ask both of these questions, and we need to make sure that the value and cost are actually related to the problems we're trying to solve.

Some examples of different angles to approach these question:

#### Is this something new?

Is there something new, or is this just a repackaging of something else with the added opinions and preferences of a developer?

#### Does it solve a real problem?

Do I *really* have this problem, or does it just sound good? If a library offers a 50% speed increase and you can't precisely describe the portion of your app that suffers from performance problems and how the library would improve it, then the library doesn't solve a real problem for you. "X% faster" is a siren song that we need to learn to recognize as Not A Real Thing.

#### Is this problem solved elsewhere, in a more relevant or cheaper way?

"How do I get 80% of the juice with a 20% squeeze?" A very successful CEO I've worked for asks this all the time. Sometimes the answer is, "That's the wrong way to think about this," which he is fine with. But sometimes the answer is, "Throw some jQuery on it and move on," which I've had to learn to be fine with.

#### Is the value worth the cost?

We must think more about the cost of things. [A library that produces "clean code" may also slow down the UI by 2x][sablono-slow]. Is the cost worth it? Depends, but we should be asking the question.

#### Is the person getting the value or paying the cost actually someone else?

Sometimes the cost is paid by someone else. Are you forcing someone else to learn yet another thing? Does your choice make it easier or harder to hire in the future? Does it make your company harder or easier to sell?

#### What is the cost if I stay with my current solution?

Maybe something is working, but you're spending too much time in maintenance or you can't hire anyone because you rolled your own Wingdings language. Or maybe the current solution is just thrown together but working and you rarely have to touch it.

#### Is the cost or value spread over time?

Is spending a week up front to save a month over the next year a good decision? If you can afford the initial week and your project is going to be alive for a year, then of course. But I regularly see developers optimize for the first week of development and then pay X times over for those decisions over the life of the project. And on the flip side, we architect for NASA when we're building for little Sally's lemonade stand.

## Example: Evaluating React and Angular

If you were to evaluate React vs Angular, what would be your main takeaway? When I did this evaluation years ago and chose React, it came down to one single thing that I still hear people get wrong.

* *Assumed Value*: React is fast. *Actual Value?* React is fast enough.
* *Assumed Value*: It's supported by a big company. *Actual Value?* Valuable, but so is Angular.
* *Assumed Value*: It's simpler because all of your state is kept in one place. *Actual Value?* We've been doing this in ClojureScript forever, so I'm not gaining here.

The actual value, the real new thing is this: React turns your UI into a "pure" function of the state. That's it. Everything else is just developer preference and opinion.

> React's Main Value Proposition: `(ƒ application-state) => UI`

Changing the normal manipulation of the DOM into what I can treat as a pure function? Heck yeah, where do I sign up? Who else was offering this? No one that I knew of.

So when Angular 1 changes to Angular 2? Don't know, don't care. When Vue offers 30% speed increases? Not valuable enough for me to consider.

It's very quick for me to evaluate and ignore these new things as not worth my time. And when I do miss something that takes off, I spend time figuring out what value is there that I missed. Sometimes it's just popularity, sometimes there's actual value.

---
<br />
Developers need to get better at evaluating tools and technology. It's to our own benefit as we're bombarded with new things, and it's to the benefit of the people we work for when we wisely make choices they depend on. A few simple, direct questions would be an easy first step to getting there.

[sablono-slow]: https://kevinlynagh.com/notes/fast-cljs-react-templates/
