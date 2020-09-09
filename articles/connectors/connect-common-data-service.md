---
title: Common Data Service に接続する
description: Azure Logic Apps を使用して Common Data Service のレコードを作成および管理する
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 05/08/2020
tags: connectors
ms.openlocfilehash: 8cce90a8a65a7f070459e220e6d92ef0be57e909
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284117"
---
# <a name="create-and-manage-records-in-common-data-service-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して Common Data Service のレコードを作成および管理する

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) と [Common Data Service コネクタ](/connectors/commondataservice/)を使用すると、[Common Data Service](/powerapps/maker/common-data-service/data-platform-intro) データベース内のレコードを管理する自動化されたワークフローをビルドすることができます。 これらのワークフローでは、レコードの作成、レコードの更新、その他の操作を行うことができます。 また、Common Data Service データベースから情報を取得して、その出力を、ロジック アプリで使用される他のアクションで使用できるようにします。 たとえば、Common Data Service データベースでレコードが更新された場合、Office 365 Outlook コネクタを使用してメールを送信できます。

この記事では、新しい潜在顧客レコードが作成されるたびにタスク レコードを作成するロジック アプリをビルドする方法について説明します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* [Common Data Service 環境](/power-platform/admin/environments-overview)。組織がビジネス データと Common Data Service データベースを格納、管理、共有する場所です。 詳細については、次のリソースを参照してください。<p>

  * [Learn: Common Data Service の開始方法](/learn/modules/get-started-with-powerapps-common-data-service/)
  * [Power Platform - 環境の概要](/power-platform/admin/environments-overview)

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)と、Common Data Service データベースのレコードにアクセスするロジック アプリに関する基本的な知識。 Common Data Service トリガーを使用してロジック アプリを起動するには、空のロジック アプリが必要です。 Azure Logic Apps を初めて使用する場合は、「[クイックスタート: Azure Logic Apps を使用して初めてのワークフローを作成する](../logic-apps/quickstart-create-first-logic-app-workflow.md)」を参照してください。

## <a name="add-common-data-service-trigger"></a>Common Data Service トリガーを追加する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

この例では、新しいレコードが作成されたときに起動する Common Data Service トリガーを追加します。

