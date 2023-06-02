+++
title = "Theming With CSS Variables"
date = "2019-04-25T10:00:00Z"
description = "Let's learn how to theme websites using CSS variables"
keywords = ["css", "theming", "front-end", "css themes", "css theming", "css variables"]
topics =["css", "front-end"]
+++

## Introduction

It doesn’t matter if we’re creating a website or an admin page or an app, we need to deliver a solid and consistent design always. We can’t have a software where on each page we use a different font or buttons of all colours and shapes. We need to define a pattern, not only because of the visual aspect but when we create a pattern of colours and layout, the users can learn and get used quickly with our product.

To make this process easier, some time ago we had to ask for the help of the CSS pre-processors to use features like variables that make our lives easier when it comes to theming. Gladly we don’t need that anymore because of CSS already let us work with variables natively and in this article, we will see how we can use this feature to create a simple, yet efficient design system for our projects.

## Declaring and using variables

To declare a CSS variable it’s super simple. The name of the variables **MUST** start with `--` and they are case-sensitive.

```css
.my-class {
  --font-size: 1rem;
}
```

The same way that’s easy to declare a variable, using it is also super simple. We just need to use the var() function. This function takes two parameters: the first one is the name of the variable that we want to get its value and the second one is an optional parameter with a fallback value if the given variable passed as the first parameter isn't found.

```css
.my-class p {
  font-size: var(--font-size, 0.8rem);
}
```

The variables on CSS inherit values, that means that if a value is not found in a given element, the parent element value will be used.

## Two layers theming

If in our project we group all variables in a single place (Global Variables), we create a single source of truth, but we lose the ability to modularize our theme and always when a dev needs to customize a component he will need to overwrite the CSS rules.

In the other hand, if we leave the variables scattered all around, we will create a modularized theme, but we lose the consistency between our components and if we need to make a general change, we will have a lot of work.

To solve this issue we will think about our theme having two layers, we will have the **Global Variables** and the **Modules Variables**. That way we can get the best of the two worlds.

## Global Variables

Global variables are generic variables that will be used to keep the consistency between all our components. Some examples of global variables are font, default font-size and color palette. It’s super simple to define global variables on CSS, we use the :root selector and inside it, we define our variables. It's not mandatory, but to make it simple to identify that a variable is global or not, I like to use the global- prefix, that way when I'm reading the code I can already know that certain value comes from a global variable.

```css
:root {
  --global-font-family: Verdana, sans-serif;
  --global-font-size: 0.8rem;
  --global-text-color: #222;
  --global-primary-title-size: 2rem;
  --global-color-primary: #88498f;
  --global-color-secondary: #779fa1;
  --global-color-warning: #e28413;
  --global-color-danger: #ff6542;
}
```

In the code above, we define some variables that will be used as base values in all our project: font, font size for texts and for main titles, font color for texts and a color palette with four different colors. Now we will use the global variables that we just defined:

```css
body {
  font-family: var(--global-font-family);
  font-size: var(--global-font-size);
  color: var(--global-text-color);
}

h1 {
  font-size: var(--global-primary-title-size);
}
```

In the code above we define the default font, font color and font size for all our product and we also define the font size for the main titles. If we need to change any of these values in the middle of our project, we just need to change the variables declaration with the new values and it’s done! That way we can create some sort of consistency in our product and also create a common reference point for devs and designers to work together in a quick and easy way.

## Modules Variables

These are variables with a restricted scope where it’s defined. Every module variable **MUST** be defined using the value from a global variable and we also need to provide a fallback value for the case that the module will be used in an environment that doesn’t provide global variables, it doesn’t break. It’s not mandatory, but to make it simple to identify that a variable is a module variable, I like to use the module- prefix, that way when I'm reading the code I can already know that certain value comes from a variable of the X module. Let's take as an example a module for a button:

```css
:root {
  ...
  --global-border-radius-sm: 3px;
  --global-text-color-light: #fff;
}

.btn {
  --btn-border-radius: var(--global-border-radius-sm);
  --btn-text-color: var(--global-text-color-light);
  border-radius: var(--btn-border-radius, 5px);
  color: var(--btn-text-color, #ddd);
}

.btn-primary {
  --btn-primary-bg: var(--global-color-primary);
  --btn-primary-border: var(--global-color-primary);
  background-color: var(--btn-primary-bg, #605770);
  border-color: var(--btn-primary-bg, #605770);
}

.btn-secondary {
  --btn-secondary-bg: var(--global-color-secondary);
  --btn-secondart-border: var(--global-color-secondary);
  background-color: var(--btn-secondary-bg, #7fc29b);
  border-color: var(--btn-secondary-bg, #7fc29b);
}
```

## Understanding the two layers theming

Ok, now that we already saw how to structure our theme, we need to understand the reason why this Two Layers Theming works. The way that we built our theme, we make independent modules, because if they are being used on a context where we have the global variables, they will inherit the values from these variables, but if it doesn’t find these variables, we also defined fallback values that will be used.

We also made that changes can be really easy to be done. For example if we want to change the primary color everywhere in our product, we just need to change the `--global-color-primary` global variable and all our modules, including the **btn** module will be affected. Now imagine that we want to change the font color of all our buttons, we can change the `--global-text-color-light` variable, but that will affect all the other modules, so we just need to change the `--btn-text-color` module variable that will affect only the **btn** module.

## Conclusion

We saw how to create a consistent and adaptable theme for our projects using only CSS. Using this Two Layers Theming approach, we created a better environment and experience for our teams of devs and designers. You can check an example of the code we created in this article in **[this Codepen](https://codepen.io/WendellAdriel/pen/QPxRjN)**

I hope that you liked this article and if you do, don’t forget to share this article with your friends!!! See ya! :wink:


