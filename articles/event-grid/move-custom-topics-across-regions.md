---
title: Azure Event Grid カスタム トピックを別のリージョンに移動する
description: この記事では、Azure Event Grid カスタム トピックをあるリージョンから別のリージョンに移動する方法について説明します。
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: d0656a4f6ec1c7431cf7111f786b0f1d779166e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89145343"
---
# <a name="move-azure-event-grid-custom-topics-to-another-region"></a>Azure Event Grid カスタム トピックを別のリージョンに移動する
さまざまな理由により、リソースを別のリージョンに移動する場合があります。 たとえば、新しい Azure リージョンを利用するため、内部ポリシーとガバナンスの要件を満たすため、または容量計画の要件に応じるためです。 

この記事で説明する手順の概要を次に示します。 

- **カスタム トピック リソースを Azure Resource Manager テンプレートにエクスポートします**。 

    > [!IMPORTANT]
    > カスタム トピックのみがテンプレートにエクスポートされます。 トピックのサブスクリプションはエクスポートされません。
- **テンプレートを使用して、カスタム トピックをターゲット リージョンにデプロイします**。 
- **ターゲット リージョンでサブスクリプションを手動で作成します**。 現在のリージョンでカスタム トピックをテンプレートにエクスポートすると、トピックのみがエクスポートされます。 サブスクリプションはテンプレートに含まれていないため、ターゲット リージョンでカスタム トピックを作成した後に手動で作成してください。 
- **展開を確認する**。 ターゲット リージョンでカスタム トピックが作成されていることを確認します。 
- **移動を完了する** には、ソース リージョンからカスタム トピックを削除します。 

## <a name="prerequisites"></a>前提条件
- 「[Quickstart:カスタム イベントを Web エンドポイントにルーティングする](custom-event-quickstart-portal.md)」をソース リージョンで完了します。 このステップを実行すると、この記事の手順をテストできるようになります。 
- ターゲット リージョンで Event Grid サービスを使用できることを確認します。 「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)」を参照してください。

## <a name="prepare"></a>準備
まず、カスタム トピックの Resource Manager テンプレートをエクスポートします。 

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 検索バーに「**Event Grid トピック**」と入力し、結果リストから **[Event Grid トピック]** を選択します。 

    :::image type="content" source="./media/move-custom-topics-across-regions/search-topics.png" alt-text="Event Grid トピック を検索して選択する":::
3. Resource Manager テンプレートにエクスポートする **トピック** を選択します。 

    :::image type="content" source="./media/move-custom-topics-across-regions/select-custom-topic.png" alt-text="カスタム トピックを選択する":::   
4. **[Event Grid トピック]** ページで、左側のメニューの **[設定]** にある **[テンプレートのエクスポート]** を選択し、ツール バーの **[ダウンロード]** を選択します。 

    :::image type="content" source="./media/move-custom-topics-across-regions/export-template-download.png" alt-text="[テンプレートのエクスポート] -> [ダウンロード]":::   

    > [!IMPORTANT]
    > トピックのみがテンプレートにエクスポートされます。 トピックのサブスクリプションはエクスポートされません。 そのため、トピックをターゲット リージョンに移動した後に、トピックのサブスクリプションを作成する必要があります。 
5. ポータルからダウンロードした **.zip** ファイルを見つけて、選択したフォルダーにそのファイルを解凍します。 この ZIP ファイルには、テンプレートとパラメーターの JSON ファイルが含まれています。 
1. 任意のエディターで **template.json** を開きます。 
8. **トピック** リソースの `location` をターゲット リージョンまたは場所に更新します。 場所コードを取得するには、[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)に関するページを参照してください。 リージョンのコードは、スペースを含まないリージョン名です (例えば、`West US` は `westus` と同じです)。

    ```json
    "type": "Microsoft.EventGrid/topics",
    "apiVersion": "2020-06-01",
    "name": "[parameters('topics_mytopic0130_name')]",
    "location": "westus"
    ```
1. テンプレートを **[保存]** します。 

## <a name="recreate"></a>再作成する 
テンプレートをデプロイして、ターゲット リージョンでカスタム トピックを作成します。 

1. Azure Portal で、 **[リソースの作成]** を選択します。
2. **[Marketplace を検索]** で「**template deployment**」と入力し、**Enter** キーを押します。
3. **[テンプレートのデプロイ]** を選択します。
4. **［作成］** を選択します
5. **[Build your own template in the editor] \(エディターで独自のテンプレートをビルド\)** を選択します。
6. **[ファイルの読み込み]** を選択し、手順に従って、前のセクションでダウンロードした **template.json** ファイルを読み込みます。
7. **[保存]** を選択してテンプレートを保存します。 
8. **[カスタム デプロイ]** ページで、次の手順を行います。 
    1. Azure **サブスクリプション** を選択します。 
    1. ターゲット リージョンの既存の **リソース グループ** を選択するか、新しく作成します。 
    1. **[リージョン]** で、ターゲット リージョンを選択します。 既存のリソース グループを選択した場合、この設定は読み取り専用になります。 
    1. **トピック名** には、トピックの新しい名前を入力します。 
    1. ページ下部にある **[確認と作成]** を選択します。 
    
        :::image type="content" source="./media/move-custom-topics-across-regions/deploy-template.png" alt-text="カスタム デプロイ":::
    1. **[確認および作成]** ページで、設定を確認し、 **[作成]** を選択します。 

## <a name="verify"></a>確認

1. デプロイが成功したら、 **[リソースに移動]** を選択します。 

    :::image type="content" source="./media/move-custom-topics-across-regions/navigate-custom-topic.png" alt-text="リソースに移動":::
1. カスタム トピックの **[Event Grid トピック]** ページが表示されていることを確認します。   
1. 「[カスタム イベントを Web エンドポイントにルーティングする](custom-event-quickstart-portal.md#send-an-event-to-your-topic)」の手順に従って、イベントをトピックに送信します。 Webhook イベント ハンドラーが呼び出されていることを確認します。 

## <a name="discard-or-clean-up"></a>破棄またはクリーンアップ
移動を完了するには、ソース リージョンでカスタム トピックを削除します。  

最初からやり直す場合は、ターゲット リージョンでトピックを削除し、この記事の「[準備](#prepare)」と「[再作成する](#recreate)」のセクションの手順を繰り返します。

Azure portal を使用してカスタム トピックを削除するには:

1. Azure portal の上部にある検索ウィンドウで「**Event Grid トピック**」と入力し、検索結果から **[Event Grid トピック]** を選択します。 
2. 削除するトピックを選択し、ツール バーから **[削除]** を選択します。 
3. [確認] ページで、リソース グループの名前を入力し、 **[削除]** を選択します。  

Azure portal を使用して、カスタム トピックを含むリソース グループを削除するには:

1. Azure portal の上部にある検索ウィンドウで「**リソース グループ**」と入力し、検索結果から **[リソース グループ]** を選択します。 
2. 削除するリソース グループを選択し、ツール バーの **[削除]** を選択します。 
3. [確認] ページで、リソース グループの名前を入力し、 **[削除]** を選択します。  

## <a name="next-steps"></a>次のステップ
Azure Event Grid カスタム トピックをあるリージョンから別のリージョンに移動する方法について学習しました。 リージョン間でシステム トピック、ドメイン、およびパートナー名前空間を移動するには、次の記事を参照してください。

- [リージョン間でシステム トピックを移動する](move-system-topics-across-regions.md)。 
- [リージョン間でドメインを移動する](move-domains-across-regions.md)。 
- [リージョン間でパートナー名前空間を移動する](move-partner-namespaces-across-regions.md)。

リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、「[リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)」という記事を参照してください。