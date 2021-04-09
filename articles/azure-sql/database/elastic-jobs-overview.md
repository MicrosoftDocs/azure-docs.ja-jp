---
title: Elastic Database ジョブ (プレビュー)
description: Azure SQL Database で 1 つ以上のデータベースのセット間で Transact-SQL (T-SQL) スクリプトを実行するには、エラスティック データベース ジョブ (プレビュー) を構成します
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srinia
ms.author: srinia
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: f9a026ed47d662b80ef01e505bfbcf8f32d20b04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792176"
---
# <a name="create-configure-and-manage-elastic-jobs-preview"></a>エラスティック ジョブの作成、構成、および管理 (プレビュー)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

この記事では、エラスティック ジョブを作成、構成、および管理する方法を学びます。

エラスティック ジョブを使用したことがない場合は、[Azure SQL Database でのジョブ自動化の概念](job-automation-overview.md)を参照してください。

## <a name="create-and-configure-the-agent"></a>エージェントの作成と構成

1. S0 以上で、中身が空のデータベースを作成または特定します。 このデータベースが、エラスティック ジョブ エージェントの作成時に "*ジョブ データベース*" として使用されます。
2. [ポータル](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent)または [PowerShell](elastic-jobs-powershell-create.md#create-the-elastic-job-agent) を使用してエラスティック ジョブ エージェントを作成します。

   ![エラスティック ジョブ エージェントの作成中](./media/elastic-jobs-overview/create-elastic-job-agent.png)

## <a name="create-run-and-manage-jobs"></a>ジョブの作成、実行、および管理

1. [PowerShell](elastic-jobs-powershell-create.md) または [T-SQL](elastic-jobs-tsql-create-manage.md#create-a-credential-for-job-execution) を使用して、"*ジョブ データベース*" にジョブ実行のための資格情報を作成します。
2. [PowerShell](elastic-jobs-powershell-create.md) または [T-SQL](elastic-jobs-tsql-create-manage.md#create-a-target-group-servers) を使用して、ターゲット グループ (ジョブの実行の対象とするデータベース) を定義します。
3. ジョブを実行するデータベースのそれぞれに、ジョブ エージェントの資格情報を作成 [(グループ内の各データベースにユーザー (またはロール) を追加)](logins-create-manage.md) します。 例については、[PowerShell のチュートリアル](elastic-jobs-powershell-create.md)を参照してください。
4. [PowerShell](elastic-jobs-powershell-create.md) または [T-SQL](elastic-jobs-tsql-create-manage.md#deploy-new-schema-to-many-databases) を使用してジョブを作成します。
5. [PowerShell](elastic-jobs-powershell-create.md) または [T-SQL](elastic-jobs-tsql-create-manage.md#deploy-new-schema-to-many-databases) を使用してジョブ ステップを追加します。
6. [PowerShell](elastic-jobs-powershell-create.md#run-the-job) または [T-SQL](elastic-jobs-tsql-create-manage.md#begin-unplanned-execution-of-a-job) を使用してジョブを実行します。
7. ポータル、[PowerShell](elastic-jobs-powershell-create.md#monitor-status-of-job-executions)、または[T-SQL](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status) を使用して、ジョブの実行状態を監視します。

   ![ポータル](./media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>ジョブの実行のための資格情報

ジョブは[データベース スコープ資格情報](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)を使用して、実行時にターゲット グループによって指定されたデータベースに接続します。 ターゲット グループにサーバーまたはプールが含まれる場合には、利用できるデータベースを列挙するマスター データベースに接続するときに、このデータベース スコープ資格情報が使用されます。

ジョブの実行に必要な資格情報を正しく設定する作業は多少複雑ですので、次のポイントを頭に入れておくようにしてください。

- データベース スコープ資格情報は "*ジョブ データベース*" に作成する必要があります。
- **ターゲット データベースにはいずれも、ジョブが正常に完了するうえで [十分なアクセス許可](/sql/relational-databases/security/permissions-database-engine)を備えたログインを設定しておく必要があります** (以下の図の `jobuser`)。
- 資格情報は複数のジョブで再利用できます。このため、資格情報のパスワードは暗号化のうえ、ジョブ オブジェクトに対する読み取りアクセス許可が認められたユーザーに知られることがないように保護されます。

次の図は、ジョブの正しい資格情報を把握し、その設定をサポートすることを意図して用意したものです。 **ジョブの実行が必要なデータベースの 1 つひとつ ("*ターゲット ユーザー DB*" すべて) で、忘れずにユーザーを作成してください**。

![エラスティック ジョブの資格情報](./media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>セキュリティの運用方法

エラスティック ジョブを使用する際のベスト プラクティスをいくつか紹介します。

- API の使用を信頼できるユーザーに制限します。
- 資格情報には、ジョブ ステップの実行に最小限必要な権限だけを設定します。 詳細については、[承認と権限](/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)に関するページを参照してください。
- ターゲット グループのメンバーとしてサーバーまたはプールを使用する場合には、マスター データベースでデータベースを表示/一覧表示するための権限を設定した資格情報を別個に作成することを強くお勧めします。この資格情報は、ジョブの実行前にサーバーまたはプールのデータベースの一覧を展開する際に使用します。

## <a name="agent-performance-capacity-and-limitations"></a>エージェントのパフォーマンス、容量、および制約

エラスティック ジョブでは、実行時間の長いジョブの完了を待っている間に消費する計算リソースを最小限に抑えます。

データベースから成るターゲット グループの大きさと、ジョブについて希望する実行時間 (同時に実行する worker の数) に応じて、エージェントが "*ジョブ データベース*" に要求する計算量とパフォーマンスが変わります (ターゲットとジョブの数が増えるほど、要求される計算量が増大します)。

現時点のプレビュー版では、同時に実行するジョブの上限が 100 件に制限されています。

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>ジョブを原因とするターゲット データベースのパフォーマンス低下を防ぐ

SQL エラスティック プール内のデータベースにジョブを実行しているときにリソースに対する負荷が大きくなりすぎないようにするために、ジョブを構成して同時にジョブの実行対象とするデータベースの数に制限を設けることができます。

`sp_add_jobstep`ストアド プロシージャの`@max_parallelism`パラメーターを T-SQL または `Add-AzSqlElasticJobStep -MaxParallelism`PowerShell で設定することで、ジョブが実行する同時実行データベースの数を設定します。

## <a name="best-practices-for-creating-jobs"></a>ジョブ作成のベスト プラクティス

### <a name="idempotent-scripts"></a>べき等スクリプト
ジョブの T-SQL スクリプトは [べき等](https://en.wikipedia.org/wiki/Idempotence)にする必要があります。 **べき等** とは、実行に成功したスクリプトを再度実行した場合に、結果が同じになることを意味します。 一時的なネットワークの問題により、スクリプトが失敗することがあります。 その場合、ジョブは事前に設定した回数に達するまで自動的にスクリプトを再試行します。 べき等スクリプトは、2 回 (以上) 実行して成功した場合、結果が同じになります。

単純な方法として、作成前に、オブジェクトの存在をテストします。


```sql
IF NOT EXISTS (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

同様に、スクリプトは、それが検出する条件を論理的に試験し、対処することで正常に実行できる必要があります。



## <a name="next-steps"></a>次のステップ

- [PowerShell を使用したエラスティック ジョブの作成と管理](elastic-jobs-powershell-create.md)
- [Transact-SQL を使用したエラスティック ジョブの作成と管理](elastic-jobs-tsql-create-manage.md)