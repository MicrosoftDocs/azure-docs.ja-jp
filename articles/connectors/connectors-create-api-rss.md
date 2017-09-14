---
title: "Azure Logic Apps での RSS コネクタ | Microsoft Docs"
description: "Azure App Service を使用してロジック アプリを作成します。 RSS コネクタを使用して、フィード アイテムを発行および取得できます。 また、新しいアイテムがフィードに発行されたときに操作をトリガーすることもできます。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: a10a6277-ed29-4e68-a881-ccdad6fd0ad8
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 308d78550e9e60303b70d591eb4e6bfff3e49ce7
ms.contentlocale: ja-jp
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-rss-connector"></a>RSS コネクタの使用
RSS は、ブログ記事やニュースのヘッドラインのように、頻繁に更新されるコンテンツを発行するために使用する一般的な Web 配信形式です。  多くのコンテンツ発行元は、ユーザーがサブスクライブすることができる RSS フィードを提供します。  新しい項目を RSS フィードに発行するときに、RSS コネクタを使用してフィード情報を取得しフローをトリガーします。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事を参照してください。

## <a name="create-a-connection-to-rss"></a>RSS への接続の作成
> [!INCLUDE [Steps to create a connection to an RSS feed](../../includes/connectors-create-api-rss.md)]
> 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/rss/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。
