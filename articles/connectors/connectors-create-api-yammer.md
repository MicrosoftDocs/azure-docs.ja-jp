---
title: "Azure Logic Apps に Yammer コネクタを追加する | Microsoft Docs"
description: "Yammer コネクタと REST API パラメーターの概要"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: c7a213343b4fb2b5a89a5052a459061b404a431c
ms.contentlocale: ja-jp
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-yammer-connector"></a>Yammer コネクタの使用
Yammer に接続し、エンタープライズ ネットワークの会話にアクセスします。 Yammer で次のことができます。

* Yammer から取得したデータに基づいてビジネス フローを構築します。 
* グループまたはフォローしているフィードで新しいメッセージが発生したときにトリガーを使用します。
* アクションを使用し、メッセージを投稿したり、すべてのメッセージを取得したりします。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 たとえば、新しいメッセージが表示されたら、Office 365 を使用して電子メールを送信できます。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事を参照してください。

## <a name="create-a-connection-to-yammer"></a>Yammer への接続を作成する
Yammer コネクタを使用するには、最初に**接続**を作成し、以下のプロパティの詳細を指定します。 

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| トークン |あり |Yammer 資格情報を入力します |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/yammer/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。
