# Serverless Architecture

## Introduction

This document captures the general architecture of the MCPTT app, including design decisions, architecture overview, and a brief overview to each architectural component.

There are various scenarios for a push-to-talk (PTT) application, as specified by ETSI (**TODO**: cite the ETSI document and enumerate the various scenarios, mentioning which one is used in our case)

## Design Constraints

The timing for mission-critical systems is defined in (**TODO**: the KPI doc on Gitlab -- 600ms from ingress to egress). Therefore we need a fast, scalable, and responsive design. The following design considerations are introduced to address the aforementioned KPIs.

## Design Consideration

- scaling each component independently. --> microservice architectural pattern is used to address this constraint.
- developing each component independently, seperate team, framework or programming language --> microservice
- loosly couple. --> ingress and egress proxies are used to make the internal components independent of the protocol, which is currently SIP.
- low latency.

## Architecture Overview
In the following architecture we use gateway pattern to seperate internal message format from SIP message format. The `Request Proxy` convert SIP requests to protobuf messages and all internall components work with protobuf messages. Protocol buffer have libraries in many languages so we can use any programming language for each component.

For storing the state we are going to use `Hazelcast` data grid. Hazelcast is an in-memory data grid and have libraries for different programming languages. By using Hazelcast we can create stateless handlers and gain more scalabilty.

An overview to the MCPTT app's architecture is depicted in the figure below. It runs on top of OpenFaaS.

![overview](./img/mcptt-overview.png)

The MCPTT box in the figure above can be further broken down into the components depicted in the following figure.

![architecture](./img/serverless_arch.png)

In general, for the MCPTT to work correctly, several communication links with various internal components of the IMS network may be needed. In the above diagram, HSS is a representative of such components. Hence, communication with other components may also be required that is not depicted in the figure.

To minimize the message passing overhead, natty (**TODO**: is 'natty' the right name? link to the library's page) is used, which according to the developer (**TODO**: cite if public correspondence) has 1 to 10 ms delay in processing and relaying a message, depending on the language and run-time used.

## Architecture Components

## Why OpenFaaS?

**TODO**: elaborate the reason: the ease of debugging and higher targeted scalability (scale only the bottleneck func) compared to containers. + briefly saying what is openfaas

## Why Data Grid?

**TODO**: elaborate the reason: micro components should be fail-safe => should not keep state in memory => disk should be used but DBMSes are slow => data grid + briefly saying what is data grid

## Request Proxy
This component receive requests from IMS. After parsing the request it serialized the request to protobuf message and call appropriated function via `Function Gateway`.

## Function Gateway
This components acts as a gateway for available functions in OpenFaaS infrastructure. Read official documents about this component [here](https://github.com/openfaas/faas/tree/master/gateway)

**TODO**: elaborate the following: any function needs to be called via Function Gateway, as required by OpenFaaS. Therefore there's an arrow from Function Gateway to all other components (why not Media Handler??). the dashed arrow denotes that a component needs to call another's component's functions indirectly via Function Gateway.

## Register Handler
This component is a depoyed function in OpenFaaS that handles UE registerations. `Function Gateway` call this function on receving REGISTER request from `Request Proxy`.

This component notify `Group Handler` about new user registeration.

## Group Handler
This component is a deployed function in OpenFaaS that handles user groups; that is, groups of people that want to have a PTT session with each other. User can create and modify their groups.

## Session Handler
This component is a depoyed function in OpenFaaS that handles MCPTT session. This component get users in a given group from `Group Handler` and invite them to an MCPTT session. The session consists of the people from a Group, that are currently available and have already joined the PTT session (a Session is a subset of Group).

Session Handler calls Group Handler functions indirectly by asking Function Gateway for an interface to the requested functions.

This component is also responsible for managing MCPTT sessions.

## Response Handler
This component is a depoyed function in OpenFaaS that delivers protobuf messages to the `Respose Proxy`. 

## Response Proxy
This component received the respose in protobuf format and convert it to appropriated SIP reponse and deliver the message to the IMS core.

## Media Handler
This component is a depoyed function in OpenFaaS and manage `MDF`.

## MDF
This component receives RTP packet from senders and relay them to other clients.
