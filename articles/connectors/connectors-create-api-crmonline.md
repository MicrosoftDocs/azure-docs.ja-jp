---
title: Dynamics 365 に接続する - Azure Logic Apps
description: Dynamics 365 (オンライン) REST API と Azure Logic Apps を使用してレコードを作成および管理します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
manager: carmonm
ms.reviewer: estfan, LADocs
ms.topic: conceptual
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: ce83e6b1847a8f08467cb7877e517bdaace27953
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051013"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Azure Logic Apps を使用して Dynamics 365 レコードを管理する

Azure Logic Apps および Dynamics 365 コネクタを使用すると、Dynamics 365 ｎレコードに基づいて、自動化されたタスクとワークフローを作成できます。 ワークフローによって、Dynamics 365 アカウントでレコードの作成や項目の更新を行ったり、レコードを返したりすることができます。 Dynamics 365 からの応答を取得して、出力を他のアクションで使用できるようにするアクションをロジック アプリに組み込むことができます。 たとえば、Dynamics 365 で項目が更新されときに、Office 365 を使用して電子メールを送信できます。

この記事では、Dynamics 365 で新しい潜在顧客レコードが作成されるたびに、Dynamics 365 でタスクを作成するロジック アプリを構築する方法について説明します。
ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* [Dynamics 365 アカウント](https://dynamics.microsoft.com)

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* Dynamics 365 アカウントにアクセスするためのロジック アプリ。 Dynamics 365 トリガーを使用してロジック アプリを起動するには、[空のロジック アプリ](../logic-apps/quickstart-create-first-logic-app-workflow.md)が必要です。

## <a name="add-dynamics-365-trigger"></a>Dynamics 365 トリガーを追加する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

新しい潜在顧客レコードが Dynamics 365 に出現すると起動される Dynamics 365 トリガーを追加します。

1. [Azure Portal](https://portal.azure.com) において、ロジック アプリ デザイナーで空のロジック アプリを開きます (まだ開いていない場合)。

1. 検索ボックスに、フィルターとして「Dynamics 365」と入力します。 この例では、トリガーの一覧で、次のトリガーを選択します: **レコードが作成されたとき**

   ![トリガーの選択](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Dynamics 365 へのサインインを求められたら、すぐにサインインします。

1. 次に示すトリガーの詳細を指定します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **組織名** | はい | 監視対象の組織の Dynamics 365 インスタンス名 (たとえば "Contoso") |
   | **エンティティ名** | はい | 監視対象のエンティティの名前 (たとえば "Leads") | 
   | **頻度** | はい | トリガーに関連する更新を確認する間隔として使用する時間単位 |
   | **間隔** | はい | 次の確認までの時間 (秒、分、時間、日、週、または月) |
   ||| 

   ![トリガーの詳細](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Dynamics 365 アクションを追加する

ここで、新しい潜在顧客レコードのタスク レコードを作成する Dynamics 365 アクションを追加します。

1. トリガーで、 **[新しいステップ]** を選択します。

1. 検索ボックスに、フィルターとして「Dynamics 365」と入力します。 アクションの一覧から、次のアクションを選択します。**新しいレコードを作成する**

   ![選択アクション](./media/connectors-create-api-crmonline/select-action.png)

1. 次に示すアクションの詳細を指定します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **組織名** | はい | レコードを作成しようとする Dynamics 365 インスタンス (トリガーのインスタンスと同じである必要はありませんが、この例では "Contoso" です) |
   | **エンティティ名** | はい | レコードを作成しようとするエンティティ (たとえば "Tasks") |
   | | |

   ![アクションの詳細](./media/connectors-create-api-crmonline/action-details.png)

1. **[件名]** ボックスがアクションに表示されたら、 **[件名]** ボックスの内側をクリックして動的コンテンツ リストを表示します。 この一覧から、新しい潜在顧客レコードに関連するタスク レコードに含めるフィールド値を選択します。

   | フィールド | 説明 |
   |-------|-------------|
   | **[姓]** | レコードの潜在顧客の主要連絡先の姓 |
   | **トピック** | レコードの潜在顧客の説明的な名前 |
   | | |

   ![タスク レコードの詳細](./media/connectors-create-api-crmonline/create-record-details.png)

1. デザイナーのツール バーで、ロジック アプリの **[保存]** を選択します。 

1. ロジック アプリを手動で開始するには、デザイナーのツール バーで **[実行]** を選択します。

   ![ロジック アプリを実行する](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. これで Dynamics 365 に潜在顧客レコードが作成されました。ロジック アプリのワークフローをトリガーできます。

## <a name="add-filter-or-query"></a>フィルターまたはクエリを追加する

Dynamics 365 アクションでのデータのフィルター処理方法を指定するには、そのアクションで **[詳細オプションを表示する]** を選択します。 フィルターを追加するか、クエリを使用して並べ替えることができます。
たとえば、フィルター クエリを使用してアクティブなアカウントだけを取得し、それらのレコードをアカウント名で並べ替えることができます。 このタスクを実行するには、次の手順に従います。

1. **[フィルター クエリ]** で、OData フィルター クエリ `statuscode eq 1` を入力します。

2. **[並べ替え順]** で、動的コンテンツ リストが表示されたら **[アカウント名]** を選択します。 

   ![フィルターと並べ替えを指定する](./media/connectors-create-api-crmonline/advanced-options.png)

詳しくは、次の Dynamics 365 Customer Engagement Web API システム クエリ オプションをご覧ください。

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>詳細オプションのためのベスト プラクティス

アクションまたはトリガーのフィールドに値を指定する場合は、手動で値を入力するときも、動的コンテンツ リストから値を選択するときも、値のデータ型がフィールドの種類と一致する必要があります。

次の表に、フィールドの種類の一部と、その値の必須データ型を示します。

| フィールドの種類 | 必須のデータ型 | 説明 | 
|------------|--------------------|-------------|
| テキスト フィールド | 1 行のテキスト | これらのフィールドでは、1 行のテキスト、またはテキスト型の動的コンテンツが必要です。 <p><p>"*フィールドの例*": **[説明]** や **[カテゴリ]** | 
| 整数フィールド | 整数 | 一部のフィールドでは、整数、または整数型の動的コンテンツが必要です。 <p><p>"*フィールドの例*": **[達成率]** や **[期間]** | 
| 日付フィールド | 日付と時刻 | 一部のフィールドでは、/mm/dd/yyyy 形式の日付、または date 型の動的コンテンツが必要です。 <p><p>"*フィールドの例*": **[作成日]** 、 **[開始日]** 、 **[実際の開始日]** 、 **[実際の終了日]** 、 **[期限]** | 
| レコード ID と参照タイプの両方を必要とするフィールド | Primary key (プライマリ キー) | 別のエンティティ レコードを参照する一部のフィールドでは、レコード ID と参照タイプの両方が必要です。 | 
||||

このようなフィールドの種類を詳しく説明するため、レコード ID と参照タイプの両方が必要になる Dynamics 365 のトリガーとアクションのフィールドの例を次に示します。 この要件は、動的リストから選択する値が機能しないことを意味します。

| フィールド | 説明 |
|-------|-------------|
| **Owner** | 有効なユーザー ID またはチーム レコード ID にする必要があります。 |
| **[所有者の種類]** | `systemusers` または `teams` のいずれかである必要があります。 |
| **[関連]** | 有効なレコード ID (アカウント ID や連絡先レコード ID など) にする必要があります。 |
| **[関連の種類]** | 参照型 (`accounts` や `contacts` など) である必要があります。 |
| **お客様** | 有効なレコード ID (アカウント ID や連絡先レコード ID など) にする必要があります。 |
| **[顧客の種類]** | 参照型 (`accounts` や `contacts` など) である必要があります。 |
|||

この例では、 **[新しいレコードの作成]** アクションによって新しいタスク レコードが作成されます。

![レコード ID と参照タイプを使用してタスク レコードを作成する](./media/connectors-create-api-crmonline/create-record-advanced.png)

このアクションは、 **[所有者]** フィールドのレコード ID と **[所有者の種類]** フィールドの参照タイプに基づいて、特定のユーザー ID またはチーム レコード ID にタスク レコードを割り当てます。

![所有者のレコード ID と参照タイプ](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

また、このアクションでは、 **[関連]** フィールドに追加されたレコード ID と、 **[関連の種類]** フィールドの参照タイプに関連するアカウント レコードも追加されます。

![[関連] のレコード ID と参照タイプ](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>レコード ID を検索する

レコード ID を検索するには、次の手順に従います。

1. Dynamics 365 でレコード (アカウント レコードなど) を開きます。

2. アクション ツール バーで、次の手順のいずれかを選択します。

   * **[ポップ アウト]** ![レコードのポップアウト](./media/connectors-create-api-crmonline/popout-record.png) を選択します。 
   * 既定の電子メール プログラムに URL 全体をコピーできるように、 **[リンクを電子メールで送信]** を選択します。

   レコード ID は、URL 内の エンコード文字 `%7b` と `%7d` の間にあります。

   ![レコード ID を検索する](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>実行の失敗をトラブルシューティングする

ロジック アプリの失敗したステップを見つけて確認するために、ロジック アプリの実行履歴、状態、入力、出力などを表示できます。

1. Azure Portal の、ロジック アプリのメイン メニューで **[概要]** を選択します。 実行したすべてのロジック アプリの状態が表示される **[実行の履歴]** セクションで、失敗した実行を選択して詳細を確認します。

   ![ロジック アプリの実行状態](./media/connectors-create-api-crmonline/run-history.png)

1. 失敗したステップを展開して詳細を表示します。

   ![失敗したステップの展開](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. ステップの詳細 (入力や出力など) を確認すると、失敗の根本原因を見つけることに役立ちます。

   ![失敗したステップ](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

ロジック アプリのトラブルシューティングの詳細については、「[ロジック アプリの障害の診断](../logic-apps/logic-apps-diagnosing-failures.md)」をご覧ください。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの Open API (以前の Swagger) ファイルによって記述される、トリガー、アクション、制限などの技術的詳細については、[コネクタのリファレンス ページ](/connectors/dynamicscrmonline/)を参照してください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
