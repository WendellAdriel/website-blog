+++
title = "From 'Highway to (callback) Hell' to 'Stairway to (async/await) Heaven'"
date = "2017-03-01T10:00:00Z"
author = "Wendell Adriel"
authorTwitter = "wendell_adriel"
tags = ["javascript", "front-end", "back-end"]
keywords = ["javascript", "promises", "async", "callback", "async await javascript", "javascript http calls"]
description = "Let's learn the new way of working with asynchronous code on JavaScript"
cover = "img/blog/callback_async_await.png"
showFullContent = false
hideComments = false
+++

## Introduction

Hello there, I’m back to talk a little more about JavaScript. This time I will show how JavaScript changed its way of working with asynchronous code. So…let’s get started!!!

## Callbacks

A (not so) long time ago, when we needed to work with asynchronous code, we used callbacks, at the time that was the best thing to do, it was like magic, we created functions that got executed after something was ready, THAT’S WONDERFUL isn’t it?

**NO!!!!**

I won’t say that’s the worst thing in the world, at the time it was a great thing, but after some time our code wasn’t so pretty anymore and we felt like we were on HELL! Let’s see below a simple example of what I’m talking about. Let’s use the PokeAPI to get some info. We need to make four requests:

1. First for the Pokémon info: http://pokeapi.co/api/v2/pokemon/1
2. Second we will make a request to get info about its first move
3. Third we will make a request to get info about the move machine
4. Fourth we will make a request to get info about the machine item

```js
(function () {
  const API_BASE_URL = 'https://pokeapi.co/api/v2'; 
  let pokemonInfo = null;
  let moveInfo = null;
  let machineInfo = null;
  let itemInfo = null;

  const pokemonXHR = new XMLHttpRequest();
  pokemonXHR.responseType = 'json';
  pokemonXHR.open('GET', `${API_BASE_URL}/pokemon/1`);
  pokemonXHR.send();
    
  pokemonXHR.onload = function () {
    pokemonInfo = this.response

    const moveXHR = new XMLHttpRequest();
    moveXHR.responseType = 'json';
    moveXHR.open('GET', pokemonInfo.moves[0].move.url);
    moveXHR.send();
  
    moveXHR.onload = function () {
      moveInfo = this.response;
      
      const machineXHR = new XMLHttpRequest();
      machineXHR.responseType = 'json';
      machineXHR.open('GET', moveInfo.machines[0].machine.url);
      machineXHR.send();
      
      machineXHR.onload = function () {
        machineInfo = this.response;
        
        const itemXHR = new XMLHttpRequest();
      	itemXHR.responseType = 'json';
      	itemXHR.open('GET', machineInfo.item.url);
      	itemXHR.send();
        
        itemXHR.onload = function () {
          itemInfo = this.response;
          
          console.log('Pokemon', pokemonInfo);
          console.log('Move', moveInfo);
          console.log('Machine', machineInfo);
          console.log('Item', itemInfo);
        }
      }
    }
  }
})();
```

As you can see, it’s a really simple code, yet it’s already getting messy with a lot of callbacks. Now imagine this situation on a large scale application… Isn’t a thing that we want to maintain for a long time, right?

## Promises

Callbacks had its time of glory, but after a lot of Callback Hells a ray of hope appeared: Promises, they came to solve our problems with (callback) hell. So the same code above using promises would be something like:

```js
(function () {
  const API_BASE_URL = 'https://pokeapi.co/api/v2';
  let pokemonInfo = null;
  let moveInfo = null;
  let machineInfo = null;
  let itemInfo = null;

  const showResults = () => {
    console.log('Pokemon', pokemonInfo);
    console.log('Move', moveInfo);
    console.log('Machine', machineInfo);
    console.log('Item', itemInfo);
  };

  const getItemInfo = response => {
    itemInfo = response;
    showResults();
  };

  const getMachineInfo = response => {
    machineInfo = response;

    fetch(machineInfo.item.url)
      .then(res => res.json())
      .then(getItemInfo)
      .catch(err => console.log(err));
  };

  const getMoveInfo = response => {
    moveInfo = response;

    fetch(moveInfo.machines[0].machine.url)
      .then(res => res.json())
      .then(getMachineInfo)
      .catch(err => console.log(err));
  };

  const getPokemonInfo = response => {
    pokemonInfo = response;

    fetch(pokemonInfo.moves[0].move.url)
      .then(res => res.json())
      .then(getMoveInfo)
      .catch(err => console.log(err));
  };

fetch(`${API_BASE_URL}/pokemon/1`)
  .then(res => res.json())
  .then(getPokemonInfo)
  .catch(err => console.log(err));
})();
```

As you can see, the code is much better visually when we use Promises, and that’s just only a simple example (and not using the real power of Promises), it’s just to show the difference of the written code. Now we don’t need to worry about Callback Hell anymore, but we still can improve our code.

## async/await

With promises our code is much prettier than before when we used callbacks, but the code could be a little more simpler, we could write less code and make the code even better visually. We can do that using the async/await feature. Let’s see how it works:

```js
const axios = require('axios');
const API_BASE_URL = 'https://pokeapi.co/api/v2';

let pokemonInfo;
let moveInfo;
let machineInfo;
let itemInfo;

function showResults () {
  return new Promise(resolve => {
    setTimeout(() => {
      console.log('Pokemon', pokemonInfo.data);
      console.log('Move', moveInfo.data);
      console.log('Machine', machineInfo.data);
      console.log('Item', itemInfo.data);
      resolve('Finished!!!');
    }, 2000);
  });
}

async function init () {
  try {
    pokemonInfo = await axios(`${API_BASE_URL}/pokemon/1`);
    moveInfo = await axios(pokemonInfo.data.moves[0].move.url);
    machineInfo = await axios(moveInfo.data.machines[0].machine.url);
    itemInfo = await axios(machineInfo.data.item.url);

	console.log('Building results report...');
	const message = await showResults();
	console.log(message);
  } catch (error) {
    console.log('ERROR', error);
  }
}

init();
```

Look at the code above… It’s AMAZING!!! We wrote just a few lines of code and we did more than we did with the codes that I showed before and the code is much more cleaner than the others!!!

## Conclusion

That’s it, I just wanted to show you guys the road that we all passed to get where we are now… JavaScript is an amazing language and it’s getting always better with new amazing features and we have to stop for a moment, read about these features and start using them to improve our code, our productivity and our products and services!

I hope that you liked this article and if you do, don’t forget to share this article with your friends!!! See ya! :wink:
