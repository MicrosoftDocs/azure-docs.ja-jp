---
title: Azure Event Grid システム トピックを別のリージョンに移動する
description: この記事では、Azure Event Grid システム トピックをあるリージョンから別のリージョンに移動する方法について説明します。
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: eb6029b206e7d47789371ee81e75c4e05c69ee65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89082201"
---
# <a name="move-azure-event-grid-system-topics-to-another-region"></a>Azure Event Grid システム トピックを別のリージョンに移動する
さまざまな理由により、リソースを別のリージョンに移動する場合があります。 たとえば、新しい Azure リージョンを利用するため、内部ポリシーとガバナンスの要件を満たすため、または容量計画の要件に応じるためです。 

この記事で説明する手順の概要を次に示します。 

- Azure Storage アカウントとそれに関連付けられているシステム トピックを含む **リソース グループを Resource Manager テンプレートにエクスポートします**。 また、システム トピックのみのテンプレートをエクスポートすることもできます。 この方法を使用する場合は、システム トピックを移動する前に、Azure イベント ソース (この例では Azure Storage アカウント) を他のリージョンに移動することを忘れないでください。 その後、エクスポートされたシステム トピックのテンプレートで、ターゲット リージョンのストレージ アカウントの外部 ID を更新します。 
- **テンプレートを変更** して、システム トピックをサブスクライブする webhook を指すように `endpointUrl` プロパティを追加します。 システム トピックをエクスポートすると、そのサブスクリプション (この場合は webhook) もテンプレートにエクスポートされますが、`endpointUrl` プロパティは含められません。 そのため、トピックをサブスクライブするエンドポイントを指すように更新する必要があります。 また、`location` プロパティの値を新しい場所またはリージョンに更新します。 その他の種類のイベント ハンドラーの場合は、場所のみを更新する必要があります。 
- **テンプレートを使用して、リソースをターゲット リージョンにデプロイします**。 ターゲット リージョンに作成されるストレージ アカウントとシステム トピックの名前を指定します。 
- **展開を確認する**。 ターゲット リージョンの BLOB ストレージにファイルをアップロードするときに、webhook が呼び出されることを確認します。 
- **移動を完了する** には、ソース リージョンからリソース (イベント ソースとシステム トピック) を削除します。 

## <a name="prerequisites"></a>前提条件
- 「[Quickstart:Azure portal で Blob Storage のイベントを Web エンドポイントにルーティングする](blob-event-quickstart-portal.md)」をソース リージョンで実行します。 この手順は **省略可能** です。 この記事の手順をテストする場合に実行します。 ストレージ アカウントは、App Service および App Service プランとは別のリソース グループに保持します。 
- ターゲット リージョンで Event Grid サービスを使用できることを確認します。 「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)」を参照してください。

## <a name="prepare"></a>準備
開始するには、システム イベントのソース (Azure Storage アカウント) とそれに関連付けられているシステム トピックが含まれているリソース グループの Resource Manager テンプレートをエクスポートします。 

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側のメニューの **[リソース グループ]** を選択します。 次に、システム トピックが作成されたイベント ソースが含まれているリソース グループを選択します。 次の例では、**Azure Storage** アカウントです。 リソース グループには、ストレージ アカウントとそれに関連付けられているシステム トピックが含まれています。 

    :::image type="content" source="./media/move-system-topics-across-regions/resource-group-page.png" alt-text="[リソース グループ] ページ":::        
3. 左側のメニューで、 **[設定]** にある **[テンプレートのエクスポート]** を選択し、ツール バーの **[ダウンロード]** を選択します。 

    :::image type="content" source="./media/move-system-topics-across-regions/export-template-menu.png" alt-text="ストレージ アカウント - [テンプレートのエクスポート] ページ":::        