1. [Azure Portal](https://portal.azure.com) において、ロジック アプリ デザイナーで空のロジック アプリを開きます (まだ開いていない場合)。

1. 検索ボックスに「 `common data service`」と入力します。 この例では、トリガーの一覧で、次のトリガーを選択します: **レコードが作成されたとき**

   ![[レコードが作成されたとき] トリガーを選択する](./media/connect-common-data-service/select-when-record-created-trigger.png)

1. メッセージが表示されたら、Common Data Service にサインインします。

1. トリガーで、新しい "潜在顧客" レコードを監視する環境に関する情報を指定します。次に例を示します。

   ![監視する環境のトリガー情報](./media/connect-common-data-service/when-record-created-trigger-details.png)

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **Environment** | はい | 監視する環境 (例: "Fabrikam Sales Production")。 詳細については、「[Power Platform - 環境の概要](/power-platform/admin/environments-overview)」を参照してください。 |
   | **エンティティ名** | はい | 監視するエンティティ (例: "潜在顧客") |
   | **スコープ** | はい | 新しいレコードを作成したソース。たとえば、部署内のユーザーや、組織内のユーザー。 この例では、"部署" を使用しています。 |
   ||||

## <a name="add-common-data-service-action"></a>Common Data Service アクションを追加する

次に、新しい "潜在顧客" レコードのタスク レコードを作成する Common Data Service アクションを追加します。

1. **[レコードが作成されたとき]** トリガーで、 **[新しいステップ]** を選択します。

1. 検索ボックスに「 `common data service`」と入力します。 アクションの一覧から、次のアクションを選択します。**新しいレコードを作成する**

   ![[新しいレコードを作成する] アクションを選択する](./media/connect-common-data-service/select-create-new-record-action.png)

1. アクションで、新しいタスク レコードを作成する環境に関する情報を指定します。 使用可能な場合は、このアクションについて選択したエンティティに基づいて、他のプロパティも表示されます。次に例を示します。

   ![レコードを作成する環境に関するアクション情報](./media/connect-common-data-service/create-new-record-action-details.png)

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **組織名** | はい | レコードを作成する環境。これは、トリガーで指定した環境と同じにする必要はありませんが、この例では、"Fabrikam Sales Production" を指定しています。 |
   | **エンティティ名** | はい | レコードを作成しようとするエンティティ (たとえば "Tasks") |
   | **件名** | はい (この例では選択したエンティティに基づきます) | このタスクの目的に関する簡単な説明 |
   ||||

   1. **[件名]** プロパティで、末尾にスペースを付けて次のテキストを入力します。

      `Follow up with new lead:`

   1. 動的なコンテンツ リストが表示されたままになるように、ポインターを **[件名]** ボックスに置いたままにしておきます。
   
   1. 一覧で、 **[レコードが作成されたとき]** セクションから、タスク レコードに含めるトリガーの出力を選択します。次に例を示します。

      ![タスク レコードで使用するトリガーの出力を選択する](./media/connect-common-data-service/create-new-record-action-select-trigger-outputs.png)

      | トリガーの出力 | 説明 |
      |----------------|-------------|
      | **[First Name]** | タスク レコードで主要な連絡先として使用する潜在顧客レコードの名 |
      | **[Last Name]** | タスク レコードで主要な連絡先として使用する潜在顧客レコードの姓 |
      | **説明** | タスク レコードに含めるその他の出力。たとえば、メール アドレスや勤務先の電話番号など |
      |||

   作業が完了すると、アクションは次の例のようになります。

   ![[新しいレコードを作成する] アクションの完了](./media/connect-common-data-service/finished-create-record-action-details.png)

1. ロジック アプリを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

1. ロジック アプリを手動で起動するには、デザイナーのツール バーで **[実行]** を選択します。 ロジック アプリをテストするには、新しい "潜在顧客" レコードを作成します。

## <a name="trigger-only-on-updated-attributes"></a>更新された属性に対してのみトリガーする

**[レコードが更新されたとき]** アクションなど、レコードが更新されたときに実行されるトリガーの場合、フィルター属性を使用して、指定した属性が更新されたときにのみロジック アプリが実行されるようにすることができます。 この機能は、ロジック アプリの不要な実行を防止するのに役立ちます。

1. トリガーで、 **[新しいパラメーターの追加]** の一覧から、 **[属性フィルター]** を選択します。

   ![[属性フィルター] プロパティを追加する](./media/connect-common-data-service/when-record-updated-trigger-add-attribute-filters.png)

1. **[Attribute Filters Item]\(属性フィルター項目\)** ごとに、更新を監視する属性を選択します。次に例を示します。

   ![[属性フィルター] プロパティを追加する](./media/connect-common-data-service/when-record-updated-trigger-select-attribute-filter.png)

## <a name="list-records-based-on-a-filter"></a>フィルターに基づいてレコードを一覧表示する

**[レコードの一覧表示]** など、レコードを返すアクションの場合、指定されたフィルターに基づいてレコードを返す ODATA クエリを使用できます。 たとえば、アクティブなアカウントのレコードのみを一覧表示するようにアクションを設定できます。

1. アクションで、 **[新しいパラメーターの追加]** の一覧を開いて、 **[フィルター クエリ]** プロパティを選択します。

   ![[フィルター クエリ] プロパティを追加する](./media/connect-common-data-service/list-records-action-filter-query.png)

1. アクションに表示されたばかりの **[フィルター クエリ]** プロパティで、ODATA フィルター クエリ `statuscode eq 1` を入力します。

   ![レコードをフィルター処理するための ODATA フィルター クエリを入力する](./media/connect-common-data-service/list-records-action-filter-query-value.png)

`$filter` システム クエリ オプションの詳細については、「[Common Data Service - 結果のフィルター](/powerapps/developer/common-data-service/webapi/query-data-web-api#filter-results)」を参照してください。

## <a name="list-records-based-on-an-order"></a>順序に基づいてレコードを一覧表示する

**[レコードの一覧表示]** など、レコードを返すアクションの場合、指定された順序に基づいてレコードを返す ODATA クエリを使用できます。順序は、アクションが返すレコードによって異なります。 たとえば、アカウント名別にレコードを一覧表示するようにアクションを設定できます。

1. アクションで、 **[新しいパラメーターの追加]** の一覧を開いて、 **[並べ替え順]** プロパティを選択します。

   ![[並べ替え順] プロパティを追加する](./media/connect-common-data-service/list-records-action-order-by.png)

1. アクションに表示されたばかりの **[並べ替え順]** プロパティで、ODATA フィルター クエリ `name` を入力します。

   ![レコードを並べ替えるための ODATA フィルター クエリを入力する](./media/connect-common-data-service/list-records-action-order-by-value.png)

`$orderby` システム クエリ オプションの詳細については、「[Common Data Service - 結果の並べ替え](/powerapps/developer/common-data-service/webapi/query-data-web-api#order-results)」を参照してください。

## <a name="field-data-types"></a>フィールドのデータ型

トリガーまたはアクションのフィールドに値を手動で入力するか、動的なコンテンツ リストから選択するかに関係なく、値のデータ型は、フィールドに必須のデータ型と一致する必要があります。

次の表では、一部のフィールドの種類と、そのフィールドの値に必須の型について説明します。

| フィールド | データ型 | 説明 |
|-------|-----------|-------------|
| テキスト フィールド | 1 行のテキスト | 1 行のテキスト、またはテキスト データ型を含む動的コンテンツのいずれかが必要です。たとえば、次のプロパティがこれに該当します。 <p><p>- **説明** <br>- **カテゴリ** |
| 整数フィールド | 整数 | 整数、または整数データ型を含む動的コンテンツのいずれかが必要です。たとえば、次のプロパティがこれに該当します。 <p><p>- **達成率** <br>- **期間** |
| 日付フィールド | 日時 | MM/DD/YYY 形式の日付、または日付データ型を含む動的コンテンツのいずれかが必要です。たとえば、次のプロパティがこれに該当します。 <p><p>- **作成日** <br>- **開始日** <br>- **実際の開始日** <br>- **実際の終了日** <br>- **期限** |
| 別のエンティティ レコードを参照するフィールド | Primary key (プライマリ キー) | GUID などのレコード ID と参照型の両方が必要です。つまり、動的コンテンツ リストの値は機能しません。たとえば、次のプロパティがこれに該当します。 <p><p>- **所有者**: 有効なユーザー ID またはチーム レコード ID である必要があります。 <br>- **所有者の種類**: 参照型である必要があります。たとえば、それぞれ、`systemusers`、`teams` となります。 <p><p>- **関連**: アカウント ID や連絡先レコード ID などの有効なレコード ID である必要があります。 <br>- **関連の種類**: 参照型である必要があります。たとえば、それぞれ、`accounts`、`contacts` となります。 <p><p>- **顧客**: アカウント ID や連絡先レコード ID などの有効なレコード ID である必要があります。 <br>- **顧客の種類**: 参照型である必要があります。たとえば、それぞれ、`accounts`、`contacts` となります。 |
||||

次の例では、 **[新しいレコードを作成する]** アクションで、他のエンティティ レコード (具体的にはユーザー レコードとアカウント レコード) に関連付けられる新しい "タスク" レコードを作成する方法を示します。 このアクションは、関連するプロパティの必要なデータ型と一致する値を使用して、エンティティ レコードの ID と参照型を指定します。

* アクションでは、ユーザー ID を指定する **[所有者]** プロパティと、`systemusers` 参照型を指定する **[所有者の種類]** に基づいて、新しい "タスク" レコードを特定のユーザーに関連付けます。

* アクションでは、レコード ID を指定する **[関連]** プロパティと、`accounts` 参照型を指定する **[関連の種類]** プロパティに基づいて、新しい "タスク" レコードを特定のアカウントに関連付けます。

![ID と参照型に関連付けられる "タスク" レコードを作成する](./media/connect-common-data-service/create-new-record-task-properties.png)

## <a name="connector-reference"></a>コネクタのレファレンス

トリガー、アクション、制限、その他の詳細など、コネクタの Swagger の説明に基づいた技術情報については、[コネクタのリファレンス ページ](/connectors/commondataservice/)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps の他のコネクタ](../connectors/apis-list.md)の詳細情報
