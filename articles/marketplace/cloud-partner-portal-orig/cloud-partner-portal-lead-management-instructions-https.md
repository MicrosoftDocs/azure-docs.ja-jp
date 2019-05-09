---
title: HTTPS エンドポイント | Microsoft Docs
description: HTTPS エンドポイントの潜在顧客管理を構成します。
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
ms.date: 12/24/2018
ms.author: pbutlerm
ms.openlocfilehash: cfcd154b2f44c9e8acf12a9666abc9ce95fb3c26
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648352"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>HTTPS エンドポイントを使用して潜在顧客管理を構成する

HTTPS エンドポイントを使用して、Azure Marketplace と AppSource の潜在顧客を処理することができます。 これらの潜在顧客を顧客関係管理 (CRM) システムに書き込んだり、電子メールとして送信したりすることができます。 この記事では、[Microsoft Flow](https://powerapps.microsoft.com/automate-processes/) オートメーション サービスを使用して潜在顧客管理を構成する方法について説明します。

## <a name="create-a-flow-using-microsoft-flow"></a>Microsoft Flow を使用してフローを作成する

1. [Flow](https://flow.microsoft.com/) の Web ページを開きます。 **[サインイン]** または **[無料でサインアップ]** を選択して、無料の Flow アカウントを作成します。

2. サインインし、メニュー バーの **[マイ フロー]** を選択します。

    ![マイ フロー](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

3. **[一から作成]** を選択します。

    ![一から作成する](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

4. **[一から作成]** を選択します。

    ![一から作成する](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank2.png)

5. **[コネクタとトリガーを検索する]** フィールドに「要求」と入力して要求コネクタを検索します。
6. **[トリガー]** で **[HTTP 要求の受信時]** を選択します 

    ![HTTP 要求の受信トリガーを選択する](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

7. 以下のいずれかの手順を使用して、**[要求本文の JSON スキーマ]** を構成します。

   - この記事の最後にある [JSON スキーマ](#json-schema)を **[要求本文の JSON スキーマ]** テキスト ボックスにコピーします。
   - **[サンプルのペイロードを使用してスキーマを生成する]** を選びます。 **[サンプルの JSON ペイロードを入力するか、貼り付けます]** テキスト ボックスに [JSON の例](#json-example)を貼り付けます。 **[完了]** を選択してスキーマを作成します。

   >[!Note]
   >フローのこの時点で、CRM システムに接続したり、電子メール通知を構成したりすることができます。

### <a name="to-connect-to-a-crm-system"></a>CRM システムに接続するには:

1. **[+ New step (+ 新しいステップ)]** を選択します。
2. 新しいレコードを作成するアクションを含む任意の CRM システムを選択します。 次のスクリーン キャプチャでは、例として **[Dynamics 365 - 新しいレコードの作成]** が示されています。

    ![新しいレコードを作成する](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. コネクタの接続入力である **[組織名]** を入力します。 **[エンティティ名]** ドロップダウン リストから **[潜在顧客]** を選択します。

    ![潜在顧客を選択する](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. 潜在顧客の情報を提供するためのフォームが Flow に表示されます。 動的なコンテンツの追加を選択して、入力要求からの項目をマップできます。 次のスクリーン キャプチャでは、例として **OfferTitle** が示されています。

    ![動的なコンテンツの追加](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. 必要なフィールドをマップし、**[保存]** を選択してフローを保存します。

6. HTTP POST URL が要求に作成されます。 この URL をコピーし、HTTPS エンドポイントとして使用します。

    ![HTTP Post URL](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>電子メール通知を設定するには

1. **[+ New step (+ 新しいステップ)]** を選択します。
2. **[アクションを選択してください]** で **[アクション]** を選択します。
3. **[アクション]** で、**[電子メールの送信]** を選択します。

    ![電子メール アクションを作成する](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. **[電子メールの送信]** で次の必須フィールドを構成します。

   - **[To]** - 少なくとも 1 つの有効なメール アドレスを入力する必要があります。
   - **[件名]** - 次のスクリーン キャプチャの **LeadSource** のように、Flow には動的なコンテンツを追加するオプションがあります。

     ![動的なコンテンツを使用して電子メール アクションを追加する](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **[本文]** - [動的なコンテンツ] 一覧から、電子メールの本文に必要な情報を追加します。 たとえば、LastName、FirstName、Email、Company などです。

   電子メール通知の設定を完了すると、次のスクリーン キャプチャの例のようになります。

   ![電子メール アクションを作成する](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. **[保存]** を選択してフローを終了します。
6. HTTP POST URL が要求に作成されます。 この URL をコピーし、HTTPS エンドポイントとして使用します。

    ![HTTP Post URL](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>潜在顧客を HTTPS エンドポイントに送信するようにオファーを構成する

オファー用の潜在顧客管理情報を構成するときは、**[Lead Destination]\(潜在顧客の宛先\)** で **[HTTPS エンドポイント]** を選択し、前のステップでコピーした HTTP POST URL を貼り付けます。  

![動的なコンテンツの追加](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

潜在顧客が生成されると、Flow に送信されてから、構成した CRM システムまたメール アドレスにルーティングされます。

## <a name="json-schema-and-example"></a>JSON スキーマと例

この JSON テスト例では、次のスキーマを使用しています。

### <a name="json-schema"></a>JSON スキーマ

``` json
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

次の JSON 例をコピーし、編集して、MS Flow でテストとして使用することができます。

### <a name="json-example"></a>JSON の例

```json
{
"OfferTitle": "Test Microsoft",
"LeadSource": "Test run through MS Flow",
"UserDetails": {
"Company": "Contoso",
"Country": "USA",
"Email": "someone@contoso.com",
"FirstName": "Some",
"LastName": "One",
"Phone": "16175555555",
"Title": "Esquire"
}
}
```

## <a name="next-steps"></a>次の手順

まだ Cloud パートナー ポータルで[潜在顧客](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)を構成していない場合は構成します。
