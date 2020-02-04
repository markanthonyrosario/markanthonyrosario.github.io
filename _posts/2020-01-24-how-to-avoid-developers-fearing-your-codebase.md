---
layout: post
title: How to remove the fear when programmers are asked to work on your codebase
categories: [work ethics, programming]
tags: [productivity]
description: reducing muda in a software project
---

# How to remove the fear when programmers are asked to work on your codebase		
![nixcraft_codehandover.png](/assets/postimages/nixcraft_codehandover.png)


## Problems
* Sharing Projects/Codebases within a software development team is hard.
* Programmers sometimes outright say it can't be done.
* This affects project deadlines negatively.
* This is a type of `Muda`((https://en.wikipedia.org/wiki/Muda_(Japanese_term))) [Information Waste](https://markanthonyrosario.github.io/work%20ethics/2017/02/25/office-wastes-from-lean-perspective.html)  which is rework
  * the Author already created the codes, debugged it and made it run. 
  * then the next developer has to do it again because THERE IS NO AUTOMATION NOR DOCUMENTATION (see comic above)

## Proposed Solution
* INNERSOURCING - https://resources.github.com/whitepapers/introduction-to-innersource/
* Borrow proper `README` formats from opensource projects
  * there are many guides/templates available online [like this](https://gist.github.com/PurpleBooth/109311bb0361f32d87a2)
  * [github.com/PurpleBooth/README-template](https://gist.github.com/PurpleBooth/109311bb0361f32d87a2)
  * this removes the question of `WTH do i do with this repository?`
  * this removes the `muda` rework because a copy+paste guide is already available
  * this allows a new-developer-in-the-project to quickly start working on business requirements and not waste time on technical specifics that should've already been handled by the author/initial team who worked on the project.

## Quick Breakdown of the sections of the proposed README format
greatly inspired by https://gist.github.com/PurpleBooth/109311bb0361f32d87a2
* `Project Title` - a meaningful name that indicates the problem you are trying to solve
* `Getting Started` - any piece of context you wish to share to future maintaners
* `Prerequisites` - skills you need to have, tools you need to know and software needed installed
* `Installing` - how to get the project runnning~
* `Running Tests` - how to ensure that the project really works before others start working on it
* `Deployment` - how to release the finished project and some details where the application is actually in use right now
* `Built With` - tools/frameworks used, to quickly match people who could work on this project.
* `Contributing` - Coding standards / [Architecture Design Decisions](https://github.com/joelparkerhenderson/architecture_decision_record) so the codebase will retain [looking like it was written by a single person](https://www.youtube.com/watch?v=N9UPW-2wL5U) and therefore easier for future maintainers to understand.
  * https://github.com/joelparkerhenderson/architecture_decision_record
  * [Geek's Guide to leading teams - Pat Kua](https://www.youtube.com/watch?v=N9UPW-2wL5U)

## Proposed KPI for a maintanable Software Project
* Software Developers (Especially Tech Leads) metrics should include "how easy is it to onboard new developers on projects that I led"
  * this is in contrast to smartass developers who take pride in obfuscating their code because it makes them look smart.


From [Wikipedia]((https://en.wikipedia.org/wiki/Muda_(Japanese_term)))
> [`Muda`](https://en.wikipedia.org/wiki/Muda_(Japanese_term))  is a Japanese word meaning "futility; uselessness; wastefulness",[1] and is a key concept in lean process thinking, like the Toyota Production System (TPS) as one of the three types of deviation from optimal allocation of resources (the others being mura and muri).[2] Waste reduction is an effective way to increase profitability.
