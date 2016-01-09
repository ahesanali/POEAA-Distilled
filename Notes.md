# PATTERNS OF ENTERPRISE APPLICATION ARCHITECTURE

# Introduction

## Thinking About Performance
* Response Time
* Responsiveness
* Latency
* Throughput
* Load
* Load Sensitivity
* Degradation
* Efficiency
* Capacity
* Scalability
	* Vertical Scalability - Scaling Up
	* Horizontal Scalability - Scaling Out

## The Structure of Patterns
* Name
* Intent
* Sketch
* Motivating Problem
* How It Works
* When To Use It (When Not To Use It)
* Further Reading

# Chapter 1 - Layering
## Layering Benefits and Downsides
### Benefits
* Understand a layer independent of other
* Substitute layers - plug and play
* Minimize dependencies between layers
* Standardization

### Downsides
* Not everything gets abstracted, sometimes you have cascading changes
* Can harm performance

## The Evolution of Enterprise Applications
Mainframes => 2 Layers (Desktop Client, Server {DB}) => 3 Layers (Desktop Client, Domain Logic, {DB} Server) => 3 Layers (Web Interface, Domain Logic, {DB} Server)

## The Three Pricipal Layers
* Presentation : External interface for a service your system offers to an user or a client program
	* Web Client
	* Rich Client (Windows Based Application Clients)
	* Command Line or Text Based Menu System
* Domain Logic
	* A.K.A Business Logic
	* Calculation, Validation, Processing, etc.	
	* Sometimes the layers are arranged such that, domain logic completely hides data source from presentation.
* Data Source : Is the interface to things that provide a service to you
	* Database (SQL / NoSQL)
	* Transaction Monitors
	* Other Applications
	* Messaging Systems

Note on Modern Architectures: Latest systems are developed such that presentation layer apps (desktop, laptop and mobile devices {tablets, phones and wearables}) talk to backend services which inturn use polyglot persistence.

### Separating Concerns
Level of sepration purely depends on complexity and other requirements, but it is good to have some amount of separation at least at the subroutine level.

### Direction of Dependency
* Presentation -> Domain Objects -> Data Source
* Identifying and seprating code across layers - If there is some functionality getting duplicated in a higher layer, that probably belongs to lower layers and it better abstracted as part of the lower layer. e.g. Adding a command line interace to a web application, if there is significant amount of code getting duplicated in order to do this, that's a sign where domain logic has leaked into presentation.

## Choosing Where to Run Your Layers
* Decision is whether to run on a client, or on desktops, or on servers.
* Running of server and having a simple HTML client has its own benefits.
	* Easy to upgrade or fix since it's in a limited amount of places.
	* No need to worry about deploying to desktops and keeping them in sync.
	* Don't have to worry about compatibility with other desktop software.
* Running on client provides some benefits as well	
	* Responsiveness
	* Disconnected / offline capabilities (now HTML5 has offline capabilities)
* Considering options layer by layer:
	* Data source typically runs on a server, that said, for providing disconnected capabilities, we need to have some amount of data store capabilities on the client and have a way to synchronize between offline store and data source server.
	* Presentation - This depends purely on the type of client you decide upon. Web clients vs Rich Client. As per latest trends (2016) we have plethora of devices interacting with backend services. We also have Single Page Application with the recent avatar of JavaScript everywhere. Instead of relying in servers rendering the presentation, it is handled elegantly on the clientside by JS MV* Frameworks and Libraries. Thus the presentation, even if it is a web client, can run on both client and server. Rich clients as the name implies runs on client and leaves us open to challenges like version management, compatibility amidst other issues. However we have another new ecosystem of clients -> "Apps" which again get classified as (Native, Web and Hybrid) running on mobile devices like mobiles, tablets, wearables and even automobiles. Hence take into consideration the requirements putforth by business and their target audience / clients before deciding on the presentation.
	* Domain Logic - Best run on servers for reasons mentioned above as in data sources. However, a part of it could be on client for responsiveness or disconnected use.

Note - Don't try to separate the layers into discrete processes unless you absolutely need to as doing that will both degrade performance and add complexity.

# Chapter 2 - Organizing Domain Logic
* Three main patterns for organizing domain logic.
	* Transaction Script
	* Domain Model
	* Table Module

## Transaction Script
*  Script for an action or business transaction
* The disadvantages are more than the advantages like simplicity, ease of handling transaction boundaries and being similar to procedural model easily understood by developers.
* The code becomes an entangled mess as complexity increases.

## Domain Model
* Provides an Object Oriented way to handling domain logic.
* Build a model of the domain - validations and calculations are part of domain objects.
* Slightly hard to wrap your head if you are new, but once you make the paradigm shift and start using this, there is no going back!
* Handles increasingly complex logic in a well organized way.
* You still have to deal with database mappings - richness of your domain model is directly proportional to complexity of you database mapping (With the advent of NoSQL are there ways to handle this complexity?)
* A sophisticated data source layer is much like a fixed cost, it takes fair amount of time to build one or money to buy one.

## Table Module
* Important difference between this and Domain Model - In Domain Model, there is one instance per record in the database, whereas in case of Table Module there is only one instance per table - it is designed to work with a Record Set.
* Middle ground between Transaction Script and Domain Model
* Biggest Advantage - Fits into the rest of the architecture. GUI environments are build to work on the results of a SQL query organized in a Record Set.

## Making a Choice
Making a choice depends on lots of factors:
* If the complexity of the domain logic is more prefer using Domain Model. Identifying this complexity comes with experience working in multiple domains and also expertise in the given domain.
* Team's knowledge level and adapting capabilities to a great extent decides the choice. If the team makes the paradigm shift towards Domain Model with ease go for it!
* The choice is never cast on stone, only that it takes a while and it is tricky to refactor once the choice is made.
* NOTE: These patterns are not mutually exclusive choices.

## Service Layer
* Common approach to split the domain layer into two - Service Layer placed over an underlying Domain Model or Table Module. (Domain Layer using only Transaction Script doesn't warrant a separate Service Layer)
* Presentation Layer talk to this Service Layer and it acts like an API.
* Good spot for transaction control and security. (Example: Attributes in .NET used for describing transactional and security characteristics.)
* Key decision: How to much behavior to put in Service Layer?
* Typically oriented around use cases.
* Another extreme - most buiness logic is present inside Transaction Scripts inside the Service Layer. The underlying domain objects are very simple; if it's a Domain Model it will be one to one with the database and can thus use a simple data source layer such as Active Record.
* Midway - controller-entity style - have logic that is particular to a single transaction or use case placed in Transaction Scripts which are commonly referred to as controllers or services. Behavior that is used in more than one use case goes on the domain objects, which are called entities.
* If you are using Domain Model - Make It Dominant!
* Better to have thinnest Service Layer (OTOH Randy Stafford has seen success with richer Service Layer.)