---
title: Azure Event Hubs 専用クラスターを別のリージョンに移動する | Microsoft Docs
description: この記事では、Azure Event Hubs 専用クラスターを現在のリージョンから別のリージョンに移動する方法について説明します。
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 94be44ee8f9442a3a65e899d7a58524b2570f194
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89380424"
---
# <a name="move-an-azure-event-hubs-dedicated-cluster-to-another-region"></a>Azure Event Hubs 専用クラスターを別のリージョンに移動する
この記事では、既存の Event Hubs 専用クラスターの Azure Resource Manager テンプレートをエクスポートし、そのテンプレートを使用して、別のリージョンに同じ構成設定でクラスターを作成する方法について説明します。 

Event Hubs クラスターを含む Azure リソース グループに名前空間やイベント ハブなどの他のリソースがある場合は、すべての関連リソースを 1 回の手順で新しいリージョンに移動できるように、リソース グループ レベルでテンプレートをエクスポートすることをお勧めします。 この記事の手順では、 **Event Hubs クラスター** をテンプレートにエクスポートする方法について説明します。 **リソース グループ** をテンプレートにエクスポートする手順は似ています。 

## <a name="prerequisites"></a>前提条件
ターゲット リージョンに専用クラスターを作成できることを確認します。 確認する最も簡単な方法は、Azure portal を使用して [Event Hubs 専用クラスターを作成](event-hubs-dedicated-cluster-create-portal.md)してみることです。 クラスターを作成する時点でサポートされているリージョンの一覧が表示されます。 

## <a name="prepare"></a>準備
まず、Resource Manager テンプレートをエクスポートします。 このテンプレートには、Event Hubs 専用クラスターを説明する設定が含まれています。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. **[すべてのリソース]** を選択し、Event Hubs 専用クラスターを選択します。
3. **[設定]**  >  **[テンプレートのエクスポート]** を選択します。
4. **[テンプレートのエクスポート]** ページで **[ダウンロード]** を選択します。

    :::image type="content" source="./media/move-cluster-across-regions/download-template.png" alt-text="Resource Manager テンプレートをダウンロードする" lightbox="./media/move-cluster-across-regions/download-template.png":::
5. ポータルからダウンロードした .zip ファイルを見つけて、選択したフォルダーにそのファイルを解凍します。

   この zip ファイルには、テンプレートとテンプレートをデプロイするためのスクリプトから構成される .json ファイルが含まれています。


## <a name="move"></a>詳細ビュー

テンプレートをデプロイして、ターゲット リージョンに Event Hubs 専用クラスターを作成します。 


1. Azure Portal で、 **[リソースの作成]** を選択します。
2. **[Marketplace を検索]** に、「**template deployment**」と入力し、 **[テンプレートのデプロイ (カスタム テンプレートを使用したデプロイ)]** を選択します。
5. **[Build your own template in the editor] \(エディターで独自のテンプレートをビルド\)** を選択します。
6. **[ファイルの読み込み]** を選択し、手順に従って、前のセクションでダウンロードした **template.json** ファイルを読み込みます。
1. 新しいリージョンを指すように `location` プロパティの値を更新します。 場所コードを取得するには、[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)に関するページを参照してください。 リージョンのコードは、スペースを含まないリージョン名です (例えば、`West US` は `westus` と同じです)。
1. **[保存]** を選択してテンプレートを保存します。 
1. **[カスタム デプロイ]** ページで、次の手順を行います。 
    1. Azure **サブスクリプション** を選択します。 
    2. 既存の **リソース グループ** を選択するか、新しく作成することができます。 
    3. ターゲットの **場所** またはリージョンを選択します。 既存のリソース グループを選択した場合、この設定は読み取り専用になります。 
    4. **[設定]** セクションで、次の手順を行います。    
        1. 新しい **クラスター名** を入力します。 

            :::image type="content" source="./media/move-cluster-across-regions/deploy-template.png" alt-text="Resource Manager テンプレートをデプロイする":::
    5. ページ下部にある **[確認と作成]** を選択します。 
    1. **[確認と作成]** ページで、設定を確認し、 **[作成]** を選択します。  

## <a name="discard-or-clean-up"></a>破棄またはクリーンアップ
デプロイ後に、最初からやり直す場合は、**ターゲット Event Hubs 専用クラスター** を削除し、この記事の「[準備](#prepare)」と「[移動](#move)」のセクションに示されている手順を繰り返します。

変更をコミットし、Event Hubs クラスターの移動を完了するには、元のリージョンの **Event Hubs クラスター** を削除します。 

Azure portal を使用して Event Hubs クラスター (ソースまたはターゲット) を削除するには、次のようにします。

1. Azure portal の上部にある検索ウィンドウで「**Event Hubs クラスター**」と入力し、検索結果から **[Event Hubs クラスター]** を選択します。 Event Hubs クラスターが一覧に表示されます。
2. 削除するクラスターを選択し、ツール バーの **[削除]** を選択します。 
3. **[クラスターの削除]** ページで、**クラスター名** を入力して削除を確認し、 **[削除]** を選択します。 

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Event Hubs 専用クラスターをあるリージョンから別のリージョンに移動する方法について学習しました。 

名前空間をあるリージョンから別のリージョンに移動する方法については、[Event Hubs 名前空間のリージョン間での移動](move-across-regions.md)に関する記事をご覧ください。 

リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、以下を参照してください。

- [リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure VM を別のリージョンに移動する](../site-recovery/azure-to-azure-tutorial-migrate.md)
