+++
title = "Structuring a Server-Side Application"
date = "2022-08-05T10:00:00Z"
description = "Let's learn how we can structure a server-side application in a simple way, while using a clean and efficient architecture that could be used for small to large applications"
keywords = ["back-end", "architecture", "server", "mvc", "modern server side", "api structure", "back-end structure", "back-end architecture"]
topics =["back-end", "architecture"]
+++

## Introduction

This is the English version of an article that I wrote in Portuguese **[here](https://dev.to/wendell_adriel/estruturando-uma-aplicacao-server-side-548b)**

In this article, I'll talk about how we can structure a server-side application in a simple way, while using a clean and efficient architecture that could be used for small to large applications.

This will be a "theoric" article, I won't show code and it won't be tied to a specific language, it's going to be an approach based on the design/architecture of the application, this way you can apply it to any language that you may know.

I hope that this article can help you!

## Once Upon a Time the MVC

Probably you heard about the **famous MVC**:

- M - Model - Data Layer
- V - View - Visual Layer
- C - Controller - Communication Layer

This is a software architecture heavily used some time ago and probably still used nowadays for simpler software.

In this architecture we have three different layers where the access to the data (Database) is on the **Data Layer**, that's accessed by the **Communication Layer** that's the layer responsible for receiving the requests from the **Visual Layer** where the user interacts with the software, doing the needed processing and returning the response back to the **Visual Layer**.

This is a simple and practical architecture, but with the advance of the software that started to grow in complexity, this architecture started to be obsolete because of the coupling of the code and the layers starting to have a lot of responsibilities.

## Evolving the Architecture of the Applications

Nowadays the Client-Side (Front-end) applications are complex as the Server-Side (Back-end) applications and can have different architectures and patterns to be created on a way to support small and simple, but also large and complex applications. Because of that, the **MVC** architecture can be broken down, having the **Visual Layer** as a totally different application with its own architecture. The focus of this article will be the architecture of a Server-Side application.

Removing the **Visual Layer** of our architecture, if we were going to follow the **MVC** architecture we would be left with only two layers: the **Data Layer** and the **Communication Layer**, but as I said before this type of architecture results in a coupled code, where we can have code duplication and a lot of responsibilities in a single layer.

## The New Server-Side Architecture

Since the two layers that we talked about early aren't enough to create a clean and easy-to-maintain application, the solution is to recreate the architecture of our server-side application in a way that gets cleaner and decoupled.

There are many books, courses and tutorials out there and also N types of architectures that are possible to follow, here I'll explain only one of those architectures but that has been working good for me and the projects that I've been working on in the last years.

The architecture that I've been using is composed of four layers:

- Communication Layer
- Validation Layer
- Service/Business Layer
- Data Layer

Below I'll explain briefly each one of them.

### Communication Layer

This is the simplest layer of the application and the one with fewer responsibilities, as the name already says, this is the communication layer between the user and the logic of our application. It's responsible only for the IO (Input & Output) of our application, this layer receives the data sent by the user (it could be through a CLI tool or a Web page or a Mobile App) and returns the result back to the user after going through the other layers.

### Validation Layer

This layer is responsible for validating the data received by the **Communication Layer**. And if you're asking the reason for separating those two layers, the answer is simple. Imagine that currently your application communicates with a Web page and all the validation is coupled with the HTTP request. Now you or your company decide to create a CLI tool for your application. Being your validation logic coupled with the HTTP request, now you'll have to recreate all the validation logic again. If you had separated that into a specific layer, you just needed to create a new **Communication Layer** that would be your CLI tool and the whole application would be working fine.

### Service/Business Layer

This is the most critical layer of our application, here lies all the business logic, the core of our application. This layer is responsible for getting the already validated data from the **Validation Layer**, getting the needed data, applying the needed logic and formatting the data to send back to the **Communication Layer**.

### Data Layer

This is the layer responsible for getting and saving data into the application's database(s). To have the separation between the business logic and the data itself, this should be the **ONLY** layer that accesses the Database(s), this way if any other layer needs to access this data, it should be done through this layer.

## Conclusion

In this article, we learned what's the **MVC** architecture, the reason that this architecture is obsolete nowadays and we say an example of an architecture for modern Server-Side applications that's simple but efficient to create clean and easy-to-maintain applications. This architecture can be used with any language and it's not only for Web applications but as well for APIs, CLIs, etc.

I hope that this article helped you to have a better understanding of how the architecture and the design of an application can impact A LOT in the final result of our software in order to create a clean, easy to maintain and evolve software.

I hope that you liked this article and if you do, don’t forget to share this article with your friends!!! See ya! :wink:
