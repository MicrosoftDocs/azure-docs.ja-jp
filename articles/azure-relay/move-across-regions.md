---
title: Azure Relay 名前空間を別のリージョンに移動する
description: この記事では、Azure Relay 名前空間を現在のリージョンから別のリージョンに移動する方法について説明します。
ms.topic: how-to
ms.date: 09/03/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 60a182764639341fcda159356dd9fe6c65cfabd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89463320"
---
# <a name="move-an-azure-relay-namespace-to-another-region"></a>Azure Relay 名前空間を別のリージョンに移動する
この記事では、Azure Relay 名前空間をあるリージョンから別のリージョンに移動する方法について説明します。 手順の概要は次のとおりです。

1. Relay 名前空間を Azure Resource Manager テンプレートに **エクスポート** します。
1. テンプレート内のリソースの **場所 (リージョン) を更新** します。 また、テンプレートから **動的** な WCF Relay を削除します。 

    WCF Relay には 2 つのモードがあります。 最初のモードでは、WCF Relay は Azure portal または Azure Resource Manager テンプレートを使用して明示的に作成されます。 Azure portal の **[WCF Relay]** ページでは、このモードのリレーに対して **isDynamic** プロパティが **false** に設定されています。 

    2 番目のモードでは、リスナー (サーバー) が特定のエンドポイント アドレスに接続されると、WCF Relay が自動生成されます。 リスナーがリレーに接続されていれば、Azure portal の WCF Relay の一覧にそのリレーが表示されます。 このモードのリレーの場合、動的に生成されるため、**isDynamic** プロパティは **true** に設定されます。 リスナーが切断されると、動的 WCF Relay は解除されます。 
1. テンプレートを使用して、リソースをターゲット リージョンに **デプロイ** します。

## <a name="prerequisites"></a>前提条件
ターゲット リージョンで Azure Relay サービスを使用できることを確認します。 「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=service-bus&regions=all)」を参照してください。 
 
## <a name="prepare"></a>準備
まず、Resource Manager テンプレートをエクスポートします。 このテンプレートには、Azure Relay 名前空間について記述する設定が含まれています。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. **[すべてのリソース]** を選択し、Azure Relay 名前空間を選択します。
3. 左側のメニューの **[設定]** の下にある **[テンプレートのエクスポート]** を選択します。
4. **[テンプレートのエクスポート]** ページで **[ダウンロード]** を選択します。

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Resource Manager テンプレートをダウンロードする":::
5. ポータルからダウンロードした .zip ファイルを見つけて、選択したフォルダーにそのファイルを解凍します。 この ZIP ファイルには、テンプレートとパラメーターの JSON ファイルが含まれています。 
1. 任意のエディターで、解凍したフォルダーから **template.json** ファイルを開きます。
1. `location` を探して、プロパティの値を、リージョンの新しい名前に置き換えます。 場所コードを取得するには、[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)に関するページを参照してください。 リージョンのコードは、スペースを含まないリージョン名です (例えば、`West US` は `westus` と同じです)。
1. **動的 WCF Relay** リソース (種類: `Microsoft.Relay/namespaces/WcfRelays`) の定義を削除します。 動的 WCF Relay は、 **[リレー]** ページで **isDynamic** プロパティが **true** に設定されているものです。 次の例では、**echoservice** が動的 WCF Relay であり、その定義をテンプレートから削除する必要があります。 

    :::image type="content" source="./media/move-across-regions/dynamic-relays.png" alt-text="動的リレー":::

## <a name="move"></a>詳細ビュー
テンプレートをデプロイして、ターゲット リージョンに Relay 名前空間を作成します。 

1. Azure Portal で、 **[リソースの作成]** を選択します。
2. **[Marketplace を検索]** に、検索テキストとして「**template deployment**」と入力し、 **[Template deployment (deploy using custom templates)]** を選択して、**ENTER** キーを押します。

    :::image type="content" source="./media/move-across-regions/new-template-deployment.png" alt-text="新しい template deployment":::    
1. **[Template deployment]** ページで **[作成]** を選択します。

    :::image type="content" source="./media/move-across-regions/template-deployment-create-button.png" alt-text="[新しい Template deployment] - [作成] ボタン":::        
1. **[カスタム デプロイ]** ページで、 **[エディターで独自のテンプレートを作成する]** を選択します。

    :::image type="content" source="./media/move-across-regions/build-template-link.png" alt-text="[エディターで独自のテンプレートを作成する] - リンク":::            
1. **[テンプレートの編集]** ページでツール バーの **[ファイルの読み込み]** を選択し、手順に従って、前のセクションでダウンロードした **template.json** ファイルを読み込みます。

    :::image type="content" source="./media/move-across-regions/select-template.png" alt-text="テンプレートを選択する":::                
1. **[保存]** を選択してテンプレートを保存します。 

    :::image type="content" source="./media/move-across-regions/save-template.png" alt-text="テンプレートを保存する":::                    
1. **[カスタム デプロイ]** ページで、次の手順を行います。 
    1. Azure **サブスクリプション** を選択します。 
    2. 既存の **リソース グループ** を選択するか、新しく作成することができます。 
    3. ターゲットの **場所** またはリージョンを選択します。 既存のリソース グループを選択した場合、この設定は読み取り専用になります。 
    4. **名前空間の新しい名前** を入力します。
    1. **[Review + create]\(レビュー + 作成\)** を選択します。 

        :::image type="content" source="./media/move-across-regions/deploy-template.png" alt-text="Resource Manager テンプレートをデプロイする":::
    1. **[確認と作成]** ページで、ページの下部にある **[作成]** ボタンをクリックします。 
    
## <a name="verify"></a>確認
1. デプロイが正常に完了したら、 **[リソース グループに移動]** を選択します。

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="[リソース グループに移動] リンク":::    
1. **[リソース グループ]** ページで、Azure Relay 名前空間を選択します。 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Azure Relay 名前空間の選択":::    
1. **[Azure Relay 名前空間]** ページで、左側のメニューの **[ハイブリッド接続]** または **[WCF Relay]** を選択して、ハイブリッド接続と WCF Relay が作成されていることを確認します。 テンプレートをインポートする前に、動的 WCF Relay の定義を削除することを忘れた場合は、 **[WCF Relay]** ページで削除してください。 動的 WCF Relay は、クライアントが Relay 名前空間に接続するときに自動的に作成されます。 

## <a name="discard-or-clean-up"></a>破棄またはクリーンアップ
デプロイ後に、最初からやり直す場合は、**ターゲット Azure Relay 名前空間** を削除し、この記事の「[準備](#prepare)」と「[移動](#move)」のセクションに示されている手順を繰り返します。

変更をコミットして名前空間の移動を完了するには、ソース リージョンの **Azure Relay 名前空間** を削除します。 

Azure portal を使用して Azure Relay 名前空間 (ソースまたはターゲット) を削除するには、次の手順を行います。

1. Azure portal の上部にある検索ウィンドウで「**リレー**」と入力し、検索結果の **[サービス]** から **[リレー]** を選択します。 すべての Azure Relay 名前空間が一覧に表示されます。
2. 削除する対象の名前空間を選択し、 **[リレー]** ページを開きます。 
1. **[リレー]** ページで、ツール バーから **[削除]** を選択します。 

    ![名前空間の削除 - ボタン](./media/move-across-regions/delete-namespace-button.png)
3. **[名前空間の削除]** ページで、Azure Relay 名前空間の名前を入力して削除を確認し、 **[削除]** を選択します。 

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure Relay 名前空間をあるリージョンから別のリージョンに移動しました。 リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、以下を参照してください。

- [リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)
