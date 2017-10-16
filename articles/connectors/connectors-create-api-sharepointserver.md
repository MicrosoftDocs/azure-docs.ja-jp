---
title: "Logic Apps で SharePoint Server コネクタを使用する | Microsoft Docs"
description: "ロジック アプリで SharePoint Server コネクタを使用する方法の概要について説明します"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 0f3274816e279a1aa57febaa2f8294914900799a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-sharepoint-connector"></a>SharePoint コネクタの概要
SharePoint コネクタを使用すると、SharePoint リストを操作できます。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事を参照してください。

## <a name="create-a-connection-to-sharepoint"></a>SharePoint への接続を作成する
SharePoint コネクタを使用するには、まず **接続** を作成してから、以下のプロパティの詳細を指定します。 

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| トークン |あり |SharePoint の資格情報を指定します |

**SharePoint** に接続するには、SharePoint で ID (ユーザー名とパスワード、スマート カードの資格情報など) を入力します。 認証されたら、ロジック アプリで SharePoint コネクタを使用できるようになります。 

ロジック アプリのデザイナーで、次の手順に従って SharePoint にサインインし、ロジック アプリで使用する **接続** を作成します。

1. 検索ボックスに「SharePoint」と入力し、名前に SharePoint が含まれるすべてのエントリが返されるまで待ちます。   
   ![SharePoint の構成][1]  
2. **[SharePoint - When a file is created]\(SharePoint - ファイルの作成時\)** を選択します。   
3. **[Sign in to SharePoint]\(SharePoint にサインイン\)** をクリックします。   
   ![SharePoint の構成][2]    
4. SharePoint で認証するために、SharePoint 資格情報を入力してサインインします。   
   ![SharePoint の構成][3]     
5. 認証が完了すると、ロジック アプリにリダイレクトされます。SharePoint の **[When a file is created (ファイルの作成時)]** ダイアログを構成して、ロジック アプリを完成させます。          
   ![SharePoint の構成][4]  
6. ロジック アプリを完成させるために必要な他のトリガーやアクションを追加できます。   
7. 上部のメニュー バーの **[保存]** をクリックして、作業内容を保存します。  

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/sharepoint/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png
