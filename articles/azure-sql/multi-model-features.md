---
title: マルチモデル機能
description: Microsoft Azure SQL では、同じデータベース内の複数のデータ モデルを使用できます。
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: b16a2fc9f107a8420fb7d05667807a869fa3e00a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172759"
---
# <a name="multi-model-capabilities-of-azure-sql-database--sql-managed-instance"></a>Azure SQL Database と SQL Managed Instance のマルチモデル機能
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

マルチモデル データベースを使用すると、リレーショナル データ、グラフ、JSON または XML ドキュメント、キー/値ペアなどの複数のデータ形式で表されたデータを格納したり、処理したりできます。

## <a name="when-to-use-multi-model-capabilities"></a>どのようなケースでマルチモデル機能を使用するか

[Azure SQL 製品群](azure-sql-iaas-vs-paas-what-is-overview.md)は、さまざまな汎用アプリケーションに対してほとんどの場合に最適なパフォーマンスを提供するリレーショナル モデルで動作するように設計されています。 ただし、Azure SQL 製品群はリレーショナルデータのみに限定されません。 Azure SQL 製品群では、リレーショナル モデルに緊密に統合されているさまざまな非リレーショナル形式を使用できます。
次のようなケースで Azure SQL 製品群のマルチモデル機能の使用を検討します。

- NoSQL モデルに適した情報または構造があるが、別個の NoSQL データベースを使用したくない。
- データの多くがリレーショナル モデルに適しており、データの一部を NoSQL スタイルでモデル化する必要がある。
- 優れた Transact-SQL 言語を活用してリレーショナル データと NoSQL データの両方に対してクエリと分析を実行し、SQL 言語を使用できるさまざまなツールやアプリケーションに Transact-SQL 言語を統合したい。
- [インメモリ テクノロジ](in-memory-oltp-overview.md)などのデータベース機能を適用して NoSQL データ構造の分析や処理のパフォーマンスを改善し、[トランザクション レプリケーション](managed-instance/replication-transactional-overview.md)や[読み取り可能レプリカ](database/read-scale-out.md)を使用して、データのコピーを別の場所に作成し、プライマリ データベースから一部の分析ワークロードをオフロードしたい。

## <a name="overview"></a>概要

Azure SQL 製品群には、次のマルチモデル機能が用意されています。

