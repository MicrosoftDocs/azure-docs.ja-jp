---
title: "Azure Logic Apps で統合アカウントを作成、リンク、削除、または移動する | Microsoft Docs"
description: "統合アカウントを作成してロジック アプリにリンクする方法"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 2a7ecec310f4e321417364eee854409b2f85e2d3
ms.lasthandoff: 03/10/2017


---

# <a name="what-is-an-integration-account"></a>統合アカウントとは

統合アカウントによって、エンタープライズ統合アプリでスキーマ、マップ、証明書、パートナー、契約などのアーティファクトを管理できます。 作成した統合アプリケーションはすべて、統合アカウントを使用してこれらのスキーマ、マップ、証明書などにアクセスします。

## <a name="create-an-integration-account"></a>統合アカウントの作成

1.    [Azure Portal](http://portal.azure.com "Azure Portal") にサインインします。 左側のメニューの **[その他のサービス]** をクリックします。

    ![[その他のサービス] の選択](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. 検索ボックスに、フィルターとして「統合」と入力します。 結果の一覧から **[統合アカウント]** を選択します。

    !["統合" でのフィルター処理、[アカウントの統合] の選択](./media/logic-apps-enterprise-integration-accounts/account-2.png)  

3. ページの上部にある **[追加]** を選択します。

    ![[追加] の選択](./media/logic-apps-enterprise-integration-accounts/account-3.png)

4. 統合アカウントの名前を指定し、使用する Azure サブスクリプションを選択します。 新しい**リソース グループ**を作成するか、既存のリソース グループを選択することができます。 次に、統合アカウントをホストする**場所**と**価格レベル**を選択します。 

    準備ができたら、**[作成]** をクリックします。

    ![統合アカウントの詳細の指定](./media/logic-apps-enterprise-integration-accounts/account-4.png)

    Azure により、選択した場所に統合アカウントがプロビジョニングされます。通常、1 分以内で完了します。

5. ページを更新します。 新しい統合アカウントがリストされていることを確認します。

    ![新しい統合アカウントの表示](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

次に、作成した統合アカウントをロジック アプリにリンクさせます。 

## <a name="link-an-integration-account-to-a-logic-app"></a>ロジック アプリへの統合アカウントのリンク

ロジック アプリで統合アカウントにあるマップ、スキーマ、契約などのアーティファクトにアクセスするには、ロジック アプリに統合アカウントをリンクさせます。

### <a name="prerequisites"></a>前提条件

* 統合アカウント
* ロジック アプリ

> [!NOTE] 
> 開始する前に、統合アカウントおよびロジック アプリが*同じ Azure の場所*にあることを確認してください。


1. Azure Portal で、ロジック アプリを選択し、ロジック アプリの場所を確認します。

    ![ロジック アプリの選択、場所の確認](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)

2. **[設定]** で、**[統合アカウント]** を選択します。

    ![[統合アカウント] の選択](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)

3. **[統合アカウントを選択してください]** ボックスの一覧からロジック アプリにリンクする統合アカウントを選択します。 リンクを終了するには、**[保存]** を選択します。

    ![統合アカウントを選択する](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)

    統合アカウントがロジック アプリにリンクされていることと、統合アカウント内のすべてのアーティファクトがロジック アプリに対して使用できるようになったことを示す通知が表示されます。

    ![ロジック アプリが統合アカウントにリンクされた](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)

統合アカウントがロジック アプリにリンクされ、Logic Apps 内で B2B コネクタが使用できるようになりました。 一般的な B2B コネクタには、XML の検証、フラット ファイルのエンコードやデコードなどがあります。  

## <a name="delete-your-integration-account"></a>統合アカウントの削除

1. **[そのほかのサービス]** を選択します。

    ![[その他のサービス] の選択](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. 検索ボックスに、フィルターとして「統合」と入力します。 結果の一覧から **[統合アカウント]** を選択します。

    !["統合" でのフィルター処理、[アカウントの統合] の選択](./media/logic-apps-enterprise-integration-accounts/account-2.png)  

3. 削除する統合アカウントを選択します。

    ![削除する統合アカウントの選択](./media/logic-apps-enterprise-integration-accounts/account-5.png)

4. メニューの **[削除]** を選択します。

    ![[削除] の選択](./media/logic-apps-enterprise-integration-accounts/delete.png)

5. 統合アカウントの削除を確認します。

## <a name="move-your-integration-account"></a>統合アカウントの移動

統合アカウントを別の Azure サブスクリプションまたはリソース グループに移動するには、以下の手順に従います。

> [!IMPORTANT]
> 統合アカウントを移動すると、新しいリソース ID を使用するために、すべてのスクリプトを更新する必要があります。

1. **[そのほかのサービス]** を選択します。

    ![[その他のサービス] の選択](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. 検索ボックスに、フィルターとして「統合」と入力します。 結果の一覧から **[統合アカウント]** を選択します。

    !["統合" でのフィルター処理、[アカウントの統合] の選択](./media/logic-apps-enterprise-integration-accounts/account-2.png)

3. 移動する統合アカウントを選択します。 **[設定]** の **[プロパティ]** を選択します。

    ![移動する統合アカウントの選択。 [設定] の [プロパティ] の選択](./media/logic-apps-enterprise-integration-accounts/move.png)

5. 統合アカウントに関連付けられているリソース グループまたは Azure サブスクリプションを変更します。

    ![リソース グループの変更またはサブスクリプションの変更の選択](./media/logic-apps-enterprise-integration-accounts/move-2.png)

## <a name="next-steps"></a>次のステップ
* [契約についての詳細情報](../logic-apps/logic-apps-enterprise-integration-agreements.md "Enterprise Integration の契約についての詳細情報")  


