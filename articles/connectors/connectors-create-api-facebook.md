---
title: Facebook に接続する - Azure Logic Apps
description: Facebook REST API と Azure Logic Apps を使用して、タイムラインとページを管理します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 83431184d7e9c5970ece6af143ee9b5166da96d5
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050940"
---
# <a name="manage-your-facebook-timeline-and-page-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して Facebook のタイムラインとページを管理する

Facebook に接続し、タイムラインへの投稿、ページ フィードの取得などを行います。 Facebook では、次の操作を実行できます。

* Facebook から取得したデータに基づいてビジネス フローを構築できます。 
* 新しい投稿を取得したときにトリガーを使用できます。
* タイムラインへの投稿、ページ フィードの取得などのアクションを使用できます。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 たとえば、タイムラインに新しい投稿がある場合、その投稿を取得して、Twitter フィードにプッシュすることができます。 

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="create-a-connection-to-facebook"></a>Facebook への接続を作成する

ロジック アプリにこのコネクタを追加するとき、Facebook に接続するロジック アプリを承認する必要があります。

1. Facebook アカウントにサインインします。

2. **[Authorize]** を選択し、ロジック アプリが Facebook に接続して使用することを許可します。 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの Open API (以前の Swagger) ファイルによって記述される、トリガー、アクション、制限などの技術的詳細については、[コネクタのリファレンス ページ](/connectors/facebook/)を参照してください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。