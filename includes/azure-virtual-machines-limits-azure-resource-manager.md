---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 9070aee55969c1cc0fdf3870a05a065aaa5a8bf3
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "67181532"
---
| リソース | 既定の制限 |
| --- | --- |
| 可用性セットあたりの仮想マシン数 | 200 |
| サブスクリプションあたりの証明書数 |無制限<sup>1</sup> |

<sup>1</sup>Azure リソース マネージャーでは、証明書は Azure Key Vault に格納されます。 証明書の数は、サブスクリプションに対しては無制限です。 1 つの VM または可用性セットから成るデプロイあたりの証明書数には 1 MB という制限があります。

