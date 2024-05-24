## Document Control
- **Version:** 1.0
- **Authors:** Vijay Nair
- **Date:** 05/22/2024
- **Approval:** Chandra S
- **Change History:** Initial Version of the WMS iHub Proposal

## Table of Contents
1. Executive Summary
2. Requirements Summary
3. High-Level Solution Design
4. Service Design
5. Component Architecture
6. Security Architecture
7. Infrastructure Requirements
8. Non-Functional Requirements

## 1. Executive Summary

The traditional model for customers wanting to integrate with our WMS offerings have been to adopt a self-managed approach. The customers are responsible for building out connectors, transform the payloads expected by our APIs and monitor/observe them utilizing custom build tools. As our services mature, there is a growing demand by the customer base to move these capabilities to a fully managed integration platform service provided out of the box by the WMS suite. With this approach, customers would be able to reduce costs of building/operating their own tools, utilize a modern  platform to serve their integration needs and help us elevate the overall customer experience of our products.

This document presents the solution architecture for WMS iHub (Integration Hub), a proposed new capability within the WMS Suite that will cater to the business/technical requirements.

## 2. Requirements Summary
Business Requirements
* Self-service tool accessible by customer's staff
* UI console driven administration and configuration
* Ability to monitor, observe and report via console.

Technical Requirements
* Support for Enterprise Integration Patterns (Channels / Protocols)
* Multi-Tenant service
* Configuration driven design
* Catalogue loading/definition
* Simple/Complex Data Type transformations
* Resilient (Retries/Back Pressure/Stateless)
* Horizontally scaleable
* HTTP(s) Protocol support
* Cost Efficient

## 3. High-Level Solution Design

iHub will be a stand-alone application consisting of a set of services that will satisfy the business/technical requirements laid out above. 

* iHub can be deployed as a SaaS service but also can be deployed within a customer's DC if needed.
* It will have a browser based console exposed to the customer's administrators to help them setup/deploy and monitor integrations. These integrations will be stored using a metadata based model to ensure maximum flexibility.
* iHub will expose inbound APIs on which ERP systems can send their custom payloads. These payloads will be transformed to WMS formats based upon the configuration setup done
* iHub will call WMS APIs with the transformed data, collect responses and send it back to the ERP system with appropriate status.
* iHUb will log all execution activity to enable customers to monitor and act upon integration outcomes i.e. success/failures.

  A pictorial description of the high-level solution context is presented below.

