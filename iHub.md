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
4. Detailed Solution Architecture
5. Security Architecture
6. Infrastructure Requirements
7. Non-Functional Requirements

## 1. Executive Summary

The traditional model for customers wanting to integrate with our WMS offerings have been to adopt a self-managed approach. The customers are responsible for building out connectors, transform the payloads expected by our APIs and monitor/observe them utilizing custom build tools. As our services mature, there is a growing demand by the customer base to move these capabilities to a fully managed integration platform service provided out of the box by the WMS suite. With this approach, customers would be able to reduce costs of building/operating their own tools, utilize a modern  platform to serve their integration needs and help us elevate the overall customer experience of our products.

This document presents the solution architecture for WMS iHub (Integration Hub), a proposed new capability within the WMS Suite that will cater to the business/technical requirements.

## 3. Requirements Summary
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

## 4. High-Level Solution Design

iHub will be a stand-alone application consisting of a set of services that will satisfy the business/technical requirements laid out above. 

* It will have a browser based console exposed to the customer's administrators to help them setup integrations, deploy and monitor them.
* From an inbound channel perspective, iHub will expose APIs that ERPs will consume to send their payloads

![Vijay Nair's landscape - Context Diagram (Latest) (1)](https://github.com/practicalvj/sadexercise/assets/122186968/b14849f4-f783-4dfe-a5c4-cbd62fbd9b12)



## 5. Component Architecture


## 6. Security Architecture
- Outline security measures, compliance standards, and data protection mechanisms.

## 7. Infrastructure Requirements
- Specify the infrastructure needed, both hardware and software, including network and server architecture.

## 8. Non-Functional Requirements
- Describe the requirements for performance, scalability, reliability, and availability.

## 9. Risks and Mitigations
- Identify potential risks and propose mitigation strategies.
