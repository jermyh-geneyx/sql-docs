---
title: Overview of the MSSQL Extension for Visual Studio Code
description: Enhancing your developer experience with the MSSQL extension for Visual Studio Code
author: croblesm
ms.author: roblescarlos
ms.reviewer: maghan, randolphwest
ms.date: 11/20/2024
ms.service: sql
ms.subservice: vs-code-sql-extensions
ms.topic: overview
ms.collection:
  - data-tools
---

# What is the MSSQL extension for Visual Studio Code?

The [MSSQL extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql) is designed to support developers in building applications that use Azure SQL (including Azure SQL Database, Azure SQL Managed Instance, and SQL Server on Azure VMs), SQL database in Fabric (preview) or SQL Server as backend databases. With a comprehensive suite of features for connecting to databases, designing and managing database schemas, exploring database objects, executing queries, and visualizing query plans, this extension transforms the SQL development experience within Visual Studio Code.

The latest enhancements to this extension are aimed at boosting productivity. Whether you're working with databases running locally or in the cloud, the extension equips you with advanced IntelliSense, efficient Transact-SQL script execution, and customizable options. As a result, you can enjoy a modern and streamlined SQL development workflow.

## How to install the extension

Follow these steps to install the MSSQL extension for Visual Studio Code.

1. Bring up the Extensions view by selecting on the Extensions icon in the Activity Bar on the side of Visual Studio Code or the View: Extensions command.

1. Type `mssql` in the search bar.

1. Select the **SQL Server (mssql)** extension and view its details.

1. Select **Install**.

:::image type="content" source="media/mssql-extension-visual-studio-code/mssql-extension-vscode.png" alt-text="Screenshot of the MSSQL extension in Visual Studio Code.":::

## Enable the new UI features

The latest version of the MSSQL extension for Visual Studio Code introduces rich new UI features that enhance the development experience. These features make connecting to databases, managing database objects, and analyzing query performance more intuitive and efficient.

### Use the UI prompt

After installing the latest version of the MSSQL extension for Visual Studio Code, you can enable the new UI features via a prompt.

To enable the new features, select the **Enable Experiences and Reload** button in the prompt that appears when the extension is loaded for the first time.

:::image type="content" source="media/mssql-extension-visual-studio-code/mssql-enable-new-features-prompt.png" alt-text="Screenshot of UI prompt to enable new features.":::

### Use the Visual Studio Code user settings file

As an alternative, you can enable the new UI features by modifying the `user settings (JSON)` file via the Visual Studio Code command palette:

Add the `mssql.enableRichExperiences` setting to your settings.json file, and set this option to `true`.

Close the `settings.json` file, save the changes, and proceed to restart Visual Studio Code.

:::image type="content" source="media/mssql-extension-visual-studio-code/mssql-enable-new-features-json.png" alt-text="Screenshot to enable new features using the user settings json file." lightbox="media/mssql-extension-visual-studio-code/mssql-enable-new-features-json.png":::

## New features (Preview)

The latest version of the MSSQL extension for Visual Studio Code introduces a comprehensive set of new UI features that enhance the development experience. These new UI features make connecting to databases, managing database objects, and analyzing query performance more intuitive and efficient.

> [!IMPORTANT]  
> All the new UI features described below are currently in preview.

### Connection dialog

The Connection dialog allows you to quickly connect to databases hosted in Azure SQL (including Azure SQL Database, Azure SQL Managed Instance, and SQL Server on Azure VMs), SQL database in Fabric (preview) or SQL Server through a simple and intuitive interface. It provides multiple input options to cater to different scenarios:

- **Parameters**: Enter individual connection details such as server name, database name, username, and password.

- **Connection String**: Directly input a complete connection string for more advanced configurations.

- **Browse Azure**: Browse available database instances and databases in your Azure account, with options to filter by subscription, resource group, and location.

In addition to creating new connections, the dialog now includes a **Saved Connections** and **Recent Connections** panel, making it easier to reconnect to previously used servers. You can efficiently edit and save your connections with an improved layout that offers better navigation and usability. The enhanced UI makes modifying connection details or switching databases smoother than ever.

:::image type="content" source="media/mssql-extension-visual-studio-code/mssql-connection-dialog-parameters.png" alt-text="Screenshot of the new connection dialog feature." lightbox="media/mssql-extension-visual-studio-code/mssql-connection-dialog-parameters.png":::

### Object Explorer (filtering)

The Object Explorer enables users to navigate their database objects, such as databases, tables, views, and programmability items. The enhanced filtering functionality makes it easier to locate specific objects within large and complex database hierarchies:

- **Apply Filters**: Filter database objects by properties like name, owner, or creation date. Filters can be applied at multiple levels, including databases, tables, views, and programmability.

- **Edit Filters**: Refine or update existing filters to further narrow the object list.

- **Clear Filters**: Easily remove applied filters to view all objects within the hierarchy.

These filters provide flexibility and control, making it easier to quickly manage large databases and find relevant objects.

:::image type="content" source="media/mssql-extension-visual-studio-code/object-explorer-filtering.png" alt-text="Screenshot of the object explorer filter feature." lightbox="media/mssql-extension-visual-studio-code/object-explorer-filtering.png":::

### Table Designer

The Table Designer offers a new UI for creating and managing tables for your databases, with advanced capabilities to customize every aspect of the table's structure:

- **Columns**: Add new columns, set data types, define nullability, and specify default values. You can also designate a column as a primary key or identity column directly within the interface.

- **Primary Key**: Easily define one or more columns as the primary key for your table, ensuring each row is uniquely identifiable.

- **Indexes**: Create and manage indexes to improve query performance by adding additional columns as indexes for faster data retrieval.

- **Foreign Keys**: Define relationships between tables by adding foreign keys referencing primary keys in other tables, ensuring data integrity across tables.

- **Check Constraints**: Set up rules to enforce specific conditions on the data being entered, such as value ranges or patterns.

- **Advanced Options**: Configure more sophisticated properties and behaviors, such as system versioning and memory optimized tables.

Within the designer, the **Script As Create** panel provides an automatically generated T-SQL script that reflects your table design. You have the following options:

- **Publish**: Apply your changes directly to the database by selecting **Publish**. This action is powered by DacFX (Data-tier Application Framework), which ensures the smooth and reliable deployment of your schema updates.

- **Copy script**: You can copy the generated T-SQL script from the preview panel for manual execution or open it directly in the editor for further adjustments and modifications as needed.

:::image type="content" source="media/mssql-extension-visual-studio-code/table-designer.png" alt-text="Screenshot of the new table designer feature." lightbox="media/mssql-extension-visual-studio-code/table-designer.png":::

### Query Results pane

The MSSQL extension for Visual Studio Code provides an enhanced query results experience, helping you efficiently visualize and understand your data output. The query results display within the bottom panel of Visual Studio Code, which also hosts the integrated terminal, output, debug console, and other tools, creating a unified interface for easy access.

> [!TIP]  
> You can now open query results in a new tab for an expanded view, similar to the previous experience.

Key features of the Query Results pane include:

- **Grid View**: Displays query results in a familiar grid format, allowing for easy inspection of the data. You now have the option to display results in a New Tab for a clearer, more organized view

- **Copy Options**: Right-click within the results grid to access options like *Select All, Copy, Copy with Headers, and Copy Headers*, making it convenient to transfer data for other uses.

- **Save Query Results**: Includes the ability to save query results to multiple formats such as JSON, Excel, and CSV, allowing you to work with the data outside of Visual Studio Code.

- **Inline Sorting**: You can sort the data by clicking on the column headers directly in the query results view. Sorting can be done in ascending or descending order to make it easier to analyze specific subsets of the data.

- **Estimated Plan**: The Estimated Plan button is located in the query toolbar, next to the Run Query button. It appears as a flowchart icon and allows you to generate an estimated execution plan without executing the query itself. This feature provides valuable insight into query performance, helping identify potential bottlenecks and inefficiencies before running the actual query.

- **Enable Actual Plan**: A new button labeled Enable Actual Plan, located right after Estimated Plan button in the upper right corner of the results pane, lets you view the actual query plan for executed queries. This addition provides deeper insight into query performance and helps identify bottlenecks and inefficiencies.

This updated query results experience is designed to offer flexibility and improved workflow integration, empowering developers to work more effectively with their data.

:::image type="content" source="media/mssql-extension-visual-studio-code/query-results-vscode.png" alt-text="Screenshot of the query results feature." lightbox="media/mssql-extension-visual-studio-code/query-results-vscode.png":::

### Customization settings for the Query Results pane

