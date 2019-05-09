---
title: B2B ソリューション用の統合アカウントの作成と管理 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps を使用したエンタープライズ統合と B2B ソリューション用の統合アカウントの作成、リンク、移動、および削除
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: 43ecdafac4f0a5cdc9e619537cdbe2a42ff7fe1b
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57191920"
---
# <a name="create-and-manage-integration-accounts-for-b2b-solutions-with-logic-apps"></a>ロジック アプリを使用した B2B ソリューション用の統合アカウントの作成と管理

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) を使って[エンタープライズ統合と B2B ソリューション](../logic-apps/logic-apps-enterprise-integration-overview.md)を構築するには統合アカウントが必要です。このアカウントでは、B2B アーティファクト (取引先、契約、マップ、スキーマ、証明書など) を作成、格納、管理します。 ロジック アプリが統合アカウントでアーティファクトを操作し、Logic Apps B2B コネクタを使用するために (XML 検証など)、ロジック アプリに[統合アカウントをリンク](#link-account)する必要があります。 リンクするには、統合アカウントとロジック アプリの両方に*同じ* Azure の場所またはリージョンが必要です。

この記事では、次のタスクの実行について説明します。

* 統合アカウントの作成
* ロジック アプリへの統合アカウントのリンク
* 別の Azure リソース グループまたはサブスクリプションへの統合アカウントの移動
* 統合アカウントの削除

Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

Azure アカウントの資格情報で <a href="https://portal.azure.com" target="_blank">Azure Portal</a> にサインインします。

## <a name="create-integration-account"></a>統合アカウントを作成する

1. Azure のメイン メニューで、**[すべてのサービス]** を選びます。 検索ボックスにフィルターとして「統合アカウント」と入力し、**[統合アカウント]** を選択します。

   ![統合アカウントの検索](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. **[統合アカウント]** で **[追加]** を選択します。

   ![[追加] を選択して統合アカウントを作成](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

3. 統合アカウントに関する情報を指定します。 

   ![統合アカウントの詳細の指定](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | プロパティ | 必須 | 値の例 | 説明 | 
   |----------|----------|---------------|-------------|
   | Name | はい | test-integration-account | 統合アカウントの名前。 たとえば、指定された名前を使用します。 | 
   | サブスクリプション | はい | <*Azure サブスクリプション名*> | 使用する Azure サブスクリプションの名前。 | 
   | リソース グループ | はい | test-integration-account-rg | 関連するリソースの整理に使用する[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)の名前。 この例では、指定された名前の新しいリソース グループを作成します。 | 
   | 価格レベル | はい | 無料 | 使用する価格レベル。 この例では、**[Free]** を選択します。詳しくは、「[LLogic Apps の制限と構成](../logic-apps/logic-apps-limits-and-config.md)」と「[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)」を参照してください。 | 
   | Location | はい | 米国西部 | 統合アカウントの情報の保存先となるリージョン ロジック アプリと同じ場所を選択するか、統合アカウントと同じ場所でロジック アプリを作成します。 | 
   | Log Analytics ワークスペース | いいえ  | オフ | 診断ログの場合は、この設定を**オフ**のままにしてください。 | 
   ||||| 

4. 準備ができたら、**[ダッシュボードにピン留めする]**、**[作成]** の順に選択します。

   Azure によって、選択された場所に統合アカウントがデプロイされると (通常 1 分以内で完了)、統合アカウントが表示されます。

   ![Azure によって開かれた統合アカウント](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

ここで、アプリ ロジックが統合アカウントを使用する前に、統合アカウントをロジック アプリにリンクする必要があります。

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>ロジック アプリにリンクする

ロジック アプリが統合アカウントにある取引先、契約、マップ、スキーマなどの B2B アーティファクトにアクセスできるようにするには、ロジック アプリに統合アカウントをリンクする必要があります。 

> [!NOTE]
> 統合アカウントとロジック アプリは同じリージョン内にあることが必要です。

1. Azure Portal で、ご利用のロジック アプリを探して選択します。

2. ロジック アプリのメニューの **[設定]** で、**[ワークフロー設定]** を選択します。 **[統合アカウントを選択してください]** ボックスの一覧で、ロジック アプリにリンクする統合アカウントを選択します。

   ![統合アカウントを選択する](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-2.png)

3. リンクを終了するには、**[保存]** を選択します。

   ![統合アカウントを選択する](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-3.png)

   統合アカウントが正常にリンクされている場合、確認メッセージが表示されます。 

   ![Azure による正常なリンクの確認](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-5.png)

これで、ロジック アプリは、統合アカウントと B2B コネクタですべてのアーティファクトを使用できるようになりました (XML 検証、フラット ファイルのエンコードまたはデコードなど)。  

## <a name="unlink-from-logic-app"></a>ロジック アプリのリンクを解除する

ロジック アプリを別の統合アカウントにリンクするか、ロジック アプリで統合アカウントの使用を止めるには、Azure Resource Explorer を使用してリンクを削除できます。

1. ブラウザーで、<a href="https://resources.azure.com" target="_blank">Azure Resource Explorer(https://resources.azure.com)</a> に移動します。 同じ Azure 資格情報を使用してサインインしていることを確認します。

   ![Azure リソース エクスプローラー](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

2. 検索ボックスで、ロジック アプリの名前を入力して検索し、ロジック アプリを選択します。

   ![ロジック アプリの検索と選択](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

3. エクスプローラーのタイトル バーで、**[読み取り/書き込み]** を選択します。

   !["読み取り/書き込み" モードを有効にする](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-read-write-mode.png)

4. **[データ]** タブで **[編集]** を選択します。

   ![[データ] タブで [編集] を選択](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-edit.png)

5. エディターで、統合アカウントの `integrationAccount` プロパティを検索してそのプロパティを削除します。形式は以下のとおりです。

   ```json
   "integrationAccount": {
      "name": "<integration-account-name>",
      "id": "<integration-account-resource-ID>",
      "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   例: 

   !["integrationAccount" プロパティの定義を見つける](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

6. **[データ]** タブで **[PUT]** を選択して変更を保存します。 

   !["Put" を選択して変更を保存](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

7. Azure Portal のロジック アプリの**ワークフロー設定**で、**統合アカウント** プロパティが空になっていることを確認します。

   ![統合アカウントがリンクされていないことを確認](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>統合アカウントの移動

統合アカウントを、別の Azure サブスクリプションまたはリソース グループに移動できます。

1. Azure のメイン メニューで、**[すべてのサービス]** を選びます。 検索ボックスにフィルターとして「統合アカウント」と入力し、**[統合アカウント]** を選択します。

   ![統合アカウントの検索](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. **[統合アカウント]** で、移動する統合アカウントを選びます。 統合アカウント メニューの **[設定]** で、**[プロパティ]** を選択します。

   ![[設定] で [プロパティ] を選択](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-properties.png)

3. 統合アカウントの Azure リソース グループまたはサブスクリプションを変更します。

   !["リソース グループの変更" または "サブスクリプションの変更" の選択](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

4. 完了したら、アーティファクトの新しいリソース ID を持つすべてのスクリプトを更新していることを確認します。  

## <a name="delete-integration-account"></a>統合アカウントの削除

1. Azure のメイン メニューで、**[すべてのサービス]** を選びます。 検索ボックスにフィルターとして「統合アカウント」と入力し、**[統合アカウント]** を選択します。

   ![統合アカウントの検索](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. **[統合アカウント]** で、削除する統合アカウントを選びます。 統合アカウント メニューで、**[概要]**、**[削除]** の順に選択します。 

   ![統合アカウントを選択します。 "概要" ページで "削除" を選択](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

3. 統合アカウントを削除することを確認するために **[はい]** を選択します。

   ![削除を確認するために "はい" を選択](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>次の手順

* [取引先を作成する](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [契約を作成する](../logic-apps/logic-apps-enterprise-integration-agreements.md)
