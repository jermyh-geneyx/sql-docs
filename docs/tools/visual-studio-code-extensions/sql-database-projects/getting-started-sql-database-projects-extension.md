---
title: Getting Started with the SQL Database Projects Extension
description: Getting started using the SQL Database Projects extension for Visual Studio Code
author: dzsquared
ms.author: drskwier
ms.reviewer: randolphwest, maghan
ms.date: 10/10/2025
ms.service: sql
ms.subservice: sql-database-projects
ms.topic: get-started
ms.collection:
  - data-tools
ms.custom:
  - intro-get-started
---

# Getting started with the SQL Database Projects extension

[!INCLUDE [azure-data-studio-deprecation](../../../includes/azure-data-studio-deprecation.md)]

This article describes three ways to get started with the SQL Database Projects extension:

1. [Create a new database project](#create-an-empty-database-project) by going to the **Database Projects** view or by searching for **Database Projects: New** in the command palette.

1. [Existing database projects](#open-an-existing-project) can be opened via **Database Projects: Open existing** in the command palette.

1. [Start from an existing database](#create-a-database-project-from-an-existing-database) by using **Database Projects: Create Project from Database** from the command palette or by selecting **Create Project from Database** in the **Connections** view.

   :::image type="content" source="media/sql-database-projects-extension/projects-view.png" alt-text="Screenshot of new view.":::

Once you've created or opened a SQL project, you're ready to start developing with SQL projects. Some actions you might take are:

- edit a table or other database objects
- building and publishing the project
- using schema compare to visualize changes
- updating the project from changes made to a database

For in-depth information about SQL projects concepts and more tutorials, see [What are SQL database projects?](../../sql-database-projects/sql-database-projects.md)

## Create an empty database project

In the **Database Projects** view, select the **New Project** button and enter a project name in the text input that appears. In the "Select a Folder" dialog that appears, select a directory for the project's folder, `.sqlproj` file, and other contents to reside in.
The empty project is opened and visible in the **Database Projects** view for editing.

## Open an existing project

In the **Database Projects** view, select the **Open Project** button and open an existing `.sqlproj` file from the file picker that appears. Existing projects can originate from Azure Data Studio, Visual Studio Code, or [Visual Studio SQL Server Data Tools](../../../ssdt/sql-server-data-tools.md).

The existing project is opened and its contents are visible in the **Database Projects** view for editing.

## Create a database project from an existing database

Instead of starting from an empty project, you can quickly populate a SQL Database Project with the existing objects from a database.

### In Object Explorer

In the **Connections** view, connect to the SQL instance that contains the database to be extracted. Right-click on the database and select **Create Project from Database** from the context menu.

:::image type="content" source="media/sql-database-projects-extension/create-project-from-database.png" alt-text="Screenshot of create Project from Database dialog." lightbox="media/sql-database-projects-extension/create-project-from-database.png":::

The folder structure setting is set to *Schema/Object Type* by default and offers different ways to automatically organize the existing objects when they are scripted out. The options for the folder structure setting are:

- File: a single file is created for all the objects
- Flat: a single folder is created for all the objects in individual files
- Object Type: a folder is created per object type and each object is scripted out to a file
- Schema: a folder is created per schema and each object is scripted out to a file
- Schema/Object Type: a folder is created per schema and within the folder a folder is created per object type and each object is scripted out to a file

### In Database Projects view

In the **Project** view, select the **Import Project from Database** button and connect to a SQL instance. Once the connection is established, select a database from the list available databases and set the name of the project.

Finally, select a folder structure of the extraction. The new project is opened and contains SQL scripts for the contents of the selected database.

## Further actions

### Build and publish

Deploying the database project is achieved in the SQL Database Projects extension by building the project into a [data-tier application file](../../sql-database-projects/concepts/data-tier-applications/overview.md) (dacpac) and publishing to a supported platform. In the **Database Projects** view, right-click on a project and select **Build** to create a dacpac file and validate the SQL project. When you're ready to deploy your project to a database, right-click on the project again and select **Publish** to publish the dacpac to a database.

For more on this process, see the [tutorial on creating and deploying a SQL project](../../sql-database-projects/tutorials/create-deploy-sql-project.md).

### Schema compare

The SQL Database Projects extension interacts with [Schema Compare](../mssql/mssql-schema-compare.md), enabling you to compare the contents of a project to a dacpac, existing database, or another project. The resulting schema comparison can be used to view and apply the differences from source to target.

:::image type="content" source="media/sql-database-projects-extension/sql-project-schema-compare.png" alt-text="Screenshot of schema compare dialog comparing a SQL project to a database." lightbox="media/sql-database-projects-extension/sql-project-schema-compare.png":::

Learn more about schema comparison and SQL projects in the [Schema Compare for SQL projects](../../sql-database-projects/concepts/schema-comparison.md) article.

### Update project from database

If changes are made to a database that aren't yet made to the SQL project, the SQL project can be updated from the state of a database. This update is done by selecting **Update Project from Database** from the context menu of a database in the **Connections** view or from the context menu of a SQL project in the **Database Projects** view. Keeping a SQL project up to date with changes in a database is one method of source control for database changes.

:::image type="content" source="media/sql-database-projects-extension/update-project-from-database.png" alt-text="Screenshot of update Project from Database dialog." lightbox="media/sql-database-projects-extension/update-project-from-database.png":::

## Related content

- [What are SQL database projects?](../../sql-database-projects/sql-database-projects.md)
- [Publish a project with GitHub sql-action](https://github.com/azure/sql-action)
- [Get help with the SQL Database Projects extension](https://github.com/microsoft/azuredatastudio/issues)
