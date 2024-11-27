# Unit 9b: The Third Way of DevOps - Continuous Learning and Experimentation

## Behavioural Objectives

- [ ] **Describe** *how to learn as part of daily work.*
- [ ] **Describe** *how learning can be communicated throughout the organisation.*
- [ ] **Explain** *why time is required to enable organisational learning.*

## Enable and Inject Learning into Daily Work

We cannot predict the outcomes of our changes in a complex system, such as the software we build.  Therefore, we have to ensure learning is always occurring.

Key to enabling safe work in our complex system is self-diagnostics and self-improvement.  We must get better at diagnosing problems and we must get better at improving our approach to work.  Or, we must get better at detecting problems, solving these problems, and ensuring the effects of the solutions are available to all in the organisation.

An often cited example is the April 2011 outage of Amazon Web Service's US-EAST zone.  This took down multiple services, including Reddit and Quora.  Netflix didn't go down and appeared unaffected.  This is partially due to their architectural approach (micro-services), but also their culture.  Netflix used a tool called Chaos Monkey which simulated failures.  The engineering teams were constantly improving their failure response and had thus built-in resilience other services had not.

### Establish a Just Learning Culture

When failures occur, the response has to be seen as just.  This is considered a prerequisite for a learning culture.  If responses are unjust, investigations are impeded and fear replaces mindfulness in the engineers.  Slowly, organisations become more bureaucratic rather than careful, and an air of secrecy, evasion, and self-protection emerges.

When people feel safe in reporting mistakes they are willing to be held accountable.  They become enthused in helping avoid the same error in future.  If people are punished, they do not feel safe reporting a problem.  And it is likely to occur again.

To support a just, learning-based culture we will look at two techniques:

- Blameless post-mortems.
- Controlled introduction of failures.

### Schedule Blameless Post-Mortem Meetings after Accidents Occur

A post-mortem should be scheduled as-soon-as-possible after an incident.  Thereby we capture information while it is still fresh in people's mind.

A blameless post-mortem does the following (from The DevOps Handbook):

- Construct a timeline and gather details from multiple perspectives on failures, ensuring we don’t punish people for making mistakes.
- Empower all engineers to improve safety by allowing them to give detailed accounts of their contributions to failures.
- Enable and encourage people who do make mistakes to be the experts who educate the rest of the organization on how not to make them in the future.
- Accept that there is always a discretionary space where humans can decide to take action or not and that the judgment of those decisions lies in hindsight.
- Propose countermeasures to prevent a similar accident from happening in the future and ensure these countermeasures are recorded with a target date and an owner for follow-up.

The following stakeholders should be present:

- The people involved in decisions that may have contributed to the problem.
- The people who identified the problem.
- The people who responded to the problem.
- The people who diagnosed the problem.
- The people who were affected by the problem.
- And anyone else who is interested in attending the meeting.

### Publish Out Post-mortems as Widely as Possible

We need to reduce the stigma of failure: *Always fear regret more than failure*.

One technique is to hold failure parties.  These celebrate a rigorous, high-quality, scientific approach that fails to deliver the desired outcome.

### Decrease Incident Tolerances to Find Ever-weaker Failure Signals

People can become complacent over time.  As we become more efficient at solving problems, we have to decrease the problem signal we are responding to.  To do this, we must amplify weak failure signals.  Our telemetry can help us here.

Consider the 2003 Columbia space shuttle disaster.  The space shuttle exploded as it reentered Earth's atmosphere.  A piece of insulating foam broke off an external fuel tank during takeoff.  Prior to reentry, mid-level NASA engineers had reported the problem, but they were ignored.  The foam break had been observed on video during a post-launch review and the engineers had notified management.  But the engineers were told this was nothing new and foam breaks had happened before and never resulted in an accident.  It was considered a maintenance problem.  All seven crewmembers were killed.

An organisation can work in one of two ways:

- Standardised, where routine and processes govern everything.  Budgets and timelines are strictly managed.
- Experimental, where new information is evaluated every day.  The culture resembles an R&D lab.

Ideally, a company should work in an experimental model.  This will allow any information to be judged fairly.  Trust is a key aspect of this culture.

### Redefine Failure and Encourage Calculated Risk-taking

Again, always fear regret more than failure.  We must reinforce a culture that everyone feels comfortable to experiment in and will surface learning from failure.

### Inject Production Failures to Enable Resilience and Learning

Just as Netflix used Chaos Monkey, we must inject failures so we can test our response and resilience.  This forms part of our testing strategy.

### Institute Game Days to Rehearse Failures

Resilience engineering is defined as an exercise designed to increase resilience through large-scale fault injection across critical systems.

A Game Day is when teams simulate and rehearse accidents to practice resilience.  A serious event is scheduled (e.g., destruction of an entire data centre).  Teams are given time to prepare such as fixing points of failure and creating monitoring processes.

