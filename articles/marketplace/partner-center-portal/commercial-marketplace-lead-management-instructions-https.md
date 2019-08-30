---
title: HTTPS エンドポイント | Azure Marketplace
description: HTTPS エンドポイントの潜在顧客管理を構成します。
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: evansma
ms.openlocfilehash: 6a34bdcab5a13af682515bbae96e9a1800ccc37f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69901403"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>HTTPS エンドポイントを使用して潜在顧客管理を構成する

Azure Marketplace と AppSource のリードを受け取るためにパートナー センターで顧客関係管理 (CRM) システムが明示的にサポートされていない場合は、MS Flow の HTTPS エンドポイントを使用してこれらのリードを処理できます。 HTTPS エンドポイントでは、これらのリードを電子メール通知として送信したり、MS Flow でサポートされる顧客関係管理 (CRM) システムに書き込んだりすることができます。 この記事の手順では、Microsoft Flow を使用して新しいフローを作成するための基本的なプロセスについて説明します。これにより、[リード管理] > **[HTTPS エンドポイントの URL]** フィールドの発行ポータルに入力する HTTP POST URL が生成されます。 また、オンラインで検索できる [Postman](https://www.getpostman.com/downloads/) というツールを使用してフローをテストする方法についても説明します。

## <a name="create-a-flow-using-microsoft-flow"></a>Microsoft Flow を使用してフローを作成する

1. [Flow](https://flow.microsoft.com/) の Web ページを開きます。 **[サインイン]** を選択するか、アカウントがまだない場合は **[無料でサインアップ]** を選択して、無料の Flow アカウントを作成します。

2. サインインし、メニュー バーの **[マイ フロー]** を選択します。

3. **[+Automated – from blank]\(+自動 - 白紙から作成\)** を選択します。

    ![[マイ フロー] [+Automated – from blank]\(+自動 – 白紙から作成\)](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. *[自動化されたフローを作成します]* ウィンドウで、 **[スキップ]** を選択します。 

    ![[自動化されたフローを作成します] - [スキップ]](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. **[コネクタとトリガーを検索する]** フィールドに「要求」と入力して要求コネクタを検索します。
6. *[トリガー]* で **[HTTP 要求の受信時]** を選択します 

    ![[要求] コネクタ - [トリガー]](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. " *[HTTP 要求の受信時]* " ウィンドウで、次の JSON スキーマをコピーして **[要求本文の JSON スキーマ]** テキスト ボックスに貼り付けます。 このスキーマは、リード データを格納するために Microsoft によって使用されます。

    ![[要求] コネクタ - [トリガー]](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

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

>[!Note]
>構成のこの時点で、CRM システムに接続したり、電子メール通知を構成したりすることを選択できます。 選択した内容に基づいて、残りの手順に従います。

### <a name="to-connect-to-a-crm-system"></a>CRM システムに接続するには:

1. **[+ New step (+ 新しいステップ)]** を選択します。
2. "*コネクタとアクションを検索する*" と表示されている場所を検索して、任意の CRM システムを選択し、新しいレコードを作成するためのアクションが表示された *[アクション]* セクションでこれを選択します。 次のスクリーン キャプチャでは、例として **[Dynamics 365 - 新しいレコードの作成]** が示されています。

    ![新しいレコードを作成する](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. CRM システムに関連付けられている**組織名**を指定します。 **[エンティティ名]** ドロップダウン リストから **[潜在顧客]** を選択します。

    ![潜在顧客を選択する](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. 潜在顧客の情報を提供するためのフォームが Flow に表示されます。 動的なコンテンツの追加を選択して、入力要求からの項目をマップできます。 次のスクリーン キャプチャでは、例として **OfferTitle** が示されています。

    ![動的なコンテンツの追加](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. 必要なフィールドをマップし、 **[保存]** を選択してフローを保存します。 HTTP POST URL が作成され、" *[HTTP 要求の受信時]* " ウィンドウ内でアクセスできます。 HTTP POST URL の右側にあるコピー コントロールを使用して、この URL をコピーします。これは、URL 全体の一部を誤って失わないようにするために重要です。 この URL は、発行ポータル内でリード管理を構成するときに必要になるため、保存しておきます。

    ![HTTP 要求の受信時。](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>電子メール通知を設定するには

1. JSON スキーマが完成したので、 **[+ 新しいステップ]** を選択します。
2. **[アクションを選択してください]** で **[アクション]** を選択します。
3. **[アクション]** で、 **[電子メールの送信 (Office 365 Outlook)]** を選択します。

    >[!Note]
    >別の電子メール プロバイダーを使用する場合は、代わりに " *[Send an email notification (Mail)]\(電子メール通知を送信する (メール)\)* " をアクションとして選択します。

    ![電子メール アクションを作成する](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. **[電子メールの送信]** ウィンドウで次の必須フィールドを構成します。

   - **[送信先]** - リードが送信される有効な電子メール アドレスを少なくとも 1 つ入力します。
   - **[件名]** - 次のスクリーン キャプチャの **LeadSource** のように、Flow には動的なコンテンツを追加するオプションがあります。 まず、フィールド名を入力し、次にポップアップ ウィンドウから [動的なコンテンツ] 候補リストをクリックします。 

        >[!Note] 
        > フィールド名を追加する場合は、それぞれの後に ":" を指定し、Enter キーを押して新しい行を作成します。 フィールド名を追加したら、関連付けられている各パラメーターを動的候補リストから追加できます。

        ![動的なコンテンツを使用して電子メール アクションを追加する](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **[本文]** - [動的なコンテンツ] 候補リストから、電子メールの本文に必要な情報を追加します。 たとえば、LastName、FirstName、Email、Company などです。 <br> <br> 電子メール通知の設定を完了すると、次のスクリーン キャプチャの例のようになります。


       ![電子メール アクションを作成する](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. **[保存]** を選択してフローを終了します。 HTTP POST URL が作成され、" *[HTTP 要求の受信時]* " ウィンドウ内でアクセスできます。 HTTP POST URL の右側にあるコピー コントロールを使用して、この URL をコピーします。これは、URL 全体の一部を誤って失わないようにするために重要です。 この URL は、発行ポータル内でリード管理を構成するときに必要になるため、保存しておきます。

   ![HTTP POST URL ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>テスト

オンラインでダウンロードできる [Postman](https://app.getpostman.com/app/download/win64) というツールを使用して、次の手順に従ってすべてが期待どおりに動作することをテストできます。 これは、Windows に対応しています。 

1. Postman を起動し、 **[新規]**  >  **[要求]** を選択してテスト ツールを設定します。 

   ![テスト ツールのセットアップ要求](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. " *[要求の保存]* " フォームに入力し、作成したフォルダーに**保存**します。

   ![要求の保存](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. ドロップダウン リストから **[POST]** を選択します。 

   ![フローのテスト](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. "*要求 URL を入力してください*" と表示された、MS Flow 内で作成したフローから、HTTP POST URL を貼り付けます。

   ![HTTP POST URL の貼り付け](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. [[Flow]](https://flow.microsoft.com/) に戻り、Flow メニュー バーの **[マイ フロー]** に移動して、リードを送信するために作成したフローを見つけます。  フロー名の横にある 3 つのドットを選択し、 **[編集]** を選択します。

   ![[マイ フロー] - [編集]](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. 右上隅にある **[テスト]** を選択し、[トリガー アクションを実行する] を選択してから、 **[テスト]** を選択します。 画面の上部に、テストが開始したことを示す通知が表示されます

   ![[フローのテスト] - トリガー](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Postman アプリに戻り、HTTPS URL を貼り付けた場所の横にある **[送信]** を選択します。

   ![[フローのテスト] - [送信]](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. フローに戻り、結果を確認します。 すべてが期待どおりに正常に動作する場合は、成功したことを示すメッセージが表示されます。

   ![[フロー ]- チェックの結果](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. 電子メールも受信しているはずです。 メールの受信トレイを確認してください。 

    >[!Note] 
    >テストからの電子メールが表示されない場合は、スパム フォルダーと迷惑メール フォルダーを確認してください。 以下では、電子メール通知を構成するときに追加したフィールド ラベルだけが表示されていることがわかります。 これがオファーから生成された実際のリードである場合は、本文と件名行にあるリードの担当者からの実際の情報も表示されます。

   ![受信した電子メール](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>潜在顧客を HTTPS エンドポイントに送信するようにオファーを構成する

発行ポータル内でオファーのリード管理情報を構成する準備ができたら、次の手順に従います。

1. オファーの **[オファーのセットアップ]** ページに移動します。
2. [リード管理] セクションで **[接続]** を選択します。
3. [接続の詳細] ポップアップ ウィンドウで、 **[リードのターゲット]** に対して **[HTTPS エンドポイント]** を選択し、作成したフローからの HTTP POST URL を **[HTTPS エンドポイントの URL]** フィールドに貼り付けます。
4. **[保存]** を選択します。 

>[!Note] 
>オファーのリードを受け取るには、まずオファーの残りの部分の構成を完了して発行する必要があります。

潜在顧客が生成されると、Flow に送信されてから、構成した CRM システムまたメール アドレスにルーティングされます。

![[リード管理] - [接続]](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![接続の詳細](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![接続の詳細](./media/commercial-marketplace-lead-management-instructions-https/connection-details-1.png)

