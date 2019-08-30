---
title: Azure テーブル | Azure Marketplace
description: Azure テーブル用にリード管理を構成します。
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 7151be3ac9f55825fd2e9dde35c9afda6a30726a
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69901539"
---
# <a name="configure-lead-management-using-an-azure-table"></a>Azure テーブルを使用してリード管理を構成する

Azure Marketplace と AppSource のリードを受け取るためにパートナー センターで顧客関係管理 (CRM) システムが明示的にサポートされていない場合は、Azure テーブルを使用してこれらのリードを処理できます。 その後、データをエクスポートして CRM システムにインポートすることを選択できます。 この記事の手順では、Azure Storage アカウントと、そのアカウント下の Azure テーブルを作成するプロセスについて説明します。 さらに、Microsoft Flow を使用して新しいフローを作成し、オファーでリードを受け取ったときに電子メール通知を送信することもできます。

## <a name="configure-azure-table"></a>Azure テーブルを構成する

1. Azure アカウントを持っていない場合は、[無料試用版アカウントを作成する](https://azure.microsoft.com/pricing/free-trial/)ことができます。
2. Azure アカウントがアクティブになった後、[Azure portal](https://portal.azure.com) にサインインします。
3. Azure portal 上で、次の手順を使用してストレージ アカウントを作成します。  
    1. 左側のメニュー バーにある **[+ リソースの作成]** を選択します。  **[新規]** ウィンドウ (ブレード) が、右側に表示されます。
    2. **[新規]** ウィンドウで **[ストレージ]** を選択します。  **[おすすめ]** 一覧が右側に表示されます。
    3. **[ストレージ アカウント]** を選択して、アカウントの作成を開始します。  「[ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)」の記事にある手順に従ってください。

        ![Azure ストレージ アカウントを作成する手順](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        ストレージ アカウントについて詳しくは、[クイック スタートのチュートリアル](https://docs.microsoft.com/azure/storage/)に関するページをご覧ください。  ストレージの価格について詳しくは、[ストレージの価格](https://azure.microsoft.com/pricing/details/storage/)に関する記事をご覧ください。

4. ストレージ アカウントがプロビジョニングされるまで待機します。通常は数分かかる処理です。  その後、 **[リソースをすべて表示する]** を選択するか、または Azure portal の左側のナビゲーション メニュー バーから **[すべてのリソース]** を選択して、Azure portal の **[ホーム]** ページからお使いのストレージ アカウントへアクセスします。

    ![Azure ストレージ アカウントにアクセスする](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. ストレージ アカウントのウィンドウで、 **[アクセス キー ]** を選択し、キーの *[接続文字列]* 値をコピーします。 この値を保存します。これは、マーケットプレース オファーのリードを受け取るために発行ポータル内で提供する必要がある "*ストレージ アカウント接続文字列*" 値です。 

    接続文字列の例を次に示します。

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Azure ストレージ キー](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. ストレージ アカウント ウィンドウで、 **[テーブル]** を選択し、 **[+テーブル]** を選択してテーブルを作成します。 テーブルの名前を入力し、 **[OK]** を選択します。 この値を保存します。これは、リードを受け取ったときに電子メール通知を受信するように MS Flow を構成する場合に必要になります。

    ![Azure テーブル](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    [Azure のストレージ エクスプローラー](https://archive.codeplex.com/?p=azurestorageexplorer)またはその他任意のツールを使用して、ストレージ テーブル内のデータを表示できます。 Azure テーブル内のデータをエクスポートすることもできます。 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>(省略可能) Azure テーブルで Microsoft Flow を使用する  

[Microsoft Flow](https://docs.microsoft.com/flow/) を使用すると、Azure テーブルに潜在顧客が追加されるたびに通知を自動化できます。 アカウントを持っていない場合は、[無料アカウントにサインアップ](https://flow.microsoft.com/)できます。

### <a name="lead-notification-example"></a>潜在顧客の通知の例

この例を、Azure テーブルに新しい潜在顧客が追加されたら電子メール通知を自動的に送信する単純なフローを作成するためのガイドとして使用してください。 この例では、テーブル ストレージが更新された場合は、1 時間ごとにリード情報を送信するための繰り返しを設定します。

1. Microsoft Flow アカウントにサインインします。
2. 左側のナビゲーション バーで、 **[マイ フロー]** を選択します。
3. 上部のナビゲーション バーで、 **[+ 新規]** を選択します。  
4. ドロップダウン リストで、 **[+ Scheduled - from blank]\(+ スケジュール済み - 白紙から作成\)** を選択します

   ![[マイ フロー] **[+ スケジュール済み - 空白から作成]**](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.  *[繰り返し間隔]* の下の " *[スケジュールされたフローをビルド]* " ウィンドウで、間隔に "1" を、頻度に "時間" を選択します。 また、必要に応じて、フローに名前を付けます。 **作成** を選択します。

    >[!Note]
    >この例では 1 時間の間隔を使用していますが、ビジネス ニーズに最適な間隔と頻度を選択できます。

    ![スケジュールされたフローをビルドします。](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. **[+ New step (+ 新しいステップ)]** を選択します。
7. " *[アクションを選択してください]* " ウィンドウで "過去の時間を取得" を検索し、[アクション] の下の **[過去の時間を取得]** を選択します。

   ![アクションを選択します。](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. **[過去の時間を取得]** ウィンドウで、 **[間隔]** を 1 に設定します。 **[時間単位]** ドロップダウン リストから、 **[時間]** を選択します。

    >[!Important]
    >この間隔と時間単位が、手順 5. で繰り返しとして構成した間隔と頻度に一致することを確認してください。

    ![過去の時間間隔の取得を設定する](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>いつでもフローをチェックして、各ステップが正しく構成されていることを確認できます。 フローをチェックするには、[フロー] メニュー バーから **[Flow checker] (フロー チェッカー)** を選択します。

次の一連のステップでは、Azure テーブルに接続し、新しい潜在顧客を処理するための処理ロジックを設定します。

9. [過去の時間の取得] ステップの後で、 **[+ 新しいステップ]** を選択し、" *[アクションの選択]* " ウィンドウで "過去の時間の取得" を検索します。
10. **[アクション]** で、 **[エンティティの取得 (Azure Table Storage)]** を選択します。
11. **[Azure Table Storage]** ウィンドウで、次のフィールドに情報を指定し、 **[作成]** を選択します。

    * "*接続名*" – このフローと Azure テーブルの間に確立する接続のわかりやすい名前を指定します。
    * "*ストレージ アカウント名*" – ご自身の Azure テーブルのストレージ アカウントの名前を選択します。 これは、ストレージ アカウントの **[アクセス キー]** ページで確認できます。
    * "*共有ストレージ キー*" – ご自身の Azure テーブルのストア アカウントのキー値を指定します。 これは、ストレージ アカウントの **[アクセス キー]** ページで確認できます。

        ![Azure Table Storage。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

    [作成] をクリックすると、" *[エンティティの取得]* " ウィンドウが表示されます。 ここで、 **[詳細オプションの表示]** を選択し、次のフィールドに情報を指定します。

       * "*テーブル*" – Azure Table Storage の名前を選択します (手順 6. の Azure テーブルの構成方法に関する説明を参照)。 次の画面キャプチャは、この例で "marketplaceleads" テーブルが選択されたときのプロンプトを示しています。

            ![Azure テーブルのエンティティの取得。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

        * "*フィルター クエリ*" – このフィールドを選択し、次の関数をフィールドに貼り付けます: `Timestamp gt datetime'@{body('Get_past_time')}'`

            ![Azure テーブルのエンティティの取得 - [フィルター クエリ]。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. これで、Azure テーブルへの接続の設定が完了したので、 **[新しいステップ]** を選択して、Azure テーブルで新しいリードをスキャンする条件を追加します。 

13. **[アクションの選択]** ウィンドウで、 **[アクション]** を選択してから、 **[条件コントロール]** を選択します。

    ![Azure テーブル - [アクションの選択]。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. **[条件]** ウィンドウで、 **[値の選択]** フィールドを選択してから、ポップアップ ウィンドウで **[式]** を選択します。

15. ***[fx]*** フィールドに `length(body('Get_entities')?['value'])` を貼り付けます。 **[OK]** を選択して、この関数を追加します。 

16. 条件の設定を完了するには、次の手順を実行します。
    1. ドロップダウン リストから [is greater than] (次の値より大きい) を選択します。
    2. 値として 0 を入力する

        ![Azure テーブル - [条件]。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

次のいくつかのステップで、条件の結果に基づいて実行するアクションを設定します。

* 条件が **[If no] (いいえの場合)** に解決される場合は、何もしません。
* 条件が **[If yes] (はいの場合)** に解決される場合は、電子メールを送信するために Office 365 アカウントに接続するアクションをトリガーします。 

17. **[はいの場合]** の下で **[アクションの追加]** のリンクを選択します。

    ![Azure テーブル - [条件]、**[はいの場合]**。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. **[電子メールの送信 (Office 365 Outlook)]** を選択します。

    ![Azure テーブル - [条件]、**[はいの場合]**、[電子メールの送信]。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >別の電子メール プロバイダーを使用する場合は、代わりに [Send an email notification (Mail)]\(電子メール通知を送信する (メール)\) をアクションとして選択します。 この手順では、Office 365 Outlook を使用して構成する方法を説明しますが、手順は別の電子メール プロバイダーでも同様です。

19. **[Office 365 Outlook]** ウィンドウで、以下のフィールドの情報を指定します。

    1. **[宛先]** - この通知を受信するすべてのユーザーの電子メール アドレスを入力します。
    1. **[件名]** – 電子メールの件名を指定します。 例: 新しい潜在顧客
    1. **[本文]** - 各電子メールに含めるテキストを追加し (省略可能)、body `body('Get_entities')?['value']` を貼り付けます。

    >[!Note]
    >この電子メールの本文に追加の静的または動的データ ポイントを挿入できます。

    ![Azure テーブル - [条件]、**[はいの場合]**、[Office 365 Outlook] ウィンドウ。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. **[保存]** を選択してフローを保存します。 Microsoft Flow は、フローにエラーがないかどうか自動的にテストします。 エラーがない場合は、保存された後、フローが実行を開始します。

次の画面キャプチャは、最終的なフローがどのようになるかの例を示しています。

![最後のフローの例。](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>フローを管理する

実行された後のフローの管理は簡単です。 そのフローを完全に制御できます。 たとえば、停止、編集、実行履歴の表示、分析の取得などが可能です。 次の画面キャプチャは、フローを管理するために使用できるオプションを示しています。 

 ![フローの管理](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

フローは、 **[Turn flow off] (フローを停止)** オプションを使用して停止するまで実行状態を維持します。

潜在顧客の電子メール通知を何も受信していない場合、それは Azure テーブルに新しい潜在顧客が追加されていないことを示しています。 フローに何かエラーがある場合は、次の画面キャプチャにある例のような電子メールを受信します。

 ![フローのエラーの電子メール通知](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>リードを Azure テーブルに送信するようにオファーを構成する

発行ポータル内でオファーのリード管理情報を構成する準備ができたら、次の手順に従います。

1. オファーの **[オファーのセットアップ]** ページに移動します。
2. [リード管理] セクションで **[接続]** を選択します。
3. [接続の詳細] ポップアップ ウィンドウで、 **[リードのターゲット]** として **[Azure Table]** を選択し、前の手順で作成した Azure ストレージ アカウントからの接続文字列を **[ストレージ アカウント接続文字列]** フィールドに貼り付けます。
4. **[保存]** を選択します。 

>[!Note]
>オファーのリードを受け取るには、まずオファーの残りの部分の構成を完了して発行する必要があります。

リードが生成されると、マイクロソフトから Azure テーブルにリードが送信されます。 フローを構成した場合は、構成したメール アドレスにもメールが送信されます。

![リードの管理](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![[リード管理] - [接続の詳細]](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![[リード管理] - [接続の詳細] のストレージ アカウント](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

