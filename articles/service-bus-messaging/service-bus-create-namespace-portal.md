---
title: Azure portal で Service Bus 名前空間を作成する方法
description: この記事では、Azure portal で Azure Service Bus 名前空間を作成する手順を説明します。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 3c665dad06ca7cf7bba39b5e1a0f748b420ad770
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85337762"
---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Azure Portal を使用して Service Bus 名前空間を作成する

名前空間は、すべてのメッセージング コンポーネントのスコープ コンテナーです。 複数のキューとトピックを 1 つの名前空間に格納できます。多くの場合、名前空間はアプリケーション コンテナーとして機能します。 この記事では、Azure portal で名前空間を作成する手順を説明します。 

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

お疲れさまでした。 これで、Service Bus メッセージング名前空間が作成されました。

## <a name="next-steps"></a>次のステップ

Service Bus メッセージングのさらに高度な機能を紹介するサンプルについては、Service Bus [GitHub サンプル][github-samples]を参照してください。

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure/azure-service-bus/tree/master/samples
