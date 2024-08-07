---
sidebar_position: 1
slug: /developers/building-an-extension
toc_min_heading_level: 2
toc_max_heading_level: 5
---

# Introduction

Welcome to the Fleetbase Extension Development Guide! In this document, we'll explore how to enhance the capabilities of the Fleetbase platform by creating extensions. Extensions are modular packages that enable new features, integrations, or complete modules, seamlessly expanding the functionality the Fleetbase OS.

An extension in Fleetbase is a combination of an [Ember Engine](https://ember-engines.com/), which handles frontend interactions, and a [Laravel package](https://laravel.com/docs/11.x/packages) that serves as the API backend. This dual structure allows for robust and scalable development.

To streamline your development process, you can use the Fleetbase CLI to scaffold new extensions. This tool helps you quickly set up the basic structure of your extension, allowing you to focus on customizing and extending its functionality.

By the end of this guide, you will learn the necessary steps to create, test, and deploy your own extensions, enhancing the Fleetbase ecosystem to meet your specific business needs. For detailed instructions on setting up and using the Fleetbase CLI, refer to our [CLI documentation](/extensions/cli).

## API Core Framework

Developing an extension with Fleetbase is facilitated by the Fleetbase Core API, which is essential for any extension. This API serves as the backbone of your extension, providing the necessary infrastructure for feature integration and extension scalability. 

By leveraging the Core API, developers can:

- **Register and schedule commands**: Automate tasks and processes within your extension.
- **Create expansions**: These are classes which allow you to extend functions on any other class in Fleetbase.
- **Manage middleware and notifications**: Enhance the functionality and responsiveness of your extension.
- **Set up routes and views**: Define the navigational structure and user interface of your extension.
- **Utilize core data models and utility functions**: These are designed to streamline the development process and ensure consistency across extensions.
- **Build resource APIs**: Use the composable REST API framework to rapidly develop resource-oriented services.

The Core API not only simplifies the creation of robust extensions but also ensures they integrate smoothly with the broader Fleetbase ecosystem. For more details on using the Core API, refer to [Fleetbase Core API documentation](https://github.com/fleetbase/core-api).

## Ember Core Framework

For the frontend, Fleetbase equips extension developers with two essential libraries: `fleetbase/ember-core` and `fleetbase/ember-ui`. These libraries are integral to initiating and running your Ember Engine for extensions.

- **`fleetbase/ember-core`**: Provides the foundational services, adapters, and utilities necessary for initializing and managing your extension's frontend. This includes integration capabilities with other extensions, ensuring a cohesive operation within the Fleetbase environment.
- **`fleetbase/ember-ui`**: Offers a suite of standardized UI components and styles, making it straightforward to develop attractive and user-friendly interfaces. This library ensures that the visual elements of your extension maintain consistency with the overall Fleetbase aesthetic, promoting a seamless user experience.

Together, these libraries form the core of the Ember framework within Fleetbase, enabling developers to efficiently create and deploy visually consistent and functionally robust frontend applications.

