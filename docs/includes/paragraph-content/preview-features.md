---
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: randolphwest
ms.date: 10/06/2025
ms.service: sql
ms.topic: include
---

The following table identifies features originally released as preview their current status:

| Feature | Current status | Version of latest update | Description |
| --- | --- | --- | --- |
| [Change event streaming](../../relational-databases/track-changes/change-event-streaming/overview.md) | Preview | RC 1 | Stream changes from SQL Server to Azure Event Hubs. |
| [CREATE EXTERNAL MODEL](../../t-sql/statements/create-external-model-transact-sql.md) | Preview | RC 1 | Supports local ONNX models hosted directly on the SQL Server file system. |
| [CREATE VECTOR INDEX](../../t-sql/statements/create-vector-index-transact-sql.md) | Preview | RC 1 | Create an approximate index on a vector column to improve performances of nearest neighbors search. |
| [Half-precision (2-byte) vectors](../../t-sql/data-types/vector-data-type.md) | Preview | RC 1 | Store vectors using half-precision (2-byte) floating-point values, allowing up to 3996 dimensions in a single vector. |
| [VECTOR_SEARCH](../../t-sql/functions/vector-search-transact-sql.md) | Preview | RC 1 | Search for vectors similar to a given query vector using an approximate nearest neighbors vector search algorithm. |

> [!CAUTION]  
> Preview features aren't recommended for production environments.
