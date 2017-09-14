---
title: "Azure Logic Apps での GitHub コネクタ | Microsoft Docs"
description: "Azure App Service を使用してロジック アプリを作成します。 GitHub は、 web ベースの Git リポジトリホスティングサービスです。 独自の機能を追加するだけでなく、Git の分散リビジョン コントロール機能とソース コード管理 (SCM) 機能をすべて提供します。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: d4614b0ceff0ec0d36dbb1a136551f985f2fc1a1
ms.contentlocale: ja-jp
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-github-connector"></a>GitHub コネクタの使用
GitHub は、 web ベースの Git リポジトリホスティングサービスです。 独自の機能を追加するだけでなく、Git の分散リビジョン コントロール機能とソース コード管理 (SCM) 機能をすべて提供します。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事を参照してください。

## <a name="create-a-connection-to-github"></a>GitHub への接続を作成する
GitHub を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。 

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| トークン |はい |GitHub の資格情報を提供します |

接続を作成したら、その接続を使用してアクションを実行し、この記事で説明するトリガーをリッスンできます。 

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/github/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。