You can customize your query results experience using specific settings. The table below explains these settings and the resulting behaviors:

| Setting | Behavior |
| --- | --- |
| **Nothing set** | The default experience, referred to as the old user experience (UX). |
| `mssql.enableRichExperiences`: `true` | Activates the new user experience, which includes a refreshed query results pane. |
| `mssql.enableRichExperiences`: `true` and `mssql.openQueryResultsInTabByDefault`: `true` | Opens query results in a new tab by default, rather than displaying them in the bottom query pane. This can help declutter your workspace and provide a more organized view. |
| `mssql.enableRichExperiences`: `true` and `mssql.enableNewQueryResultsFeature`: `false` | Retains the new user experience but with the query results pane appearing in the old style. This setting is useful if you prefer the traditional look for the results pane while benefiting from other new user experience features. |

These settings give you flexibility in how you view and interact with query results, making it easier to adapt the experience to your workflow. To modify these settings, navigate to the settings in Visual Studio Code and update the configurations as needed.

### Query Plan Visualizer

The Query Plan Visualizer in the MSSQL extension for Visual Studio Code allows developers to analyze SQL query performance by displaying detailed execution plans. This tool provides insights into how SQL queries are executed, helping developers identify bottlenecks and optimize their queries.

Key features and capabilities include:

- **Node Navigation**: Each step in the execution plan is represented as a node, allowing you to interact with the plan in various ways. You can select nodes to view tooltips or detailed information about specific operations. Additionally, you can collapse or expand node trees to simplify the view and focus on key areas of the query plan.
- **Zoom Controls**: The visualizer offers flexible zoom options to help you analyze the plan in detail. You can zoom in or out to adjust the level of detail, use the "zoom to fit" feature to resize the view and fit the entire plan on your screen, or set custom zoom levels to examine specific elements precisely.
- **Metrics and Highlighting**: The metrics toolbar allows you to analyze key performance indicators and highlight expensive operations. You can select metrics such as **Actual Elapsed Time**, **Cost**, **Subtree Cost**, or **Number of Rows Read** from the dropdown list to identify bottlenecks and use these metrics to search for specific nodes within the query plan for deeper analysis.

The right-hand sidebar provides quick access to additional actions:

- **Save Plan**: Save the current execution plan for future reference.
- **Open XML**: Open the XML representation of the query plan to inspect details at the code level.
- **Open Query**: View the query that generated the execution plan directly from the toolbar.
- **Toggle Tooltips**: Enable or disable tooltips for additional details on each node.
- **Properties**: View the properties of each node in the execution plan, with options to sort by importance or alphabetically.

:::image type="content" source="media/mssql-extension-visual-studio-code/query-plan-visualizer-vscode.png" alt-text="Screenshot of the query plan visualizer feature." lightbox="media/mssql-extension-visual-studio-code/query-plan-visualizer-vscode.png":::

## Supported operating systems

Currently, this extension supports the following operating systems:

- Windows (x64 | x86 | Arm64)
- macOS (x64 | Arm64)
- Linux Arm64
- Ubuntu 18.04, 20.04, 22.04
- Debian 10, 11, 12
- CentOS 7, 8 / Oracle Linux 7, 8
- Red Hat Enterprise Linux (RHEL): 8, 9
- Fedora 35,36
- OpenSUSE Leap 15

## Offline installation

The extension can download and install a required SqlToolsService package during activation. You can still use the extension for machines with no Internet access by choosing the Install from VSIX... option in the Extension view and installing a bundled release from our Releases page. Each operating system has a .vsix file with the required service included. Pick the file for your OS, download, and install it to get started. We recommend you choose a full release and ignore any alpha or beta releases, as these are our daily builds used in testing.

## Feedback and support

If you have any feedback or comments, create a GitHub issue at [https://github.com/microsoft/vscode-mssql/issues](https://github.com/microsoft/vscode-mssql/issues).

## Related content

- [Quickstart: Connect to and query a database with the MSSQL extension for Visual Studio Code](connect-database-visual-studio-code.md)
- [Learn more about Visual Studio Code](https://code.visualstudio.com/docs)
- [Learn more about contributing to the mssql extension](https://github.com/Microsoft/vscode-mssql/wiki)
- [What is the local development experience for Azure SQL Database?](/azure/azure-sql/database/local-dev-experience-overview)
