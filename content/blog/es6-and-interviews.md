+++
title = "ES6 and Interviews"
date = "2016-12-27T10:00:00Z"
author = "Wendell Adriel"
authorTwitter = "wendell_adriel"
tags = ["javascript", "front-end", "back-end"]
keywords = ["javascript", "interview", "career"]
description = "Let's see how ES6 features can help you get the job you want"
showFullContent = false
hideComments = false
+++

## Introduction

Hello to everyone, I’m here with an article about JavaScript, ES6 and interviews. I’ll talk a little about how ES6 features can help you get the job you want, so let’s get down to it!!!

## The interview

Imagine that you have an interview in a great company that uses JavaScript as the main stack (both Front-end and Back-end development). When you get there you have to pass by three interviews: a talk with the HR team, a talk with a Tech Lead and a JavaScript test to see your skills with it. The first interview was great and now you have to talk to a Tech Lead. In this talk you have to tell about your previous experiences, your skills and so on. At this point and so on, ES6 can help you a lot… You must be thinking, “WHY?” and this is what I’ll show.

## Talking about ES6

The Tech Lead can ask you about technical subjects, questions about code, best practices and a lot more. Showing that you know about new features in JavaScript is a great plus, because there are A LOT OF JAVASCRIPT DEVELOPERS OUT THERE, but the ones that knows how the ES6/ES7 features works and that knows how to work with them are ONLY A FEW. At this point, the Tech Lead must be impressed (or at least happy) to see that you’re up with the latest features in JavaScript and now is the time to test if you really know about what you’re talking about, because you know…

> “Talk is cheap. Show me the code.” — Linus Torvalds

## Getting into the code

At this point you’re excited, happy and nervous, because your interviews with the HR team and the Tech Lead were great and now you need to show what you really know! You enter the room for the JavaScript test thinking about HUGE PROBLEMS to solve, but they give you only one simple task:

> Create a function that receives a string like “[[[]]]” or “[][]][“ and returns true if the string is balanced or false if the string isn’t (to be balanced the string must have the same number of opening and closing brackets and they must be in the correct order). For example the first given string must return true and the second one must return false.

There are a LOT OF WAYS to do this, but think about this: if you show that you know how to use AT LEAST ONE ES6 FEATURE (and well used) you can really get the attention to you and possibly the job will be yours. So….what are you waiting for?!?!?! Let’s get down to it and show that you’re their best shot:

{{< code language="js" >}}
/**
 * PROBLEM:
 * Create a function that receives a string like "[[[]]]" or "[][]][" and returns true if the string is balanced
 * or false if the string isn't (to be balanced the string must have the same number of opening and closing brackets
 * and they must be in the correct order). For example the first given string must return true and the second one must
 * return false.
*/
const isStringBalanced = string => {
  return !string.split('').reduce((previous, char) => {
    if (previous < 0) return previous;
    if (char === '[') return ++previous;
    if (char === ']') return --previous;
    return previous;
  }, 0);
};

console.log(isStringBalanced('[[[]]]'));
console.log(isStringBalanced('[][]]['));
{{< /code >}}

I’m not saying that this is the best code, but I think that when they saw this, they will really like it and your chances of getting hired will raise a lot!

## Conclusion

Well, that’s it!!! I just wanted to show you that being updated with the latest releases of the technologies that are trending in the companies can give you a GREAT ADVANTAGE over other candidates and that sometimes the tests in some interviews are simple, but the way you resolve it MATTERS A LOT!!!

I hope that you liked this article and if you do, don’t forget to share this article with your friends!!! See ya! :wink:
