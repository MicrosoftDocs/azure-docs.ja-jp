---
title: GitHub に接続する - Azure Logic Apps
description: GitHub REST API と Azure Logic Apps を使用して、GitHub のイベントを監視します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: 6a6e2a803ee2a272189abf0f21796b2305eea40b
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050891"
---
# <a name="connect-to-github-from-azure-logic-apps"></a>Azure Logic Apps から GitHub に接続する

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

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの OpenAPI (以前の Swagger) の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、[コネクタのリファレンス ページ](/connectors/github/)を参照してください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。