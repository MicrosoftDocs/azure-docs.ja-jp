---
title: "Azure ロジック アプリで Slack コネクタを使用する | Microsoft Docs"
description: "ロジック アプリで Slack に接続します。"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: fc5fc128efe01bd0727e3ff30d8938918e89ac3a
ms.contentlocale: ja-jp
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-slack-connector"></a>Slack コネクタの使用
Slack はチーム コミュニケーション ツールです。チームのすべてのコミュニケーションが 1 個所にまとめられ、どこにいてもすぐに検索、使用できるようになります。 

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事を参照してください。

## <a name="create-a-connection-to-slack"></a>Slack への接続を作成する
Slack コネクタを使用するには、最初に **接続** を作成し、以下のプロパティの詳細を指定します。 

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| トークン |あり |Slack の資格情報を指定します |

次の手順に従って Slack にサインインし、ロジック アプリの Slack **接続**の構成を完了します。

1. **[繰り返し]**
2. **[頻度]** を選択し、**[間隔]** を入力します。
3. **[アクションの追加]** を選択します。  
   ![Slack の構成][1]  
4. 検索ボックスに「Slack」と入力し、名前に Slack が含まれたすべてのエントリが返されるまで待ちます。
5. **[Slack - メッセージの投稿]**
6. **[Slack にサインイン]** をクリックします。  
   ![Slack の構成][2]
7. アプリケーションを承認するために、Slack 資格情報を入力してサインインします。    
   ![Slack の構成][3]  
8. 組織のログイン ページにリダイレクトされます。 Slack がロジック アプリと対話することを**承認**します。      
   ![Slack の構成][5] 
9. 承認が完了すると、ロジック アプリにリダイレクトされます。**[Slack - すべてのメッセージを取得]** セクションを構成して、ロジック アプリを完成させます。 必要な他のトリガーやアクションを追加します。  
   ![Slack の構成][6]
10. 上部のメニュー バーの **[保存]** をクリックして、作業内容を保存します。

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/slack/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png

