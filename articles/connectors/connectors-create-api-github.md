---
title: GitHub に接続する - Azure Logic Apps | Microsoft Docs
description: GitHub REST API と Azure Logic Apps を使用して、GitHub のイベントを監視します
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 03/02/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: ce567dc631c3a147b795eb2355a4961faa8881d6
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295814"
---
# <a name="connect-to-github"></a>GitHub に接続する

GitHub は、Git のすべての分散リビジョン コントロールとソース コード管理 (SCM) 機能、およびその他の機能を提供する、Web ベースの Git リポジトリ ホスティング サービスです。

GitHub コネクタの使用を開始するには、[最初にロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。

## <a name="create-a-connection-to-github"></a>GitHub への接続を作成する

ロジック アプリで GitHub コネクタを使用するには、最初に "*接続*" を作成してから、以下のプロパティの詳細を指定します。 

| プロパティ | 必須 | 説明 | 
| -------- | -------- | ----------- | 
| トークン | はい | GitHub の資格情報を指定します。 |

接続を作成したら、アクションを実行して、この記事で説明するトリガーをリッスンできます。

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

Swagger で定義されているトリガーとアクション、および制限事項については、[コネクタの詳細](/connectors/github/)に関するページをご覧ください。

## <a name="find-more-connectors"></a>その他のコネクタ

* [コネクタの一覧](apis-list.md)をご覧ください。