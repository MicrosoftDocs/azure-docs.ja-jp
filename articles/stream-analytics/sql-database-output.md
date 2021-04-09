---
title: Azure Stream Analytics からの Azure SQL Database 出力
description: この記事では、Azure Stream Analytics の出力としての Azure SQL Database について説明します。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 631fdba451f69e44a675d396a42e1cddaea50a3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98013959"
---
# <a name="azure-sql-database-output-from-azure-stream-analytics"></a>Azure Stream Analytics からの Azure SQL Database 出力

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) は、本質的にリレーショナルであるデータや、リレーショナル データベースにホストされているコンテンツに依存するアプリケーションの出力として使用できます。 Azure Stream Analytics ジョブは、SQL Database の既存のテーブルに書き込まれます。 テーブル スキーマは、ご自分のジョブの出力内のフィールドとその型に正確に一致する必要があります。 また、SQL Database 出力オプションを使用して、[Azure Synapse Analytics](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) を出力として指定することもできます。 書き込みのスループットを向上させる方法については、[Azure SQL Database を出力として使用する Stream Analytics](stream-analytics-sql-output-perf.md) に関する記事を参照してください。

[Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) を出力として使用することもできます。 [SQL Managed Instance でパブリック エンドポイントを構成](../azure-sql/managed-instance/public-endpoint-configure.md)してから、Azure Stream Analytics で次の設定を手動で構成する必要があります。 データベースがアタッチされた SQL Server が実行されている Azure 仮想マシンも、以下の設定を手動で構成することによりサポートされます。

## <a name="output-configuration"></a>出力の構成

次の表に、SQL Database の出力を作成するためのプロパティの名前とその説明を示します。

| プロパティ名 | 説明 |
| --- | --- |
| 出力エイリアス |クエリの出力をこのデータベースに出力するためにクエリで使用されるわかりやすい名前です。 |
| データベース | ご自分の出力を送信するデータベースの名前です。 |
| サーバー名 | 論理 SQL サーバーの名前またはマネージド インスタンスの名前。 SQL Managed Instance の場合は、ポート 3342 を指定する必要があります。 たとえば、*sampleserver.public.database.windows.net,3342* のようになります |
| ユーザー名 | データベースに書き込むためのアクセス権が割り当てられているユーザー名です。 Stream Analytics では、SQL 認証のみがサポートされます。 |
| Password | データベースに接続するためのパスワード。 |
| テーブル | 出力の書き込み先のテーブル名です。 テーブル名は、大文字と小文字が区別されます。 このテーブルのスキーマは、ご自分のジョブの出力によって生成されるフィールドの数とその型に正確に一致する必要があります。 |
|パーティション構成を継承する| ご自分の以前のクエリ ステップのパーティション構成を継承し、テーブルへの複数のライターによる完全な並列トポロジを有効にするためのオプションです。 詳細については、「[Azure SQL Database への Azure Stream Analytics の出力](stream-analytics-sql-output-perf.md)」を参照してください。|
|最大バッチ カウント| 各一括挿入トランザクションで送信されるレコード数の推奨される上限です。|

Azure Stream Analytics から Azure Synapse Analytics への出力を可能にする 2 つのアダプターがあります。SQL Database と Azure Synapse です。 次のいずれかの条件に該当する場合は、SQL Database アダプターではなく、Azure Synapse Analytics アダプターを選択することをお勧めします。

* **スループット**:現在または将来の予想されるスループットが 10 MB/秒を超える場合は、Azure Synapse のパフォーマンスを向上させるための出力オプションを使用します。

* **入力パーティション数**: 入力パーティションが 8 つ以上ある場合は、Azure Synapse のスケールアウトを向上させるための出力オプションを使用します。

## <a name="partitioning"></a>パーティション分割

パーティション分割は有効になっている必要があり、クエリの PARTITION BY 句に基づいています。 パーティション分割の継承オプションが有効になっている場合は、[完全並列化可能なクエリ](stream-analytics-scale-jobs.md)に対する入力のパーティション分割に従います。 SQL Azure Database にデータを読み込むときの書き込みスループット パフォーマンスの向上の詳細については、「[Azure SQL Database への Azure Stream Analytics の出力](stream-analytics-sql-output-perf.md)」を参照してください。

## <a name="output-batch-size"></a>出力バッチ サイズ

最大メッセージ サイズを構成するには、**最大バッチ カウント** を使用します。 既定の最大値は 10,000 で、既定の最小値は、1 回の一括挿入あたり 100 行です。 詳細については、[Azure SQL の制限](../azure-sql/database/resource-limits-logical-server.md)に関する記事を参照してください。 すべてのバッチは、最初に最大バッチ カウントを使用して一括挿入されます。 バッチは、SQL の再試行可能なエラーに基づいて (最小バッチ カウントに達するまで) 半分に分割されます。

## <a name="next-steps"></a>次のステップ

* [マネージド ID を使用して Azure Stream Analytics ジョブから Azure SQL Database または Azure Synapse Analytics にアクセスする (プレビュー)](sql-database-output-managed-identity.md)
* [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)