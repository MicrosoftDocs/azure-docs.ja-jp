---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 3c9679f3d66d58c7a6c847b54c84438c979ecd39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936866"
---
[Azure プライベート エンドポイント](../articles/private-link/private-endpoint-overview.md)は、Azure Private Link を使用するサービスに、プライベートで安全に接続するためのネットワーク インターフェイスです。  プライベート エンドポイントでは、仮想ネットワークのプライベート IP アドレスを使用して、サービスが仮想ネットワークに実質的に組み込まれます。

[Premium](../articles/azure-functions/functions-premium-plan.md) および [App Service](../articles/azure-functions/dedicated-plan.md) の各プランでホストされている機能にプライベート エンドポイントを使用することができます。

機能に対して受信プライベート エンドポイント接続を作成する場合は、プライベート アドレスを解決するための DNS レコードも必要です。  既定では、Azure portal を使用してプライベート エンドポイントを作成するときに、プライベート DNS レコードが作成されます。

詳細については、[Web Apps でのプライベート エンドポイントの使用](../articles/app-service/networking/private-endpoint.md)に関する記事を参照してください。