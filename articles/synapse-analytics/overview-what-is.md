---
title: Azure Synapse Analytics とは
description: Azure Synapse Analytics の概要
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: f9277842339d7c4fa74e2e0f1febcf5916d86f4a
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180781"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Azure Synapse Analytics (ワークスペース プレビュー) とは

[!INCLUDE [preview](includes/note-preview.md)]

今日のエンタープライズ分析では、生データ、洗練化されたデータ、高度にキュレーションされたデータなど、あらゆる種類のデータを大規模に操作する必要があります。 従来、エンタープライズでこの種の分析ソリューションを構築する場合、ビッグ データと、Spark や SQL などのデータ ウェアハウジング テクノロジーを組み合わせる必要がありました。 さらに、リレーショナル ストアやデータ レイク内のデータ全体に対して機能する多機能なデータ パイプラインにこれらを統合する必要がありました。  

このようなソリューションは、構築、構成、セキュリティ保護、および保守が難しいため、インテリジェントな分析情報を迅速に抽出するうえで妨げになります。

**Azure Synapse** は、データ ウェアハウスやビッグ データ分析システム全体にわたってあらゆる規模のすべてのデータから分析情報を取得する時間を早める統合分析サービスです。 これは、エンタープライズ データ ウェアハウジングで使用される **SQL** テクノロジー、ビッグ データ分析で使用される **Spark** テクノロジー、そしてデータ統合と ETL/ELT のための**パイプライン**の長所を組み合わせたものです。

Azure Synapse には、管理、監視、コーディング、およびセキュリティのための単一のエクスペリエンスとモデルを提供する、Web ネイティブの **Studio** ユーザー エクスペリエンスが用意されています。

Azure Synapse は、あらゆるサイズのデータについて、エンタープライズが最も使い慣れた分析論で最も簡単かつ迅速に分析情報を収集できる手段を提供します。 **Power BI** と密に統合されるため、データ エンジニアは、エンドツーエンドで動作する分析ソリューションを構築してビジネス インテリジェンスを提供できます。

さらに、Azure Synapse は、**AzureML** の組み込みサポートを通じて、機械学習による予測モデルの構築と高度な分析を容易にします。

## <a name="key-features--benefits"></a>主な機能と利点

### <a name="industry-leading-sql"></a>業界最高水準の SQL

* **Synapse SQL** は、データ エンジニアにとって使い慣れた標準の T-SQL エクスペリエンスを使用して、エンタープライズがデータ ウェアハウジングおよびデータ仮想化シナリオを実装できるようにする分散クエリ システムです。 また、SQL の機能を拡張して、ストリーミングや機械学習のシナリオに対応します。

* Synapse SQL には、**サーバーレス**と**プロビジョニング済み**の両方のリソース モデルが用意されており、ユーザーのニーズに合った使用量および課金のオプションが提供されます。 予測可能なパフォーマンスとコストに対しては、プールをプロビジョニングして、SQL テーブルに格納されているデータの処理能力を確保します。 計画外または突発性のワークロードに対しては、サーバーレスの常に使用可能な SQL エンドポイントを使用します。
* 組み込みの**ストリーミング**機能を使用して、クラウド データ ソースから SQL テーブルにデータを取り込みます
* **機械学習**モデルを使用し、[T-SQL PREDICT 関数](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)を使用してデータをスコア付けすることにより、AI を SQL と統合します

### <a name="industry-standard-apache-spark"></a>業界標準の Apache Spark

**Apache Spark for Azure Synapse** は、Apache Spark と密にシームレスに統合されます。Apache Spark は、データ準備、データ エンジニアリング、ETL、および機械学習に使用される最も人気のあるオープン ソースのビッグ データ エンジンです。

* SparkML アルゴリズムを使用した ML モデルと、Linux Foundation Delta Lake の組み込みサポートを備える Apache Spark 2.4 用の AzureML 統合。
* クラスターの管理について心配する必要からユーザーを解放する、簡素化されたリソース モデル。
* Spark の迅速なスタートアップと積極的な自動スケーリング。
* Spark 向け .NET の組み込みサポートにより、C# の専門知識と既存の .NET コードを Spark アプリケーション内で再利用できます。

### <a name="interop-of-sql-and-apache-spark-on-your-data-lake"></a>データ レイクでの SQL と Apache Spark の相互運用

Azure Synapse は、SQL と Spark を組み合わせて使用する場合の従来のテクノロジーの障害を取り除きます。 ニーズと専門知識に基づいて、シームレスにいろいろな組み合わせを使用することができます。

* 共有の Hive 互換のメタデータ システムにより、データ レイク内のファイルで定義されたテーブルを、Spark または Hive でシームレスに使用できます。
* SQL と Spark を使用して、データ レイクに保存されている Parquet、CSV、TSV、JSON ファイルを直接探索して分析できます。
* SQL データベースと Spark データベース間でやり取りされるデータの高速かつスケーラブルな読み込みとアンロード

### <a name="built-in-data-integration-via-pipelines"></a>パイプラインを介した組み込みのデータ統合

Azure Synapse には Azure Data Factory と同じデータ統合エンジンとエクスペリエンスが組み込まれているため、Synapse Analytics から離れることなく、多機能で大規模な ETL パイプラインを作成できます。

* 90 以上のデータ ソースからデータを取り込む
* データ フロー アクティビティを使用したコードフリーの ETL
* Notebooks、Spark ジョブ、ストアド プロシージャ、SQL スクリプトなどを調整する

### <a name="unified-management-monitoring-and-security"></a>統合された管理、監視、およびセキュリティ

Azure Synapse は、エンタープライズが分析リソースを管理し、使用状況とアクティビティを監視し、セキュリティを適用するための唯一の方法を提供します。

* ユーザーをロールに割り当てて、分析リソースへのアクセスを簡素化
* データとコードに対するきめ細かなアクセス制御
* SQL と Spark 全体のリソース、使用状況、およびユーザーを監視するための単一のダッシュボード

### <a name="synapse-studio"></a>Synapse Studio

**Synapse Studio** は、データ エンジニアのためにすべてを結び付け、完全なソリューションを構築するために必要なすべてのタスクを 1 か所で行うことができるようにする Web ネイティブのエクスペリエンスです。

* エンドツーエンドの分析ソリューションを 1 か所で構築: 取り込み、探索、準備、調整、視覚化
* SQL または Spark コードを記述するデータ エンジニアのための業界最高水準の生産性: オーサリング、デバッグ、およびパフォーマンスの最適化
* エンタープライズ CI/CD プロセスとの統合

## <a name="next-steps"></a>次のステップ

* [ワークスペースを作成する](quickstart-create-workspace.md)
* [Synapse Studio を使用する](quickstart-synapse-studio.md)
* [SQL プールを作成する](quickstart-create-sql-pool-portal.md)
* [SQL オンデマンドを使用する](quickstart-sql-on-demand.md)
* [Apache Spark プールを作成する](quickstart-create-apache-spark-pool-portal.md)
