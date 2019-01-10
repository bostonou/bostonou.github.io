---
title: How To Create Better Estimates
tags: estimates design hiring-career
---

*If you haven't read [Wash Your Hands][wash] and [Estimates Are About Communication][estimate], give those a read first and then come back to this one.*

[In a previous post][estimate], I discussed approaching estimates from the view of communication between stakeholders and developers. Recognizing that solves a lot of the pressure and contention around estimates, but there are some helpful steps in actually creating estimates.


### How to think about estimates

**An estimate is simply a design communicated.** The confidence and accuracy of our estimates are fully dependent on the accuracy and completeness of our designs.

If you let me create a fully complete design, i.e. the actual software, I can give you a estimate with almost 100% confidence and accuracy. I simply create the software, and then tell you how long it took. The only thing I can't guarantee is that I've recognized all of the relevant risks to the business. The further I get away from creating the software, the lower my confidence and accuracy.

### Steps to create useful estimates

#### 1. Figure out how long you have to create the estimate

There's a simple way to do this: just ask. When someone asks for an estimate, ask when they need it. Communicate that the longer they give you to create the estimate, the more accurate it will be. Every estimate need not take weeks or months to create, but similarly every estimate shouldn't be built on minutes of thinking.

Off the cuff estimates are often useful when talking to people who have little concept of how long software takes. When people ask how long something will take, their assumptions are frequently off by several orders of magnitude.

Our goal is to help provide the information people need. It's our job to understand what it is they actually need.

#### 2. Work as if you're building the software

Do the [initial design work][wash] as if you were going to build the software. Do not start with guessing how long everything will take. Estimates have become synonymous with "time estimates", but remember that we're trying to communicate more than just time and cost.

So go through the full design work, including the code/architectural designs and alternatives. Timebox the work by the constraints given in step 1.

#### 3. Evaluate the design

Asks questions of the design to determine the completeness:

* What are the business risks?
* What are the technical risks? 
* What areas need to be broken down farther?
* What areas are likely hiding more requirements?
* What are the unknowns? How impactful might they be on your estimate?

#### 4. Evaluate your confidence

Given all the work up to this step, you should have a general feel for your confidence levels. Explore your design and give some overall confidence levels to the appropriate portions of the design. This doesn't need to be precise. Certain parts of your design will be well defined and well understood, so your confidence will be high. Other parts will be vague and make you feel uneasy, so mark it as low confidence.

### It's ok to take time creating estimates

In the past, I've felt internal pressure to create estimates on the fly, no matter the context. That's normal to feel, and it's also wrong.

If the business is trying to plan a major initiative for the next six months, it's completely appropriate to take a day or week to think through the design. Off the cuff estimates are essentially off the cuff designs. You can't build a great business with only impromptu "planning" in the same way that you can't build great software with no up-front design.

---
<br />
If we take the time to systematically work through estimates, we can provide more than just time estimates built on rules like "add X% to every estimate". We can give stakeholders what they really need: insight into the time, costs, and risks, along with a better understanding of how reliable those insights are.

*Thanks to [Tim Pote][pote] for reading a rough draft and providing good feedback.*

[estimate]: /blog/estimates-are-about-communication.html
[wash]: /blog/wash-your-hands.html
[pote]: https://twitter.com/potetm
