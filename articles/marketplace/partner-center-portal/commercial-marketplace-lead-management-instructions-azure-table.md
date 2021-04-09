---
title: Azure Table ストレージによるリード管理 - Microsoft 商用マーケットプレース
description: Azure Table ストレージを使用して Microsoft AppSource および Azure Marketplace のリードを構成する方法について説明します
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 08/25/2020
ms.openlocfilehash: 3c8b9444344e5bae414145e2b3367c265114a423
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586844"
---
# <a name="use-azure-table-storage-to-manage-commercial-marketplace-leads"></a>Azure Table ストレージを使用して商用マーケットプレースのリードを管理する

Microsoft AppSource と Azure Marketplace のリードを受信できるようにパートナー センターでカスタマー リレーションシップ マネージメント (CRM) システムが明示的にサポートされていない場合、Azure Table ストレージを使用してこれらのリードを処理できます。 その後、データをエクスポートして CRM システムにインポートすることを選択できます。 この記事では、Azure ストレージ アカウントとそのアカウントの下にテーブルを作成する方法について説明します。 さらに、Power Automate を使用して新しいフローを作成し、オファーでリードを受信したときに電子メール通知を送信することもできます。

## <a name="configure-an-azure-storage-account"></a>Azure ストレージ アカウントを構成する

1. Azure アカウントを持っていない場合は、[無料試用版アカウントを作成する](https://azure.microsoft.com/pricing/free-trial/)ことができます。
1. Azure アカウントがアクティブになった後、[Azure portal](https://portal.azure.com) にサインインします。
1. Azure portal で、次の手順を使用して、ストレージ アカウントを作成します。

    1. 左側のメニュー バーにある **[+ リソースの作成]** を選択します。 **[新規]** ウィンドウが右側に表示されます。
    1. **[新規]** ウィンドウで **[ストレージ]** を選択します。 **[おすすめ]** の一覧が右側に表示されます。
    1. **[ストレージ アカウント]** を選択して、アカウントの作成を開始します。 [ストレージ アカウントの作成](../../storage/common/storage-account-create.md?tabs=azure-portal)に関するページの手順に従います。

        :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png" alt-text="Azure ストレージ アカウントを作成する手順。":::

        ストレージ アカウントの詳細については、[クイック スタートのチュートリアル](../../storage/index.yml)に関するページをご覧ください。 ストレージの価格の詳細については、[ストレージの価格](https://azure.microsoft.com/pricing/details/storage/)に関するページをご覧ください。

1. ストレージ アカウントがプロビジョニングされるまで待ちます。 通常、このプロセスには数分かかります。

## <a name="create-a-table-in-your-storage-account"></a>ストレージ アカウント内にテーブルを作成する

1. Azure portal の **[ホーム]** ページから **[すべてのリソースを表示する]** を選択して、お使いのストレージ アカウントにアクセスします。 Azure portal の左側のメニュー バーから **[すべてのリソース]** を選択することもできます。

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png" alt-text="Azure ストレージ アカウントにアクセスする。":::

1. ストレージ アカウントのウィンドウで、 **[アクセス キー ]** を選択し、キーの **[接続文字列]** 値をコピーします。 この値を保存します。これは、Azure Marketplace オファーのリードを受信するために発行ポータル内で提供する必要がある **[ストレージ アカウント接続文字列]** の値です。

    接続文字列の例を次に示します。

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.screens.net
    ```

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png" alt-text="Azure ストレージ キー。":::


1. ストレージ アカウント ウィンドウで、 **[テーブル]** を選択し、 **[+ テーブル]** を選択してテーブルを作成します。 テーブルの名前を入力し、 **[OK]** を選択します。 この値を保存します。これは、リードを受信したときに電子メール通知を受け取るようにフローを構成する場合に必要になります。

    ![Azure テーブル](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    [Azure Storage Explorer](https://www.storageexplorer.com) またはその他の任意のツールを使用して、ストレージ テーブル内のデータを表示できます。 Azure テーブル内のデータをエクスポートすることもできます。

## <a name="optional-use-power-automate-to-get-lead-notifications"></a>(省略可能) Power Automate を使用してリード通知を取得する

[Power Automate](/flow/) を使用して、Azure Storage テーブルにリードが追加されるたびに通知を自動的に送信できます。 アカウントを持っていない場合は、[無料アカウントにサインアップ](https://flow.microsoft.com/)できます。

### <a name="lead-notification-example"></a>潜在顧客の通知の例

この例では、Azure Table ストレージに新しいリードが追加されたときに電子メール通知を自動的に送信するフローが作成されます。 この例では、テーブル ストレージが更新された場合は、1 時間ごとにリード情報を送信するための繰り返しを設定します。

1. Power Automate アカウントにサインインします。
1. 左側のバーで、 **[マイ フロー]** を選択します。
1. 上部のバーで、 **[+ 新規]** を選択します。
1. ドロップダウン リストで、 **[+ スケジュール--空白から作成]** を選択します。

   ![[マイ フロー] と [+ スケジュール--空白から作成]](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

1. **[予定フローを作成]** ウィンドウの **[繰り返し間隔]** で、間隔として **[1]** を、頻度として **[時間]** を選択します。 また、必要に応じて、フローに名前を付けます。 **［作成］** を選択します

   >[!NOTE]
   >この例では 1 時間の間隔を使用していますが、ビジネス ニーズに最適な間隔と頻度を選択できます。

   ![予定フローを作成する](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

1. **[+ New step (+ 新しいステップ)]** を選択します。
1. **[アクションを選択してください]** ウィンドウで、 **[過去の時間の取得]** を検索します。 次に、 **[アクション]** で、 **[過去の時間の取得]** を選択します。

   ![アクションを選択する](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

1. **[過去の時間の取得]** ウィンドウで、 **[間隔]** を **1** に設定します。 **[時間単位]** ドロップダウン リストから、 **[時間]** を選択します。

    >[!IMPORTANT]
    >手順 8 で設定した間隔および時間単位が、手順 5 で繰り返しとして構成した間隔および頻度と一致することを確認してください。

    ![[過去の時間の取得] の間隔を設定する](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

   >[!TIP]
   >いつでもフローをチェックして、各ステップが正しく構成されていることを確認できます。 フローをチェックするには、 **[フロー]** メニュー バーから **[フロー チェッカー]** を選択します。

   次の一連の手順で、テーブルに接続し、新しいリードを処理するための処理ロジックを設定します。

1. **[+ New step (+ 新しいステップ)]** を選択します。 **[アクションを選択してください]** ウィンドウで、 **[エンティティの取得]** を検索します。
1. **[アクション]** で、 **[エンティティの取得 (Azure Table Storage)]** を選択します。
1. **[Azure Table Storage]** ウィンドウで、次のボックスに情報を指定し、 **[作成]** を選択します。

    * **[接続名]** : このフローとテーブルの間に確立する接続に付ける、わかりやすい名前を指定します。
    * **[ストレージ アカウント名]** : お使いのテーブルのストレージ アカウントの名前を指定します。 この名前は、ストレージ アカウントの **[アクセス キー]** ページで確認できます。
    * **[共有ストレージ キー]** : お使いのテーブルのストア アカウントのキー値を指定します。 この値は、ストレージ アカウントの **[アクセス キー]** ページで確認できます。

      ![[Azure Table Storage] ウィンドウ](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

   **[作成]** を選択すると、 **[エンティティの取得]** ウィンドウが表示されます。 ここで、 **[詳細オプションの表示]** を選択し、次のボックスに情報を指定します。

   * **テーブル**:お使いのテーブルの名前を選択します ([テーブルの作成](#create-a-table-in-your-storage-account)に関するセクションから)。 次の画像は、この例で `marketplaceleads` テーブルが選択されているときに表示されるプロンプトです。

     ![[エンティティの取得] ウィンドウ](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

   * **[フィルター クエリ]** : このボックスを選択して、関数 `Timestamp gt datetime'@{body('Get_past_time')}'` をボックスに貼り付けます。

     ![[エンティティの取得]、[フィルター クエリ] ボックス](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

1. これで、Azure テーブルへの接続の設定が完了したので、 **[新しいステップ]** を選択して、Azure テーブルで新しいリードをスキャンする条件を追加します。

1. **[アクションを選択してください]** ウィンドウで、 **[アクション]** を選択します。 次に、 **[条件コントロール]** を選択します。

    ![アクション ウィンドウを選択する](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

1. **[条件]** ウィンドウで、 **[値の選択]** を選択します。 次に、ポップアップ ウィンドウで **[式]** を選択します。

1. `length(body('Get_entities')?['value'])` を **[fx]** ボックスに貼り付けます。 **[OK]** を選択して、この関数を追加します。

1. 条件の設定を完了するには、次の手順を実行します。
    1. ドロップダウン リストから **[が次の値より大きい]** を選択します。
    2. 値として「**0**」を入力します。

        ![[条件] ウィンドウ](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

   次の数個の手順では、条件の結果に基づいて実行するアクションを設定します。

   * 条件が **[If no] (いいえの場合)** に解決される場合は、何もしません。
   * 条件を解決すると **はいの場合** は、メールを送信するために職場または学校のアカウントに接続するアクションをトリガーします。 

1. **[はいの場合]** の下で **[アクションの追加]** のリンクを選択します。

    ![[条件] ウィンドウ、[はいの場合]、[アクションの追加]](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

1. **[電子メールの送信 (Office 365 Outlook)]** を選択します。

    ![[条件] ウィンドウ、[はいの場合]、[メールの送信]](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!NOTE]
    >別の電子メール プロバイダーを使用するには、代わりに **[Send an email notification (Mail)]\(電子メール通知を送信する (メール)\)** をアクションとして選択します。 この手順では、Office 365 Outlook を使用して構成する方法を説明しますが、手順は別の電子メール プロバイダーでも同様です。

1. [Office 365 Outlook] ウィンドウで、以下のボックスの情報を指定します。

    1. **[宛先]** :この通知を受信するすべてのユーザーの電子メール アドレスを入力します。
    1. **Subject**:電子メールの件名を入力します。 たとえば、「**新しいリード!** 」と入力します。
    1. **本文**:各電子メールに含めるテキストを追加し (省略可能)、`body('Get_entities')?['value']` を貼り付けます。

    >[!NOTE]
    >この電子メールの本文に追加の静的または動的データ ポイントを挿入できます。

    ![[条件] ウィンドウ、はいの場合、[Office 365 Outlook] ウィンドウ](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

1. **[保存]** を選択してフローを保存します。 Power Automate がフローのテストを自動的に実行して、エラーの有無を確認します。 エラーがない場合は、保存された後、フローが実行を開始します。

次の図では、最終的なフローの例が示されています。

![最終的なフローの例](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>フローを管理する

フロー実行後の管理は簡単です。 そのフローを完全に制御できます。 たとえば、停止、編集、実行履歴の表示、分析の取得などが可能です。 次の画像は、フローを管理するために使用できるオプションを示しています。

 ![フローを管理する](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

フローは、 **[Turn flow off] (フローを停止)** オプションを使用して停止するまで実行状態を維持します。

リードの電子メール通知を何も受信していない場合、それは Azure テーブルに新しいリードが追加されていないことを示しています。 フローのエラーがある場合、次の例のようなメールが届きます。

 ![フローのエラーの電子メール通知](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>リードを Azure テーブルに送信するようにオファーを構成する

発行ポータル内でオファー用にリード管理情報を構成する準備ができたら、次の手順に従います。

1. オファーの **[オファーのセットアップ]** ページに移動します。

1. **[潜在顧客]** セクションで、 **[接続]** を選択します。

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-table/customer-leads.png" alt-text="[潜在顧客]":::

1. **[接続の詳細]** ポップアップ ウィンドウで、 **[リードのターゲット]** として **[Azure テーブル]** を選択します。 
     ![リード管理、接続の詳細](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

1. 前の手順に従って作成した Azure ストレージ アカウントの接続文字列を **[ストレージ アカウント接続文字列]** ボックスに貼り付けます。
     ![リード管理、接続の詳細のストレージ アカウント](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

1. **連絡先の電子メール**: 新しいリードを受信したときに、メール通知を受け取る必要がある社内のユーザーにメールを送信します。 セミコロンで区切って複数の電子メールを指定できます。

1. **[OK]** を選択します。

リードのターゲットに正常に接続されたことを確認するには、 **[検証]** ボタンを選択します。 成功した場合は、リードのターゲットにテスト リードが表示されます。

>[!NOTE]
>オファーのリードを受け取るには、まずオファーの残りの部分の構成を完了して発行する必要があります。

リードが生成されると、Microsoft から Azure テーブルにリードが送信されます。 フローを構成した場合は、構成したメール アドレスにもメールが送信されます。

## <a name="next-steps"></a>次のステップ

- [リード管理に関する FAQ とトラブルシューティング](../lead-management-faq.md)
