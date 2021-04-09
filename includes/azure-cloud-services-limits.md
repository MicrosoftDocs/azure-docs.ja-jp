---
author: rothja
ms.service: cloud-services
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 390460cf6a22c164ee41a9b6679c7b7f2979d8ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85839058"
---
| リソース | 制限 |
| --- | --- |
| [デプロイあたりの Web または worker ロール数](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| デプロイあたりの[インスタンスの入力エンドポイント](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint)数 |25 |
| デプロイあたりの[入力エンドポイント](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint)数 |25 |
| デプロイあたりの[内部エンドポイント](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint)数 |25 |
| デプロイあたりの[ホストされるサービスの証明書数](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) |199 |

<sup>1</sup>Web または worker ロールの Azure Cloud Service には、それぞれ運用環境用とステージング環境用の 2 つのデプロイを作成することができます。 この制限は、個別のロールの数、つまり、構成を示しています。 この制限は、ロールあたりのインスタンスの数、つまり、スケーリングを示しています。

