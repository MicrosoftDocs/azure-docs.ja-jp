---
title: Delta Lake とは
description: Delta Lake の概要とそれが Azure Synapse Analytics の一部として動作するしくみ
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 23a269fe9498295a3ff325a261c6539f5e1e31aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676220"
---
# <a name="what-is-delta-lake"></a>Delta Lake とは

Azure Synapse Analytics は、Linux Foundation Delta Lake と互換性があります。 Delta Lake は、ACID (原子性、一貫性、分離性、持続性) トランザクションを Apache Spark とビッグ データ ワークロードに導入するオープンソースのストレージ レイヤーです。

Azure Synapse に付属している Delta Lake の現在のバージョンは、Scala、PySpark、.NET の言語サポートを提供しています。 ページの下部には、より詳細な例とドキュメントへのリンクがあります。

## <a name="key-features"></a>主要な機能

| 機能 | 説明 |
| --- | --- |
| **ACID トランザクション** | データ レイクは一般に、複数のプロセスやパイプラインを介してデータが設定されますが、その中には、読み取りと同時にデータを書き込むものがあります。 Delta Lake 以前のトランザクションの追加では、データ エンジニアは、データの整合性を確保するために、手動によるエラーが発生しやすいプロセスを実行する必要がありました。 Delta Lake によって、使い慣れた ACID トランザクションがデータ レイクに導入されます。 これにより、最も強力な分離レベルである直列化可能性が実現されます。 詳細については、「[Delta Lake の詳細: トランザクション ログのアンパック](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html)」を参照してください。|
| **スケーラブルなメタデータの処理** | ビッグ データでは、メタデータ自体も "ビッグ データ" になることがあります。 Delta Lake では、データとまったく同じようにメタデータが扱われ、Spark の分散処理機能を利用して、そのすべてのメタデータが処理されます。 そのため、Delta Lake では、数十億のパーティションやファイルを含むペタバイト規模のテーブルを簡単に処理できます。 |
| **タイム トラベル (データのバージョン管理)** | 変更を "元に戻す" か、以前のバージョンに戻す機能は、トランザクションの重要な機能の 1 つです。 Delta Lake では、データのスナップショットが提供されるため、監査、ロールバック、または実験の再現のために、以前のバージョンのデータに戻すことができます。 詳細については、「[大規模データ レイク向けの Delta Lake タイム トラベルの紹介](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html)」を参照してください。 |
| **オープン形式** | Apache Parquet は Delta Lake のベースライン形式で、形式にネイティブである効率的な圧縮およびエンコード スキームを利用できます。 |
| **バッチとストリーミング ソースとシンクの統合** | Delta Lake のテーブルは、ストリーミング ソースやシンクであるだけでなく、バッチ テーブルでもあります。 ストリーミング データの取り込み、バッチ履歴バックフィル、対話型クエリは、すべてすぐに機能します。 |
| **スキーマの適用** | スキーマの適用によって、データ型が正しく、必要な列が存在することが保証されるため、不正なデータによってデータの不整合が発生することを防止できます。 詳細については、「[Delta Lake の詳細: スキーマの適用と展開](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html)」を参照してください |
| **スキーマの展開** | Delta Lake によって、移行 DDL を記述しなくても、自動的に適用できるテーブル スキーマの変更を行うことができます。 詳細については、「[Delta Lake の詳細: スキーマの適用と展開](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html)」を参照してください |
| **監査履歴** | Delta Lake トランザクション ログでは、データに加えられたすべての変更の詳細が記録され、変更の完全な監査証跡が得られます。 |
| **更新と削除** | Delta Lake では、さまざまな機能のために、Scala、Java、Python、および SQL API がサポートされています。 マージ、更新、削除操作のサポートにより、コンプライアンス要件を満たすために役立ちます。 詳細については、「[Delta Lake 0.6.1 リリースの発表](https://delta.io/news/delta-lake-0-6-1-released/)」、「[Delta Lake 0.7 リリースの発表](https://delta.io/news/delta-lake-0-7-0-released/)」、「[Python API を使用した Delta Lake テーブルのシンプルで信頼性の高い upsert と削除](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html)」を参照してください。これには、マージ、更新、削除 DML コマンドのコード スニペットが記載されています。 |
| **Apache Spark API との 100% の互換性** | Delta Lake は既存の Spark 実装と完全に互換性があるため、開発者は、最小限の変更によって、既存のデータパイプラインで Delta Lake を使用できます。 |

完全なドキュメントについては、[Delta Lake のドキュメント ページ](https://docs.delta.io/latest/delta-intro.html)を参照してください

詳細については、[Delta Lake プロジェクト](https://github.com/delta-io/delta)を参照してください。

## <a name="next-steps"></a>次のステップ

- [.NET for Apache Spark ドキュメント](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)