## Convert Local Discoveries into Global Improvements

### Use Chat Rooms and Chat Bots to Automate and Capture Organisational Knowledge

Chat rooms are quite common in modern development teams (e.g., Slack).  These chat rooms can have automation built in.  Rather than running a script from the command line, someone can send a message to a chat bot which will trigger the process.  This has some benefits (from The DevOps Handbook):

- Engineers on their first day of work could see what daily work looked like and how it was performed.
- People were more apt to ask for help when they saw others helping each other.
- Rapid organizational learning was enabled and accumulated.

In chat rooms, communication is public and recorded.  This enables organisational learning.  Email is private and thus cannot enable organisational learning easily.

### Automate Standardised Processes in Software for Reuse

Specifications and standards are often written in dense Word documents stored somewhere outside the development work.  This goes against our single repository of truth principle.  Therefore, engineers do not know where these documents are, or if they even exist.  This leads to quality problems in the products we build.

Putting these documents into our central code repository, and then building a tool to search them, is the solution.  Thus, engineers will have the information at their fingertips from the space in which they work.

### Create a Single, Shared Source Code Repository for Our Organisation

We discussed this at the start of the module, and have been living this idea throughout.  All our code, artefacts, configuration, and knowledge exists in one place: our GitHub repository.  We can go further and add tutorials, plugins, etc. in here.  The point is to be able to have a single place where we can rebuild everything.

### Spread Knowledge by Using Automated Tests as Documentation and Communities of Practice

We covered testing previously.  Our tests should provide documentation so that others can understand what we have built.  A good test tells us something about how the code works.

### Design for Operations Through Codified Non-functional Requirements

DevOps is about development and operations working together.  As developers, we should support the non-functional requirements that assist the operations team by having (from The DevOps Handbook):

- Sufficient production telemetry in our applications and environments.
- The ability to accurately track dependencies.
- Services that are resilient and degrade gracefully.
- Forward and backward compatibility between versions.
- The ability to archive data to manage the size of the production data set.
- The ability to easily search and understand log messages across services.
- The ability to trace requests from users through multiple services.
- Simple, centralized runtime configuration using feature flags and so forth.

### Build Reusable Operations User Stories into Development

Operations work should also exist in the backlog.  We should automate as much work as possible, and documenting this activity to better plan.  In the backlog, recurring operations work should have (from The DevOps Handbook):

- What work is required?
- Who is needed to perform it?
- What the steps to complete it are?
- etc.

### Ensure Technology Choices Help Achieve Organisational Goals

Any technology used should be reviewed regularly and systematically to discover which ones are causing a disproportionate amount of failure and unplanned work.  We need to identify the technologies that (from The DevOps Handbook):

- Impede or slow down the flow of work.
- Disproportionately create high levels of unplanned work.
- Disproportionately create large numbers of support requests.
- Are most inconsistent with our desired architectural outcomes (e.g . throughput, stability, security, reliability, business continuity).

By removing these problems we provide more time, resources, and focus on the technology that best helps achieve the goals of the organisation.

## Reserve Time to Create Organizational Learning and Improvement

An organisation should set time for learning.  A good technique is to use a "blitz" where improvement is the goal.  This is in addition to time reserved to pay off technical debt.

### Institutionalise Rituals to Pay Down Technical Debt

A blitz:

- Focuses on problems people care about.
- Is self-organised.
- Does no feature work.

The goal is experimentation, innovation, and learning but to do so to improve daily work and solve workarounds.  Playing with new technology for the sake of it is not beneficial.

Blitzes and hack weeks allow people to bring innovation to the work undertaken.  This builds pride and purpose, and we gain improvements from the time saved in the work undertaken.

### Enable Everyone to Teach and Learn

We must dedicate time for people to teach and learn.  Code reviews are one such example.  Don't just criticise, but mentor, coach and assist other team members.  Teaching others will actually improve your understanding and retention of knowledge.  It is a well-known fact.

### Share Your Experiences from DevOps Conferences

Or any conference.  Give a presentation.  Run a workshop.  Write a tutorial.  All of these help in growing organisational learning.

### Create Internal Consulting and Coaches to Spread Practices

Have people who are seen as experts and mentors.  Others can go to them for advice.  This will help spread ideas throughout the organisation as the coach trains others.

## Summary

- Described how to learn as part of daily work, such as post-mortems and increasing failure signals.
- Described how learning can be communicated throughout the organisation, such chat rooms and items on the backlog.
- Explained why time is required to enable organisational learning, such as enabling teaching and internal consulting work.

## Recommended Reading

We have effectively summarised The DevOps Handbook Part V – chapters 19 to 21.

![The DevOps Handbook](img/devops-book.jpg)