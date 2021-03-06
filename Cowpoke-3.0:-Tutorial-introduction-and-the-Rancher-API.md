This series of articles (All starting with "Cowpoke 3") walk you through working with Rancher. It seeks to accomplish the following goals and objectives.
## Goals:
1. Gain an understanding of Rancher's core Resource and Process concepts.
2. Gain an understanding of Rancher's distributed microservice architecture.

## Objectives:
1. [Explore Rancher's API.](https://github.com/rancherio/rancher/wiki/Cowpoke-3:-Tutorial-introduction-and-the-Rancher-API#explore-ranchers-api) 
1. [[Modify an existing resource.|Cowpoke 3.1: Modifying an existing resource]]
1. [[Create a new resource.|Cowpoke 3.2: Creating a new resource]]
1. [[Add a new process handler to a resource.|Cowpoke 3.3: Add a new process handler to a resource]]
1. [[Create an external event handler for a resource.|Cowpoke 3.4: Create an external event handler for a resource]]
1. [[Interact with a resource in an existing agent.|Cowpoke 3.5: Interact with a resource in an existing agent]]

## Pre-requisites:
You've setup rancher according to [Cowpoke 1](https://github.com/rancher/rancher/wiki/Cowpoke-1%3A-Getting-Started-with-Rancher). We're going to be modifying the source code in a number of projects and running it.

## Explore Rancher's API.
Navigate to [http://localhost:8080/v1](http://localhost:8080/v1)

Everything in Rancher is a resource in this REST API. Even most of the metadata about resources is represented as resources in the API. The motivation is for the API to be completely consistent, discoverable and self-documenting.

To illustrate this point, consider the fact that when you hit the API in a browser, you are actually interacting with a javascript/html wrapper around the API. This wrapper is a generic client capable of presenting all of the Rancher API because of the consistency and discoverability of the API.

We accomplish this by adhering to a strict REST API specification that we've worked on over the past few years. It is required reading for understanding the guiding priniciples of the Rancher API: https://github.com/rancher/api-spec

**Next:** [[Cowpoke 3.1: Modifying an existing resource]]