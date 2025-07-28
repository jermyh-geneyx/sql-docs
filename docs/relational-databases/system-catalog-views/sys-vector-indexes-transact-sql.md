---
title: "sys.vector_indexes (Transact-SQL)"
description: "sys.vector_indexes contains a row per vector index."
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: damauri, pookam
ms.date: 07/24/2025
ms.service: sql
ms.subservice: system-objects
ms.topic: reference
f1_keywords:
  - "sys.vector_indexes"
  - "vector_indexes"
  - "sys.vector_indexes_TSQL"
  - "vector_indexes_TSQL"
helpviewer_keywords:
  - "sys.vector_indexes catalog view"
dev_langs:
  - "TSQL"
monikerRange: "=sql-server-ver17 || =sql-server-linux-ver17"
---
# sys.vector_indexes (Transact-SQL)

[!INCLUDE [SQL Server 2025](../../includes/applies-to-version/sqlserver2025.md)]

The `sys.vector_indexes` system catalog view contains one row per vector index.

|Column name|Data type|Description|  
|-----------------|---------------|-----------------|  
|`object_id`|**int**|Reference to `sys.indexes`.|
|`index_id`|**int**|Reference to `sys.indexes`.|
|`metric`|**varchar(20)**|Type of vector index (Currently, DiskANN only).|
|`type_desc`|**varchar(20)**|Metric used to create the vector index.|
|`build_parameters`|**nvarchar(max)**|Internal usage only.|

## Permissions

[!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] For more information, see [Metadata Visibility Configuration](../security/metadata-visibility-configuration.md).  

## Examples

The following example returns all indexes for the table `[dbo].[[wikipedia_articles_embeddings]` used in the [DiskANN sample](https://github.com/Azure-Samples/azure-sql-db-vector-search/tree/main/DiskANN/Wikipedia) available in the [https://github.com/Azure-Samples/azure-sql-db-vector-search](https://github.com/Azure-Samples/azure-sql-db-vector-search) GitHub sample repo.  

```sql  
SELECT
    vi.obj_id,
    vi.index_id,
    vi.index_type,
    vi.dist_metric,
    vi.build_parameters
FROM
    sys.indexes AS i 
INNER JOIN 
    sys.vector_indexes AS vi 
    ON vi.obj_id = i.object_id 
    AND vi.index_id = i.index_id
WHERE
    obj_id = object_id('[dbo].[wikipedia_articles_embeddings]');
```  

## Related content

- [Object catalog views (Transact-SQL)](object-catalog-views-transact-sql.md)
- [System catalog views (Transact-SQL)](catalog-views-transact-sql.md)
- [sys.indexes (Transact-SQL)](sys-indexes-transact-sql.md)
- [CREATE VECTOR INDEX (Transact-SQL)](../../t-sql/statements/create-vector-index-transact-sql.md)
