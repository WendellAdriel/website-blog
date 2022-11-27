+++
title = "Working With Multiple CSS Themes"
date = "2019-04-27T10:00:00Z"
author = "Wendell Adriel"
authorTwitter = "wendell_adriel"
tags = ["css", "front-end"]
keywords = ["css", "theming", "front-end"]
description = "Let's learn how to create and work with multiple themes in CSS"
showFullContent = false
hideComments = false
+++

## Introduction

In **[the first article of this series](https://wendelladriel.com/blog/theming-with-css-variables)** we saw how variables work on CSS and how we can use them to create themes for our projects. In this article, we will put this into practice creating some simple themes and seeing how to change between them.

## Creating the Default Theme

As we saw before, the `:root` selector can be used to store the global variables of our themes. To create a default theme for our app, we will define some variables in this selector that will be used by our elements:

{{< code language="css" >}}
:root {
  --global-bg-color: #fff;
  --global-font-family: Verdana, sans-serif;
  --global-text-color: #222;
  --global-link-color: blue;
  --global-link-hover: lightblue;
}
{{< /code >}}

In the code above we defined some variables that will be used as the base values for our theme. That way we can use these values to style our elements. Since we’re only working on an example, the number of variables we have are just a few, but in a large project, this list can be much bigger. Now, let’s use our variables:

{{< code language="css" >}}
html {
  background: var(--global-bg-color);
  font-family: var(--global-font-family);
  color: var(--global-text-color);
}
a {
  color: var(--global-link-color);
  text-decoration: none;
  font-weight: 700;
}
a:hover {
  color: var(--global-link-hover);
}
{{< /code >}}

In the code above we used our variables to control the background and text colors, the font and also the color of the links. If in the middle of our project we want to customize any of these values, we just need to change the value of the variable that we want and all the elements that use it will update, pretty simple. Now let’s create some elements to check our theme in action:

{{< code language="html" >}}
<!DOCTYPE html>
<html>
    <head>
        <title>Theming with CSS</title>
        <meta charset="UTF-8" />
        <style>
            <!-- STYLE HERE -->
        </style>
    </head>
    <body>
        <h1>Theming with CSS variables</h1>
        <p>
            Example to be used in an article at 
            <a href="https://wendelladriel.com" target="_blank">my personal blog</a>
        </p>
    </body>
</html>
{{< /code >}}


## Creating a Night Mode Theme

Now that we already have our default theme, we can work in a new theme for our app. A lot of sites have a **“Night Mode”** to make it easier to read during the night. Let’s create a theme for this.

There are some ways of working with themes, but my favorite is to use the `data-` attributes on **HTML**. These `data-` attributes are a convention used to define custom attributes for tags and to identify which theme we're using we will create a `data-theme` attribute. All the elements with this attribute and their children will be modified. Since our new theme will be for our Night Mode we will call it **_dark_**.

Since our elements are already using the variables that we created, to create a new theme we just need to update the values of the ones that we need to modify, that way the values that we change will overwrite the others and the ones we don’t change will be inherited from the `:root` element. We will use the attribute selector on **CSS** to apply our theme. Since our theme is a **Global Theme**, we will link it to the `<html>` tag:

{{< code language="css" >}}
html[data-theme='dark'] {
  --global-bg-color: #444;
  --global-text-color: #ddd;
  --global-link-color: yellow;
  --global-link-hover: lightyellow;
}
{{< /code >}}

In the code above we changed the colors for the background, text and links. Now we just need to put the `data-theme="dark"` attribute in our `<html>` tag.

## Letting the user switch themes

Now that we created two themes for our app, it would be interesting if we let the user switch between them right? It’s not something hard to do, let’s create this option. First, let’s remove the data-theme="dark" attribute from our <html> tag and create the element that will let the user switch themes when clicked.

{{< code language="html" >}}
<!DOCTYPE html>
<html>
    ...
    <body>
        <span id="theme-toggle" class="theme-toggle"></span>
        ...
    </body>
</html>
{{< /code >}}

Now let’s style this element, for that we will create two new variables. We will create this element as a fixed element, so the user can switch themes whenever he wants to. We will also assign different values for these variables when our **_dark_** theme is enabled, making that this element will also update its appearance:

{{< code language="css" >}}
:root {
  ...
  --global-theme-toggle-bg: #ccc;
  --global-theme-toggle-content: '🌞 THEME';
}
html[data-theme='dark'] {
  ...
  --global-theme-toggle-bg: #000;
  --global-theme-toggle-content: '🌝 THEME';
}
.theme-toggle {
  position: fixed;
  right: 20px;
  top: 20px;
  background: var(--global-theme-toggle-bg);
  padding: 10px;
  border-radius: 5px;
  font-size: 0.8rem;
  font-weight: 700;
  cursor: pointer;
}
.theme-toggle::after {
  content: var(--global-theme-toggle-content);
}
{{< /code >}}

Now that we have finished setting up the visual part of our component, let’s add the needed action to make that our theme will switch when the element is clicked:

{{< code language="js" >}}
function toggleTheme () {
  const htmlTag = document.getElementsByTagName('html')[0]
  if (htmlTag.hasAttribute('data-theme')) {
    htmlTag.removeAttribute('data-theme')
    return
  }
  htmlTag.setAttribute('data-theme', 'dark')
}
document
  .getElementById('theme-toggle')
  .addEventListener('click', toggleTheme);
{{< /code >}}

In the code above we created a function that will check if the `<html>` tag has a `data-theme` attribute and if so, it will remove it and if doesn't, it will add this attribute with the value **_dark_** to activate our Night Mode Theme. After that we create a listener in our element that when it gets clicked it will run the function that we just created.

## Improving the Theme Switching

Congrats for us! We already let that our users can switch the theme of our app, but we still have some little details that we can improve. For example, when we switch the theme, the update is made in a very sudden way, it would be interesting to make this update in a smoother way and we can do that easily using the `transition` rule from **CSS**, check how simple it is:

{{< code language="css" >}}
html {
  ...
  transition: all 1s;
}
a {
  ...
  transition: all 1s;
}
{{< /code >}}

And it’s done!!! See how now the transition between themes is being made much smoother and pleasing to the eye!!! But we can still improve something else. When the user enters in our app and change the theme and leave, we want that when he returns, the site will load the last chosen theme, for that we will use the `localStorage` to store this info. We will need to make a little change in our `toggleTheme` function and create one more function to help us:

{{< code language="js" >}}
function toggleTheme () {
  const htmlTag = document.getElementsByTagName('html')[0]
  if (htmlTag.hasAttribute('data-theme')) {
    htmlTag.removeAttribute('data-theme')
    return window.localStorage.removeItem('site-theme')
  }
  htmlTag.setAttribute('data-theme', 'dark')
  window.localStorage.setItem('site-theme', 'dark')
}

function applyInitialTheme () {
  const theme = window.localStorage.getItem('site-theme')
  if (theme !== null) {
    const htmlTag = document.getElementsByTagName('html')[0]
    htmlTag.setAttribute('data-theme', theme)
  }
}

applyInitialTheme();
document
  .getElementById('theme-toggle')
  .addEventListener('click', toggleTheme);
{{< /code >}}

That’s it, now we can offer a special experience for our users!!!

## Applying Themes in Specific Zones

We saw above how to create and switch between two **Global Themes**, that means that they’re applied in our application as a whole, but we can also apply themes to specific zones in our app. To show that, let’s create a theme that will change only the font and let’s apply that to some elements:

{{< code language="css" >}}
[data-theme='comic'] {
  --global-font-family: 'Comic Sans MS';
}
p, span {
  font-family: var(--global-font-family);
}
{{< /code >}}

{{< code language="html" >}}
<body>
    <span data-theme="comic" id="theme-toggle" class="theme-toggle"></span>
    <h1>Theming with CSS variables</h1>
    <p>
        Example to be used in an article at
        <a href="https://wendelladriel.com" target="_blank">my personal blog</a>
    </p>
    <p data-theme="comic">
        Example of a theme applied to specific parts of the page! This text has the "comic" theme!
    </p>
</body>
{{< /code >}}

## Conclusion

In our examples above, we worked with three simple themes, but in larger projects, the complexity of the themes can be much bigger. Thinking about that, I created a lib called **[CSS Theme Manager](https://github.com/WendellAdriel/css-theme-manager)** that helps in the management of themes based in CSS variables, check it out and if you find it interesting, use it in your projects!

I hope that you liked this article and if you do, don’t forget to share this article with your friends!!! See ya! :wink:
