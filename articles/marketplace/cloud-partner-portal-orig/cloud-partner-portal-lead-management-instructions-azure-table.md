---
title: Azure テーブル | Microsoft Docs
description: Azure テーブル用にリード管理を構成します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 2a8ae3ab71b258d92d9761cc813b168717e44d82
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58878011"
---
# <a name="lead-management-instructions-for-azure-table"></a>Azure テーブルでの潜在顧客管理の手順

この記事では、潜在顧客を格納するために Azure テーブルを構成する方法について説明します。 Azure テーブルを使用して、顧客情報を保存およびカスタマイズできます。

## <a name="to-configure-azure-table"></a>Azure テーブルを構成するには

1.  Azure アカウントを持っていない場合は、[無料試用版アカウントを作成する](https://azure.microsoft.com/pricing/free-trial/)ことができます。

2.  Azure アカウントがアクティブになった後、[Azure portal](https://portal.azure.com) にサインインします。
3.  Azure portal でストレージ アカウントを作成します。 次の画面キャプチャでは、ストレージ アカウントを作成する方法を示します。 ストレージの価格について詳しくは、[ストレージの価格](https://azure.microsoft.com/pricing/details/storage/)に関する記事をご覧ください。

    ![Azure ストレージ アカウントを作成する手順](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

4.  キーのストレージ アカウント接続文字列をコピーし、Cloud パートナー ポータルの **[ストレージ アカウント接続文字列]** フィールドに貼り付けます。 `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` は接続文字列の例です。
    
    ![Azure ストレージ キー](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

[Azure のストレージ エクスプローラー](https://azurestorageexplorer.codeplex.com/)またはその他任意のツールを使用して、ストレージ テーブル内のデータを表示できます。 Azure テーブルのデータをエクスポートすることもできます
。

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>**(省略可能)** Azure テーブルで Microsoft Flow を使用する

[Microsoft Flow](https://docs.microsoft.com/flow/) を使用すると、Azure テーブルに潜在顧客が追加されるたびに通知を自動化できます。 アカウントを持っていない場合は、[無料アカウントにサインアップ](https://flow.microsoft.com/)できます。

### <a name="lead-notification-example"></a>潜在顧客の通知の例

この例を、Azure テーブルに新しい潜在顧客が追加されたら電子メール通知を自動的に送信する単純なフローを作成するためのガイドとして使用してください。 この例では、テーブル ストレージが更新された場合は、1 時間ごとに潜在顧客情報を送信するための繰り返しを設定します。

1. Microsoft Flow アカウントにサインインします。
2. 左側のナビゲーション バーで、**[マイ フロー]** を選択します。
3. 上部のナビゲーション バーで、**[+ 新規]** を選択します。  
4. ドロップダウン リストで、**[一から作成]** を選択します。
5. [フローを一から作成する] で、**[一から作成]** を選択します。

   ![新しいフローを一から作成する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

6. コネクタとトリガーの検索ページで、**[トリガー]** を選択します。
7. **[トリガー]** で、**[繰り返し]** を選択します。
8. **[繰り返し]** ウィンドウで、**[間隔]** に対する 1 の既定の設定を保持します。 **[頻度]** ドロップダウン リストから、**[時間]** を選択します。

   >[!NOTE] 
   >この例では 1 時間の間隔を使用していますが、ビジネス ニーズに最適な間隔と頻度を選択できます。

   ![繰り返しとして 1 時間の頻度を設定する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

9. **[+ New step (+ 新しいステップ)]** を選択します。
10. "過去の時間を取得" を検索してから、[アクション] の **[過去の時間を取得]** を選択します。 

    ![[過去の時間を取得] アクションを検索して選択する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

11. **[過去の時間を取得]** ウィンドウで、**[間隔]** を 1 に設定します。  **[時間単位]** ドロップダウン リストから、**[時間]** を選択します。
    >[!IMPORTANT] 
    >この間隔と時間単位が、繰り返しとして構成した間隔と頻度に一致することを確認してください。

    ![過去の時間間隔の取得を設定する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >いつでもフローをチェックして、各ステップが正しく構成されていることを確認できます。 フローをチェックするには、[フロー] メニュー バーから **[Flow checker] (フロー チェッカー)** を選択します。

次の一連のステップでは、Azure テーブルに接続し、新しい潜在顧客を処理するための処理ロジックを設定します。

1. [過去の時間を取得] ステップの後、**[+ 新しいステップ]** を選択し、"[Get entities] (エンティティの取得)" を検索します。
2. **[アクション]** で、**[Get entities] (エンティティの取得)**、**[詳細オプションを表示する]** の順に選択します。
3. **[Get entities] (エンティティの取得)** ウィンドウで、次のフィールドの情報を指定します。

   - **[テーブル]** – Azure Table Storage の名前を入力します。 次の画面キャプチャは、この例で "MarketPlaceLeads" が入力されたときのプロンプトを示しています。 

     ![Azure テーブル名のカスタム値を選択する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **[Filter query] (フィルター クエリ)** – このフィールドをクリックすると、ポップアップ ウィンドウに [過去の時間を取得] アイコンが表示されます。 **[Past time] (過去の時間)** を選択して、これをクエリのフィルター処理のタイムスタンプとして使用します。 あるいは、そのフィールドに関数 `gt datetime'@{body('Get_past_time')}'` を貼り付けることができます。

     ![フィルター クエリ関数を設定する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

4. **[新しいステップ]** を選択して、Azure テーブルで新しい潜在顧客をスキャンする条件を追加します。

   ![新しいステップを使用して Azure テーブルをスキャンする条件を追加する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

5. **[アクションの選択]** ウィンドウで、**[アクション]** を選択してから、**[条件]** コントロールを選択します。

     ![[条件] コントロールを追加する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

6. **[条件]** ウィンドウで、**[値の選択]** フィールドを選択してから、ポップアップ ウィンドウで **[式]** を選択します。
7. ***[fx]*** フィールドに `length(body('Get_entities')?['value'])` を貼り付けます。 **[OK]** を選択して、この関数を追加します。 条件の設定を完了するには、次の手順を実行します。

   - ドロップダウン リストから [is greater than] (次の値より大きい) を選択します。
   - 値として 0 を入力する 

     ![条件に関数を追加する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

8. 条件の結果に基づいて実行するアクションを設定します。

     ![条件の結果に基づくアクションを設定する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

9. 条件が **[If no] (いいえの場合)** に解決される場合は、何もしません。 
10. 条件が **[If yes] (はいの場合)** に解決される場合は、電子メールを送信するために Office 365 アカウントに接続するアクションをトリガーします。 **[アクションの追加]** を選択します。
11. **[電子メールを送信する]** を選択します。 
12. **[電子メールを送信する]** ウィンドウで、次のフィールドの情報を指定します。

    - **[宛先]** - この通知を受信するすべてのユーザーの電子メール アドレスを入力します。
    - **[件名]** – 電子メールの件名を指定します。 例: 新しい潜在顧客
    - **本文**: 各電子メールに含めるテキストを追加し (省略可能)、潜在顧客情報を挿入するための関数として body `('Get_entities')?['value']` を貼り付けます。

      >[!NOTE] 
      >この電子メールの本文に追加の静的または動的データ ポイントを挿入できます。

       ![潜在顧客の通知の電子メールを設定する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

13. **[保存]** を選択してフローを保存します。 Microsoft Flow は、フローにエラーがないかどうか自動的にテストします。 エラーがない場合は、保存された後、フローが実行を開始します。

次の画面キャプチャは、最終的なフローがどのようになるかの例を示しています。

 ![最終的なフローのシーケンス](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

### <a name="managing-your-flow"></a>フローの管理

実行された後のフローの管理は簡単です。  そのフローを完全に制御できます。 たとえば、停止、編集、実行履歴の表示、分析の取得などが可能です。 次の画面キャプチャは、フローを管理するために使用できるオプションを示しています。 

 ![フローの管理](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

フローは、**[Turn flow off] (フローを停止)** オプションを使用して停止するまで実行状態を維持します。

潜在顧客の電子メール通知を何も受信していない場合、それは Azure テーブルに新しい潜在顧客が追加されていないことを示しています。 フローに何かエラーがある場合は、次の画面キャプチャにある例のような電子メールを受信します。

 ![フローのエラーの電子メール通知](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>次の手順

[潜在顧客の構成](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)