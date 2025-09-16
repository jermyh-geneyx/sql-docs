---
author: MikeRayMSFT
ms.author: mikeray
ms.date: 08/14/2025
ms.service: sql
ms.topic: include
---

The following table identifies features originally released as preview their current status:

| Feature | Current status | Version of latest update | Description |
| --- | --- | --- | --- |
| [AI_GENERATE_CHUNKS](../../t-sql/functions/ai-generate-chunks-transact-sql.md) | Preview | RC 1 | Generate chunks of text from a given input text using an AI model. |
| [Change event streaming](../../relational-databases/track-changes/change-event-streaming/overview.md) | Preview | RC 1 | Stream changes from SQL Server to Azure Event Hubs. |
| [CREATE EXTERNAL MODEL](../../t-sql/statements/create-external-model-transact-sql.md) | Preview | RC 1 | Supports local ONNX models hosted directly on the SQL Server file system. |
| [CREATE VECTOR INDEX](../../t-sql/statements/create-vector-index-transact-sql.md) | Preview | RC 1 | Create an approximate index on a vector column to improve performances of nearest neighbors search. |
| [EDIT_DISTANCE](../../t-sql/functions/edit-distance-transact-sql.md) | Preview | RC 1 | Calculates the number of insertions, deletions, substitutions, and transpositions needed to transform one string to another. |
| [EDIT_DISTANCE_SIMILARITY](../../t-sql/functions/edit-distance-similarity-transact-sql.md) | Preview | RC 1 | Calculates a similarity value ranging from 0 (indicating no match) to 100 (indicating full match). |
| [Half-precision (2-byte) vectors](../../t-sql/data-types/vector-data-type.md) | Preview | RC 1 | Store vectors using half-precision (2-byte) floating-point values, allowing up to 3996 dimensions in a single vector. |
| [VECTOR_SEARCH](../../t-sql/functions/vector-search-transact-sql.md) | Preview | RC 1 | Search for vectors similar to a given query vector using an approximate nearest neighbors vector search algorithm. |

> [!CAUTION]  
> Preview features aren't recommended for production environments.
