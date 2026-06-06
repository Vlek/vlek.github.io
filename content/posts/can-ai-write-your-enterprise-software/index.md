---
title: "Can AI Write Your Enterprise Software?"
date: 2026-03-18T16:17:00-07:00
description: ""
tags: ["ai", "software development"]
draft: true
---

## Introduction

AI is a massive elephant in the room for software developers. Amidst the
constant flow of news articles from AI model CEOs saying that software
development is a solved problem and that most or all development will be done
using AI by this year or next, it is hard not to worry about what my job might
look like in five to ten years if I even have one.

On the one hand, I am dealing with my personal bias. I have dedicated a good
portion of my life at this point learning the trade of software development, and
it is difficult to hear that I may have wasted thousands of hours working
towards a goal that no longer makes sense. For me, it is more than simply losing
a job. The prospect of 'artisanal coding' becoming antiquated in favor of
AI-generated software is an identity crisis.

On the other hand, for all the bluster and the hype surrounding AI, I have
what I believe are credible and serious concerns.

## The End Goal of Software Development

In order to continue, we need to answer a more philosophical question first:
"What are we even trying to do here?"

The AI enthusiast would answer that we have business functionality requirements
that need to be met. How we get there is merely a side effect of a previous
process where those things mattered. What matters now is getting an output,
testing it, and ensuring that it meets the specifications. In this way,
development time is cut down from days or weeks to tens of minutes as an agentic
AI session walks through a requirements Markdown document and talks about the
steps it is taking to create the software project. It is easy to see in this way
how AI could save considerable time.

## The Moving Target of Software

If the end goal is to satisfy the current needs of the business, AI seems to do
the trick. The problem though is that software is not a static thing. New
features and bug fixes are inevitable during the lifetime of a piece of
software. For AI to be used to meet the new goals, it would either need to
attempt an iterative approach, i.e., modifying the current implementation that
it generated to add in those new features, or create a new solution. It is
impossible from the outside without knowledge of the inner-workings of the
generated code to know which to do in a nontrivial case. One could have both
solutions made and test both, ask AI to decide, or have a human-being do the
review at that point. The process though gets much murkier than it would
otherwise if using current development approaches.

## Software Is Rarely Siloed

For a mature enterprise environment, bug fixes and feature additions to a piece
of software is rarely the bottleneck. The main issue comes with the
insurmountable mountain of regression impacts that can arise from touching even
a seemingly small part of code as multiple pieces of software interact with one
another.

I personally love to write utility packages. One of my favorite is a wrapper on
top of a C# HTTP retry library, Polly. It has a bunch of goodies baked in that
make it easy to add to projects in a way that sets a department-wide standard
for how retries and authentication are handled. I tried recently to add
additional optional features to the helper methods that I had generated, but I
was told to hold off on those changes for a bit as the regression impact was
fairly large and growing. To my happy surprise, it was being used across nearly
ten of our processes. Several were under active development with feature
additions. Any change would impact their work as our pipelines complain about
outdated dependency versions. So the code change was simple, but it had to wait
until next sprint to see our development branch as it would step on the toes of
other efforts actively in flight.

Service APIs are also a regression nightmare sometimes. Having one or more
upstream services trying to communicate with an API that talks to downstream
systems necessitates considerable testing when changed. There have been multiple
times that I have been told to let sleeping dogs lie in terms of code I did not
like as otherwise the regression impact would keep the QA team busy for weeks.

## Quality Assurance Quagmire

This all seems to create a giant quality assurance quagmire. AI code generation
tools are sold as being able to meet or exceed human-level development and
generate enterprise level software. But hallucinations exist and cannot be
removed. So, while CEOs are drooling over the prospect of reduced headcounts
with greater output, the reverse needs to be true. The resource allocation at
the very least moves away from greater number of developers and a bigger QA team
then must take over the burden of ensuring correctness.

However, this goes against decades of development process research that looks at
the cost of bugs. A developer catching a bug and resolving it at the development
stage is less costly than a QA person. A QA person less costly than it being
found during user acceptance testing. A UAT bug being less costly than a
production fix. By removing development oversight of each line of code, the
ability for developers to catch and fix bugs is removed.

## What AI-Generated Code Really Is

This all helps us understand the artifacts that are generated by AI. We are
essentially pulling a lever on a slot machine and a randomized, probabilistic
output is created. 

In one-off cases, this is fine. When you need to trust something to run in
production and be able to make incremental changes over time, it becomes a
can of worms as each new iteration can break existing functionality in new and
strange ways that human developers would not normally do.

I am worried that human QA teams will not be prepared for the ways that AI code
breaks. Weird issues will slip through that they would not think to even test.

## Ownership of AI Code

At the end of the day, who takes ownership when AI code does have problems in
production? If I am told to use AI tools to generate code and that code reviews
are not necessary. Does it fall on the QA team? Is anyone going to be happy with
this?

I have also told my manager we can either use AI to generate code, generate unit
tests, but no one is going to be okay if I use AI to generate both and something
goes wrong.

If I ship something, I am putting my personal stamp of approval on it. I have
end users that I have created a level of trust with over a decade that I could
squander in a very short time if I catastrophically mess something up while
doing what I am told to do. I cannot in good conscience do this. 

## Conclusion

So it feels like higher-ups were fed product demos that create nightmare
scenarios once rubber meets the road. They fail to consider the moving target
that software aims to hit over time, and are attempting to make a portion of the
software development process trivially easy that further complicates the
essential portions of the development process. Development was never the
bottleneck. Getting users to state what they need, ensuring that they are
comfortable with outputs they are getting once development is completed, and
going through the arduous but important steps to promote code to production take
considerably more time.

If we were to wrap up what I believe we are trying to do by creating enterprise
software, we are trying to write a piece of code that meets their current needs,
is reasonably extendable to have additional features added, and relies on good
fundamentals, frameworks, and utilities that mature over time in order to take
advantage of what we learn.
