---
title: Azure SQL Database のマルチモデルの機能 | Microsoft Docs
description: Azure SQL Database では、同じデータベース内の複数のデータ モデルを使用できます。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: 4351017cc1848e29cca038f82fd96548ae3492e0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58892468"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Azure SQL Database のマルチモデル機能

マルチモデル データベースを使用すると、リレーショナル データ、グラフ、JSON/XML ドキュメント、キー/値ペアなどの複数のデータ形式で表されたデータを格納したり、処理したりすることができます。

## <a name="when-to-use-multi-model-capabilities"></a>どのようなケースでマルチモデル機能を使用するか

Azure SQL Database は、さまざまな汎用アプリケーションに対してほとんどの場合に最適なパフォーマンスを提供するリレーショナル モデルで動作するように設計されています。 しかし、Azure SQL Database はリレーショナル データのみに限定されません。 Azure SQL Database では、リレーショナル モデルに緊密に統合されているさまざまな非リレーショナル形式を使用できます。
次のようなケースで Azure SQL Database のマルチモデル機能の使用を検討します。
- NoSQL モデルに適した情報または構造があるが、別個の NoSQL データベースを使用したくない。
- データの多くがリレーショナル モデルに適しており、データの一部を NoSQL スタイルでモデル化する必要がある。
- 優れた Transact-SQL 言語を活用してリレーショナル データと NoSQL データの両方に対してクエリと分析を実行し、SQL 言語を使用できるさまざまなツールやアプリケーションに Transact-SQL 言語を統合したい。
- [インメモリ テクノロジー](sql-database-in-memory.md)などのデータベース機能を適用して NoSQL データ構造の分析や処理のパフォーマンスを改善し、[トランザクション レプリケーション](sql-database-managed-instance-transactional-replication.md)や[読み取り可能レプリカ](sql-database-read-scale-out.md)を使用して、データのコピーを別の場所に作成し、プライマリ データベースから一部の分析ワークロードをオフロードしたい。

## <a name="overview"></a>概要

