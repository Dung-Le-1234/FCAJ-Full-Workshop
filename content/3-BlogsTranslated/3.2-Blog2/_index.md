---
title: "AWS Database Blog"
date: 2025-09-29
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---

# How Smartsheet Enhances Recommendations Using Amazon Neptune and Knowledge Graphs

Smartsheet is a leading SaaS-based collaborative work management platform trusted by enterprises worldwide to manage projects, automate workflows, and drive collaboration at scale. Smartsheet, a long time AWS customer, has continuously evolved to meet the growing needs of its global user base. Today, Smartsheet is taking its platform to the next level by implementing Knowledge Graphs built on Amazon Neptune with the goal of delivering intelligent, proactive, and contextual insights from collaboration data in Smartsheet. This innovation aims to enhance customer experience, boost productivity, and drive long-term retention and growth.

In this post, we describe the Smartsheet Knowledge Graph, built in partnership between Smartsheet and AWS. The Smartsheet Knowledge Graph is a unified data model connecting people, content, and work in Smartsheet, representing how users interact with assets, content, and their collaborators. Simply put, it is a professional network tailored for each user within Smartsheet. Capturing this collaboration between users and their work helps unlock personalization, insights, and recommendations for each user.

## Existing Solution

As a leading collaborative work management platform, Smartsheet scales to support some of the largest enterprises in the world. The platform houses a vast amount of collaboration data created by users as they plan, track, and execute work. The challenge confronted by the Smartsheet engineering team is modeling this data in a way that represents the collaboration between users and their work to surface intelligent, contextual insights and expose them within the product experience, while also ensuring scalability, security, and performance across the user base.

Smartsheet uses best engineering practices to implement features as micro-services. As a result, each micro-service uses its own data store in a technology that is suited to the service architecture. Having data split across many different data stores and technologies makes it hard to analyze and map relationships between objects at scale in real time. A unified graph data model solves this challenge by creating object nodes and edges that define the relationships between these objects. Doing so allows Smartsheet to then traverse this graph data model to understand who a user’s close collaborators are and which assets are relevant to the user, enabling Smartsheet to provide more individualized, contextual guidance and solutions to users. Solving this data challenge is an important step in improving wayfinding and personalization throughout the Smartsheet platform.

Before developing the graph data model, unearthing relationships between users and their work in Smartsheet meant performing joins across tables in Snowflake, migrating the data to Amazon DynamoDB via AWS Lambda functions, and deriving insights using ML models that needed constant retraining as the data and relationships changed.

The flexibility of the graph data model allows Smartsheet to model relationships and keep these up to date as users collaborate on the platform, mapping relationships up to 4 degrees away from the user with a single optimized openCypher query on Neptune. For example, Smartsheet can now provide suggested contacts relevant to each user for sharing sheets, dashboards, and workspaces, or suggest relevant content to users based on context or priority.

## Solution Built with AWS

Smartsheet partnered with AWS to develop a comprehensive knowledge graph platform that brings customer usage data in Snowflake and graph technologies together. At the heart of the solution is a knowledge graph powered by Neptune, designed to model complex relationships across Smartsheet’s collaboration work management platform. The architecture brings together several AWS services:

Amazon Neptune provides the graph database foundation, enabling relationship-based queries across interconnected data.
Snowflake stores the customer’s usage data of their Smartsheet Plan
Amazon Elastic Container Service (Amazon ECS)
Tasks transform and prepare data for graph ingestion and facilitate web service that exposes graph insights and recommendations using Cypher queries via internal APIs to Smartsheet services
Based web service that exposes graph insights and recommendations using Cypher queries via internal APIs to Smartsheet services
Amazon Simple Storage Service (Amazon S3) stores graph data CSV files for bulk loading to Neptune
AWS Step Functions orchestrate ECS tasks to transform and load data to Neptune
With this architecture, Smartsheet delivers contextual insights and personalized recommendations, helping users to discover their work and connections faster.

For Smartsheet, designing a graph data model that ensured logical separation of data amongst its customers was of paramount importance – we reviewed the “Multi-tenancy guidance for ISVs running Amazon Neptune databases” guidance and chose a pool model for labeled property graphs, that is every tenant’s (Smartsheet customer) data is stored in a single graph but is logically separated to achieve data partitioning.

Smartsheet partnered with AWS to optimize the data ingestion process from Snowflake into Neptune using bulk loader operations for creating and updating nodes and edges in the graph. Deletes from the graph were optimized using Gremlin queries with per query timeouts and optimized batch processing in place. Having the ability to run both Gremlin and openCypher on the same graph allowed us to use the best approach for each job. In the future, our engineers will improve this further by moving away from the bulk loader to a transactional streaming workflow. To avoid graph sprawl and storing relationships that were not relevant, the team regularly pruned the graph nodes and edges that were inactive for extended periods of time retaining only the most recent and relevant activity.

At the enterprise scale of the Smartsheet platform, the graph data model grows quickly, reaching million scale orders of magnitude — over 70 million nodes and 150 million edges. At this scale, it becomes important to optimize graph queries so that Smartsheet can deliver insights within its SLA. The AWS solution architects partnered with the Smartsheet team to optimize the openCypher queries, using best practices such as using WITH statements, breaking down larger queries into subqueries, and optimizing date calculations over date fields previously stored as strings.

## Partnership Impact

TThe collaboration between Smartsheet and AWS has delivered significant value for both companies. Through AWS’s 3-day Experience Based Acceleration (EBA) workshop, what began as a proof-of-concept, transformed into an accelerated initiative after leadership witnessed the transformative potential of graph-powered insights during the intensive collaborative engagement.

“Our partnership with AWS and Amazon Neptune has given us a powerful foundation to innovate while delivering the scale, security, and reliability our customers expect. The Knowledge Graph we’ve built together is more than just a data model — it’s how we’re able to provide smarter, more personalized insights that help teams move from simply managing work to solving challenges and executing strategy with speed and precision.”

– JB Brown, Vice President of Engineering at Smartsheet

“By building its Knowledge Graph on Amazon Neptune, Smartsheet is unlocking the power of connected data to deliver more contextual, personalized experiences for customers. Together, we’re combining an innovative, flexible data model with the enterprise-grade scale and security of AWS, enabling organizations to work smarter and faster.”

– Brad Bebee, Director at AWS

Smartsheet recently released the first Knowledge Graph-powered feature (following image) to its Enterprise customers. While the following results are still early, in just a few weeks:

1 in 7 Enterprise customers are using Knowledge Graph-powered suggestions to share content
99.9% of users share from the first 3 suggestions
These suggestions help Smartsheet customers and users share faster and more easily, with the people they commonly collaborate with. With just a few clicks, this new yet simple feature helps reinforce and remind Smartsheet solution builders and admins who to share with.
