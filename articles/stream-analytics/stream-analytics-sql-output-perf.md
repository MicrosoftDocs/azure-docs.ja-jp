---
title: Azure Stream Analytics から Azure SQL Database へのスループットのパフォーマンスを向上させる
description: Azure Stream Analytics から Azure SQL Database にデータを出力し、より高い書き込みスループット レートを実現する方法について説明します。
author: chetanmsft
ms.author: chetang
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 8baa33c8d9622ff76db04345f5c6c465f026e261
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020232"
---
# <a name="increase-throughput-performance-to-azure-sql-database-from-azure-stream-analytics"></a>Azure Stream Analytics から Azure SQL Database へのスループットのパフォーマンスを向上させる

この記事では、Azure Stream Analytics を使用して Azure SQL Database にデータを読み込むときに、よりよい書き込みスループット パフォーマンスを実現するためのヒントについて説明します。

Azure Stream Analytics の SQL 出力では、オプションとして並列書き込みがサポートされます。 このオプションでは[完全に並列](stream-analytics-parallelization.md#embarrassingly-parallel-jobs)なジョブ トポロジが可能で、複数の出力パーティションが書き込み先テーブルに並列で書き込まれます。 ただし、Azure Stream Analytics でこのオプションを有効にするだけでは、より高いスループットを実現するのに十分ではありません。スループットは、データベースの構成とテーブル スキーマに大きく依存しているためです。 インデックス、クラスタリング キー、インデックスの FILL FACTOR、および圧縮の選択が、テーブルを読み込む時間に影響します。 内部ベンチマークに基づいてデータベースを最適化してクエリと読み込みのパフォーマンスを向上させる方法について詳しくは、[SQL Database のパフォーマンス ガイダンス](../azure-sql/database/performance-guidance.md)に関するページをご覧ください。 SQL Database に並列で書き込む場合、書き込みの順序は保証されません。

ここでは、ソリューションの全体的なスループットの向上に役立つ各サービス内のいくつかの構成を示します。

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **パーティション分割の継承** – この SQL 出力構成オプションを使用すると、前のクエリ ステップや入力のパーティション構成を継承できます。 これを有効にして、ディスク ベースのテーブルに書き込み、ジョブを[完全並列](stream-analytics-parallelization.md#embarrassingly-parallel-jobs)トポロジにすると、スループットの向上を期待できます。 他の多くの[出力](stream-analytics-parallelization.md#partitions-in-inputs-and-outputs)に対しては、このパーティション分割は既に自動的に行われています。 このオプションで行われる一括挿入に対しては、テーブル ロック (TABLOCK) も無効になります。

> [!NOTE] 
> 入力パーティションが 8 個より多い場合、入力パーティション構成の継承は適切な選択ではない可能性があります。 この上限は、ID 列とクラスター化インデックスが 1 つだけのテーブルにおいて観察されたものです。 この場合、出力ライターの数を明示的に指定するために、クエリで [INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 8 を使用することを検討してください。 スキーマとインデックスの選択によっては、結果が異なる場合があります。

- **バッチ サイズ** - SQL の出力構成では、書き込み先テーブル/ワークロードの特性に基づいて、Azure Stream Analytics SQL 出力の最大バッチ サイズを指定することができます。 バッチ サイズは、すべての一括挿入トランザクションで送信されるレコードの最大数です。 クラスター化された列ストア インデックスでは、バッチ サイズを約 [100 K](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) にすると、並列化を高くし、ログ記録を最小にし、ロックを最適にできます。 ディスク ベースのテーブルでは、10 K (既定値) 以下にするとソリューションに最適な場合があります。バッチ サイズを大きくすると、一括挿入中にロックのエスカレーションがトリガーされる可能性があります。

- **入力メッセージのチューニング** – パーティション分割の継承とバッチ サイズの使用を最適化している場合、1 つのパーティションのメッセージあたりの入力イベントの数を増やすと、書き込みスループットをさらに上げるのに役立ちます。 入力メッセージのチューニングにより、Azure Stream Analytics 内のバッチ サイズを指定したバッチ サイズまで上げることができ、それによってスループットが向上します。 これは、[圧縮](stream-analytics-define-inputs.md)を使用するか、または EventHub または BLOB での入力メッセージ サイズを増やすことによって実現できます。

## <a name="sql-azure"></a>SQL Azure

- **パーティション テーブルとパーティション インデックス** – パーティション キー (たとえば PartitionId) と同じ列を含むテーブルで [パーティション分割された](/sql/relational-databases/partitions/partitioned-tables-and-indexes) SQL テーブルとパーティション分割されたインデックスを使用すると、書き込み中のパーティション間の競合を大幅に減らすことができます。 パーティション テーブルの場合は、プライマリ ファイル グループに[パーティション関数](/sql/t-sql/statements/create-partition-function-transact-sql)と[パーティション構成](/sql/t-sql/statements/create-partition-scheme-transact-sql)を作成する必要があります。 これにより、新しいデータの読み込み中の既存データの可用性も向上します。 パーティションの数によってはログ IO の上限に達する可能性があり、これは SKU をアップグレードすることで増やすことができます。

- **一意キー違反の回避** – Azure Stream Analytics のアクティビティ ログで [複数キー違反の警告メッセージ](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output)が発生する場合は、復旧の間に発生する可能性がある一意制約違反によってジョブが影響を受けていないことを確認します。 インデックスに対して [IGNORE\_DUP\_KEY](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) オプションを設定することでこれを回避できます。

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure Data Factory とインメモリ テーブル

- **一時テーブルとしてのインメモリ テーブル** – [インメモリ テーブル](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)を使用すると、非常に高速のデータ読み込みが可能になりますが、データをメモリ内に収める必要があります。 ベンチマークでは、インメモリ テーブルからディスク ベースのテーブルへの一括読み込みの方が、ID 列とクラスター化インデックスのあるディスク ベースのテーブルへの単一ライターを使用した一括挿入より、約 10 倍速いことが示されています。 この一括挿入のパフォーマンスを利用するには、インメモリ テーブルからディスク ベースのテーブルにデータをコピーする、[Azure Data Factory を使用するコピー ジョブ](../data-factory/connector-azure-sql-database.md)を設定します。

## <a name="avoiding-performance-pitfalls"></a>パフォーマンスの落とし穴の回避
データの一括挿入は、データの転送、挿入ステートメントの解析、ステートメントの実行、トランザクション レコードの発行などの繰り返しのオーバーヘッドが回避されるため、単一の挿入でのデータの読み込みよりはるかに高速です。 代わりに、データをストリーミングするために、ストレージ エンジンへのより効率的なパスが使用されます。 ただし、このパスの設定コストは、ディスク ベースのテーブル内の 1 つの挿入ステートメントよりはるかに高くなります。 この損益分岐点は一般に約 100 行であり、それを超えると、一括読み込みの方がほぼ常に効率的です。 

受信イベントのレートが低いと、100 行より小さいバッチ サイズが容易に作成される可能性があり、それによって一括挿入が非効率的になり、大量のディスク領域が使用されます。 この制限を回避するには、次のいずれかのアクションを実行できます。
* すべての行に単純な挿入を使用するために INSTEAD OF [トリガー](/sql/t-sql/statements/create-trigger-transact-sql)を作成します。
* 前のセクションで説明されているインメモリ一時テーブルを使用します。

このような別のシナリオは、非クラスター化列ストア インデックス (NCCI) に書き込む場合に発生します。ここでは、小さい一括挿入で作成されるセグメントが多くなりすぎて、インデックスがクラッシュする可能性があります。 この場合は、代わりにクラスター化列ストア インデックスを使用することをお勧めします。

## <a name="summary"></a>まとめ

まとめると、SQL 出力に対する Azure Stream Analytics でのパーティション分割された出力機能では、SQL Azure 内のパーティション テーブルに合わせて、ジョブの並列化を調整することにより、スループットが大幅に向上します。 インメモリ テーブルからディスク ベースのテーブルへのデータの移動の調整に Azure Data Factory を利用すると、1 桁高いスループットが得られます。 可能であれば、メッセージの密度を上げることも、全体的なスループットの向上において重要な要素になる可能性があります。
