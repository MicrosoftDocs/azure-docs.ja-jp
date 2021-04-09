---
title: Azure Event Grid のパートナー名前空間を別のリージョンに移動する
description: この記事では、Azure Event Grid のパートナー名前空間をあるリージョンから別のリージョンに移動する方法について説明します。
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: 6783db6b9bb1c7d48b308234a179925d6f30e281
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89082212"
---
# <a name="move-azure-event-grid-partner-namespaces-to-another-region"></a>Azure Event Grid のパートナー名前空間を別のリージョンに移動する
さまざまな理由により、リソースを別のリージョンに移動する場合があります。 たとえば、新しい Azure リージョンを利用するため、内部ポリシーとガバナンスの要件を満たすため、または容量計画の要件に応じるためです。 

この記事で説明する手順の概要を次に示します。 

- **パートナー名前空間リソースを Azure Resource Manager テンプレートにエクスポート** します。 テンプレート内のイベント チャネル リソースの定義を削除します。 イベント チャネルには、お客様が所有するパートナー トピックの Azure Resource Manager ID への参照が含まれている場合があります。 そのため、ターゲット リージョンでテンプレートを使用して作成することはできません。  
- **テンプレートを使用して、パートナー名前空間をターゲット リージョンにデプロイ** します。 次に、ターゲット リージョンの新しいパートナー名前空間にイベント チャネルを作成します。 
- **移動を完了する** には、ソース リージョンからパートナー名前空間を削除します。 

    > [!NOTE]
    > - お客様はパートナー トピックを直接作成することはできないため、**パートナー トピック** を Azure Resource Manager テンプレートにエクスポートすることはサポートされていません。 
    > - **パートナー登録** はグローバル リソースであるため (特定の地域に関連付けられていません)、あるリージョンから別のリージョンに移動することはできません。 

## <a name="prerequisites"></a>前提条件
- ターゲット リージョンで Event Grid サービスを使用できることを確認します。 「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)」を参照してください。

## <a name="prepare"></a>準備
まず、パートナー名前空間の Resource Manager テンプレートをエクスポートします。 

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 上部の検索バーに「**Event Grid のパートナー名前空間**」と入力し、結果一覧から **[Event Grid のパートナー名前空間]** を選択します。 
3. Resource Manager テンプレートにエクスポートする **パートナー名前空間** を選択します。 
4. **[Event Grid のパートナー名前空間]** ページで、左側のメニューの **[設定]** にある **[テンプレートのエクスポート]** を選択し、ツール バーの **[ダウンロード]** を選択します。 

    :::image type="content" source="./media/move-partner-namespaces-across-regions/download-template.png" alt-text="[テンプレートのエクスポート] -> [ダウンロード]" lightbox="./media/move-partner-namespaces-across-regions/download-template.png":::   
5. ポータルからダウンロードした **.zip** ファイルを見つけて、選択したフォルダーにそのファイルを解凍します。 この ZIP ファイルには、テンプレートとパラメーターの JSON ファイルが含まれています。 
1. 任意のエディターで **template.json** を開きます。 
8. **トピック** リソースの `location` をターゲット リージョンまたは場所に更新します。 場所コードを取得するには、[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)に関するページを参照してください。 リージョンのコードは、スペースを含まないリージョン名です (例えば、`West US` は `westus` と同じです)。

    ```json
    {
        "type": "Microsoft.EventGrid/partnerNamespaces",
        "apiVersion": "2020-04-01-preview",
        "name": "[parameters('partnerNamespace_name')]",
        "location": "westus",
        "properties": {
            "partnerRegistrationFullyQualifiedId": "[parameters('partnerRegistrations_ContosoCorpAccount1_externalid')]"
        }
    }
    ``` 
1. テンプレートを **[保存]** します。 

## <a name="recreate"></a>再作成する 
テンプレートをデプロイして、ターゲット リージョンにパートナー名前空間を作成します。 

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
    1. **[場所]** で、ターゲット リージョンを選択します。 既存のリソース グループを選択した場合、この設定は読み取り専用になります。 
    1. **[パートナー名前空間名]** に、新しいパートナー名前空間の名前を入力します。 
    1. パートナー登録の外部 ID に、`/subscriptions/<Azure subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.EventGrid/partnerRegistrations/<Partner registration name>` という形式でパートナー登録のリソース ID を入力します。
    1. **[上記の使用条件に同意する]** チェック ボックスをオンにします。     
    1. **[確認および作成]** を選択してデプロイ プロセスを開始します。 
    1. **[確認および作成]** ページで、設定を確認し、 **[作成]** を選択します。 

## <a name="discard-or-clean-up"></a>破棄またはクリーンアップ
移動を完了するには、ソース リージョンのパートナー名前空間を削除します。  

最初からやり直す場合は、ターゲット リージョンのパートナー名前空間を削除し、この記事の「[準備](#prepare)」と「[再作成する](#recreate)」のセクションの手順を繰り返します。

Azure portal を使用してパートナー名前空間を削除するには:

1. Azure portal の上部にある検索ウィンドウで、「**Event Grid のパートナー名前空間**」と入力し、検索結果から **[Event Grid のパートナー名前空間]** を選択します。 
2. 削除するパートナー名前空間を選択し、ツール バーの **[削除]** を選択します。 
3. 削除の操作を **[確認]** して、パートナー名前空間を削除します。 

## <a name="next-steps"></a>次のステップ
Azure Event Grid のパートナー名前空間をあるリージョンから別のリージョンに移動する方法について学習しました。 リージョン間でシステム トピック、カスタム トピック、およびドメインを移動するには、次の記事を参照してください。

- [リージョン間でのシステム トピックの移動](move-system-topics-across-regions.md)。 
- [リージョン間でのカスタム トピックの移動](move-custom-topics-across-regions.md)。 
- [リージョン間でのドメインの移動](move-domains-across-regions.md)。

リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、「[リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)」という記事を参照してください。