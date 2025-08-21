---
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: randolphwest
ms.date: 04/10/2025
ms.service: sql
ms.subservice: polybase
ms.topic: include
ms.custom:
  - build-2025
---

On Linux, [!INCLUDE [sssql25-md](sssql25-md.md)] Release Candidate (RC) 0 requires [polybase network encryption](../database-engine/configure-windows/polybase-network-encryption-server-configuration-option.md) set to `0`.

```sql
EXEC sp_configure @configname = 'polybase network encryption', @configvalue = 0;
RECONFIGURE WITH OVERRIDE;
```