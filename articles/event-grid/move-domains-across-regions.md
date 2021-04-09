---
title: Azure Event Grid ドメインを別のリージョンに移動する
description: この記事では、Azure Event Grid ドメインをあるリージョンから別のリージョンに移動する方法について説明します。
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: fff8638a819511f84f15c52ad0695cdd5759f971
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89082213"
---
# <a name="move-azure-event-grid-domains-to-another-region"></a>Azure Event Grid ドメインを別のリージョンに移動する
さまざまな理由により、リソースを別のリージョンに移動する場合があります。 たとえば、新しい Azure リージョンを利用するため、内部ポリシーとガバナンスの要件を満たすため、または容量計画の要件に応じるためです。 

この記事で説明する手順の概要を次に示します。 

- **ドメイン リソースを Azure Resource Manager テンプレートにエクスポートします**。 

    > [!IMPORTANT]
    > ドメイン リソースとドメインのトピックがテンプレートにエクスポートされます。 ドメイン トピックへのサブスクリプションはエクスポートされません。 
- **テンプレートを使用して、ドメインをターゲット リージョンにデプロイします**。 
- ターゲット リージョンで **ドメイン トピックのサブスクリプションを手動で作成します**。 現在のリージョンのテンプレートにドメインをエクスポートした場合、ドメイン トピックのサブスクリプションはエクスポートされません。 そのため、ターゲット リージョンにドメインとドメイン トピックが作成された後にサブスクリプションを作成します。 
- **展開を確認する**。 ドメイン内のドメイン トピックにイベントを送信し、サブスクリプションに関連付けられているイベント ハンドラーが呼び出されることを確認します。 
- **移動を完了する** には、ソース リージョンからドメインを削除します。 

## <a name="prerequisites"></a>前提条件
- ターゲット リージョンで Event Grid サービスを使用できることを確認します。 「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)」を参照してください。

## <a name="prepare"></a>準備
まず、ドメインの Resource Manager テンプレートをエクスポートします。 

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 検索バーに「**Event Grid ドメイン**」と入力し、結果リストから **[Event Grid ドメイン]** を選択します。 

    :::image type="content" source="./media/move-domains-across-regions/search-domains.png" alt-text="Event Grid ドメインを検索して選択する":::
3. Resource Manager テンプレートにエクスポートする **ドメイン** を選択します。 

    :::image type="content" source="./media/move-domains-across-regions/select-domain.png" alt-text="ドメインの選択":::   
4. **[Event Grid ドメイン]** ページで、左側のメニューの **[設定]** にある **[テンプレートのエクスポート]** を選択し、ツール バーの **[ダウンロード]** を選択します。 

    :::image type="content" source="./media/move-domains-across-regions/export-template-download.png" alt-text="[テンプレートのエクスポート] -> [ダウンロード]" lightbox="./media/move-domains-across-regions/export-template-download.png":::   

    > [!IMPORTANT]
    > ドメインとドメイン トピックがエクスポートされます。 ドメイン トピックのサブスクリプションはエクスポートされません。 そのため、ドメイン トピックを移動した後に、ドメイン トピックのサブスクリプションを作成する必要があります。 
5. ポータルからダウンロードした **.zip** ファイルを見つけて、選択したフォルダーにそのファイルを解凍します。 この ZIP ファイルには、テンプレートとパラメーターの JSON ファイルが含まれています。 
1. 任意のエディターで **template.json** を開きます。 
8. **ドメイン** リソースの `location` をターゲット リージョンまたは場所に更新します。 場所コードを取得するには、[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)に関するページを参照してください。 リージョンのコードは、スペースを含まないリージョン名です (例えば、`West US` は `westus` と同じです)。

    ```json
    "type": "Microsoft.EventGrid/domains",
    "apiVersion": "2020-06-01",
    "name": "[parameters('domains_spegriddomain_name')]",
    "location": "westus",
    ```
1. テンプレートを **[保存]** します。 

## <a name="recreate"></a>再作成する 
テンプレートをデプロイして、ターゲット リージョンにドメインとドメイン トピックを作成します。 

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
    1. **[ドメイン名]** で、ドメインの新しい名前を入力します。 
    1. **[Review + create]\(レビュー + 作成\)** を選択します。 
    
        :::image type="content" source="./media/move-domains-across-regions/deploy-template.png" alt-text="テンプレートのデプロイ":::        
    1. テンプレートの検証に成功したら、ページの下部にある **[作成]** を選択して、リソースをデプロイします。 
    1. デプロイが正常に完了したら、 **[リソース グループに移動]** を選択して、[リソース グループ] ページに移動します。 リソース グループにドメインがあることを確認します。 ドメインを選択します。 ドメイン内にドメイン トピックがあることを確認します。 

## <a name="discard-or-clean-up"></a>破棄またはクリーンアップ
移動を完了するには、ソース リージョンのドメインを削除します。  

最初からやり直す場合は、ターゲット リージョンのドメインを削除し、この記事の「[準備](#prepare)」と「[再作成する](#recreate)」のセクションの手順を繰り返します。

Azure portal を使用してドメインを削除するには:

1. Azure portal の上部にある検索ウィンドウで「**Event Grid ドメイン**」と入力し、検索結果から **[Event Grid ドメイン]** を選択します。 
2. 削除するドメインを選択し、ツール バーの **[削除]** を選択します。 
3. [確認] ページで、リソース グループの名前を入力し、 **[削除]** を選択します。  

Azure portal を使用して、ドメインを含むリソース グループを削除するには:

1. Azure portal の上部にある検索ウィンドウで「**リソース グループ**」と入力し、検索結果から **[リソース グループ]** を選択します。 
2. 削除するリソース グループを選択し、ツール バーの **[削除]** を選択します。 
3. [確認] ページで、リソース グループの名前を入力し、 **[削除]** を選択します。  

## <a name="next-steps"></a>次のステップ
Azure Event Grid ドメインをあるリージョンから別のリージョンに移動する方法について学習しました。 リージョン間でシステム トピック、カスタム トピック、およびパートナー名前空間を移動するには、次の記事を参照してください。

- [リージョン間でのシステム トピックの移動](move-system-topics-across-regions.md)。 
- [リージョン間でのカスタム トピックの移動](move-custom-topics-across-regions.md)。 
- [リージョン間でのパートナー名前空間の移動](move-partner-namespaces-across-regions.md)。

リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、「[リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)」という記事を参照してください。