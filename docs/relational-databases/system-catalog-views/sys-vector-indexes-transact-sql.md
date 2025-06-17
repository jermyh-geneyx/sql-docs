---
title: "sys.vector_indexes (Transact-SQL)"
description: "sys.vector_indexes contains a row per vector index."
author: damauri
ms.author: damauri
ms.reviewer: damauri, pookam
ms.date: 06/11/2025
ms.topic: reference
ms.service: sql
ms.subservice: system-objects
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

[!INCLUDE [SQL Server 2025](../../includes/applies-to-version/_ss2025.md)]

Contains a row per vector index.  
  
|Column name|Data type|Description|  
|-----------------|---------------|-----------------|  
|**object_id**|int|Reference to sys.indexes|  
|**index_id**|int|Reference to sys.indexes|  
|**metric**|varchar(20)|Type of vector index (DiskANN only for now)|
|**type_desc**|varchar(20)|Metric used to create the vector index|
|**build_parameters**|nvarchar(max)|Internal usage only|


## Permissions  

[!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] For more information, see [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md).  
  
## Examples  

The following example returns all indexes for the table `[dbo].[[wikipedia_articles_embeddings]` used in the [DiskANN sample](https://github.com/Azure-Samples/azure-sql-db-vector-search/tree/main/DiskANN/Wikipedia) available in the [https://github.com/Azure-Samples/azure-sql-db-vector-search](https://github.com/Azure-Samples/azure-sql-db-vector-search) GitHub sample repo.  
  
```sql  
select 
	vi.obj_id,
	vi.index_id,
	vi.index_type,
	vi.dist_metric,
	vi.build_parameters
from 
	sys.indexes i 
inner join
	sys.vector_indexes as vi on vi.obj_id = i.object_id and vi.index_id = i.index_id
where 
	obj_id = object_id('[dbo].[wikipedia_articles_embeddings]')
```  
  
## Next steps

[Object Catalog Views (Transact-SQL)](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
[Catalog Views (Transact-SQL)](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
[sys.indexes (Transact-SQL)](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)   
[CREATE VECTOR INDEX (Transact-SQL)](../../t-sql/statements/create-vector-index-transact-sql.md)
