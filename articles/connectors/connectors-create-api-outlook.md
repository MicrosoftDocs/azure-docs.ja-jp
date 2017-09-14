---
title: "Azure Logic Apps での Outlook.com コネクタ | Microsoft Docs"
description: "Azure App Service を使用してロジック アプリを作成します。 Outlook.com コネクタでは、メール、予定表、連絡先を管理できます。 メールを送信する、会議のスケジュールを設定する、連絡先を追加するなど、さまざまなアクションを実行できます。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: bde1629504c97cf6706b42219570ffa6243073dd
ms.contentlocale: ja-jp
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-outlookcom-connector"></a>Outlook.com コネクタの使用
Outlook.com コネクタでは、メール、予定表、連絡先を管理できます。 メールを送信する、会議のスケジュールを設定する、連絡先を追加するなど、さまざまなアクションを実行できます。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事を参照してください。

## <a name="create-a-connection-to-outlookcom"></a>Outlook.com への接続を作成する
Outlook.com を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| トークン |はい |Outlook.com の資格情報を提供します |

接続を作成したら、その接続を使用してアクションを実行し、この記事で説明するトリガーをリッスンできます。

> [!INCLUDE [Steps to create a connection to Outlook.com](../../includes/connectors-create-api-outlook.md)]
>

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/outlook/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。
