---
title: フラット ファイルのエンコードまたはデコード
description: Enterprise Integration Pack を使用して、Azure Logic Apps でエンタープライズ統合用のフラット ファイルをエンコードまたはデコードする
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 05/09/2020
ms.openlocfilehash: aebce8f284ed4bb21d99efffc8dd6d0c51b39533
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87001487"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps-by-using-the-enterprise-integration-pack"></a>Enterprise Integration Pack を使用して、Azure Logic Apps でフラット ファイルをエンコードまたはデコードする

企業間 (B2B) シナリオでビジネス パートナーに XML コンテンツを送信する前に、そのコンテンツをあらかじめエンコードすることが必要になる場合があります。 ロジック アプリをビルドすることにより、**フラット ファイル** コネクタを使用してフラット ファイルをエンコードおよびデコードできます。 ロジック アプリは、要求トリガー、別のアプリ、または[Azure Logic Apps でサポートされる他のコネクタ](../connectors/apis-list.md)など、さまざまなソースからこの XML コンテンツを取得できます。 詳細については、「[Azure Logic Apps とは](logic-apps-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* **フラット ファイル** コネクタを使用するロジック アプリと、ロジック アプリのワークフローを開始するトリガー。 **フラット ファイル** コネクタでは、アクションのみが提供され、トリガーは提供されません。 トリガーまたは別のアクションを使用して、エンコードまたはデコードのために XML コンテンツをロジック アプリにフィードできます。 ロジック アプリを初めて使用する場合は、[クイック スタート:初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

* ご使用の Azure サブスクリプションに関連付けられており、**フラット ファイル** コネクタの使用を計画している [ロジック アプリにリンクされている](./logic-apps-enterprise-integration-create-integration-account.md#link-account)[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 使用するロジックアプリと統合アカウントは両方とも同じ場所または同じ Azure リージョンに存在する必要があります。

* XML コンテンツをエンコードまたはデコードするために統合アカウントにアップロードしたフラット ファイル [スキーマ](logic-apps-enterprise-integration-schemas.md)

* ご使用の統合アカウント内で既に定義されている少なくとも 2 つの[取引先](logic-apps-enterprise-integration-partners.md)

## <a name="add-flat-file-encode-action"></a>フラット ファイルのエンコード アクションを追加する

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. ロジック アプリのトリガーまたはアクションで、 **[新しいステップ]**  >  **[アクションの追加]** を選択します。 この例では、要求トリガーを使用します。これは、 **[HTTP 要求の受信時]** という名前で、ロジック アプリの外部からの受信要求を処理します。

   > [!TIP]
   > JSON スキーマの指定は省略可能です。 受信要求からのサンプルのペイロードがある場合は、 **[サンプルのペイロードを使用してスキーマを生成する]** を選択し、サンプルのペイロードを入力して、 **[完了]** を選択します。 **[要求本文の JSON スキーマ]** ボックスにスキーマが表示されます。

1. **[アクションを選択してください]** で、「`flat file`」と入力します。 アクションの一覧から、次のアクションを選択します。 **[フラット ファイルのエンコード]**

   ![[フラット ファイルのエンコード] アクションを選択する](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-encoding.png)

1. **[コンテンツ]** ボックス内をクリックして、動的コンテンツ リストを表示します。 一覧から、 **[HTTP 要求の受信時]** セクションで、 **[本文]** プロパティを選択します。これには、トリガーからの要求本文の出力とエンコードするコンテンツが含まれます。

   ![動的コンテンツ リストからエンコードするコンテンツを選択する](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode.png)

   > [!TIP]
   > 動的コンテンツ リストに **[本文]** プロパティが表示されない場合は、 **[HTTP 要求の受信時]** セクションのラベルの横にある **[表示数を増やす]** を選択します。
   > デコードするコンテンツを **[コンテンツ]** ボックスに直接入力することもできます。

1. **[スキーマ名]** の一覧から、エンコードに使用するリンク済みの統合アカウント内にあるスキーマを選択します。

   ![エンコードに使用するスキーマを選択する](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-encoding.png)

   > [!NOTE]
   > 一覧にスキーマが表示されない場合は、ご使用の統合アカウントに、エンコードに使用するスキーマ ファイルが含まれていません。 使用するスキーマをご使用の統合アカウントにアップロードしてください。

1. ロジック アプリを保存します。 コネクタをテストするには、HTTPS エンドポイントへの要求を作成します。これは、要求トリガーの **[HTTP POST の URL]** プロパティに表示され、エンコードする XML コンテンツを要求の本文に含めます。

以上で、フラット ファイルのエンコード アクションの設定が完了しました。 実際のアプリでは、エンコードしたデータを Salesforce などの基幹業務 (LOB) アプリに保存することが必要になる場合があります。 または、そのエンコードしたデータを取引先に送信することもできます。 エンコード アクションの出力を Salesforce または取引先に送信するには、[Azure Logic Apps で使用可能な他のコネクタ](../connectors/apis-list.md)を使用します。

## <a name="add-flat-file-decode-action"></a>フラット ファイルのデコード アクションを追加する

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. ロジック アプリのトリガーまたはアクションで、 **[新しいステップ]**  >  **[アクションの追加]** を選択します。 この例では、要求トリガーを使用します。これは、 **[HTTP 要求の受信時]** という名前で、ロジック アプリの外部からの受信要求を処理します。

   > [!TIP]
   > JSON スキーマの指定は省略可能です。 受信要求からのサンプルのペイロードがある場合は、 **[サンプルのペイロードを使用してスキーマを生成する]** を選択し、サンプルのペイロードを入力して、 **[完了]** を選択します。 **[要求本文の JSON スキーマ]** ボックスにスキーマが表示されます。

1. **[アクションを選択してください]** で、「`flat file`」と入力します。 アクションの一覧から、次のアクションを選択します。 **[フラット ファイルのデコード]**

   ![[フラット ファイルのデコード] アクションを選択する](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-decoding.png)

1. **[コンテンツ]** ボックス内をクリックして、動的コンテンツ リストを表示します。 一覧から、 **[HTTP 要求の受信時]** セクションで、 **[本文]** プロパティを選択します。これには、トリガーからの要求本文の出力とデコードするコンテンツが含まれます。

   ![動的コンテンツ リストからデコードするコンテンツを選択する](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode.png)

   > [!TIP]
   > 動的コンテンツ リストに **[本文]** プロパティが表示されない場合は、 **[HTTP 要求の受信時]** セクションのラベルの横にある **[表示数を増やす]** を選択します。 デコードするコンテンツを **[コンテンツ]** ボックスに直接入力することもできます。

1. **[スキーマ名]** の一覧から、デコードに使用するリンク済みの統合アカウント内にあるスキーマを選択します。

   ![デコードに使用するスキーマを選択する](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-decoding.png)

   > [!NOTE]
   > 一覧にスキーマが表示されない場合は、ご使用の統合アカウントに、デコードに使用するスキーマ ファイルが含まれていません。 使用するスキーマをご使用の統合アカウントにアップロードしてください。

1. ロジック アプリを保存します。 コネクタをテストするには、HTTPS エンドポイントへの要求を作成します。これは、要求トリガーの **[HTTP POST の URL]** プロパティに表示され、デコードする XML コンテンツを要求の本文に含めます。

以上で、フラット ファイルのデコード アクションの設定が完了しました。 実際のアプリでは、デコードしたデータを Salesforce などの基幹業務 (LOB) アプリに保存することが必要になる場合があります。 または、そのデコードしたデータを取引先に送信することもできます。 デコード アクションの出力を Salesforce または取引先に送信するには、[Azure Logic Apps で使用可能な他のコネクタ](../connectors/apis-list.md)を使用します。

## <a name="next-steps"></a>次のステップ

* [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) について詳細を確認する
