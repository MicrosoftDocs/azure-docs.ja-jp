---
title: 自動チューニングの有効化
description: データベースの自動チューニングは、Azure portal を使用して簡単に有効にすることができます。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/03/2021
ms.openlocfilehash: d60810c291984e0f57df1968f69678de8179273c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042523"
---
# <a name="enable-automatic-tuning-in-the-azure-portal-to-monitor-queries-and-improve-workload-performance"></a>クエリの監視とワークロード パフォーマンスの向上のために Azure portal で自動チューニングを有効にする
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database では、データ サービスが自動的に管理されます。データ サービスが常にクエリを監視し、ワークロードのパフォーマンスを向上させるために実行できるアクションを識別します。 推奨事項を確認し、手動で適用できます。また、Azure SQL Database で自動的に是正措置を適用することもできます (**自動チューニング モード** ともいう)。

サーバーまたはデータベース レベルで自動チューニングを有効にすることができます。次の方法があります。

- [Azure Portal](automatic-tuning-enable.md#azure-portal)
- [REST API](automatic-tuning-enable.md#rest-api) 呼び出し
- [T-SQL](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true) コマンド

> [!NOTE]
> Azure SQL Managed Instance でサポートされているオプション FORCE_LAST_GOOD_PLAN は、[T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) を使用してのみ構成できます。 この記事で説明している Azure portal ベースの構成とインデックスの自動チューニング オプションは、Azure SQL Managed Instance には適用されません。

> [!NOTE]
> 現時点では、ARM (Azure Resource Manager) テンプレートを使った自動チューニング オプションの構成はサポートされていません。

## <a name="enable-automatic-tuning-on-server"></a>サーバーでの自動チューニングの有効化

自動チューニングの構成を [Azure の既定値] から継承するかどうかをサーバー レベルで選択できます。 Azure の既定値では、FORCE_LAST_GOOD_PLAN が有効で、CREATE_INDEX と DROP_INDEX が無効です。

> [!IMPORTANT]
> 2020 年 3 月より、自動チューニングに関する新しい Azure の既定値は次のようになります。
>
> - FORCE_LAST_GOOD_PLAN が有効、CREATE_INDEX が無効、DROP_INDEX が無効になります。
> - 自動チューニング設定が構成されていない既存のサーバーは、Azure の既定値を継承するように自動的に構成されます。 これは、現在未定義の状態の自動チューニングのサーバー設定があるすべてのお客様に適用されます。
> - 新しく作成されたサーバーは、Azure の既定値を継承するように自動的に構成されます (新しいサーバーの作成時に自動チューニング構成が未定義の状態だった以前とは異なります)。

### <a name="azure-portal"></a>Azure portal

Azure SQL Database の [サーバー](logical-servers.md)で自動チューニングを有効にするには、Azure portal でサーバーに移動し、メニューで **[自動チューニング]** を選択します。

![スクリーンショットには、サーバーのオプションを適用できる、Azure portal での自動チューニングが示されています。](./media/automatic-tuning-enable/server.png)

> [!NOTE]
> **DROP_INDEX** オプションは、現時点では、パーティションの切り替えとインデックス ヒントを使うアプリケーションと互換性がなく、このような場合は有効にしてはならないことに注意してください。 Premium および Business Critical サービスレベルでは、使われていないインデックスの削除はサポートされていません。

次のように、有効にする自動チューニング オプションを選択し、 **[適用]** を選択します。

サーバーの自動チューニング オプションは、このサーバー上のすべてのデータベースに適用されます。 既定では、すべてのデータベースがその親サーバーから構成を継承しますが、これをオーバーライドし、各データベースに対して個別に指定することができます。

### <a name="rest-api"></a>REST API

REST API を使用して **サーバー** の自動チューニングを有効にする方法については、[サーバーの自動チューニングの UPDATE メソッドと GET HTTP メソッド](/rest/api/sql/serverautomatictuning)に関するページを参照してください。

## <a name="enable-automatic-tuning-on-an-individual-database"></a>個々のデータベースで自動チューニングを有効にする

Azure SQL Database では、各データベースの自動チューニング構成を個別に指定することができます。 自動チューニングの構成を親サーバーから継承するか、[Azure の既定値] から継承するか、または構成を継承しないかをデータベース レベルで選択できます。 Azure の既定値では、FORCE_LAST_GOOD_PLAN が有効に、CREATE_INDEX が無効に、DROP_INDEX が無効に設定されています。

> [!TIP]
> 一般的な推奨事項は、すべてのデータベースで同じ構成設定を自動的に適用できるように、**サーバー レベル** で自動チューニング構成を管理することです。 個々のデータベースの設定が、同じサーバーから設定を継承している他のデータベースの設定と異なるようにする必要がある場合にのみ、個々のデータベースで自動チューニングを構成します。

### <a name="azure-portal"></a>Azure portal

**単一データベース** で自動チューニングを有効にするには、Azure portal でデータベースに移動し、 **[自動チューニング]** を選択します。

個々の自動チューニング設定は、データベースごとに個別に構成できます。 個々の自動チューニング オプションを手動で構成したり、オプションがサーバーから設定を継承するように指定したりできます。

![スクリーンショットには、単一データベースのオプションを適用できる、Azure portal での自動チューニングを示されています。](./media/automatic-tuning-enable/database.png)

DROP_INDEX オプションは、現時点では、パーティションの切り替えとインデックス ヒントを使うアプリケーションと互換性がなく、このような場合は有効にしてはならないことに注意してください。

目的の構成を選択したら、 **[適用]** をクリックします。

### <a name="rest-api"></a>Rest API

REST API を使用して単一データベースの自動チューニングを有効にする方法については、[Azure SQL Database の自動チューニングの UPDATE メソッドと GET HTTP メソッド](/rest/api/sql/databaseautomatictuning)に関するページを参照してください。

### <a name="t-sql"></a>T-SQL

T-SQL から単一データベースの自動チューニングを有効にするには、データベースに接続して次のクエリを実行します。

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

自動チューニングを AUTO に設定すると、"Azure の既定値" が適用されます。 INHERIT に設定した場合は、自動チューニングの構成が親サーバーから継承されます。 CUSTOM を選択した場合は、自動チューニングを手動で構成する必要があります。

T-SQL で個々の自動チューニング オプションを構成するには、データベースに接続して、たとえば次のクエリを実行します。

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = ON, DROP_INDEX = OFF)
```

個々のチューニング オプションを ON に設定した場合、データベースによって継承された設定がオーバーライドされて、そのチューニング オプションが有効になります。 個々のチューニング オプションを OFF に設定した場合は、データベースによって継承された設定が同じようにオーバーライドされたうえで、そのチューニング オプションが無効になります。 自動チューニング オプションに DEFAULT を指定した場合、サーバー レベル設定から自動チューニング構成が継承されます。  

> [!IMPORTANT]
> [アクティブ geo レプリケーション](auto-failover-group-overview.md)の場合、プライマリ データベースのみで自動チューニングを構成する必要があります。 インデックスの作成や削除など、自動的に適用されるチューニング アクションは、読み取り専用のセカンダリに自動的にレプリケートされます。 読み取り専用のセカンダリで T-SQL を使用して自動チューニングを有効にしようとすると、その読み取り専用のセカンダリで別のチューニング構成を使用することはサポートされていないため、失敗します。
>

自動チューニングを構成する T-SQL のオプションの詳細については、[ALTER DATABASE SET オプション (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true) に関するページを参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="automated-recommendation-management-is-disabled"></a>レコメンデーションの自動管理が無効になっている

レコメンデーションの自動管理が無効になっているというエラー メッセージが表示された場合、または単にシステムによって無効にされた場合、最も一般的な原因は次のとおりです。
- クエリ ストアが有効になっていない、または
- クエリ ストアが、指定されたデータベースに対して読み取り専用モードになっている、または
- クエリ ストアが割り当てられた記憶域スペースを使用したため、実行を停止した

この問題を修正するには、次の手順を検討してください。
- クエリ ストアをクリーンアップするか、T-SQL を使用してデータ保持期間を "auto" に変更します。 クエリ ストアに推奨される保持期間とキャプチャ ポリシーを構成する方法については、[こちら](/azure/azure-sql/database/query-performance-insight-use#recommended-retention-and-capture-policy)を参照してください。
- SQL Server Management Studio (SSMS) を使用して、次の手順を行います。
  - Azure SQL Database に接続する
  - データベースを右クリックする
  - [プロパティ] にアクセスし、[クエリ ストア] をクリックする
  - [操作モード] を [読み取り/書き込み] に変更する
  - [ストア キャプチャ モード] を [自動] に変更する
  - [サイズ ベースのクリーンアップ モード] を [自動] に変更する

### <a name="permissions"></a>アクセス許可

自動チューニングは Azure の機能であるため、これを使用するには、Azure の組み込みロールを使用する必要があります。 SQL 認証を使用するだけでは、Azure portal からその機能を使用することはできません。

自動チューニングを使用するには、ユーザーに付与する必要がある最小限のアクセス許可は、Azure の組み込みロールである [SQL Database 共同作成者](../../role-based-access-control/built-in-roles.md#sql-db-contributor)ロールです。 SQL Server 共同作成者、SQL Managed Instance 共同作成者、共同作成者、所有者など、上位の特権ロールの使用を検討することもできます。

## <a name="configure-automatic-tuning-e-mail-notifications"></a>メール通知の自動チューニングの構成

自動チューニングによって作成されたレコメンデーションに関する通知を電子メールで自動的に受信するには、[自動チューニングの電子メール通知](automatic-tuning-email-notifications-configure.md)ガイドを参照してください。

## <a name="next-steps"></a>次のステップ

- [自動チューニングに関する記事](automatic-tuning-overview.md)を読み、自動チューニングと、パフォーマンスの向上にいかに役立つかを確認します。
- Azure SQL Database のパフォーマンスに関する推奨事項の概要については、「[パフォーマンスに関する推奨事項](database-advisor-implement-performance-recommendations.md)」を参照してください。
- よく使用されるクエリによるパフォーマンスへの影響を確認する方法については、[クエリ パフォーマンスの洞察](query-performance-insight-use.md)に関する記事をご覧ください。