5. ポータルからダウンロードした **.zip** ファイルを見つけて、選択したフォルダーにそのファイルを解凍します。 この ZIP ファイルには、テンプレートとパラメーターの JSON ファイルが含まれています。 
1. 任意のエディターで **template.json** を開きます。 
1. Webhook の URL は、テンプレートにエクスポートされません。 そのため、次の手順を実行します。
    1. テンプレート ファイルで、**WebHook** を探します。 
    1. **Properties** セクションで、最後の行の末尾にコンマ (`,`) 文字を追加します。 この例では `"preferredBatchSizeInKilobytes": 64` です。 
    1. 次の例に示すように、`endpointUrl` プロパティを追加し、値を Webhook の URL に設定します。 

        ```json
        "destination": {
            "properties": {
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64,
                "endpointUrl": "https://mysite.azurewebsites.net/api/updates"
            },
            "endpointType": "WebHook"
        }
        ```

        > [!NOTE]
        > その他の種類のイベント ハンドラーの場合は、すべてのプロパティがテンプレートにエクスポートされます。 次の手順で示すように、`location` プロパティをターゲット リージョンに更新する必要があるだけです。 
7. **ストレージ アカウント** リソースの `location` をターゲット リージョンまたは場所に更新します。 場所コードを取得するには、[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)に関するページを参照してください。 リージョンのコードは、スペースを含まないリージョン名です (例えば、`West US` は `westus` と同じです)。

    ```json
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccounts_spegridstorage080420_name')]",
    "location": "westus",
    ```
8. この手順を繰り返して、テンプレートの **システム トピック** リソースの `location` を更新します。 

    ```json
    "type": "Microsoft.EventGrid/systemTopics",
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('systemTopics_spegridsystopic080420_name')]",
    "location": "westus",
    ```
1. テンプレートを **[保存]** します。 

## <a name="recreate"></a>再作成する 
テンプレートをデプロイして、ターゲット リージョンにストレージ アカウントとストレージ アカウントのシステム トピックを作成します。 

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
    1. **システムのトピック名** で、ストレージ アカウントに関連付けるシステム トピックの名前を入力します。  
    1. **ストレージ アカウント名** で、ターゲット リージョンに作成するストレージ アカウントの名前を入力します。 

        :::image type="content" source="./media/move-system-topics-across-regions/deploy-template.png" alt-text="Resource Manager テンプレートをデプロイする":::
    5. ページ下部にある **[確認と作成]** を選択します。 
    1. **[確認および作成]** ページで、設定を確認し、 **[作成]** を選択します。 

## <a name="verify"></a>確認
1. デプロイが成功したら、 **[リソース グループに移動]** を選択します。 
1. **[リソース グループ]** ページで、イベント ソース (この例では Azure Storage アカウント) とシステム トピックが作成されていることを確認します。 
1. Azure BLOB ストレージ内のコンテナーにファイルをアップロードし、webhook がイベントを受信したことを確認します。 詳細については、「[エンドポイントへのイベントの送信](blob-event-quickstart-portal.md#send-an-event-to-your-endpoint)」を参照してください。

## <a name="discard-or-clean-up"></a>破棄またはクリーンアップ
移動を完了するには、ソース リージョンでストレージ アカウントとそれに関連付けられているシステム トピックを含むリソース グループを削除します。  

最初からやり直す場合は、ターゲット リージョンのリソース グループを削除し、この記事の「[準備](#prepare)」と「[再作成する](#recreate)」のセクションの手順を繰り返します。

Azure portal を使用してリソース グループ (ソースまたはターゲット) を削除するには、次の手順を実行します。

1. Azure portal の上部にある検索ウィンドウで「**リソース グループ**」と入力し、検索結果から **[リソース グループ]** を選択します。 
2. 削除するリソース グループを選択し、ツール バーの **[削除]** を選択します。 

    :::image type="content" source="./media/move-system-topics-across-regions/delete-resource-group-button.png" alt-text="リソース グループの削除":::
3. [確認] ページで、リソース グループの名前を入力し、 **[削除]** を選択します。  

## <a name="next-steps"></a>次のステップ
Azure イベント ソースとそれに関連付けられているシステム トピックをあるリージョンから別のリージョンに移動する方法について学習しました。 リージョン間でカスタム トピック、ドメイン、およびパートナー名前空間を移動するには、次の記事を参照してください。

- [リージョン間でのカスタム トピックの移動](move-custom-topics-across-regions.md)。 
- [リージョン間でのドメインの移動](move-domains-across-regions.md)。 
- [リージョン間でパートナー名前空間を移動する](move-partner-namespaces-across-regions.md)。 

リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、「[リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)」という記事を参照してください。
