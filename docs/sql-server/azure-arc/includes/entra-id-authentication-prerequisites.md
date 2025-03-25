---
author: nitinvbhartiM
ms.author: nitinbharti
ms.date: 03/24/2025
ms.topic: include
---

Enabling Entra ID authentication for [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] enabled by Azure Arc requires some URLs to be allow listed if a firewall blocks outbound URLs. Allow list the following URLs for these cases:

- `https://login.microsoftonline.com/`
- `https://<azure-keyvault-name>.vault.azure.net/`
- `https://graph.microsoft.com/`
