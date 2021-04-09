---
title: 統合アカウント アーティファクト メタデータを管理する
description: Enterprise Integration Pack を使用して Azure Logic Apps の統合アカウントからアーティファクト メタデータを追加または取得する
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/17/2019
ms.openlocfilehash: bc119f1ce8efb821781dabfb9dd259cc5c8d9c23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "74792475"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Azure Logic Apps と Enterprise Integration Pack を使用して統合アカウントからアーティファクト メタデータを管理する

統合アカウント内でアーティファクトのカスタム メタデータを定義し、ロジック アプリを使用する目的で実行時にそのメタデータを取得できます。 たとえば、パートナー、契約、スキーマ、マップなどのアーティファクトに対してメタデータを指定できます。これらすべてのアーティファクトには、キーと値のペアを使用してメタデータが保存されます。 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合には、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。

* メタデータを追加するアーティファクトが与えられている基本[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。例: 

  * [パートナー](logic-apps-enterprise-integration-partners.md)
  * [契約](logic-apps-enterprise-integration-agreements.md)
  * [スキーマ](logic-apps-enterprise-integration-schemas.md)
  * [Map](logic-apps-enterprise-integration-maps.md)

* 統合アカウントと使用するアーティファクト メタデータにリンクされているロジック アプリ。 ロジック アプリがまだリンクされていない場合、[統合アカウントにロジック アプリをリンクする方法](logic-apps-enterprise-integration-create-integration-account.md#link-account)を確認してください。 

  ロジック アプリを用意していない場合は、[ロジック アプリを作成する方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)を確認してください。 
  アーティファクト メタデータの管理に使用するトリガーとアクションを追加します。 あるいは、試用目的で、**要求** や **HTTP** などのトリガーをロジック アプリに追加します。

## <a name="add-metadata-to-artifacts"></a>アーティファクトにメタデータを追加する

1. Azure アカウントの資格情報で <a href="https://portal.azure.com" target="_blank">Azure Portal</a> にサインインします。 統合アカウントを見つけて開きます。

1. メタデータを追加するアーティファクトを選択し、**[編集]** を選択します。 下の画像のように、そのアーティファクトのメタデータ詳細を入力します。

   ![メタデータを入力する](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. 完了したら、 **[OK]** を選びます。

1. 統合アカウントの JavaScript Object Notation (JSON) 定義でこのメタデータを表示するには、**[JSON として編集]** を選択し、JSON エディターを開きます。 

   ![パートナー メタデータの JSON](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>アーティファクト メタデータを取得する

1. Azure portal で、目的の統合アカウントにリンクされているロジック アプリを開きます。 

1. ロジック アプリ デザイナーで、ワークフローのトリガーまたは最後のアクションの下にメタデータを取得する手順を追加する場合、 **[新しいステップ]** 、 **[アクションの追加]** の順に選択します。 

1. 検索ボックスに「integration account」と入力します。 検索ボックスで、 **[すべて]** を選択します。 アクションの一覧から **[統合アカウントのアーティファクトの検索 - 統合アカウント]** アクションを選択します。

   ![[統合アカウントのアーティファクトの検索] を選択する](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. 検索するアーティファクトに関する情報を指定します。

   | プロパティ | 必須 | 値 | 説明 | 
   |----------|---------|-------|-------------| 
   | **アーティファクトの種類** | はい | **スキーマ**、**マップ**、**パートナー**、**契約**、カスタム型 | 目的のアーティファクトの種類 | 
   | **アーティファクトの名前** | はい | <*artifact-name*> | 目的のアーティファクトの名前 | 
   ||| 

   たとえば、取引先アーティファクトのメタデータを取得するとします。

   ![アーティファクトの種類を選択し、アーティファクトの名前を入力する](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. そのメタデータを処理するためのアクションを追加します。例:

   1. **[統合アカウントのアーティファクトの検索]** で **[次のステップ]** を選択し、**[アクションの追加]** を選択します。 

   1. 検索ボックスに「http」と入力します。 検索ボックスの下で **[ビルトイン]** を選択し、 **[HTTP - HTTP]** アクションを選択します。

      ![HTTP アクションを追加する](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. 管理するアーティファクト メタデータに関する情報を指定します。 

      たとえば、このトピックで前に追加した `routingUrl` メタデータを取得するとします。 次のようなプロパティ値を指定します。 

      | プロパティ | 必須 | 値 | 説明 | 
      |----------|----------|-------|-------------| 
      | **方法** | はい | <*operation-to-run*> | アーティファクトで実行する HTTP 操作。 たとえば、この HTTP アクションでは **GET** メソッドが使用されます。 | 
      | **URI** | はい | <*metadata-location*> | 取得したアーティファクトから `routingUrl` メタデータ値にアクセスするには、下のような式を使用できます。 <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **ヘッダー** | いいえ | <*header-values*> | HTTP アクションに渡すトリガーからのヘッダー出力。 たとえば、トリガーの `headers` プロパティ値を渡すには、次のような式を使用できます。 <p>`@triggeroutputs()['headers']` | 
      | **本文** | いいえ | <*body-content*> | HTTP アクションの `body` プロパティで渡すその他のコンテンツ。 この例では、アーティファクトの `properties` 値が HTTP アクションに渡されます。 <p>1. **[本文]** プロパティ内をクリックし、動的コンテンツ リストを表示します。 プロパティが表示されていない場合、**[さらに表示する]** を選択します。 <br>2.動的コンテンツ リストの **[統合アカウントのアーティファクトの検索]** で **[プロパティ]** を選択します。 | 
      |||| 

      次に例を示します。

      ![HTTP アクションの値と式を指定する](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. HTTP アクションに指定した情報を確認するには、ロジック アプリの JSON 定義を表示します。 ロジック アプリ デザイナーのツール バーで **[コード ビュー]** を選択し、次のようにアプリの JSON 定義を表示します。

      ![ロジック アプリの JSON 定義](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      ロジック アプリ デザイナーに切り替えると、次の画像のように、使用した式が解決された状態で表示されます。

      ![ロジック アプリ デザイナーの解決された式](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>次のステップ

* [契約の詳細について学習する](logic-apps-enterprise-integration-agreements.md)