- [グラフ機能](#graph-features)では、データをノードとエッジのセットとして表現し、グラフ `MATCH` 演算子で強化された標準の Transact-SQL クエリを使用して、グラフ データをクエリすることができます。
- [JSON 機能](#json-features)では、テーブルに JSON ドキュメントを配置し、リレーショナル データから JSON ドキュメントへ、またはその逆方向の変換を行うことができます。 ドキュメントを解析するために JSON 関数で強化された標準の Transact-SQL 言語を使用し、非クラスター化インデックス、列ストア インデックス、または、メモリ最適化テーブルを使用して、クエリを最適化できます。
- [空間機能](#spatial-features)では、地理的および幾何学的データを格納し、空間インデックスを使用してインデックスを作成し、空間クエリを使用してデータを取得することができます。
- [XML 機能](#xml-features)では、データベースに XML データを格納してインデックスを作成し、ネイティブの XQuery/XPath 演算を使用して XML データを処理することができます。 Azure SQL 製品群には、XML データを処理する特殊な XML クエリ エンジンが組み込まれています。
- [キー/値ペア](#key-value-pairs)は、特殊な機能として明示的にはサポートされていません。これは、キー/値ペアを 2 列のテーブルとしてネイティブにモデル化できるためです。

  > [!Note]
  > 同じ Transact-SQL クエリ内で JSON パス式、XQuery/XPath 式、空間関数、およびグラフ クエリ式を使用して、データベースに格納されている任意のデータにアクセスできます。 また、Transact-SQL クエリを実行できるすべてのツールまたはプログラミング言語は、そのクエリ インターフェイスを使用してマルチモデル データにアクセスすることもできます。 これは、[Azure Cosmos DB](../cosmos-db/index.yml) など、さまざまなデータ モデルに対して特殊な API を提供するマルチモデル データベースとの主な違いです。

次のセクションでは、Azure SQL 製品群の最も重要なマルチモデル機能について学習できます。

## <a name="graph-features"></a>グラフ機能

Azure SQL 製品群では、データベース内の多対多リレーションシップをモデル化するグラフ データベース機能が提供されています。 グラフは、ノード (頂点) とエッジ (リレーションシップ) のコレクションです。 ノードはエンティティ (たとえば、個人や組織) を表し、エッジは接続されている 2 つのノード間のリレーションシップ (たとえば、お気に入りや友人) を表します。 グラフ データベースを特徴付けるいくつかの特性を次に示します。

- エッジ (リレーションシップ) は、グラフ データベース内のファースト クラスのエンティティであり、属性またはプロパティを関連付けることができます。
- 1 つのエッジは、グラフ データベース内の複数のノードを柔軟に接続できます。
- パターン マッチングとマルチホップ ナビゲーション クエリを簡単に表現できます。
- 推移閉包およびポリモーフィック クエリを簡単に表現できます。

[グラフ リレーションシップとグラフ クエリ機能](/sql/relational-databases/graphs/sql-graph-overview)は Transact-SQL に統合されており、基本的なデータベース管理システムとして SQL Server データベース エンジンを使用することから恩恵を受けます。

### <a name="when-to-use-a-graph-capability"></a>グラフ機能を使用する場合

リレーショナル データベースを使用して実現できないことのうち、グラフ データベースで実現できることは何もありません。 しかし、グラフ データベースでは、特定のクエリをより簡単に表現することができます。 優先事項の決定は、次の要因に基づいて行うことができます。

- 1 つのノードが複数の親を持つ可能性があるため HierarchyId を使用できない、モデル階層データ
- アプリケーションには、複雑な多対多リレーションシップが含まれます。アプリケーションの進化に伴って、新しいリレーションシップが追加されます。
- 相互接続されたデータとリレーションシップを分析する必要があります。

## <a name="json-features"></a>JSON 機能

Azure SQL 製品群では、JavaScript Object Notation [(JSON)](https://www.json.org/) 形式で表されたデータを解析およびクエリし、リレーショナル データを JSON テキストとしてエクスポートすることができます。

JSON は、最新の Web およびモバイル アプリケーションのデータを交換するために使用される一般的なデータ形式です。 また、JSON は、ログ ファイルや [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) のような NoSQL データベースに半構造化データを格納するためにも使用されます。 REST Web サービスの多くは結果を JSON テキスト形式で返し、データを JSON 形式で受け取ります。 [Azure Cognitive Search](https://azure.microsoft.com/services/search/)、[Azure Storage](https://azure.microsoft.com/services/storage/)、および [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) などの多くの Azure サービスには、JSON を返したり、使用したりする REST エンドポイントがあります。


Azure SQL 製品群では、JSON データを簡単に操作し、お使いのデータベースを最新のサービスと統合できます。また、JSON データを操作するとき、次の機能が与えられます。

![JSON 関数](./media/multi-model-features/image_1.png)

JSON テキストがある場合、組み込み関数 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql)、[JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql)、および [ISJSON](/sql/t-sql/functions/isjson-transact-sql) を使用して、JSON からデータを抽出したり、JSON が適切に書式設定されていることを確認したりすることができます。 [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql) 関数では、JSON テキスト内の値を更新することができます。 高度なクエリと分析には、 [OPENJSON](/sql/t-sql/functions/openjson-transact-sql) 関数を使用して、JSON オブジェクトの配列を行セットに変換できます。 すべての SQL クエリを返された結果セットで実行することができます。 最後に、JSON テキストとしてリレーショナル テーブルに格納されたデータを書式設定できる、 [FOR JSON](/sql/relational-databases/json/format-query-results-as-json-with-for-json-sql-server) 句があります。

詳細については、[JSON データを処理する方法](database/json-features.md)に関するページを参照してください。
[JSON](/sql/relational-databases/json/json-data-sql-server) は SQL Server データベース エンジンの中核となる機能です。

### <a name="when-to-use-a-json-capability"></a>JSON 機能を使用する場合

次の特定のシナリオでは、リレーショナル モデルではなくドキュメント モデルを使用できます。

- オブジェクトのすべてのフィールドに一度にアクセスするために、スキーマの高度な正規化によって大きなメリットが得られないか、またはオブジェクトの正規化された部分を更新することがない場合。 ただし、正規化されたモデルでは、データを取得するために結合する必要のあるテーブルの数が多いため、クエリの複雑さが増します。
- 通信やデータ モデルに対して JSON ドキュメントをネイティブに使用するアプリケーションを使用しており、リレーショナル データから JSON へ、またはその逆の変換を行う追加のレイヤーを導入したくない場合。
- 子テーブルまたはエンティティ/オブジェクト/値パターンの正規化を解除することで、データ モデルを簡略化する必要がある場合。
- データを解析するいくつかの追加のツールを使用せずに、JSON 形式で格納されているデータを読み込むか、またはエクスポートする必要がある場合。

## <a name="spatial-features"></a>空間機能

空間データは、幾何学的オブジェクトの物理的な場所と形状に関する情報を表します。 これらのオブジェクトは、ポイントの場所や、国/地域、道路、湖などのより複雑なオブジェクトである可能性があります。

 サポートされている 2 つの空間データ型: 

- geometry 型では、ユークリッド (平面) 座標系でデータを表します。
- geography 型では、球状の地球座標系でデータを表します。

Azure SQL 製品群で使用できるさまざまな空間オブジェクトがあります。それを利用し、JavaScript Object Notation [(JSON)](https://www.json.org/) 形式で表されたデータを解析およびクエリし、リレーショナル データを JSON テキストとしてエクスポートすることができます。
[Point](/sql/relational-databases/spatial/point)、[LineString](/sql/relational-databases/spatial/linestring)、[Polygon](/sql/relational-databases/spatial/polygon) などです。

Azure SQL 製品群では、空間クエリのパフォーマンスを向上させるために使用できる特殊な[空間インデックス](/sql/relational-databases/spatial/spatial-indexes-overview)も提供されています。

[空間サポート](/sql/relational-databases/spatial/spatial-data-sql-server)は SQL Server データベース エンジンの中核となる機能です。

## <a name="xml-features"></a>XML 機能

SQL Server データベース エンジンでは、半構造化データ管理用の豊富なアプリケーションを開発するための強力なプラットフォームが提供されています。 XML のサポートはデータベース エンジンのあらゆるコンポーネントに統合されており、以下が含まれます。

- XML データ型。 XML 値は、XML スキーマのコレクションに従って型指定できる XML データ型列にネイティブに格納するか、または型指定しないままにすることができます。 XML 列のインデックスを作成できます。
- XML 型の列および変数に格納されている XML データに対して XQuery クエリを指定する機能。 XQuery 機能は、データベースで使用するいずれかのデータ モデルにアクセスするすべての Transact-SQL クエリで使用できます。
- [プライマリ XML インデックス](/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index)を使用して、XML ドキュメント内のすべての要素のインデックスを自動的に作成するか、または[セカンダリ XML インデックス](/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes)を使用して、インデックスを作成する必要のある正確なパスを指定します。
- XML データの一括読み込みを可能にする OPENROWSET。
- リレーショナル データを XML 形式に変換します。

[XML](/sql/relational-databases/xml/xml-data-sql-server) は SQL Server データベース エンジンの中核となる機能です。

### <a name="when-to-use-an-xml-capability"></a>XML 機能を使用する場合

次の特定のシナリオでは、リレーショナル モデルではなくドキュメント モデルを使用できます。

- オブジェクトのすべてのフィールドに一度にアクセスするために、スキーマの高度な正規化によって大きなメリットが得られないか、またはオブジェクトの正規化された部分を更新することがない場合。 ただし、正規化されたモデルでは、データを取得するために結合する必要のあるテーブルの数が多いため、クエリの複雑さが増します。
- 通信やデータ モデルに対して XML ドキュメントをネイティブに使用するアプリケーションを使用しており、リレーショナル データから XML へ、またはその逆の変換を行う追加のレイヤーを導入したくない場合。
- 子テーブルまたはエンティティ/オブジェクト/値パターンの正規化を解除することで、データ モデルを簡略化する必要がある場合。
- データを解析するいくつかの追加のツールを使用せずに、XML 形式で格納されているデータを読み込むか、またはエクスポートする必要がある場合。

## <a name="key-value-pairs"></a>キー/値ペア

Azure SQL 製品群では、キー/値の構造体を標準のリレーショナル テーブルとしてネイティブに表現できるため、キー/値ペアをサポートする特殊な型または構造体がありません。

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

このキー/値の構造体は、何の制約も受けることなく、ニーズに合わせてカスタマイズできます。 たとえば、値に `nvarchar(max)` 型ではなく XML ドキュメントを指定できます。値が JSON ドキュメントである場合、JSON コンテンツの有効性を検証する `CHECK` 制約を指定できます。 たとえば、追加の列内に、1 つのキーに関連する任意の数の値を指定したり、計算列とインデックスを追加してデータ アクセスを簡略化および最適化したり、パフォーマンスを向上させるためにテーブルをメモリ/最適化スキーマ専用テーブルとして定義したりすることができます。

リレーショナル モデルを実際にキー/値ペア ソリューションとして効果的に使用する方法の例としては、[BWin でインメモリ OLTP を使用して前例のないパフォーマンスとスケールを実現する方法](/archive/blogs/sqlcat/how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale)に関する記事に書かれている、1 秒あたり 1.200.000 のバッチを実現した ASP.NET キャッシュ ソリューションの説明を参照してください。

## <a name="next-steps"></a>次のステップ

Azure SQL 製品群のマルチモデル機能は、Azure SQL 製品群の間で共有される、SQL Server データベース エンジンのコア機能でもあります。 これらの機能の詳細については、次の SQL リレーショナル データベース ドキュメントのページを参照してください。

- [グラフの処理](/sql/relational-databases/graphs/sql-graph-overview)
- [JSON データ](/sql/relational-databases/json/json-data-sql-server)
- [空間サポート](/sql/relational-databases/spatial/spatial-data-sql-server)
- [XML データ](/sql/relational-databases/xml/xml-data-sql-server)
