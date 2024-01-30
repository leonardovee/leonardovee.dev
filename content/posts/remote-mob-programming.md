---
title: Remote Mob Programming 
subtitle: The magic of working from home, but not feeling alone. 
date: 2022-12-22
description: The magic of working from home, but not feeling alone. 
tags:
  - programming 
  - softskills
---

Recently I've had the opportunity to work on a project where my remote team used this methodology, here are some of my thoughts.

### What is Mob Programming?

Mob Programming is an approach to software development that consists of a whole team working on the same **feature**, at the same **time** and in the same **space**.

You can say is a Pair Programming session with more people.

In essence, **Remote** Mob Programming is a method of coding that unites remote teams and Mob Programming.

### How to do a Remote Mob Programming Session?

A session of Remote Mob Programming consists of two types of participants, the Typist, and the rest of the Mob.

The Typist takes care of the keyboard, this is by far the hardest position, the responsibility is to share the screen and be reactive to what the Mob tells you to do on the code, simple as that!

Why the Typist is the hardest position, you might ask me, it is because you can't code, you can't do anything on your own, and you HAVE to be reactive, otherwise, you no longer are the Typist, you are someone like the rest of the Mob, but with privileges to the code, and that's no fair to the rest of the Mob.

This type of division ensures that are no "protagonists" in a Mob session, no one will be the leader or even the decision maker, and everyone will have the same space and time to share ideas and discuss them.

The rest of the mob has the responsibility to guide the Typist, to ensure that their ideas are being transferred to the code properly.

Remote Mob Programming works around people's screen turns as the Typist and handing over the code after each turn. A good source control is highly recommended, and there are even special tools that help with the code handover, one that people recommended to us and helped a lot is the: [https://mob.sh/](https://mob.sh/)

With commands like `mob start` and `mob end` you can initiate and finish a Remote Mob Programming session with no issues:

```bash
$ mob start
  git fetch origin --prune
  git merge FETCH_HEAD --ff-only
> starting new session from origin/main
  git checkout -B mob/main origin/main
  git push --push-option ci.skip --no-verify --set-upstream origin mob/main
  git push --push-option ci.skip --no-verify --set-upstream origin mob/main
> you are on wip branch 'mob/main' (base branch 'main')
> It's now 12:33. Happy collaborating! :)

$ mob done
  git fetch origin --prune
  git push --no-verify origin mob/main
  git checkout main
  git merge origin/main --ff-only
  git merge --squash --ff mob/main
  git merge --squash --ff mob/main
  git branch -D mob/main
  git push --no-verify origin --delete mob/main
> nothing was done, so nothing to commit
```

Everyone works in the same branch and at the final of the mob session all commits are squashed to main, this tool works incredibly well and if you don't want to commit directly to main you can just open a pull/merge request of the created branch.

The tool even has an integration with its site that keeps track of the time spent by each one on the sessions: [https://timer.mob.sh](https://timer.mob.sh/)

![Image of the site that tracks the time, showing the spent time in a graph and also the timestamps of the participants.](/images/c319e941-8e4d-4603-9122-ab7fc1246a81.png)

### How was the experience?

Different is a good word to describe it.

Being the Typist is complicated because you're in a reactive position, you can't scroll freely through the code and open files at will because this will make the life of the rest of the Mob difficult.

Being on the rest of the Mob is also difficult, you have to correctly guide the Typist while getting on accord with the others, and many times you just take too long to get to a conclusion that makes the Typist stay idle for a long time.

Overall, the knowledge share is really good using this approach of software development, everyone working on the project is aware of the decisions taken and also can understand what that particular part of the software does.

And the productivity is not that good, some discussions take way too much time, and, if something occurs that it's not planned, like a bug introduced on the application while at the Mob, the debugging process can also take a significant amount of time, because you have to guide the Typist, and not just that, the Typist has just to what you tell him to.

I don't think that this is something that improves over time, people have different skills and familiarity with some aspects of software development, and in some cases, the Remote Mob Programming just doesn't fit that feature development.

### When to use Remote Mob Programming?

From my small experience, I can take three cases where Remote Mob Programming would be beneficial:

1. Whenever there is a knowledge gap on the team that you wanna fill
    
2. Whenever there are important decisions to be made in the feature of a software
    
3. Whenever there are big and complicated features to be implemented in the software (that you can't break into smaller features)
    

This is everything that I'd to say about Remote Mob Programming, for more information you can visit the following site that we used as reference: [https://www.remotemobprogramming.org/](https://www.remotemobprogramming.org/)

Thanks for reading this far! 👍
