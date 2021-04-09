---
title: Azure SignalR Service のインスタンスのスケーリング
description: Azure portal または Azure CLI を使用して Azure SignalR Service のインスタンスをスケーリングし、容量を増減する方法について説明します。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 9/9/2020
ms.author: zhshang
ms.custom: devx-track-azurecli
ms.openlocfilehash: bb01f2f96e5db4c94e759b114818360e6084255f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89595769"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Azure SignalR Service のインスタンスをスケーリングする方法
この記事では、Azure SignalR Service のインスタンスをスケーリングする方法を示します。 スケーリングには、スケールアップとスケールアウトの 2 つのシナリオがあります。

* [スケールアップ](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling):ユニット、接続、メッセージなどの数を増やします。 価格レベルを Free から Standard に変更してスケールアップします。
* [スケールアウト](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling):SignalR ユニットの数を増やします。 最大 100 ユニットまでスケールアウトできます。 スケーリングのために選択できる単位オプションは限られています。単一の SignalR サービス インスタンスに対して 1、2、5、10、20、50、および 100 ユニットです。

スケール設定が適用されるまでに数分かかります。 まれに、適用されるまでに 30 分程度かかる場合があります。 コードの変更やサーバー アプリケーションの再デプロイは必要ありません。

個別の SignalR Service の価格および容量の詳細については、[Azure SignalR Service の価格詳細](https://azure.microsoft.com/pricing/details/signalr-service/)に関するページを参照してください。  

> [!NOTE]
> SignalR Service を **Free** レベルから **Standard** レベルに (またはその逆に) 変更すると、パブリック サービス IP が変更され、インターネット全体で DNS サーバーに変更が反映されるまで通常は 30 分から 60 分かかります。 DNS が更新されるまで、サービスにアクセスできない可能性があります。 一般に、価格レベルを頻繁に変更することはお勧めしません。


## <a name="scale-on-azure-portal"></a>Azure portal でのスケーリング

1. ブラウザーで、[Azure Portal](https://portal.azure.com) を開きます。

2. SignalR Service のページで、左側のメニューから **[Scale]\(スケール\)** を選択します。
   
3. 価格レベルを選んで **[選択]** をクリックします。 **Standard** レベルのユニット数を設定します。
   
    ![ポータルでのスケーリング](./media/signalr-howto-scale/signalr-howto-scale.png)

4. **[保存]** をクリックします。

## <a name="scale-using-azure-cli"></a>Azure CLI を使用したスケーリング

このスクリプトは、**Free** レベルの新しい SignalR Service リソースと新しいリソース グループを作成し、**Standard** レベルにスケールアップします。 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Free_F1 \
  --service-mode Default

# Scale up to Standard Tier, and scale out to 50 units
az signalr update \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 50
```

新しいリソース グループに対して生成された実際の名前はメモしておいてください。 このリソース グループ名は、すべてのグループ リソースを削除するときに使います。

[!INCLUDE [cli-script-clean-up](../../includes/cli-script-clean-up.md)]

## <a name="compare-pricing-tiers"></a>価格レベルの比較

それぞれの価格レベルに含まれるメッセージや接続などの詳細については、[SignalR Service の価格詳細](https://azure.microsoft.com/pricing/details/signalr-service/)に関するページを参照してください。

サービスの制限、クォータ、各レベルでの制約の表は、[SignalR Service の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

このガイドでは、1 つの SignalR Service インスタンスをスケーリングする方法について説明しました。

スケーリング、シャード化、クロスリージョンの各シナリオでは、複数のエンドポイントもサポートされています。

> [!div class="nextstepaction"]
> [複数のインスタンスがある SignalR Service のスケーリング](./signalr-howto-scale-multi-instances.md)
