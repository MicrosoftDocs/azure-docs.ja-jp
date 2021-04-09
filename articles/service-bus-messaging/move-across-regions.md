---
title: Service Bus 名前空間を別のリージョンに移動する | Microsoft Docs
description: この記事では、Azure Service Bus 名前空間を現在のリージョンから別のリージョンに移動する方法について説明します。
ms.topic: how-to
ms.date: 06/23/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 573ea96437cf6cb76854ffa417fd3ad3fb86138b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88860428"
---
# <a name="move-an-azure-service-bus-namespace-to-another-region"></a>Azure Service Bus 名前空間を別のリージョンに移動する
既存の Service Bus 名前空間を、あるリージョンから別のリージョンに移動するシナリオには、さまざまなものがあります。 たとえば、テスト用に同じ構成で名前空間を作成することができます。 [ディザスター リカバリー計画](service-bus-geo-dr.md)の一部として、別のリージョンにセカンダリ名前空間を作成することもできます。

手順の概要は次のとおりです。

1. 現在のリージョンの Service Bus 名前空間を Azure Resource Manager テンプレートにエクスポートします。 
1. テンプレート内のリソースの場所を更新します。 また、既定のルールは自動的に作成され、ユーザーが作成することはできないため、テンプレートから既定のサブスクリプション フィルターを削除します。 
1. テンプレートを使用して、Service Bus 名前空間をターゲット リージョンにデプロイします。 
1. デプロイを検証して、トピックの名前空間、キュー、トピック、およびサブスクリプションがすべてターゲット リージョンに作成されていることを確認します。 
1. すべてのメッセージを処理した後で、ソース リージョンから名前空間を削除して移動を完了します。 

## <a name="prerequisites"></a>前提条件
お使いのアカウントで使用している Azure Service Bus と機能が、ターゲット リージョンでサポートされていることを確認してください。
 
## <a name="prepare"></a>準備
まず、Resource Manager テンプレートをエクスポートします。 このテンプレートには、Service Bus 名前空間を説明する設定が含まれています。

1. [Azure portal](https://portal.azure.com) にサインインする
2. **[すべてのリソース]** を選択し、Service Bus 名前空間を選択します。
3. **[設定]**  >  **[テンプレートのエクスポート]** を選択します。
4. **[テンプレートのエクスポート]** ページで **[ダウンロード]** を選択します。

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Resource Manager テンプレートをダウンロードする":::
5. ポータルからダウンロードした .zip ファイルを見つけて、選択したフォルダーにそのファイルを解凍します。 この ZIP ファイルには、テンプレートとパラメーターの JSON ファイルが含まれています。 
1. 抽出したフォルダー内の template.json ファイルを開きます。 
1. `location` を検索し、プロパティの値を、リージョンまたは場所の新しい名前に置き換えます。 場所コードを取得するには、[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)に関するページを参照してください。 リージョンのコードは、スペースを含まないリージョン名です (例えば、`West US` は `westus` と同じです)。
1. 種類が `Microsoft.ServiceBus/namespaces/topics/subscriptions/rules` のリソースの定義を削除します。 JSON を有効な状態に保つには、このセクションの前にあるコンマ (`,`) 文字を忘れずに削除してください。  

    > [!NOTE]
    > Resource Manager テンプレートを使用して、サブスクリプションの既定のルールを作成することはできません。 既定のルールは、ターゲット リージョンにサブスクリプションが作成されたときに自動的に作成されます。 

## <a name="move"></a>詳細ビュー
テンプレートをデプロイして、ターゲット リージョンに Service Bus 名前空間を作成します。 

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
1. **[リソース グループ]** ページで、Service Bus 名前空間を選択します。 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Service Bus 名前空間の選択":::    
1. **[Service Bus 名前空間]** ページに、ソース リージョンのキュー、トピック、およびサブスクリプションが表示されていることを確認します。 
    1. **キュー** は、右側のウィンドウの下部にある名前空間に表示されています。         
    
        :::image type="content" source="./media/move-across-regions/queue-namespace.png" alt-text="名前空間のキュー":::
    2. **[トピック]** タブに切り替えると、名前空間のトピックが表示されます
    
        :::image type="content" source="./media/move-across-regions/topics-namespace.png" alt-text="名前空間のトピック":::
    3. トピックを選択して、サブスクリプションが作成されたことを確認します。 

        :::image type="content" source="./media/move-across-regions/topic-subscriptions.png" alt-text="トピック サブスクリプション":::      
    
    

## <a name="discard-or-clean-up"></a>破棄またはクリーンアップ
デプロイ後に、最初からやり直す場合は、**ターゲット Service Bus 名前空間** を削除し、この記事の「[準備](#prepare)」と「[移動](#move)」のセクションに示されている手順を繰り返します。

変更をコミットし、Service Bus 名前空間の移動を完了するには、**ソース Service Bus 名前空間** を削除します。 名前空間を削除する前に、すべてのメッセージを処理するようにしてください。 

Azure portal を使用して Service Bus 名前空間 (ソースまたはターゲット) を削除するには、次の手順を行います。

1. Azure portal の上部にある検索ウィンドウで「**Service Bus**」と入力し、検索結果から **[Service Bus]** を選択します。 Service Bus 名前空間が一覧に表示されます。
2. 削除するターゲット名前空間を選択し、ツールバーの **[削除]** を選択します。 

    ![名前空間の削除 - ボタン](./media/move-across-regions/delete-namespace-button.png)
3. **[リソースの削除]** ページで、選択したリソースを確認し、「**yes**」と入力して削除を確定し、 **[削除]** を選択します。 

    その他のオプションとして、Service Bus 名前空間を持つリソース グループを削除する方法があります。 **[リソース グループ]** ページで、ツール バーの **[リソース グループの削除]** を選択し、削除を確定します。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Service Bus 名前空間をあるリージョンから別のリージョンに移動し、ソース リソースをクリーンアップしました。  リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、以下を参照してください。

- [リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)
