---
title: 複数値のトラフィックのルーティングを構成する - Azure Traffic Manager
description: この記事では、トラフィックを A/AAAA エンドポイントにルーティングするように Traffic Manager を構成する方法について説明します。
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 85e088dda767a6f6c80ac0a9f6eed84e8802e5ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994929"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>Traffic Manager での複数値ルーティング方法の構成

この記事では、複数値のトラフィックのルーティング方法を構成する方法について説明します。 **複数値** のトラフィックのルーティング方法を使用すると、複数の正常なエンドポイントを返して、アプリケーションの信頼性を向上させることができます。これは、もう一度 DNS 参照を行わなくても再試行することができる、より多くのオプションがクライアントに提供されるためです。 複数値ルーティングは、すべてのエンドポイントが IPv4 または IPv6 アドレスを使用して指定されているプロファイルに対してだけ有効にすることができます。 このプロファイルに対するクエリが受信されると、指定されている構成可能な最大リターン数に基づいて、すべての正常なエンドポイントが返されます。 

>[!NOTE]
> 現時点では、IPv4 または IPv6 アドレスを使用するエンドポイントの追加は、タイプが **外部** のエンドポイントのみでサポートされているため、複数値ルーティングもこのようなエンドポイントに対してのみサポートされます。

## <a name="sign-in-to-azure"></a>Azure へのサインイン 

Azure Portal ( https://portal.azure.com ) にサインインします。
## <a name="create-a-resource-group"></a>リソース グループを作成する
Traffic Manager プロファイル用のリソース グループを作成します。
1. Azure portal の左側のウィンドウで、**[リソース グループ]** を選択します。
2. **[リソース グループ]** で、ページ上部の **[追加]** を選択します。
3. **[リソース グループ名]** で「*myResourceGroupTM1*」という名前を入力します。 **[リソース グループの場所]** で **[米国東部]** を選択し、**[OK]** を選択します。

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成
待機時間が最も短いエンドポイントに送信することでユーザー トラフィックを転送する Traffic Manager プロファイルを作成します。

1. 画面の左上で、 **[リソースの作成]**  >  **[ネットワーク]**  >  **[Traffic Manager プロファイル]**  >  **[作成]** の順に選択します。
2. **[Traffic Manager プロファイルの作成]** で、次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、**[作成]** を選択します。
    
    | 設定                 | 値                                              |
    | ---                     | ---                                                |
    | 名前                   | この名前は trafficmanager.net ゾーン内で一意である必要があります。結果的に、Traffic Manager プロファイルへのアクセスに使用される、DNS 名.trafficmanager.net になるためです。                                   |
    | ルーティング方法          | **[複数値]** ルーティング方式を選択します。                                       |
    | サブスクリプション            | サブスクリプションを選択します。                          |
    | Resource group          | *myResourceGroupTM1* を選択します。 |
    | 場所                | これはリソース グループの場所を指定する設定であり、グローバルにデプロイされる Traffic Manager プロファイルには影響しません。                              |
   |        |           | 
  
   ![Traffic Manager プロファイルの作成](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager エンドポイントの追加

前の手順で作成した複数値 Traffic Manager プロファイルに、外部エンドポイントとして 2 つの IP アドレスを追加します。

1. ポータルの検索バーで、前のセクションで作成した Traffic Manager プロファイルの名前を検索し、表示された結果からそのプロファイルを選択します。
2. **[Traffic Manager プロファイル]** の **[設定]** セクションで **[エンドポイント]** をクリックし、 **[追加]** をクリックします。
3. 次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[OK]** を選択します。

    | 設定                 | 値                                              |
    | ---                     | ---                                                |
    | Type                    | 外部エンドポイント                                   |
    | 名前           | myEndpoint1                                        |
    | 完全修飾ドメイン名 (FQDN) または IP           | この Traffic Manager プロファイルに追加するエンドポイントのパブリック IP アドレスを入力します                         |
    |        |           |

4. 手順 2. と 3. を繰り返して、*myEndpoint2* という名前のもう 1 つのエンドポイントを追加します。**[完全修飾ドメイン名 (FQDN) または IP]** には、2 つ目のエンドポイントのパブリック IP アドレスを入力します。
5. 両方のエンドポイントは、追加が完了すると、 **[Traffic Manager プロファイル]** に、監視ステータスが **[オンライン]** の状態で表示されます。

   ![Traffic Manager エンドポイントの追加](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>次のステップ

- [重み付けによるトラフィック ルーティング方法](traffic-manager-configure-weighted-routing-method.md)について学習します。
- [優先順位によるルーティング方法](traffic-manager-configure-priority-routing-method.md)について学習します。
- [パフォーマンスによるルーティング方法](traffic-manager-configure-performance-routing-method.md)について学習します。
- [地理的なルーティング方法](traffic-manager-configure-geographic-routing-method.md)について学習します。


