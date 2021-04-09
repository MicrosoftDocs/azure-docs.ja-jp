---
title: GitHub リポジトリに対してアクセス、監視、および管理を行う
description: Azure Logic Apps で自動化されたワークフローを作成することにより、GitHub イベントを監視し、GitHub リポジトリを管理します
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: b67d2e860e8c69f6f2f43441144ef2f60ed3a104
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999547"
---
# <a name="monitor-and-manage-your-github-repo-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して GitHub リポジトリの監視と管理を行う

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

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。