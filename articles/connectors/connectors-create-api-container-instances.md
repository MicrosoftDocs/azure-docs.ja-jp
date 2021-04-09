---
title: Azure Container Instances をデプロイして管理する
description: Azure Logic Apps を使用して、Azure Container Instances でのコンテナーのデプロイを作成および管理するタスクとワークフローを自動化します
services: logic-apps, container-instances
ms.service: logic-apps
ms.suite: integration
author: dlepow
ms.author: danlep
ms.manager: gwallace
ms.reviewer: estfan, macolso
ms.topic: article
tags: connectors
ms.date: 01/14/2020
ms.openlocfilehash: ecb1049d64197f2a60438df7eedfb244907f7327
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "76046205"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して Azure Container Instances をデプロイおよび管理する

Azure Logic Apps と Azure Container Instance コネクタを使用すると、[コンテナー グループ](../container-instances/container-instances-container-groups.md)をデプロイして管理するタスクとワークフローを自動化できます。 コンテナー インスタンス コネクタでは、次のアクションがサポートされています。

* コンテナー グループを作成または削除する
* コンテナー グループのプロパティを取得する
* コンテナー グループの一覧を取得する
* コンテナー インスタンスのログを取得する

Logic Apps トリガーに応答してコンテナー ワークロードを実行する、といったタスクに対し、ロジック アプリでこれらのアクションを使用します。 また、コンテナー インスタンス アクションからの出力を、他のアクションで使用することもできます。 

このコネクタではアクションのみが提供されます。そのため、ロジック アプリを開始するには、定期的にコンテナー ワークロードを実行するための **繰り返し** トリガーなど、別のトリガーを使用します。 または、Outlook 電子メールの着信などのイベントの後で、コンテナー グループのデプロイをトリガーすることが必要になる場合があります。 

ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* [ロジック アプリを作成する方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)および[コンテナー インスタンスを作成して管理する方法](../container-instances/container-instances-quickstart.md)に関する基本的な知識

* コンテナー インスタンスにアクセスするロジック アプリ。 アクションを使用するには、**繰り返し** トリガーなどの別のトリガーでロジック アプリを開始します。

## <a name="add-a-container-instance-action"></a>コンテナー インスタンス アクションを追加する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. 以下からパスを選択します。 

   * アクションを追加する最後のステップの下で、 **[新しいステップ]** を選択します。 

     または

   * アクションを追加するステップの間で、ステップ間の矢印の上にポインターを移動します。 
   表示されるプラス記号 ( **+** ) を選択し、 **[アクションの追加]** を選択します。

1. 検索ボックスに、フィルターとして「コンテナー インスタンス」と入力します。 アクションの一覧で、必要な Azure Container Instance コネクタ アクションを選択します。

1. 接続の名前を指定します。 

1. 選択したアクションのために必要な詳細を指定し、ロジック アプリのワークフローの構築を続けます。

  たとえば、次の図に示すように (部分的な詳細)、**[Create container group]\(コンテナー グループの作成\)** を選択し、コンテナー グループと、グループ内の 1 つ以上のコンテナー インスタンスのプロパティを入力します。

  ![コンテナー グループを作成する](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの OpenAPI (以前の Swagger) の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、コネクタの[リファレンス ページ](/connectors/aci/)またはコンテナー グループの [YAML リファレンス](../container-instances/container-instances-reference-yaml.md)を参照してください。

## <a name="next-steps"></a>次のステップ

* Azure Container Instances でコンテナーを実行し、電子メールまたは Twitter テキストのセンチメントを分析する[サンプル ロジック アプリ](https://github.com/Azure-Samples/aci-logicapps-integration)を参照してください。

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。