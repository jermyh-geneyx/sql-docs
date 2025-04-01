---
title: Overview of Microsoft Copilot in Azure SQL Database
description: "Learn more about the possibilities of Microsoft Copilot in Azure SQL Databases for administrators and developers."
author: markingmyname
ms.author: maghan
ms.reviewer: wiassaf, kendalv
ms.date: 03/18/2025
ms.service: azure-sql-database
ms.subservice: sql-ai-copilot
ms.topic: concept-article
ms.collection:
- ce-skilling-ai-copilot
ms.custom:   
- build-2025
monikerRange: "=azuresql || =azuresql-db"
---

# What is Microsoft Copilot in Azure SQL Database

[!INCLUDE [appliesto-sqldb](../includes/appliesto-sqldb.md)]

Copilot in Azure SQL Database is a [capability](/azure/copilot/capabilities#perform-tasks) of the [Microsoft Copilot in Azure](/azure/copilot/overview) experience, enhancing the management and operation of SQL-dependent applications. It provides a conversational interface for users to interact with their databases, allowing them to ask questions and receive relevant answers based on the context of their database. It improves productivity in the Azure portal by offering self-help for database administration. This makes it easier for developers and database administrators to manage their databases, troubleshoot issues, and optimize performance.

## How Copilot works in Azure SQL Database

Microsoft Copilot in Azure is now integrated with Azure SQL Database.

Copilot in Azure SQL Database uses large language models (LLMs) to analyze the context of your database and provide relevant answers to your questions. It leverages various data sources, including public documentation, dynamic management views, catalog views, and Azure supportability diagnostics, to generate applicable responses. This allows users to ask questions in natural language and receive detailed explanations and suggestions for their database-related queries.

## Features of Copilot in Azure SQL Database

Copilot provides relevant answers to user questions, simplifying database management by applying database context, documentation, dynamic management views, Query Store, and other knowledge sources.

For example:
 
- Database administrators can independently manage databases and resolve issues, or learn more about the performance and capabilities of your database.
 
- Developers can ask questions about their data as they would in text or conversation to generate a T-SQL query. Developers can also learn to write queries faster through detailed explanations of the generated query.
 
- **Microsoft Copilot in Azure integration**: This experience adds Azure SQL Database skills into Microsoft Copilot in Azure, customers with self-guided assistance, empowering them to manage their databases and solve issues independently.
 
- Copilot integrates data and formulates applicable responses using public documentation, dynamic management views, catalog views, and Azure supportability diagnostics.

## Enable Copilot in your Azure tenant

For information on enabling Microsoft Copilot, visit [What is Microsoft Copilot for Azure](/azure/copilot/overview).

## Best practices for using Copilot in Azure SQL Database

You can ask and receive helpful, context-rich suggestions from [Microsoft Copilot in Azure](/azure/copilot/overview) within the Azure portal.

Microsoft Copilot in Azure is a set of experiences that are powered by large language models (LLMs). Output produced by Copilot might contain inaccuracies, biases, or other unintended content. As with any generative AI model, humans should review the output produced by Copilot before use.

Some example scenarios for the **Microsoft Copilot in Azure**:

- When you're working with a slow Azure SQL Database, you could provide the prompt```copilot-prompt
My database is slow
  ```.

  Microsoft Copilot in Azure (preview) starts looking at your database based on your context in the Azure portal. After the check, Copilot will detail specific areas that might be contributing to the issue. In this example, there was a specific query driving high CPU utilization:

  :::image type="content" source="media/copilot-azure-sql-overview/slow-database.png" alt-text="Screenshot showing the query prompt and generated sample query about high CPU utilization." lightbox="media/copilot-azure-sql-overview/slow-database.png":::

- You can continue the conversation and investigation with a prompt of```copilot-prompt
How can I tune that high CPU query?
  ```.

  Copilot understands that this prompt refers to the query identified earlier, and provides a new index suggestion:

  :::image type="content" source="media/copilot-azure-sql-overview/high-cpu-query.png" alt-text="Screenshot showing a second query prompt in the conversation and generated sample query about high CPU utilization." lightbox="media/copilot-azure-sql-overview/high-cpu-query.png":::

## Example prompts

You can provide prompts for the **Microsoft Copilot in Azure** around different capability areas, for example:

- **Active User Connections**: Shows active user connections to the database.

```copilot-prompt
  Who are currently actively connected to the database?
  ```

- **Antipattern Query Analysis**: Identifies queries with anti-patterns and their potential impact on performance.

  ```copilot-prompt
  Show me all the queries in my workload that have anti-patterns in them.
  ```

- **Automatic Tuning Analysis**: Investigates automatic tuning failures and potential solutions.

  ```copilot-prompt
  Why is automatic plan correction failing?
  ```

- **Basic Database Information**: Retrieves basic information about the database.

  ```copilot-prompt
  What is the name of the logical server for this database?
  ```

- **Blocking Session Analysis**: Identifies and analyzes blocking sessions.

  ```copilot-prompt
  Check top blocking sessions.
  ```

- **Compatibility Level**: Provides information about the database compatibility level.

  ```copilot-prompt
  What's the compatibility level of this database?
  ```

- **Connection String Generation**: Generates the appropriate connection string for the database.

  ```copilot-prompt
  Which connection string should I use to connect to my DB?
  ```

- **Copilot Help**: Provides general assistance and guidance with Azure SQL.

  ```copilot-prompt
  What can you do related to Azure SQL?
  ```

- **Data Synchronization Analysis**: Troubleshoots data synchronization issues, particularly with secondaries.

  ```copilot-prompt
  Why do my secondaries not have the latest data?
  ```

- **Database and Table Size**: Provides information about the database and table sizes.

  ```copilot-prompt
  What's the size of this database?
  ```
  
- **Database Performance Analysis**: Analyzes overall database performance and suggests improvements.

  ```copilot-prompt
  Why is my database slow?
  ```
  
- **Database Permission Listing**: Lists database permissions and access levels for users.

  ```copilot-prompt
  Which users have access to master database?
  ```

- **Deadlock Analysis**: Investigates deadlocks and suggests solutions.

  ```copilot-prompt
  Why am I getting deadlock errors? How can I fix it?
  ```
  
- **Dropped Connections Analysis**: Investigates instances of dropped database connections.

  ```copilot-prompt
  Show me all the instances where my database had a dropped connection.
  ```

- **Fragmented Index Analysis**: Identifies fragmented indexes and their impact on performance.

  ```copilot-format
  Help me find fragmented indexes.
  ```
  
- **General Antipattern Information**: Provides general information about common SQL anti-patterns.  

  ```copilot-prompt
  What are the most common SQL antipatterns?
  ```

- **Get Database Names for Server**: Lists all databases on a specific server.  

  ```copilot-prompt
  List all the databases on this server.
  ```

- **High CPU Consuming Query Analysis**: Identifies and analyzes queries with high CPU usage.  

  ```copilot-prompt
  Why is the CPU usage high on this database?
  ```

- **High IO Troubleshooting**: Checks if the database is experiencing high I/O.  

  ```copilot-prompt
  Is my database experiencing high I/O?
  ```
  
- **Index Listing**: Shows all indexes in the database.  

  ```copilot-prompt
  What are all the indexes?
  ```

- **Index Recommendations for Specific Table**: Provides index recommendations for one or more tables.  

  ```copilot-prompt
  Should I add an index on this table?
  ```

- **Latest Backup Information**: Provides information about the most recent database backup.  

  ```copilot-prompt
  When was the most recent backup of my database created?
  ```

- **Low Storage Space Troubleshooting**: Provides suggestions to free up space in the database.  

  ```copilot-prompt
  Is there a way I can free up space in my database?
  ```
  
- **MAXDOP Optimization**: Analyzes and suggests optimizations for the MAXDOP setting.  

```copilot-prompt
What's the current MAXDOP and how to optimize?
```

- **Memory Grant Analysis**: Analyzes memory grant issues and potential causes.  

  ```copilot-prompt
  Why am I having memory grant issues?
  ```

- **Missing Index Suggestions**: Suggests missing indexes to improve query performance.  

```copilot-prompt
Missing index suggestion for improving query performance?
  ```

- **Point-in-Time Restore Retention**: Provides information about the point-in-time restore retention period.  

  ```copilot-prompt
  How far back in time can I go for a point-in-time restore?
  ```

- **Query Performance Analysis**: Investigates and suggests solutions for slow-running queries.  

  ```copilot-prompt
  Why is this query running so slow?
  ```

- Find Forced Plans**: Shows queries with forced plans within a specified timeframe.  

  ```copilot-prompt
  Show me all the queries from the past 2 days that have forced plans.
  ```

- Find High Execution Time Variation**: Identifies queries with high variation in execution time.  

```copilot-prompt
Which queries on my database have a high variation in execution time?
```
 
- **Query Store  
- Find Highest I/O Queries**: Shows queries with the highest I/O usage.  


```copilot-prompt
What queries on this database use the most I/O?
  ```
 
- **Query Store  
- Get Query Text by ID**: Shows the query text based on the provided Query ID.  


```copilot-prompt
What is the query text for Query ID 1333?
  ```
 
- **Query Store  
- Latest Executed Queries**: Displays the most recently executed queries.  


```copilot-prompt
What are the most recently executed queries in my database?
  ```
 
- **Query Store  
- Longest Running Queries**: Shows the longest running queries within a specified timeframe.  


```copilot-prompt
What are the longest running queries in the past day?
  ```
 
- **Query Store  
- Queries with Highest Wait Times**: Identifies queries with the highest wait times.  


```copilot-prompt
Which queries have had the highest wait times?
  ```
 
- **Query Store  
- Queries with Multiple Plans**: Checks for queries with multiple execution plans.  


```copilot-prompt
Show me the queries that have had more than one execution plan.
  ```
 
- **Query Store  
- Regressed Queries**: Identifies queries that have regressed in performance.  


```copilot-prompt
Have any of my queries gotten significantly slower recently?
  ```
 
- **Query Store  
- Regressed Queries with Plan Changes**: Shows queries with plan changes that have regressed in performance.  


```copilot-prompt
Are there any queries that had plan changes and regressed in performance?
  ```
 
- **Query Store  
- Show Executions per Query**: Displays the number of executions for each query.  


```copilot-prompt
What queries are being executed most often?
  ```
 
- **Query Store  
- Top Resource Consuming Queries**: Identifies and analyzes queries with the highest resource consumption.  


```copilot-prompt
What are the most expensive queries in my workload?
  ```
 
- **Query Store Mode Troubleshooting**: Investigates and provides solutions for Query Store being in read-only mode.  


```copilot-prompt
Why is Query Store in read-only mode? How can I fix it?
  ```
 
- **Related Documentation**: Provides links to relevant documentation based on the user's query.  


```copilot-prompt
What does database compatibility level mean?
  ```
 
- **Resource Usage Analysis**: Analyzes resource usage and potential bottlenecks.  


```copilot-prompt
Is the database hitting resource limits? Which limits?
  ```
 
- **Table Listing**: Lists all tables in the database.  


```copilot-prompt
What are the names of all the tables?
  ```
 
- **Troubleshoot Error 18456**: Helps with SQL error 18456.  


```copilot-prompt
Help me with SQL error 18456.
  ```
 
- **Troubleshoot Error 40615**: Helps with SQL error 40615.  


```copilot-prompt
Help me with SQL error 40615.
  ```
 
- **Troubleshoot Connection Errors**: Checks for causes of connection timeouts.  


```copilot-prompt
Check for causes of connection timeouts in my database.
  ```
 
- **Troubleshoot Login Failures**: Diagnoses login failure issues.  


```copilot-prompt
Why can't I login to my database?
  ```
 
- **Troubleshoot Query Timeout**: Addresses query timeout issues.  


```copilot-prompt
Help me with query timeout issues.
  ```
 
- **Wait Statistics Analysis**: Analyzes wait statistics and potential performance bottlenecks.  


```copilot-prompt
What do the wait statistics look like for my database?
  ```

- **Workload Increase and Scaling Analysis**: Assesses workload increases and potential need for scaling.  

  ```copilot-prompt
  Has increased workload or traffic caused performance issues?
    ```
  
## Responsible AI

For more information on how Microsoft implements responsible AI tools in [Microsoft Copilot in Azure](/azure/copilot/overview), see [Responsible AI FAQ for Microsoft Copilot in Azure (preview)](/azure/copilot/responsible-ai-faq).

## Related content

- [Microsoft Copilot in Azure overview](/azure/copilot/overview)
-[Frequently asked questions about Microsoft Copilot skills in Azure SQL Database (preview)](copilot-azure-sql-faq.yml)
- [Intelligent applications with Azure SQL Database](../database/ai-artificial-intelligence-intelligent-applications.md)
