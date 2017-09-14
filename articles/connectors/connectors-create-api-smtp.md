---
title: "Azure Logic Apps での SMTP コネクタ | Microsoft Docs"
description: "Azure App Service を使用してロジック アプリを作成します。 SMTP に接続してメールを送信します。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 1cf96bbf8bd215d7ddb3c99860a5cb4e668be3c2
ms.contentlocale: ja-jp
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-smtp-connector"></a>SMTP コネクタの概要
SMTP に接続してメールを送信します。

[任意のコネクタ](apis-list.md)を使用するには、まずロジック アプリを作成する必要があります。 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)から始めることができます。

## <a name="connect-to-smtp"></a>SMTP への接続
ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの*接続*を作成する必要があります。 [接続](connectors-overview.md)により、ロジック アプリと別のサービスとの接続が実現します。 たとえば、SMTP に接続するには、まず SMTP "*接続*" が必要です。 接続を作成するには、接続対象のサービスへのアクセスに通常使用する資格情報を入力します。 そのため、SMTP の例では、SMTP への接続を作成するために、接続名、SMTP サーバーのアドレス、ユーザーのログイン情報に対して資格情報を入力します。  

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

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/smtpconnector/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。
