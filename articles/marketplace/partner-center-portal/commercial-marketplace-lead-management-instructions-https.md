---
title: HTTPS エンドポイントを使用したリード管理 - Microsoft コマーシャル マーケットプレース
description: Power Automate と HTTPS エンドポイントを使用して、Microsoft AppSource および Azure Marketplace からリードを管理する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: 5bea2cf256e30bd896957bbee0e0ad824057a569
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98247184"
---
# <a name="use-an-https-endpoint-to-manage-commercial-marketplace-leads"></a>HTTPS エンドポイントを使用してコマーシャル マーケットプレースのリードを管理する

Microsoft AppSource と Azure Marketplace のリードを受け取るためにパートナー センターで顧客関係管理 (CRM) システムが明示的にサポートされていない場合は、[Power Automate](https://powerapps.microsoft.com/automate-processes/) の HTTPS エンドポイントを使用してこれらのリードを処理できます。 HTTPS エンドポイントでは、コマーシャル マーケットプレースのリードを電子メール通知として送信したり、Power Automate でサポートされる CRM システムに書き込んだりすることができます。

この記事では、Power Automate の新しいフローを作成して、パートナー センターでリードを構成するために使用する HTTP POST URL を生成する方法について説明します。 また、[Postman](https://www.getpostman.com/downloads/) によりフローをテストする手順も含まれています。

>[!NOTE]
>以下の手順で使用される Power Automate コネクタには、Power Automate の有料サブスクリプションが必要です。 このフローを構成する前に、このことを考慮してください。

## <a name="create-a-flow-by-using-power-automate"></a>Power Automate を使用してフローを作成する

1. [Power Automate](https://flow.microsoft.com/) Web ページを開きます。 **[サインイン]** をクリックします。 アカウントがまだない場合は **[無料でサインアップ]** を選択して、無料の Power Automate アカウントを作成します。

1. サインインし、メニューの **[マイ フロー]** を選択します。

    ![マイ フローにサインイン](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. **[+ 新規]** の下で、 **[+ Instant—from blank]\(インスタント - 一から作成\)** を選択します。

    ![[マイ フロー] [+ Automated--from blank]\(+自動 – 白紙から作成\)](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. フローに名前を付け、次に、 **[このフローをトリガーする方法を選択します]** の下で **[HTTP 要求の受信時]** を選択します。

    ![[自動化されたフローを作成します] ウィンドウの [スキップ] ボタン](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. フロー ステップをクリックして展開します。

    ![フロー ステップを展開](./media/commercial-marketplace-lead-management-instructions-https/expand-flow-step.png)

1. 次のいずれかの方法を使用して、 **[要求本文の JSON スキーマ]** を構成します。

    - JSON スキーマを **[要求本文の JSON スキーマ]** テキスト ボックスにコピーします。
    - **[サンプルのペイロードを使用してスキーマを生成する]** を選びます。 **[サンプルの JSON ペイロードを入力するか、貼り付けます]** テキスト ボックスに JSON の例を貼り付けます。 **[完了]** を選択してスキーマを作成します。

    **JSON スキーマ**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "Description": {
          "id": "/properties/Description",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

    **JSON の例**
    
    ```json
    {
      "UserDetails": {
        "FirstName": "Some",
        "LastName": "One",
        "Email": "someone@contoso.com",
        "Phone": "16175555555",
        "Country": "USA",
        "Company": "Contoso",
        "Title": "Esquire"
     },
      "LeadSource": "AzureMarketplace",
      "ActionCode": "INS",
      "OfferTitle": "Test Microsoft",
      "Description": "Test run through Power Automate"
    }
    ```

>[!NOTE]
>構成のこの時点で、CRM システムに接続したり、電子メール通知を構成したりすることを選択できます。 選択した内容に基づいて、残りの手順に従います。

### <a name="connect-to-a-crm-system"></a>CRM システムに接続する

1. **[+ New step (+ 新しいステップ)]** を選択します。
1. **[コネクタとアクションを検索する]** と表示されている場所を検索して、任意の CRM システムを選択します。 それを、新しいレコードを作成するためのアクションが表示された **[アクション]** タブで選択します。 次の画面では、例として **[新しいレコードを作成する (Dynamics 365)]** が示されています。

    ![新しいレコードを作成する](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

1. CRM システムに関連付けられている **組織名** を指定します。 **[エンティティ名]** ドロップダウン リストから **[潜在顧客]** を選択します。

    ![潜在顧客を選択する](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

1. 潜在顧客の情報を提供するためのフォームが Power Automate に表示されます。 動的なコンテンツの追加を選択して、入力要求からの項目をマップできます。 次の画面では、例として **OfferTitle** が示されています。

    ![動的なコンテンツの追加](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

1. 必要なフィールドをマップし、 **[保存]** を選択してフローを保存します。 HTTP POST URL が作成され、" **[HTTP 要求の受信時]** " ウィンドウ内でアクセスできます。 この URL を、HTTP POST URL の右側にあるコピー コントロールを使用してコピーします。 URL のすべての部分が欠落することがないよう、コピー コントロールを使用することが重要です。 この URL は、発行ポータル内でリード管理を構成するときに必要になるため、保存しておきます。

    ![HTTP 要求を受信したとき](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="set-up-email-notification"></a>電子メール通知を設定する

1. JSON スキーマが完成したので、 **[+ 新しいステップ]** を選択します。
1. **[アクションを選択してください]** で **[アクション]** を選択します。
1. **[アクション]** タブで、 **[電子メールの送信 (Office 365 Outlook)]** を選択します。

    >[!NOTE]
    >別の電子メール プロバイダーを使用する場合は、代わりに **[Send an email notification (Mail)]\(電子メール通知を送信する (メール)\)** をアクションとして選択します。

    ![電子メール アクションを作成する](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. **[電子メールの送信]** ウィンドウで次の必須フィールドを構成します。

   - **[宛先]** :リードが送信される有効なメール アドレスを少なくとも 1 つ入力します。
   - **Subject**:次の画面に示す **LeadSource** のように、Power Automate には動的なコンテンツを追加するオプションがあります。 まず、フィールド名を入力します。 次に、ポップアップ ウィンドウから動的なコンテンツ選択リストを選択します。 

        >[!NOTE] 
        > フィールド名を追加するときは、それぞれの名前の後にコロン (:) を付け、**Enter** キーを選択して、新しい行を作成することができます。 フィールド名を追加したら、関連付けられている各パラメーターを動的な選択リストから追加できます。

        ![動的なコンテンツを使用して電子メール アクションを追加する](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **本文**:動的なコンテンツ選択リストから、電子メールの本文に必要な情報を追加します。 たとえば、LastName、FirstName、Email、Company などを使用します。 電子メール通知の設定を完了すると、次の画面の例のようになります。


       ![電子メール通知の例](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. **[保存]** を選択してフローを終了します。 HTTP POST URL が作成され、" **[HTTP 要求の受信時]** " ウィンドウ内でアクセスできます。 この URL を、HTTP POST URL の右側にあるコピー コントロールを使用してコピーします。 URL のすべての部分が欠落することがないよう、このコピー コントロールを使用することが重要です。 この URL は、発行ポータル内でリード管理を構成するときに必要になるため、保存しておきます。

   ![HTTP POST URL](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>テスト

[Postman](https://app.getpostman.com/app/download/win64) により構成をテストできます。 Windows 向けの Postman のオンライン ダウンロードを利用できます。 

1. Postman を起動し、 **[新規]**  >  **[要求]** を選択してテスト ツールを設定します。 

   ![テスト ツールのセットアップ要求](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. **[要求の保存]** フォームに入力し、作成したフォルダーに保存します。

   ![[要求の保存] フォーム](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. ドロップダウン リストから **[POST]** を選択します。 

   ![フローのテスト](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. "**要求 URL を入力してください**" と表示された、Power Automate 内で作成したフローから、HTTP POST URL を貼り付けます。

   ![HTTP POST URL の貼り付け](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. [Power Automate](https://flow.microsoft.com/) に戻ります。 Power Automate メニュー バーで **[マイ フロー]** に移動して、リードを送信するために作成したフローを見つけます。 フロー名の横にある省略記号を選択して他のオプションを表示し、 **[編集]** を選択します。


1. 右上隅にある **[テスト]** を選択し、 **[トリガー アクションを実行する]** を選択してから、 **[テスト]** を選択します。 画面の上部に、テストが開始したことを示す通知が表示されます。

   ![[トリガーアクションを実行する] オプション](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. Postman アプリに戻り、 **[送信]** を選択します。

   ![[送信] ボタン](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. フローに戻り、結果を確認します。 すべてが期待どおりに正常に動作する場合は、フローが成功したことを示すメッセージが表示されます。

   ![結果をチェックする](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. 電子メールも受信しているはずです。 メールの受信トレイを確認してください。 

    >[!NOTE] 
    >テストからの電子メールが見つからない場合は、スパム フォルダーと迷惑メール フォルダーを確認してください。 以下の画面では、電子メール通知を構成するときに追加したフィールド ラベルだけが表示されていることがわかります。 これがオファーから生成された実際のリードである場合は、本文と件名行にあるリードの担当者からの実際の情報も表示されます。

   ![受信した電子メール](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>潜在顧客を HTTPS エンドポイントに送信するようにオファーを構成する

発行ポータル内でオファー用にリード管理情報を構成する準備ができたら、次の手順に従います。

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。

1. オファーを選択し、 **[Offer setup]\(オファーのセットアップ\)** タブにアクセスします。

1. **[潜在顧客]** セクションで、 **[接続]** を選択します。

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-https/customer-leads.png" alt-text="[潜在顧客]":::

1. **[接続の詳細]** ポップアップ ウィンドウで、 **[リードのターゲット]** として **[HTTPS エンドポイント]** を選択します。 前の手順に従って作成したフローから HTTP POST URL を **[HTTPS エンドポイントの URL]** フィールドに貼り付けます。
    ![[接続の詳細] の [連絡先の電子メール]](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

1. **[連絡先の電子メール]** で、新しいリードを受信したときに、メール通知を受け取る必要がある社内のユーザーのメール アドレスを入力します。 セミコロンで区切ることで、複数の電子メールを指定できます。

1. **[OK]** を選択します。

リードのターゲットに正常に接続されたことを確認するには、 **[検証]** ボタンを選択します。 成功した場合は、リードのターゲットにテスト リードが表示されます。

>[!NOTE] 
>オファーのリードを受け取るには、まずオファーの残りの部分の構成を完了して発行する必要があります。

リードが生成されると、Microsoft からフローにリードが送信されます。 リードは、構成した CRM システムまたはメール アドレスにルーティングされます。
