---
title: B2B エンタープライズ統合用の XML を検証する
description: Enterprise Integration Pack を備えた Azure Logic Apps でスキーマを使用して XML を検証する
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74792169"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Enterprise Integration Pack が適用された Azure Logic Apps で B2B エンタープライズ統合用の XML を検証する

多くの場合、B2B シナリオでは、契約の取引先は、データ処理を開始する前に、交換するメッセージが有効であることを確認する必要があります。 定義済みのスキーマに対してドキュメントを検証するには、Enterprise Integration Pack で使用できる XML 検証アクションを使用します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをまだお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* XML 検証アクションを使用する空または既存のロジック アプリ。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」と[クイック スタートの初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

* Azure サブスクリプションに関連付けられ、XML 検証アクションを使用する予定のロジック アプリにリンクされ、XML コンテンツの検証に使用するスキーマが含まれている[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 使用するロジックアプリと統合アカウントは両方とも同じ場所または同じ Azure リージョンに存在する必要があります。

## <a name="add-xml-validation-action"></a>XML 検証アクションを追加する

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. 空のロジック アプリがある場合は、ロジック アプリ デザイナーの検索ボックスにフィルターとして「`HTTP request`」と入力し、 **[HTTP 要求の受信時]** を選択します。 使用する場合は、次の手順に進みます。

1. ワークフローの最後のステップで、 **[新しいステップ]** を選択します。

   既存のステップの間にアクションを追加するには、それらのステップを接続する矢印にポインターを移動します。すると、プラス記号 ( **+** ) が表示されます。 プラス記号を選択し、 **[アクションの追加]** を選択します。

1. **[アクションを選択してください]** で **[Built-in]\(組み込み\)** を選択します。 検索ボックスに、フィルターとして「`xml validation`」と入力します。 アクション リストから **[XML の検証]** を選択します。

   ![[XML の検証] アクションを見つけて選択します。](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. 検証する XML コンテンツを指定するには、 **[コンテンツ]** ボックス内をクリックします。すると、動的コンテンツ リストが表示されます。

   ![動的コンテンツ リストを開く](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   動的コンテンツ リストには、ワークフローの前のステップの出力を表すプロパティ トークンが表示されます。 リストに想定のプロパティが表示されない場合は、 **[詳細表示]** を選択できるかどうかのトリガーまたはアクションの見出しを確認します。

1. 動的コンテンツ リストから、検証するコンテンツが含まれているプロパティを選択します。

   この例では、トリガーから **[本文]** の出力を選択します。

   ![検証するコンテンツを選択する](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. 検証に使用するスキーマを指定するには、 **[スキーマ名]** リストを開き、リンクされた統合アカウントに追加した検証スキーマを選択します。

   ![検証に使用するスキーマを選択します](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. ロジック アプリを保存します。

   これで検証の設定は完了です。 実際のアプリでは、検証したデータを Salesforce などの業務 (LOB) アプリケーションに保存する必要がある場合があります。 検証済みの出力を Salesforce に送信するには、アクションを追加します。

1. 検証アクションをテストするには、ロジック アプリのワークフローをトリガーする要求を送信します。

## <a name="next-steps"></a>次のステップ

* [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) について詳細を確認する