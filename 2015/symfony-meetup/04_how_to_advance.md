# How to escape from legacy code 

- Full rewrite
- Page by page
- Microsevices
- Integrate components
- Bootstrap full Symfony next to legacy application

Note:
All are very big/wide subjects, won't cover all of them here

Mention will focus on Symfony components, common libraries and to bootstrap Symfony in a long run

^

## Full rewrite
- Not always possible
- Require good quality code or business logic experts
- Need business logic expert/knowledge

Note:
Time consuming- results visible in a long time period

^

## Page by page/set of features
- Touches many features at once - big risk of errors
- That feature/page needs to be closed for new functionality during the rewrite, hard to catch up

Note:
Separation by „user experience”
 
need business logic experts/knowledge
 
do not touch old code
 
hard at the beginning, easier later

^

## Microservices
- Extract features from the "internal" point of view
- Many examples - easier to apply generic logic
- Resource consuming
- Not always needed
- Communicate through general interface

Note:
Company point of view, features

Interact with application but only new code

Can live without some business logic, smaller chunks

Resource consuming, more complicated deployment, maintenance etc


^

## Integrate components
- Ability to jump to full Symfony
- Great features out of the box
- Follow good practices
- Symfony is cool!

Note:
Company point of view, features
 
similar to previous in terms of new code but interact with old logic/codebase

easy to roll back, on/off flags

Will focus on this

^

## Bootstrap full Symfony next to legacy application
- Next step after components
- Ability to switch gradually to full Symfony
- Symfony is cool!

Note:
Similar to components

Symfony is cool

Will focus on this

^

#What is the worst case scenario
- No long freezes = no rewrites
- Can not do page by page/feature by feature
- The only way to start moving to better solutions is to deploy them!