![Vijay Nair's landscape - Context Diagram (Latest) (1)](https://github.com/practicalvj/sadexercise/assets/122186968/b14849f4-f783-4dfe-a5c4-cbd62fbd9b12)

## 4. Service Design

The iHub application consists of 4 main services

* iHub Studio Service -> Provides the capability to define and deploy integrations as metadata 
* iHub Runtime Service -> Provides the capability to execute the integrations
* iHub Metadata Cache -> Provides the capability to store integration metadata as a cache
* iHub Reporting Service -> Provides the capability to monitor/observe and report on integration executions.

A pictorial description of the service design is depicted below.

![image](https://github.com/practicalvj/sadexercise/assets/122186968/033301b1-222e-4ea7-8939-fd5d8d570bb5)

**_iHub Studio Service_**

* The Studio Service provides the core metamodel for the integration platform. The metadata consists of a set of entities that enable the definition of integration services. These entities include - Channels, Routes and Transformation (see component architecture below for more details).
* The metadata is modeled based on Apache Camel's DSL - an enterprise grade integration framework.
* Customers utilize the Studio service console as a canvas to define the metadata as per their requirements. They will also be able to version and activate integrations.
* The Service consists of UI and Backend services

| Choice of Technology    | Layer | Reasoning |
| -------- | ------- | -------- |
| React | Front End| Enterprise Standard |
| Spring Boot | Backend | Enterprise Standard |
| Postgres | Datastore | Enterprise Standard |
| Apache Camel | Metadata Model  | Provides a complete model for defining enterprise integration patterns. Open source based on a permissive license enabling us to rollout this capability with lower costs. Alternatives investigated included Spring Integration which was based on an XML based DSL that resulted in it not being chosen.|
| jslt | Transformation Layer | Json Query and Transformation Language. Required for payload transformation between customer's formats to our formats and compatible with Apache Camel |


**_iHub Metadata Cache_**

* The Metadata cache serves as an in-memory representation of the meta data.
* As customers define and deploy integrations, the cache will be reflected to update the latest versions.
  
| Choice of Technology    | Layer | Reasoning |
| -------- | ------- | -------- |
| Redis | Cache | Enterprise Standard |
  

**_iHub Runtime Service_**

* The runtime service acts as the execution engine for the iHub Application
* As requests come in to satisfy a particular integration request, it loads the corresponding integration model and executes it.
* During the execution of a specific integration request, it is responsible for ensuring connectivity, transformation and logging of the requests.
  
| Choice of Technology    | Layer | Reasoning |
| -------- | ------- | -------- |
| Spring Boot | Backend | Enterprise Standard |
| Postgres | Datastore | Enterprise Standard |
| Apache Camel | Metadata Model  | As stated above|

**_iHub Reporting Service_**

* The reporting service provides insights into the execution log of integration requests
* Utilizing the reporting service, customers can get data about completed/stalled/non-performant executions and action accordingly

| Choice of Technology    | Layer | Reasoning |
| -------- | ------- | -------- |
| Spring Boot | Backend | Enterprise Standard |
| Postgres | Datastore | Enterprise Standard |
| Apache Camel | Metadata Model  | As stated above|

## 5. Component Architecture

**_iHub Studio Service_**

The Studio Service consists of the following components.
* Console SPA
  * Single Page Application based on React Components
  * Components include Cataloguing, Integration Definition, Testing, Versioning and Deployment
  * Canvas for these components
  * Delivered via a CDN
* Metadata Core Entity
  * Responsible for operations around the core metadata model based on Apache Camel Java DSL
  * Includes Catalogue Loading - WMS Open API specifications and ERP APIs
  * Includes Channel Definitions - Define inbound and outbound connectors/protocols
  * Includes Transformations - Define transformation routines between inbound messages coming in from ERP systems to our WMS instances. Transformation allows field mapping, field transformation and default values for WMS specifications
  * Includes Routes - Gateway for inbound payloads, transformers and outbound publishing
* Metadata Persistent Store
  * Relational store for metadata configuration 
* Metadata Cache
  * Cache store for metadata configuration
    
![image](https://github.com/practicalvj/sadexercise/assets/122186968/ff92b43c-d934-403e-ae0c-5fcc7276062a)

The Runtime Service consists of the following components.
* API Endpoints
  * Endpoints published to ERP consumers. Endpoints classified by business APIs provided by WMS
* Route Handler
  * Loads metadata configuration and passes through a chain of connectors, transformations and publishing.
* Execution Persistent Store
  * Relational store for execution data


![image](https://github.com/practicalvj/sadexercise/assets/122186968/c5748785-c8d5-4be8-8fbd-b8c11b5c32c9)


## 6. Security Architecture


## 7. Infrastructure Requirements

**SaaS Deployment**

| Service  | Deployment Model | Resources | Primary Region | Backup Region | Tenancy Model |
| -------- | ------- | -------- | --------- | ------- | -------- |
| Studio Service | Microservice | 4 OCPU, 120 GB RAM | us-west-1 | us-east-1 | K8s shared namespace |
| Runtime Service | Microservice | 4 OCPU, 120 GB RAM | us-west-1 | us-east-1 | K8s shared namespace |
| Reporting Service | Microservice | 4 OCPU, 120 GB RAM | us-west-1 | us-east-1 | K8s shared namespace |
| Studio Persistent Store | Database Schema | 4 OCPU, 120 GB RAM, 1 TB | us-west-1 | us-east-1 | Separate instance per tenant |
| Execution Persistent Store | Database Schema | 4 OCPU, 120 GB RAM, 10 TB | us-west-1 | us-east-1 | Separate instance per tenant |
| Metadata Cache | Redis Instance | 4 OCPU, 480 GB RAM | us-west-1 | us-east-1 | Separate instance per tenant |


## 8. Non-Functional Requirements

| Area    | Requirements | Response |
| -------- | ------- | -------- |
| Service Level Availability / Objectives  | Have service level availability targets been established, and does the solution support them?    | Yes. The service will cater to our standard SLAs of 99.9 and for response times less than 500ms|
| Performance | Have Targets Been established ?| The System needs to support 1 MM transactions per hour across all our tenant with a response time SLA of less than 500 ms|
| Scalability | Is solution designed for scaling aimed at supporting the targeted use case?) | Yes. The system will be able to scale horizontally based on the design constructs |
| Resiliency | In the event of an outage of dependent solution components, has service resiliency been built in to avoid revenue or customer facing impact? | Yes. Appropriate redundancy is built into the solution at each layer|
| Visibility & Control | Have operational measurements been established against which proper operations of the solution can be measured; are procedures in place for alerts and correction? | Yes. Solution will be able to integrate with our M&O infrastructure|
