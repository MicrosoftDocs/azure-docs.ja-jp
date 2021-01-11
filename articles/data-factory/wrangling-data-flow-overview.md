---
title: Azure Data Factory のラングリング データ フロー
description: Azure Data Factory のラングリング データ フローの概要
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 5d0393ab5f2582efa2a4b727543bc799cdb1143e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082870"
---
# <a name="what-are-wrangling-data-flows"></a>ラングリング データ フローとは

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


組織では、日々増え続ける複雑なデータを正確に分析するために、データの準備とラングリングを行う必要があります。 さまざまなビジネス プロセスのデータを使用して、価値創造までの時間を短縮できるようにするには、データの準備が不可欠です。

Azure Data Factory のラングリング データ フローを使用すると、コードなしのデータ準備をクラウド規模で繰り返し実行できます。 ラングリング データ フローは [Power Query Online](https://docs.microsoft.com/power-query/) と統合されているので、Power Query M の関数をデータ ファクトリ ユーザー向けに提供することができます。

ラングリング データ フローを使用すれば、Power Query Online Mashup Editor によって生成された M を、クラウド規模で実行するための Spark コードに変換できます。

ラングリング データ フローは、データ エンジニアや "シティズン データ インテグレーター" にとっては特に便利です。

> [!NOTE]
> ラングリング データ フローは現在パブリック プレビューで提供されています

## <a name="use-cases"></a>ユース ケース

### <a name="fast-interactive-data-exploration-and-preparation"></a>迅速な対話型のデータ探索と準備

複数のデータ エンジニアやシティズン データ インテグレーターが、データセットをクラウド規模で対話的に探索し、準備することができます。 データ レイク内のデータの量、種類、速さが増大するなか、ユーザーは、データ セットを探索して準備するための効果的な方法を必要としています。 たとえば、2017 年以降の新規顧客を対象に、すべての顧客人口統計情報を含んだデータセットを作成するようなケースもあるでしょう。 既知のターゲットにマッピングするのでなければ、 レイクに公開する前に、データセットの探索、ラングリング、準備を行って、要件を満たすことになります。 ラングリング データ フローは、それほど厳格ではない分析シナリオで多く使用されます。 準備されたデータセットは、変換や機械学習のために使用できます。

### <a name="code-free-agile-data-preparation"></a>コードフリーのアジャイルなデータ準備

シティズン データ インテグレーターは、データを探して準備するのに、業務時間の 60% 以上を費やしています。 業務の生産性を向上させるため、彼らはこれらの作業をコーディングなし行う方法を探しています。 シティズン データ インテグレーターが Power Query Online などの既知のツールを使用して、データをスケーラブルに強化、整形、公開できるようになれば、生産性は大幅に向上します。 Azure Data Factory のラングリング データ フローを使用すれば、シティズン データ インテグレーターは使い慣れた Power Query Online マッシュアップ エディターを使用して、エラーを迅速に修正し、データを標準化し、高品質なデータを生成して、ビジネス上の意思決定をサポートできるようになります。

### <a name="data-validation"></a>データ検証

コードを使用せずにデータを視覚的にスキャンして、外れ値や異常を除外し、データを高速分析用に整形することができます。

## <a name="supported-sources"></a>サポートされているソース

| コネクタ | データ形式 | 認証の種類 |
| -- | -- | --|
| [Azure Blob Storage](connector-azure-blob-storage.md) | CSV、Parquet | アカウント キー |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | サービス プリンシパル |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV、Parquet | アカウント キー、サービス プリンシパル |
| [Azure SQL Database](connector-azure-sql-database.md) | - | SQL 認証 |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL 認証 |

## <a name="the-mashup-editor"></a>マッシュアップ エディター

ラングリング データ フローを作成する場合、すべてのソース データがデータセット クエリになり、**ADFResource** フォルダーに配置されます。 既定では、UserQuery は最初のデータセット クエリを指します。 データセット クエリに対する変更はサポートされておらず、永続化もされないため、すべての変換は UserQuery で実行される必要があります。 クエリの名前変更、追加、削除は、現在サポートされていません。

![ラングリング](media/wrangling-data-flow/editor.png)

現時点では、Power Query M 関数は、作成中に利用可能であっても、そのすべてがデータ ラングリングでサポートされているわけではありません。 ラングリング データ フローを構築しているときに、関数がサポートされていない場合は、次のエラー メッセージが表示されます。

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

サポートされている変換の詳細については、[ラングリング データ フローの関数](wrangling-data-flow-functions.md)に関するページを参照してください。

現在、ラングリング データ フローでは、1 つのシンクへの書き込みのみがサポートされています。

## <a name="next-steps"></a>次のステップ

[ラングリング データ フローの作成](wrangling-data-flow-tutorial.md)方法について確認します。
