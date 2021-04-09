---
title: SQL Server、Azure SQL Database、または Azure SQL Managed Instance に接続する
description: Azure Logic Apps を使用して、オン プレミスまたはクラウド内の SQL データベースに関するタスクを自動化します
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/07/2021
tags: connectors
ms.openlocfilehash: 75c657236b6e06a7e0f6c717d746bcc8c034d423
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98013449"
---
# <a name="automate-workflows-for-a-sql-database-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して SQL データベースのワークフローを自動化する

この記事では、SQL Server コネクタを使用してロジック アプリの中から SQL データベースのデータにアクセスする方法を示します。 ロジック アプリを作成することによって、SQL データとリソースを管理するタスク、プロセス、ワークフローを自動化できます。 SQL Server コネクタは、[SQL Server](/sql/sql-server/sql-server-technical-documentation) だけでなく、[Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) と [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) でも機能します。

SQL データベースや Dynamics CRM Online などの他のシステム内のイベントによってトリガーされたときに実行されるロジック アプリを作成できます。 ロジック アプリは、データの取得、挿入、削除のほか、SQL クエリやストアド プロシージャを実行することもできます。 たとえば、Dynamics CRM Online の新しいレコードを自動的に確認し、新しいレコード用の項目を SQL データベースに追加した後、追加した項目に関する電子メール アラートを送信するロジック アプリをビルドできます。

ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」と[クイック スタートの初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。 コネクタ固有の技術情報、制限事項、既知の問題については、[SQL Server コネクタ リファレンスのページ](/connectors/sql/)をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* [SQL Server データベース](/sql/relational-databases/databases/create-a-database)、[Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md)、または [Azure SQL Managed Instance](../azure-sql/managed-instance/instance-create-quickstart.md)。

  テーブルには、ロジック アプリが操作を呼び出したときに結果を返すことができるように、データが含まれている必要があります。 Azure SQL Database を使用する場合は、用意されているサンプル データベースを使用できます。

* SQL サーバー名、データベース名、ユーザー名およびパスワード。 ロジックを承認して SQL Server にアクセスできるようにするには、これらの資格情報が必要です。

  * オンプレミスの SQL Server の場合、これらの詳細は、接続文字列で確認できます。

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Azure SQL Database の場合、これらの詳細は、接続文字列で確認できます。
  
    たとえば、Azure portal でこの文字列を探すには、データベースを開きます。 データベース メニューで、 **[接続文字列]** または **[プロパティ]** を選択します。

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

<a name="multi-tenant-or-ise"></a>

* ロジック アプリがグローバル、マルチテナント Azure、または[統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) のいずれで実行されるかに基づいて、オンプレミスの SQL Server に接続には次のような他の要件があります。

  * オンプレミスの SQL Server に接続するグローバルなマルチテナント Azure のロジック アプリの場合は、[オンプレミス データ ゲートウェイ](../logic-apps/logic-apps-gateway-install.md)を、ローカル コンピューターと、[Azure に既に作成されているデータ ゲートウェイ リソース](../logic-apps/logic-apps-gateway-connection.md)に、インストールする必要があります。

  * オンプレミスの SQL Server に接続して Windows 認証を使用する ISE 内のロジック アプリの場合、ISE でバージョン管理された SQL Server コネクタでは Windows 認証がサポートされていません。 そのため、データ ゲートウェイと ISE 以外の SQL Server コネクタを使用する必要があります。 その他の認証の種類では、データ ゲートウェイを使用する必要はなく、ISE でバージョン管理されたコネクタを使用できます。

* SQL データベースにアクセスする必要があるロジック アプリ。 SQL トリガーを使用してロジック アプリを起動するには、[空のロジック アプリ](../logic-apps/quickstart-create-first-logic-app-workflow.md)が必要です。

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>データベースに接続する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

次に、以下の手順を続けます。

* [クラウドベースの Azure SQL Database または Managed Instance に接続します](#connect-azure-sql-db)
* [オンプレミスの SQL Server に接続します](#connect-sql-server)

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database-or-managed-instance"></a>Azure SQL Database または Managed Instance に接続する

オンプレミス データ ゲートウェイまたは統合サービス環境を使用せずに Azure SQL Managed Instance にアクセスするには、[Azure SQL Managed Instance にパブリック エンドポイントをセットアップする](../azure-sql/managed-instance/public-endpoint-configure.md)必要があります。 パブリック エンドポイントによってポート 3342 が使用されるので、ロジック アプリからの接続を作成するときに、このポート番号を必ず指定します。


[SQL トリガー](#add-sql-trigger)または [SQL アクション](#add-sql-action)を初めて追加するときに、データベースへの接続をまだ作成していない場合は、次の手順を実行するように求められます。

1. **[認証の種類]** では、Azure SQL Database または Azure SQL Managed Instance のデータベースで必要であり、有効にされている認証を選択します。

   | 認証 | 説明 |
   |----------------|-------------|
   | [**Azure AD 統合**](../azure-sql/database/authentication-aad-overview.md) | - 非 ISE と ISE 両方の SQL Server コネクタをサポートします。 <p><p>- Azure Active Directory (Azure AD) でデータベースへのアクセス権を持つ有効な ID が必要です。 <p>詳細については、以下のトピックを参照してください。 <p>- [Azure SQL セキュリティの概要 - 認証](../azure-sql/database/security-overview.md#authentication) <br>- [Azure SQL へのデータベース アクセスを承認する - 認証と承認](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) <br>- [Azure SQL - Azure AD 統合認証](../azure-sql/database/authentication-aad-overview.md) |
   | [**SQL Server 認証**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | - 非 ISE と ISE 両方の SQL Server コネクタをサポートします。 <p><p>- 作成されてデータベースに格納されている有効なユーザー名と強力なパスワードが必要です。 <p>詳細については、以下のトピックを参照してください。 <p>- [Azure SQL セキュリティの概要 - 認証](../azure-sql/database/security-overview.md#authentication) <br>- [Azure SQL へのデータベース アクセスを承認する - 認証と承認](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) |
   |||

   この例では、**Azure AD 統合** を使用します。

   ![[SQL Server] の接続ウィンドウを示すスクリーンショット。[認証の種類] の一覧が表示され、[AZURE AD 統合] が選択されています。](./media/connectors-create-api-sqlazure/select-azure-ad-authentication.png)

1. **[Azure AD 統合]** を選択した後、 **[サインイン]** を選択します。 Azure SQL Database または Azure SQL Managed Instance のどちらを使用するかに基づいて、認証用のユーザー資格情報を選択します。

1. データベースに対して次の値を選択します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **サーバー名** | はい | SQL サーバーのアドレス (例: `Fabrikam-Azure-SQL.database.windows.net`) |
   | **データベース名** | はい | SQL データベースの名前 (例: `Fabrikam-Azure-SQL-DB`) |
   | **[テーブル名]** | はい | 使用するテーブル (例: `SalesLT.Customer`) |
   ||||

   > [!TIP]
   > データベースとテーブルの情報を指定するには、次のオプションがあります。
   > 
   > * データベースの接続文字列でこの情報を確認します。 たとえば、Azure portal でデータベースを探して開きます。 データベース メニューで、 **[接続文字列]** または **[プロパティ]** を選択すると、この文字列が見つかります。
   >
   >   `Server=tcp:{your-server-address}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`
   >
   > * 既定では、システム データベース内のテーブルはフィルターで除外されるため、システム データベースを選択したときに自動的に表示されない場合があります。 別の方法として、データベース一覧から **[カスタム値の入力]** を選択した後、テーブル名を手動で入力することもできます。
   >

   次の例では、これらの値がどのように表示されるかを示します。

   ![SQL データベースへの接続を作成する](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. 次に、「[SQL トリガーを追加する](#add-sql-trigger)」または「[SQL アクションを追加する](#add-sql-action)」でまだ完了していない手順を続行します。

<a name="connect-sql-server"></a>

### <a name="connect-to-on-premises-sql-server"></a>オンプレミスの SQL Server に接続する

[SQL トリガー](#add-sql-trigger)または [SQL アクション](#add-sql-action)を初めて追加するときに、データベースへの接続をまだ作成していない場合は、次の手順を実行するように求められます。

1. オンプレミスのデータ ゲートウェイを必要とするオンプレミスの SQL Server への接続の場合は、[これらの前提条件が満たされている](#multi-tenant-or-ise)ことを確認します。

   そうでないと、接続を作成するときに、 **[接続ゲートウェイ]** の一覧にお使いのデータ ゲートウェイ リソースが表示されません。

1. **[認証の種類]** では、SQL Server で必要であり、有効にされている認証を選択します。

   | 認証 | 説明 |
   |----------------|-------------|
   | [**Windows 認証**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) | - ISE ではない SQL Server コネクタのみがサポートされます。これには、マルチテナント Azure と ISE のどちらを使用しているかに関係なく、接続のために Azure で以前に作成したデータ ゲートウェイ リソースが必要です。 <p><p>- Windows アカウントを使用して ID を確認するには、有効な Windows ユーザー名とパスワードが必要です。 <p>詳細については、[Windows 認証](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication)に関する記事を参照してください |
   | [**SQL Server 認証**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | - 非 ISE と ISE 両方の SQL Server コネクタをサポートします。 <p><p>- SQL Server で作成されて格納されている有効なユーザー名と強力なパスワードが必要です。 <p>詳細については、[SQL Server 認証](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)に関する記事を参照してください。 |
   |||

   この例では、**Windows 認証** を使用します。

   ![使用する認証の種類を選択する](./media/connectors-create-api-sqlazure/select-windows-authentication.png)

1. SQL データベースに関する次の値を選択または指定します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **SQL サーバー名** | はい | SQL サーバーのアドレス (例: `Fabrikam-Azure-SQL.database.windows.net`) |
   | **SQL データベース名** | はい | SQL Server データベースの名前 (例: `Fabrikam-Azure-SQL-DB`) |
   | **ユーザー名** | はい | SQL サーバーおよびデータベースのユーザー名 |
   | **パスワード** | はい | SQL サーバーおよびデータベースのパスワード |
   | **サブスクリプション** |  はい (Windows 認証の場合) | Azure で前に作成したデータ ゲートウェイ リソースの Azure サブスクリプション |
   | **接続ゲートウェイ** | はい (Windows 認証の場合) | Azure で前に作成したデータ ゲートウェイ リソースの名前 <p><p>**ヒント**:ゲートウェイが一覧に表示されない場合は、正しく [ゲートウェイを設定](../logic-apps/logic-apps-gateway-connection.md)していることを確認します。 |
   |||

   > [!TIP]
   > この情報は、データベースの接続文字列で確認できます。
   > 
   > * `Server={your-server-address}`
   > * `Database={your-database-name}`
   > * `User ID={your-user-name}`
   > * `Password={your-password}`

   次の例では、これらの値がどのように表示されるかを示します。

   ![SQL Server 接続の作成完了](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. 準備ができたら、 **[作成]** を選択します。

1. 次に、「[SQL トリガーを追加する](#add-sql-trigger)」または「[SQL アクションを追加する](#add-sql-action)」でまだ完了していない手順を続行します。

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>SQL トリガーを追加する

1. [Azure portal](https://portal.azure.com) または Visual Studio で、ロジック アプリ デザイナーを開いて、空のロジック アプリを作成します。 この例では、Azure portal を使用します。

1. デザイナーの検索ボックスに、「`sql server`」と入力します。 トリガーの一覧から、使用する SQL トリガーを選択します。 この例では、 **[項目が作成されたとき]** トリガーを使用します。

   ![[項目が作成されたとき] トリガーの選択](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. SQL データベースに初めて接続する場合は、[ここで SQL データベース接続を作成する](#create-connection)ことを求めるメッセージが表示されます。 この接続を作成した後は、次のステップを続けることができます。

1. トリガーでテーブルを確認する間隔と頻度を指定します。

1. このトリガーで使用可能な他のプロパティを追加するには、 **[新しいパラメーターの追加]** の一覧を開きます。

   このトリガーでは、選択したテーブルから 1 行のみが返され、それ以外には何も返されません。 他のタスクを実行するには、ロジック アプリ ワークフローで必要な次のタスクを実行する [SQL コネクタ アクション](#add-sql-action)または[別のアクション](../connectors/apis-list.md)を追加することで続けます。

   たとえば、この行のデータを表示するには、返された行のフィールドを含むファイルを作成する他のアクションを追加し、電子メール通知を送信します。 このコネクタで使用できるその他のアクションの詳細については、[コネクタのリファレンス ページ](/connectors/sql/)を参照してください。

1. デザイナーのツール バーで、 **[保存]** を選択します。

   このステップでは、Azure でライブ状態のロジック アプリが自動的に有効にされて発行されますが、ロジック アプリで現在実行されているアクションは、指定した間隔と頻度に基づくデータベースのチェックだけです。

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>トリガーの繰り返しのシフトやずれ

最初に接続を作成する必要がある接続ベースのトリガー (SQL トリガーなど) は、Azure Logic Apps でネイティブに実行される組み込みトリガー ([繰り返しトリガー](../connectors/connectors-native-recurrence.md)など) とは異なります。 繰り返し発生する接続ベースのトリガーでは、繰り返しスケジュールだけが実行を制御するわけではなく、タイム ゾーンによって最初の開始時刻のみが決定されます。 それ以降の実行は、繰り返しスケジュール、最後のトリガー実行、"*さらには*" 実行時間をずらしたり、予期しない動作を発生させたりする可能性があるその他の要因に依存します。たとえば、夏時間 (DST) の開始または終了時に、指定されたスケジュールが保持されないという要因があります。 DST が有効になったときに繰り返し時刻がシフトされないようにするには、ロジック アプリが引き続き予期された時刻に実行されるように、繰り返しを手動で調整します。 そうしないと、開始時刻が DST の開始時には 1 時間先に、DST の終了時には 1 時間前にシフトされます。 詳細については、[接続ベースのトリガーの繰り返し](../connectors/apis-list.md#recurrence-connection-based)に関するページを参照してください。

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>SQL アクションを追加する

この例では、ロジック アプリは、[繰り返しトリガー](../connectors/connectors-native-recurrence.md)で起動され、SQL データベースから行を取得するアクションを呼び出します。

1. [Azure portal](https://portal.azure.com) または Visual Studio で、ロジック アプリをロジック アプリ デザイナーで開きます。 この例では、Azure portal を使用します。

1. SQL アクションを追加するトリガーまたはアクションで、 **[新しいステップ]** を選択します。

   ![ロジック アプリにアクションを追加する](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   または、既存のステップの間にアクションを追加するには、接続矢印の上にマウスを移動します。 表示されるプラス記号 ( **+** ) を選択してから、 **[アクションの追加]** を選択します。

1. **[アクションを選択してください]** で、検索ボックスに「`sql server`」と入力します。 アクションの一覧から、使用する SQL アクションを選択します。 この例では、1 つのレコードを取得する **[行の取得]** アクションを使用します。

   ![SQL の [行の取得] アクションを選択する](./media/connectors-create-api-sqlazure/select-sql-get-row-action.png)

1. SQL データベースに初めて接続する場合は、[ここで SQL データベース接続を作成する](#create-connection)ことを求めるメッセージが表示されます。 この接続を作成した後は、次のステップを続けることができます。

1. **[テーブル名]** を選択します。この例では `SalesLT.Customer` です。 目的のレコードの **[行 ID]** を入力します。

   ![テーブル名を選択し、行 ID を指定する](./media/connectors-create-api-sqlazure/specify-table-row-id.png)

   このアクションでは、選択したテーブルから 1 行のみが返され、それ以外は何も行われません。 そこで、この行のデータを表示するには、返される行のフィールドが含まれるファイルを作成し、そのファイルをクラウド ストレージ アカウントに格納するその他のアクションを追加します。 このコネクタで使用できるその他のアクションの詳細については、[コネクタのリファレンス ページ](/connectors/sql/)を参照してください。

1. 操作が完了したら、デザイナーのツールバーで、 **[保存]** を選択します。

   この手順によって、ロジック アプリが自動的に有効化され、Azure に発行されます。

<a name="handle-bulk-data"></a>

## <a name="handle-bulk-data"></a>一括データを処理します。

サイズが大きすぎてコネクタが同時にすべての結果を返さない結果セットを操作する必要がある場合、または、結果セットのサイズと構造を詳細に制御したい場合があります。 このような大きな結果セットを処理する方法は、いくつかあります。

* 結果を、より小さなセットとして管理しやすくするには、*改ページ位置の自動修正* をオンします。 詳細は、「[Get bulk data, records, and items by using pagination](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)」(改ページ位置の自動修正を使用した一括データ、レコードおよび項目) を参照してください。 詳細については、「[Logic Apps での一括データ転送に対する SQL の改ページ処理](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)」を参照してください。

* 希望どおりの結果を編成する "[*ストアド プロシージャ*](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)" を作成します。 SQL データベース テーブルを操作するビジネス タスクをより簡単に自動化できるように、SQL コネクタには、Azure Logic Apps を使用してアクセスできる多数のバックエンド機能が用意されています。

  複数の行を取得または挿入する場合、ロジック アプリは、こちらの [制限](../logic-apps/logic-apps-limits-and-config.md)の中で "[*until ループ*](../logic-apps/logic-apps-control-flow-loops.md#until-loop)" を使用することで、行を反復処理できます。 ただし、ロジック アプリは、数千から数百万の行がある非常に大きなレコード セットを処理する場合があります。このような場合は、データベースへの呼び出しコストを最小限にする必要があります。

  代わりに、SQL インスタンスで実行され、SELECT - ORDER BYステートメントを使用して、望みどおりの方法で結果を整理する "**ストアド プロシージャ**" を作成できます。 このソリューションでは、結果のサイズと構造を詳細に制御できます。 ロジック アプリは、SQL Server コネクタの **［ストアド プロシージャの実行］** アクションを使用して、ストアド プロシージャを呼び出します。 詳細については、「[SELECT - ORDER BY 句](/sql/t-sql/queries/select-order-by-clause-transact-sql)」を参照してください。

  > [!NOTE]
  > SQL コネクタには、[2 分未満](/connectors/sql/#known-issues-and-limitations)のストアド プロシージャのタイムアウト上限があります。 一部のストアド プロシージャでは、この上限を超えることがあり、`504 Timeout` エラーが発生します。 この問題を回避するには、SQL 完了トリガー、ネイティブ SQL パススルー クエリ、状態テーブル、およびサーバー側ジョブを使用します。
  > 
  > このタスクでは、[Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) で [Azure エラスティック ジョブ エージェント](../azure-sql/database/elastic-jobs-overview.md)を使用できます。 [オンプレミスの SQL Server](/sql/sql-server/sql-server-technical-documentation) と [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) では、[SQL Server エージェント](/sql/ssms/agent/sql-server-agent)を使用できます。 詳細については、[Azure Logic Apps の SQL コネクタで長時間実行されるストアド プロシージャのタイムアウトの処理](../logic-apps/handle-long-running-stored-procedures-sql-connector.md)に関するページを参照してください。

### <a name="handle-dynamic-bulk-data"></a>動的な一括データを処理する

SQL Server コネクタを使用してストアド プロシージャを呼び出すと、返される出力が動的になることがあります。 このシナリオでは、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. テスト実行を行って、出力形式を表示します。 サンプル出力をコピーして保存します。

1. デザイナーで、ストアド プロシージャを呼び出す操作の下にある **[新しいステップ]** を選択します。

1. **[アクションの選択]** で、[ **[JSON の解析]**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) アクションを探して選択します。

1. **[JSON の解析]** アクションで、 **[サンプルのペイロードを使用してスキーマを生成する]** を選択します。

1. **[サンプルの JSON ペイロードを入力するか、貼り付けます]** ボックスにサンプル出力を貼り付けて、 **[完了]** を選択します。

   > [!NOTE]
   > Logic Apps がスキーマを生成できないというエラーが発生した場合は、サンプル出力の構文が正しい形式であることを確認してください。 それでもスキーマを生成できない場合は、 **[スキーマ]** ボックスにスキーマを手入力します。

1. デザイナーのツール バーで、 **[保存]** を選択します。

1. JSON コンテンツのプロパティを参照するには、それらのプロパティを参照する編集ボックス内をクリックして、動的コンテンツの一覧を表示します。 一覧の [ **[JSON の解析]**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) の見出しの下で、目的の JSON コンテンツ プロパティのデータ トークンを選択します。

## <a name="troubleshoot-problems"></a>問題のトラブルシューティング

<a name="connection-problems"></a>

### <a name="connection-problems"></a>接続の問題

接続の問題は普通に発生する可能性があるため、このような問題をトラブルシューティングして解決するには、「[SQL Server への接続エラーの解決](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)」を確認してください。 次に例をいくつか示します。

* `A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.`

* `(provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server) (Microsoft SQL Server, Error: 53)`

* `(provider: TCP Provider, error: 0 - No such host is known.) (Microsoft SQL Server, Error: 11001)`

## <a name="connector-specific-details"></a>コネクタ固有の詳細

このコネクタのトリガー、アクション、制限に関する技術情報については、Swagger の説明から生成される[コネクタのリファレンス ページ](/connectors/sql/)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps の他のコネクタ](../connectors/apis-list.md)の詳細情報
