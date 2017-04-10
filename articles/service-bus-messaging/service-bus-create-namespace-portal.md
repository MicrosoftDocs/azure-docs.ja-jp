---
title: "Azure Portal で Service Bus 名前空間を作成する | Microsoft Docs"
description: "Azure Portal を使用して Service Bus 名前空間を作成する方法。"
services: service-bus-messaging
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 03/23/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 5de92033eb7eb4fef8d27a215b3284ab80594065
ms.lasthandoff: 03/24/2017


---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Azure Portal を使用して Service Bus 名前空間を作成する
名前空間は、すべてのメッセージング コンポーネントの共通コンテナーです。 複数のキューとトピックを 1 つの名前空間に格納できます。多くの場合、名前空間はアプリケーション コンテナーとして機能します。 Service Bus 名前空間の作成には 2 つの方法があります。

1. Azure ポータル (この記事)
2. [リソース マネージャーのテンプレート][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Azure Portal での名前空間の作成
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

ご利用ありがとうございます。 これで、Service Bus メッセージング名前空間が作成されました。

## <a name="next-steps"></a>次のステップ
Azure Service Bus メッセージングのさらに高度な機能を紹介するサンプルについては、[GitHub サンプル][github-samples]を参照してください。

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

