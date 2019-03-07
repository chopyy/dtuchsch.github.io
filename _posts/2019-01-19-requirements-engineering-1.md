---
layout: post
title:  "Two cents on writing (automotive software) requirements"
date:   2019-02-10 21:02:00
categories: engineering automotive software
---

> Requirements are fundamental to software design, the resulting implementation and the tests for verification. Writing requirements of high quality is hard like writing clean code: It requires practice and experience. When writing requirements (for automotive embedded software) there are some rules of thumb I personally follow to maintain a certain level of quality for myself. This article shall give an overview over some rules I personally follow for writing requirements that bring value to both, the development team and stakeholders of a system / software.

## Think from the User's Perspective

As a first step: Try to think from the user's perspective when writing requirements. Users (customers, internal developers) care for a certain functionality and - in the majority of cases - want to have some feedback. When writing requirements always ask yourself: Does this really reflect the user's intended functionality?

## State "What the System Does"

Requirements state the "what the system does". A functional requirement states that there is a functionality to stop any robot's motion for instance. A requirement shall *not specify* "how" this certain requirement is going to be realized. The "how" is part of the design. Always ask yourself when writing functional requirements, if they already state a specific technical solution. If so, try to rewrite them in a way that follow the rule above (thinking from the user's perspective): Because the user does not care for the "how". The user cares for the "what".

### Examples

1. A customer owns a car with a cruise control system. The customer expects the car to maintain a configured speed, if the cruise control system is enabled. The "what the system does" is in this case: Maintain the configured speed. However, the customer *doesn't care how* this functionality (maintaining the speed) is going to be achieved. She / he doesn't care about sensors, actors, control loops and so on to achieve the *what*.

2. Your team develops a software component for communicating with an ultrasound sensor. This software component is used by other teams of your company. Users of the software component expect a functionality to receive distance samples with a certain period. The "what the system does" is in this case: Receive the distance samples with a given period. However, the users do not care how this is being achieved by using software to calculate the distance out of speed of sound, which includes the filtering etc.

## Traceability

It's essential that functional requirements are easy to test. For each individual requirement there shall be at least one test case that verifies the correct functionality. It helps to prove that the requirements really reflects what the user expects and shows fulfillment of each requirement. Always ask yourself: Is it possible to verify this requirement with an automated test?

If the requirement can't be verified in an automated test, it's not a good requirement to me.

## Adjectives to Follow: Unique, Unambiguous, Atomic

Each requirement shall be *atomic*, *unambiguous* and *unique*. 

* Unambiguous: There should be no room for interpretation.
* Atomic: If there's an *and* in the sentence it's a sign for combining two requirements in one sentence. Think about splitting them up.
* Unique: Requirements shall be unique in the sense, that there shall be no duplicates or intersections.

## Follow a Specified Pattern

Follow one defined sentence structure when writing requirements. This makes it easier to write the requirements and test cases for them. It also enables all team members to adhere to one consistent style. There's nothing more confusing than having different styles.

I follow the pattern of the The German Association of the Automotive Industry called VDA[^VDA]:

---|---
**[Condition]** | Time aspect. State of the system or the environment
**[Subject]** | The executing entity (e.g. system, component, operator)
**[Requirement Signal Word]** | shall / should
**[Action]** | Verbs
**[Object]** | The system part of the action

### Example 

---|---
**[Condition]** | If the temperature exceeds 20°C
**[Subject]** | the electronic control unit (-> who)
**[Requirement Signal Word]** | shall
**[Action]** | deactivate (-> how)
**[Object]** | the motor. (-> whom)

## Requirements Shall Bring Value

Writing down requirements shall not be some kind of *necessary evil*, only because processes like Automotive SPICE require this or to over-engineer software development. If developers and managers think about this being a necessary evil (and yes, I've experienced this mindset now multiple times) it will be hard times for everyone (stakeholders & developers). Writing down requirements in a well-formed manner brings *value* to your team and its intended users of a system / software.

Writing them down carefully considered brings clarity for everyone what the system / software shall provide. This is extremly helpful for new employees. It especially helps developers to stay focused and only design, implement and test what the user really needs. For the customers it's clear from the beginning what she / he can expect.

> Bottom-line: If done right, requirements engineering helps to prevent from additional costs and frustration in the development team.

## References

[^VDA]: Verbund der Automobilindustrie. Qualitätsmanagement in der Automobilindustrie
