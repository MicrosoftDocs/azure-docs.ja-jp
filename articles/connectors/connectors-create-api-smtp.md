---
title: SMTP | Microsoft Docs
description: "Azure App Service を使用してロジック アプリを作成します。 SMTP に接続してメールを送信します。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: 3a0fdef111fbd4a9f7491e247f2236cf70b89dca
ms.lasthandoff: 01/20/2017


---
# <a name="get-started-with-the-smtp-connector"></a>SMTP コネクタの概要
SMTP に接続してメールを送信します。

[任意のコネクタ](apis-list.md)を使用するには、まずロジック アプリを作成する必要があります。 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)から始めることができます。

## <a name="connect-to-smtp"></a>SMTP への接続
ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの*接続*を作成する必要があります。 [接続](connectors-overview.md)により、ロジック アプリと別のサービスとの接続が実現します。 たとえば、SMTP に接続するには、まず SMTP "*接続*" が必要です。 接続を作成するには、接続先のサービスへのアクセスに通常使用する資格情報を入力する必要があります。 そのため、SMTP の例では、SMTP への接続を作成するために、接続名、SMTP サーバーのアドレス、ユーザーのログイン情報に対して資格情報が必要になります。 接続の詳細については、[こちら]()をご覧ください。  

### <a name="create-a-connection-to-smtp"></a>SMTP への接続を作成する
> [!INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]
> 
> 

## <a name="use-an-smtp-trigger"></a>SMTP トリガーの使用
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 トリガーの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。

SMTP には独自のトリガーがないため、この例では **[Salesforce - When an object is created (Salesforce - オブジェクトが作成される場合)]** トリガーを使用します。 このトリガーは、Salesforce で新しいオブジェクトが作成されるとアクティブになります。 この例では、Salesforce で新しい潜在顧客が作成されるたびに、新しい潜在顧客の作成を通知する "*電子メールの送信*" アクションが SMTP コネクタ経由で実行されるようにトリガーを設定します。

1. Logic Apps デザイナーの検索ボックスに「 *salesforce* 」と入力し、 **[Salesforce - When an object is created (Salesforce - オブジェクトが作成される場合)]** トリガーを選択します。  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  
2. **[When an object is created (オブジェクトが作成される場合)]** コントロールが表示されます。
   ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  
3. **[オブジェクトの種類]** を選択し、オブジェクトのリストから *[潜在顧客]* を選択します。 この手順では、Salesforce で新しい潜在顧客が作成されるたびに、ロジック アプリを通知するトリガーを作成することが示されています。  
   ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  
4. トリガーが作成されました。  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>SMTP アクションの使用
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 アクションの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。

トリガーが追加されたら、次の手順に従って、Salesforce で新しい潜在顧客が作成されたときに実行される SMTP アクションを追加します。

1. **[+ 新しいステップ]** をクリックして、新しい潜在顧客が作成されたときに実行するアクションを追加します。  
   ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  
2. **[アクションの追加]**を選択します。 これにより検索ボックスが開き、行う操作について検索できます。  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  
3. 「*smtp*」と入力して、SMTP に関連するアクションを検索します。  
4. 新しい潜在顧客が作成されたときに実行するアクションとして、**[SMTP - Send Email (SMTP - 電子メールの送信)]** を選択します。 アクションの制御ブロックが表示されます。 これまで SMTP 接続を確立したことがない場合は、このデザイナー ブロックで接続を確立する必要があります。  
   ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    
5. **[SMTP - Send Email (SMTP - 電子メールの送信)]** ブロックで、必要な電子メール情報を入力します。  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  
6. ワークフローをアクティブにするために、作業内容を保存します。  

## <a name="technical-details"></a>技術的な詳細
ここでは、この接続でサポートされるトリガー、アクション、応答について詳しく説明します。

## <a name="smtp-triggers"></a>SMTP トリガー
SMTP にはトリガーはありません。 

## <a name="smtp-actions"></a>SMTP アクション
SMTP には次のアクションがあります。

| [操作] | 説明 |
| --- | --- |
| [電子メールを送信する](connectors-create-api-smtp.md#send-email) |この操作では、1 人以上の受信者に電子メールを送信します。 |

### <a name="action-details"></a>アクションの詳細
ここでは、このコネクタのアクションとその応答について詳しく説明します。

### <a name="send-email"></a>電子メールを送信する
この操作では、1 人以上の受信者に電子メールを送信します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| To |To |recipient1@domain.com;recipient2@domain.com のようにセミコロンで区切って電子メール アドレスを指定 |
| CC |cc |recipient1@domain.com;recipient2@domain.com のようにセミコロンで区切って電子メール アドレスを指定 |
| [件名] |[件名] |電子メールの件名 |
| 本文 |body |電子メールの本文 |
| ファイル |ファイル |差出人の電子メールアドレス (例: sender@domain.com) |
| IsHtml |Is HTML |電子メールを HTML として送信 (true/false) |
| [Bcc] |bcc |recipient1@domain.com;recipient2@domain.com のようにセミコロンで区切って電子メール アドレスを指定 |
| [重要度] |[重要度] |電子メールの重要度 (高、標準、または低) |
| ContentData |Attachments Content Data (添付ファイルのコンテンツ データ) |コンテンツ データ (ストリームの場合は base64 でエンコード、文字列の場合は現状のまま) |
| ContentType |Attachments Content Type (添付ファイルのコンテンツの種類) |コンテンツの種類 |
| ContentTransferEncoding |Attachments Content Transfer Encoding (添付ファイルのコンテンツ転送エンコード) |コンテンツ転送エンコード (base64 またはなし) |
| FileName |Attachments File Name (添付ファイルのファイル名) |ファイル名 |
| ContentId |Attachments Content ID (添付ファイルのコンテンツ ID) |コンテンツ ID |

* は、必須のプロパティを示します。

## <a name="http-responses"></a>HTTP 応答
上記のアクションとトリガーは、次の HTTP 状態コードを&1; つ以上返す場合があります。 

| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 202 |承認済み |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました。 |
| default |操作に失敗しました。 |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成します](../logic-apps/logic-apps-create-a-logic-app.md)


