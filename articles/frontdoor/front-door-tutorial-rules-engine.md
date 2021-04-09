---
title: チュートリアル:ルール エンジンを構成する - Azure Front Door
description: この記事では、Azure portal と CLI の両方でルール エンジンを構成する方法のチュートリアルを提供します。
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/09/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5d26d36c9f4ecb4aa0c7114b1ebef066e104f175
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198865"
---
# <a name="tutorial-configure-your-rules-engine"></a>チュートリアル:ルール エンジンを構成する

このチュートリアルでは、Azure portal と CLI の両方でルール エンジン構成と最初のルールを作成する方法を説明します。 

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - ポータルを使用してルール エンジンを構成する。
> - Azure CLI を使用してルール エンジンを構成する

## <a name="prerequisites"></a>前提条件

* このチュートリアルの手順を完了するには、最初に Front Door を作成する必要があります。 詳細については、「[クイック スタート: Front Door の作成](quickstart-create-front-door.md)」を参照してください。

## <a name="configure-rules-engine-in-azure-portal"></a>Azure portal でルール エンジンを構成する
1. お使いのフロント ドア リソース内で、 **[設定]** に移動し、 **[Rule Engine configuration]\(ルール エンジン構成\)** を選択します。 **[追加]** をクリックし、構成に名前を付けて、最初のルール エンジン構成の作成を開始します。

    ![Front Door の設定メニュー](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

1. **[ルールの追加]** をクリックして、最初のルールを作成します。 次に、 **[条件の追加]** または **[アクションの追加]** をクリックして、ルールを定義できます。
    
    > [!NOTE]
    >- 条件またはアクションをルールから削除するには、特定の条件またはアクションの右側にあるごみ箱を使用します。
    > - すべての受信トラフィックに適用されるルールを作成するには、条件を指定しないでください。
    > - 最初の一致条件が満たされたときにルールの評価を停止するには、 **[Stop evaluating remaining rule]\(残りのルールの評価を停止する\)** をオンにします。 これが選択され、特定のルールの一致条件をすべて満たしている場合、構成内の残りのルールは実行されません。  

    ![ルール エンジンの構成](./media/front-door-rules-engine/rules-engine-tutorial-4.png) 

1. [Move up]\(上に移動\)、[Move down]\(下に移動\)、および [Move to top]\(最上位に移動\) ボタンを使用して、構成内のルールの優先度を決定します。 優先度は昇順です。つまり、最初に表示されるルールが最も重要なルールです。

1. 1 つまたは複数のルールを作成したら、 **[保存]** を押します。 このアクションにより、ルール エンジン構成が作成されます。

1. 1 つまたは複数の構成を作成したら、ルール エンジン構成をルート ルールに関連付けます。 1 つの構成を複数のルート ルールに適用できますが、1 つのルート ルールに含めることができるルール エンジン構成は 1 つだけです。 関連付けを行うには、 **[Front Door デザイナー]**  >  **[Route rules]\(ルート ルール\)** にアクセスします。 ルール エンジン構成を追加するルート ルールを選択し、 **[ルートの詳細]**  >  **[Rules engine configuration]\(ルール エンジン構成\)** に移動して、関連付ける構成を選択します。

    ![ルーティング規則を構成する](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Azure CLI でルール エンジンを構成する

1. [Azure CLI](/cli/azure/install-azure-cli) をまだインストールしていない場合はインストールします。 "front-door" 拡張機能を追加します:- az extension add --name front-door。 次に、ログインし、お使いのサブスクリプションに切り替えます: az account set --subscription <名前または ID>。

1. 最初に、ルール エンジンを作成します。この例は、1 つのヘッダーベースのアクションと 1 つの一致条件を持つ 1 つのルールを示しています。 

    ```azurecli-interactive
    az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
    ```

1. すべてのルールを一覧表示します。 

    ```azurecli-interactive
    az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
    ```

1. 転送ルートのオーバーライド アクションを追加します。 

    ```azurecli-interactive
    az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
    ```

1. ルール内のすべてのアクションを一覧表示します。 

    ```azurecli-interactive
    az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
    ```

1. ルール エンジン構成をルーティング規則にリンクします。  

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
    ```

1. ルール エンジンのリンクを解除します。 

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
    ```

詳細については、AFD ルール エンジン コマンドの完全な一覧を[こちら](/cli/azure/ext/front-door/network/front-door/rules-engine)で確認してください。   

## <a name="clean-up-resources"></a>リソースをクリーンアップする

前の手順では、ルール エンジンを構成し、この構成をルーティング規則に関連付けました。 ルール エンジンの構成と Front Door の関連付けを解除する場合は、次の手順を実行することで構成を削除できます。

1. [Rule Engine name]\(ルール エンジン名\) の横にある 3 つのドットをクリックして、ルール エンジン構成からルーティング規則の関連付けを解除します。

    :::image type="content" source="./media/front-door-rules-engine/front-door-rule-engine-routing-association.png" alt-text="[Associate routing rules]\(ルーティング規則の関連付け\)":::

1. このルール エンジン構成が関連付けられているすべてのルーティング規則をオフにし、[Save]\(保存\) をクリックします。

    :::image type="content" source="./media/front-door-rules-engine/front-door-routing-rule-association.png" alt-text="[Routing rule association]\(ルーティング規則の関連付け\)":::

1. これで、Front Door からルール エンジン構成を削除できるようになりました。

    :::image type="content" source="./media/front-door-rules-engine/front-door-delete-rule-engine-configuration.png" alt-text="ルール エンジン構成を削除する":::

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

* ルール エンジンの構成を作成する
* 構成を Front Door のルーティング規則に関連付ける。

ルール エンジンを使用してセキュリティ ヘッダーを追加する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [ルール エンジンを使用したセキュリティ ヘッダー](front-door-security-headers.md)