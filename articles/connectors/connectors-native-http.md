---
title: Azure Logic Apps を使用して任意の HTTP エンドポイントに接続する | Microsoft Docs
description: Azure Logic Apps を使用して、任意の HTTP エンドポイントと通信するタスクとワークフローを自動化する
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e11c6b4d-65a5-4d2d-8e13-38150db09c0b
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 01da06ca55199989a3a27012bec101580f5ef853
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442207"
---
# <a name="call-http-or-https-endpoints-with-azure-logic-apps"></a>Azure Logic Apps を使用して HTTP または HTTPS エンドポイントを呼び出す

Azure Logic Apps とハイパーテキスト転送プロトコル (HTTP) コネクタを使用してロジック アプリを構築することで、任意の HTTP または HTTPS エンドポイントと通信するワークフローを自動化できます。 たとえば、Web サイトのサービス エンドポイントを監視できます。 Web サイトが停止するなど、そのエンドポイントでイベントが発生すると、そのイベントによってロジック アプリのワークフローがトリガーされ、指定したアクションが実行されます。 

HTTP トリガーは、定期的にエンドポイントをチェック、つまり*ポーリング*するワークフローの最初の手順として使用できます。 チェックごとに、トリガーからエンドポイントに対する呼び出し、つまり*要求*が送信されます。 エンドポイントの応答によって、ロジック アプリのワークフローが実行されるかどうかが決定します。 トリガーによって、応答の任意のコンテンツがロジック アプリのアクションに渡されます。 

必要に応じてエンドポイントを呼び出すために、ワークフローの他の手順として HTTP アクションを使用できます。 エンドポイントの応答によって、ワークフローの以降のアクションの実行方法が決まります。

ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。 

* 呼び出すターゲット エンドポイントの URL 

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* ターゲット エンドポイントを呼び出すロジック アプリ。HTTP トリガーから始めるには、[空のロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。 HTTP アクションを使用するには、トリガーでロジック アプリを起動します。

## <a name="add-http-trigger"></a>HTTP トリガーを追加する

1. [Azure portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーで空のロジック アプリをまだ開いていない場合は開きます。

1. 検索ボックスに、フィルターとして「http」と入力します。 トリガーの一覧で、**[HTTP]** トリガーを選択します。 

   ![HTTP トリガーを選択する](./media/connectors-native-http/select-http-trigger.png)

1. ターゲット エンドポイントへの呼び出しに含める [HTTP トリガーのパラメーターと値](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)を指定します。 トリガーでターゲット エンドポイントを確認する頻度を設定します。

   ![HTTP トリガー パラメーターを入力する](./media/connectors-native-http/http-trigger-parameters.png)

   HTTP トリガー、パラメーター、および値の詳細については、[トリガーとアクションの種類に関するリファレンス](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)を参照してください。

1. トリガーが起動したときに実行されるアクションを使用して、ロジック アプリのワークフローを引き続き構築します。

## <a name="add-http-action"></a>HTTP アクションを追加する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. HTTP アクションを追加する最後のステップの下で、**[新しいステップ]** を選択します。 

   この例では、ロジック アプリは最初のステップとして HTTP トリガーから開始します。

1. 検索ボックスに、フィルターとして「http」と入力します。 アクションの一覧で、**[HTTP]** アクションを選択します。

   ![HTTP アクションを選択する](./media/connectors-native-http/select-http-action.png)

   ステップの間にアクションを追加するには、ステップ間の矢印の上にポインターを移動します。 
   表示されるプラス記号 (**+**) を選択し、**[アクションの追加]** を選択します。

1. ターゲット エンドポイントへの呼び出しに含める [HTTP アクションのパラメーターと値](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)を指定します。 

   ![HTTP アクションのパラメーターを入力する](./media/connectors-native-http/http-action-parameters.png)

1. 完了したら、必ずロジック アプリを保存します。 デザイナーのツール バーで、**[保存]** を選択します。 

## <a name="authentication"></a>Authentication

認証を設定するには、アクションまたはトリガー内で **[詳細オプションの表示]** を選択します。 HTTP トリガーとアクションに使用できる認証の種類の詳細については、[トリガーとアクションの種類に関するリファレンス](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)を参照してください。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
