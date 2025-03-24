---
author: nitinvbhartiM
ms.author: nitinbharti
ms.date: 03/24/2025
ms.topic: include
---

Enabling Entra ID authentication on Arc-enabled [!INCLUDE [ssnoversion-md](../../../includes/ssnoversion-md.md)] requires some URLs to be allow listed if outbound URLs are blocked by firewall:

- https://login.microsoftonline.com/
- https://\<azure-keyvault-name\>.vault.azure.net/
- https://graph.microsoft.com/