Azure SQL では、次のマルチモデル機能が提供されています。
- [グラフ機能](#graph-features)では、データをノードとエッジのセットとして表現し、グラフ `MATCH` 演算子で強化された標準の Transact-SQL クエリを使用して、グラフ データをクエリすることができます。
- [JSON 機能](#json-features)では、テーブルに JSON ドキュメントを配置し、リレーショナル データから JSON ドキュメントへ、またはその逆方向の変換を行うことができます。 ドキュメントを解析するために JSON 関数で強化された標準の Transact-SQL 言語を使用し、非クラスター化インデックス、列ストア インデックス、または、メモリ最適化テーブルを使用して、クエリを最適化できます。
- [空間機能](#spatial-features)では、地理的および幾何学的データを格納し、空間インデックスを使用してインデックスを作成し、空間クエリを使用してデータを取得することができます。
- [XML 機能](#xml-features)では、データベースに XML データを格納してインデックスを作成し、ネイティブの XQuery/XPath 演算を使用して XML データを処理することができます。 Azure SQL Database には、XML データを処理する特殊な XML クエリ エンジンが組み込まれています。
- [キー/値ペア](#key-value-pairs)は、特殊な機能として明示的にはサポートされていません。これは、キー/値ペアを 2 列のテーブルとしてネイティブにモデル化できるためです。

  > [!Note]
  > 同じ Transact-SQL クエリ内で JSON パス式、XQuery/XPath 式、空間関数、およびグラフ クエリ式を使用して、データベースに格納されている任意のデータにアクセスできます。 また、Transact-SQL クエリを実行できるすべてのツールまたはプログラミング言語は、そのクエリ インターフェイスを使用してマルチモデル データにアクセスすることもできます。 これは、[Azure Cosmos DB](/azure/cosmos-db/) など、さまざまなデータ モデルに対して特殊な API を提供するマルチモデル データベースとの主な違いです。

次のセクションでは、Azure SQL Database の最も重要なマルチモデル機能について学習できます。

## <a name="graph-features"></a>グラフ機能

Azure SQL Database では、データベース内の多対多リレーションシップをモデル化するグラフ データベース機能が提供されています。 グラフは、ノード (頂点) とエッジ (リレーションシップ) のコレクションです。 ノードはエンティティ (たとえば、個人や組織) を表し、エッジは接続されている 2 つのノード間のリレーションシップ (たとえば、お気に入りや友人) を表します。 グラフ データベースを特徴付けるいくつかの特性を次に示します。
- エッジ (リレーションシップ) は、グラフ データベース内のファースト クラスのエンティティであり、属性またはプロパティを関連付けることができます。
- 1 つのエッジは、グラフ データベース内の複数のノードを柔軟に接続できます。
- パターン マッチングとマルチホップ ナビゲーション クエリを簡単に表現できます。
- 推移閉包およびポリモーフィック クエリを簡単に表現できます。

グラフ リレーションシップとグラフ クエリ機能は Transact-SQL に統合されており、基本的なデータベース管理システムとして SQL Server を使用することから恩恵を受けます。
[グラフ処理](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)は SQL Server データベース エンジンのコア機能であり、このリンクからグラフ処理に関する詳細情報を参照できます。

### <a name="when-to-use-a-graph-capability"></a>グラフ機能を使用する場合

リレーショナル データベースを使用して実現できないことのうち、グラフ データベースで実現できることは何もありません。 しかし、グラフ データベースでは、特定のクエリをより簡単に表現することができます。 優先事項の決定は、次の要因に基づいて行うことができます。

- 1 つのノードが複数の親を持つ可能性があるため HierarchyId を使用できない、モデル階層データ
- アプリケーションには、複雑な多対多リレーションシップが含まれます。アプリケーションの進化に伴って、新しいリレーションシップが追加されます。
- 相互接続されたデータとリレーションシップを分析する必要があります。

## <a name="json-features"></a>JSON 機能

Azure SQL Database では、JavaScript Object Notation [(JSON)](https://www.json.org/) 形式で表されたデータを解析およびクエリし、リレーショナル データを JSON テキストとしてエクスポートすることができます。

JSON は、最新の Web およびモバイル アプリケーションのデータを交換するために使用される一般的なデータ形式です。 また、JSON は、ログ ファイルや [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) のような NoSQL データベースに半構造化データを格納するためにも使用されます。 多くの REST Web サービスは、JSON テキストとして書式設定された結果を返したり、JSON 形式のデータを受け入れたりします。 [Azure Search](https://azure.microsoft.com/services/search/)、[Azure Storage](https://azure.microsoft.com/services/storage/)、および [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) などの多くの Azure サービスには、JSON を返したり、使用したりする REST エンドポイントがあります。

Azure SQL Database では、JSON データを簡単に操作し、データベースを最新のサービスと統合することができます。 Azure SQL Database には、JSON データを操作するために、次の関数が用意されています。

![JSON 関数](./media/sql-database-json-features/image_1.png)

JSON テキストがある場合、組み込み関数 [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx)、[JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)、および [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx) を使用して、JSON からデータを抽出したり、JSON が適切に書式設定されていることを確認したりすることができます。 [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) 関数では、JSON テキスト内の値を更新することができます。 高度なクエリと分析には、 [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) 関数を使用して、JSON オブジェクトの配列を行セットに変換できます。 すべての SQL クエリを返された結果セットで実行することができます。 最後に、JSON テキストとしてリレーショナル テーブルに格納されたデータを書式設定できる、 [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) 句があります。

詳細については、[Azure SQL Database で JSON データを処理する方法](sql-database-json-features.md)に関するページを参照してください。
[JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) は、SQL Server データベース エンジンのコア機能であり、このリンクから JSON 機能に関する詳細情報を参照できます。

### <a name="when-to-use-a-json-capability"></a>JSON 機能を使用する場合

次の特定のシナリオでは、リレーショナル モデルではなくドキュメント モデルを使用できます。
- オブジェクトのすべてのフィールドに一度にアクセスするために、スキーマの高度な正規化によって大きなメリットが得られないか、またはオブジェクトの正規化された部分を更新することがない場合。 ただし、正規化されたモデルでは、データを取得するために結合する必要のあるテーブルの数が多いため、クエリの複雑さが増します。
- 通信やデータ モデルに対して JSON ドキュメントをネイティブに使用するアプリケーションを使用しており、リレーショナル データから JSON へ、またはその逆の変換を行う追加のレイヤーを導入したくない場合。
- 子テーブルまたはエンティティ/オブジェクト/値パターンの正規化を解除することで、データ モデルを簡略化する必要がある場合。
- データを解析するいくつかの追加のツールを使用せずに、JSON 形式で格納されているデータを読み込むか、またはエクスポートする必要がある場合。

## <a name="spatial-features"></a>空間機能

空間データは、幾何学的オブジェクトの物理的な場所と形状に関する情報を表します。 これらのオブジェクトは、ポイントの場所や、国、道路、湖などのより複雑なオブジェクトである可能性があります。

Azure SQL Database では、geometry データ型と geography データ型の 2 つの空間データ型がサポートされています。
- geometry 型では、ユークリッド (平面) 座標系でデータを表します。
- geography 型では、球状の地球座標系でデータを表します。

などの Azure SQL Database で使用できる空間オブジェクトには、[Point](https://docs.microsoft.com/sql/relational-databases/spatial/point)、[LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring)、[Polygon](https://docs.microsoft.com/sql/relational-databases/spatial/polygon) などさまざまなものがあります。

Azure SQL Database では、空間クエリのパフォーマンスを向上させるために使用できる特殊な[空間インデックス](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview)も提供されています。

[空間サポート](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)は、SQL Server データベース エンジンのコア機能であり、このリンクから空間機能に関する詳細情報を参照できます。

## <a name="xml-features"></a>XML 機能

SQL Server では、半構造化データ管理用の豊富なアプリケーションを開発するための強力なプラットフォームが提供されています。 XML のサポートは、次のような SQL Server のすべてのコンポーネントに統合されています。

- XML データ型。 XML 値は、XML スキーマのコレクションに従って型指定できる XML データ型列にネイティブに格納するか、または型指定しないままにすることができます。 XML 列のインデックスを作成できます。
- XML 型の列および変数に格納されている XML データに対して XQuery クエリを指定する機能。 XQuery 機能は、データベースで使用するいずれかのデータ モデルにアクセスするすべての Transact-SQL クエリで使用できます。
- [プライマリ XML インデックス](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index)を使用して、XML ドキュメント内のすべての要素のインデックスを自動的に作成するか、または[セカンダリ XML インデックス](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes)を使用して、インデックスを作成する必要のある正確なパスを指定します。
- XML データの一括読み込みを可能にする OPENROWSET。
- リレーショナル データを XML 形式に変換します。

[XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) は、SQL Server データベース エンジンのコア機能であり、このリンクから XML 機能に関する詳細情報を参照できます。

### <a name="when-to-use-an-xml-capability"></a>XML 機能を使用する場合

次の特定のシナリオでは、リレーショナル モデルではなくドキュメント モデルを使用できます。
- オブジェクトのすべてのフィールドに一度にアクセスするために、スキーマの高度な正規化によって大きなメリットが得られないか、またはオブジェクトの正規化された部分を更新することがない場合。 ただし、正規化されたモデルでは、データを取得するために結合する必要のあるテーブルの数が多いため、クエリの複雑さが増します。
- 通信やデータ モデルに対して XML ドキュメントをネイティブに使用するアプリケーションを使用しており、リレーショナル データから XML へ、またはその逆の変換を行う追加のレイヤーを導入したくない場合。
- 子テーブルまたはエンティティ/オブジェクト/値パターンの正規化を解除することで、データ モデルを簡略化する必要がある場合。
- データを解析するいくつかの追加のツールを使用せずに、XML 形式で格納されているデータを読み込むか、またはエクスポートする必要がある場合。

## <a name="key-value-pairs"></a>キー/値ペア

Azure SQL Database では、キー/値の構造体を標準のリレーショナル テーブルとしてネイティブに表現できるため、キー/値ペアをサポートする特殊な型または構造体がありません。

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

このキー/値の構造体は、何の制約も受けることなく、ニーズに合わせてカスタマイズできます。 たとえば、値に `nvarchar(max)` 型ではなく XML ドキュメントを指定できます。値が JSON ドキュメントである場合、JSON コンテンツの有効性を検証する `CHECK` 制約を指定できます。 たとえば、追加の列内に、1 つのキーに関連する任意の数の値を指定したり、計算列とインデックスを追加してデータ アクセスを簡略化および最適化したり、パフォーマンスを向上させるためにテーブルをメモリ/最適化スキーマ専用テーブルとして定義したりすることができます。

リレーショナル モデルを実際にキー/値ペア ソリューションとして効果的に使用する方法の例としては、[BWin でインメモリ OLTP を使用して前例のないパフォーマンスとスケールを実現する方法](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/)に関する記事に書かれている、1 秒あたり 1.200.000 のバッチを実現した ASP.NET キャッシュ ソリューションの説明を参照してください。

## <a name="next-steps"></a>次の手順
Azure SQL Database のマルチモデル機能は、Azure SQL Database と SQL Server の間で共有される、SQL Server データベース エンジンのコア機能でもあります。 これらの機能の詳細については、次の SQL リレーショナル データベース ドキュメントのページを参照してください。

* [グラフの処理](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [JSON データ](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [空間サポート](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [XML データ](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
