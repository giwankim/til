# 3. A Framework for System Design Interviews

## System Design Interview

### What is it?

System design interview simulates real-life problem solving where two co-workers collaborate on an ambiguous problem and come up with a solution that meets their goals. The problem is open-ended, and there is no perfect answer. The final design is less important compared to the work you put in the design process. This allows you to demonstrate your design skill, defend your design choices, and respond to feedback in a constructive manner.

An effective system design interview gives strong signals about a person's ability to collaborate, to work under pressure, and to resolve ambiguity constructively. The ability to ask good questions is also an essential skill.

### Red flags

Over-engineering delights in design purity and ignores tradeoffs. There is a compounding cost to over-engineered systems that ends up costing many companies dearly. Other red flags include narrow mindedness, stubbornness, etc.

## A 4-step process

### Step 1 - Understand the problem and establish design scope

Think deeply and ask questions to clarify requirements and assumptions.

Ask questions to understand the exact requirements.

- What specific features are we going to build?
- How many users does the product have?
- How fast does the company anticipate to scale up?
- What is the company's technology stack? What existing services might you leverage to simplify the design?

### Step 2 - Propose high-level design and get buy-in

In this step, we aim to develop a high-level design and reach an agreement with the interviewer on the design.

- Come up with an initial blueprint. Ask for feedback.
- Draw box diagrams with key components on the whiteboard or paper. They might include clients (mobile/web), APIs, web servers, data stores, cache, CDN, message queue, etc.
- Back-of-the-envelope calculations to evaluate if the blueprint fits the scale constraints. Think out loud. Communicate with your interviewer if back-of-the-envelope is necessary before diving into it.

Should we include API endpoints and database schema here? It depends on the size of the design problem. _Communicate with your interviewer._

### Step 3 - Design deep dive

At this step, you and your interviewer should have already achieved the following objectives:

- Agreed on the overall goals and feature scope
- Sketched out a high-level blueprint for the overall design
- Obtained feedback from your interviewer on the high-level design
- Had some initial ideas about areas to focus on the deep dive based on the interviewer's feedback

You shall work with the interviewer to identify and prioritize components in the architecture.

Time management is essential as it is easy to get carried away with minute details that do not demonstrate your abilities.

### Step 4 - Wrap up

In this final step, the interviewer might ask you a few follow-up questions or give you the freedom to discuss other additional points.

- Interviewer might want you to identify the system bottlenecks and discuss potential improvements.
- It could be useful to give the interviewer a recap of your design.
- Error cases (server failure, network loss, etc) are interesting to talk about.
- Operational issues are worth mentioning. How do you monitor metrics and error logs? How to roll out the system?
- How to handle the next scale curve?

## Dos

- Always ask for clarification.
- Understand the requirements of the problem.
- A solution designed to solve the problems of a young startup is different from that of an established company with millions of users.
- Let the interviewer know what you are thinking. Communicate with your interviewer.
- Suggest multiple approaches if possible.
- Once you agree with your interviewer on the blueprint, go into details on each component. Design the most critical components first.
- Bounce ideas off the interviewer.
- ___Never give up.___

## Don'ts

- Don't be unprepared for typical interview questions.
- Don't jump into a solution without clarifying the requirements and assumptions.
- Don't go into too much detail on a single component in the beginning. Give the high-level design first then drill down.
- If you get stuck, don't hesitate to ask for hints.
- Communicate. Don't think in silence.
- Ask for feedback early and often. Interview is not done until the interviewer says it is done.

## Time allocation on each step

The following is a rough guide on distributing your time in a 45-minute interview session.

1. Step 1 Understand the problem and establish design scope: 3 - 10 minutes
2. Step 2 Propose high-level design and get buy-in: 10 - 15 minutes
3. Step 3 Design deep dive: 10 - 25 minutes
4. Step 4 Wrap up: 3 - 5 minutes
