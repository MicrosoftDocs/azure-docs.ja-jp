---
title: Azure Data Lake Analytics U-SQL 開発者向けの Apache Spark コードの概念について説明します。
description: この記事では、U-SQL 開発者が、Spark コードの概念を理解する上で役立つ Apache Spark の概念について説明します。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: 2abd5882e310b17c633a82009f44624fad156f14
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221130"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>U-SQL 開発者向けの Apache Spark コードについて

このセクションでは、U-SQL スクリプトの Apache Spark への変換に関する概要を説明します。

- まず、[2 つの言語の処理パラダイムを比較](#understand-the-u-sql-and-spark-language-and-processing-paradigms)することから始めます
- 次の方法に関するヒントを提供します。
   - U-SQL の[行セット式](#transform-u-sql-scripts)を含む[スクリプトを変換する](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions)
   - [.NET コード](#transform-net-code)
   - [データ型](#transform-typed-values)
   - [カタログ オブジェクト](#transform-u-sql-catalog-objects)

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>U-SQL および Spark 言語と処理パラダイムを理解する

Azure Data Lake Analytics の U-SQL スクリプトを Spark に移行する前に、これら 2 つのシステムの一般的な言語と処理の理念を理解しておくと役に立ちます。

U-SQL は、データフロー パラダイムを使用する SQL に似た宣言型クエリ言語であり、.NET で記述されたユーザーコード (たとえば C#)、Python、R を簡単に埋め込んだりスケールアウトしたりすることができます。ユーザー拡張機能を使用すると、単純式またはユーザー定義関数を実装できるだけでなく、カスタム演算子を実装するユーザー定義演算子を実装することで、ユーザーが行セットレベルの変換、抽出、および出力の書き込みを実行できるようになります。

Spark は、Scala、Java、Python、.NET などの複数の言語バインディングを提供するスケールアウト フレームワークです。主にこれらの言語のいずれかでコードを記述し、Resilient Distributed Datasets (RDD)、データフレーム、およびデータセットと呼ばれるデータの抽象化を作成し、その後 LINQ に似たドメイン固有言語 (DSL) を使用して変換します。 また、Spark は宣言サブ言語としてデータフレームとデータセットの抽象化で SparkSQL を提供します。 DSL には、変換とアクションの 2 つの操作カテゴリがあります。 データの抽象化に変換を適用しても変換は実行されませんが、代わりに、アクションにより送信される評価用の実行プランが作成されます (たとえば、一時テーブルやファイルに結果を書き込んだり、結果の印刷など)。

このため、U-SQL スクリプトを Spark プログラムに変換する場合、少なくともデータフレームの抽象化 (現在最も頻繁に使用されているデータ抽象化) を生成するために使用する言語と、DSL または SparkSQL のどちらを使用して宣言型データフロー変換を行うかどうかを決定する必要があります。 さらに複雑な一部のケースでは、U-SQL スクリプトを Azure Batch または Azure Functions で実装された一連の Spark とその他の手順に分割する必要があります。

さらに、Azure Data Lake Analytics はサーバーレス ジョブサービス環境でも U-SQL を提供していますが、Azure Databricks と Azure HDInsight の両方でクラスター サービスの形で Spark を提供しています。 アプリケーションを変換する際には、クラスターの作成、サイズ変更、スケーリング、および使用停止の影響について考慮する必要があります。

## <a name="transform-u-sql-scripts"></a>U-SQL スクリプトの変換

U-SQL スクリプトは、次の処理パターンに従います。

1. データは、場所またはファイルセットを指定する `EXTRACT` ステートメント、組み込みまたはユーザー定義のエクストラクターおよび必要なスキーマを使用して非構造化ファイルから読み取られるか、U-SQL テーブル (マネージテーブルまたは外部テーブル) から読み取られます。 これは行セットとして表されます。
2. 行セットは、行セットに U-SQL 式を適用し、新しい行セットを生成する複数の U-SQL ステートメントで変換されます。
3. 最後に、結果の行セットは、場所、組み込みまたはユーザー定義アウトプッターを指定する `OUTPUT` ステートメントと、組み込みまたはユーザー定義アウトプッター、あるいは U-SQL テーブルを使用していずれかのファイルに出力されます。

スクリプトは遅延評価されます。つまり、抽出と変換の各手順は、式ツリーに構成され、グローバルに評価されます (データフロー)。

Spark プログラムは、spark コネクタを使用してデータを読み取り、データフレームを作成した後、LINQ に似た DSL または SparkSQL を使用してデータフレームに変換を適用し、その結果をファイル、一時 Spark テーブル、プログラミング言語の種類、またはコンソールに書き込むという点で似ています。

## <a name="transform-net-code"></a>.NET コードの変換

U-SQL の式言語の C# は、カスタム .NET コードをスケールアウトするためのさまざまな方法を提供します。

現在、Spark では .NET コードの実行がネイティブにサポートされていないため、式を同等の Spark、Scala、Java、または Python の式に書き直すか、.NET コードを呼び出す方法を見つける必要があります。 スクリプトで .NET ライブラリを使用する場合は、次のオプションがあります。

- .NET コードを、Scala または Python に変換します。
- Azure Batch プロセスを使用して .NET 変換を適用するいくつかの手順に U-SQL スクリプトを分割します (許容されるスケールを取得できる場合)
- Moebius と呼ばれるオープンソースで利用可能な .NET 言語バインドを使用します。 このプロジェクトはサポートを受けられる状態ではありません。

いずれの場合も、U-SQL スクリプトに大量の .NET ロジックがある場合は、詳しいガイダンスについて Microsoft アカウントの担当者にご連絡ください。

ここでは、U-SQL スクリプトでの .NET と C# のさまざまな使用方法について詳しく説明します。

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>スカラー インライン U-SQL C#式の変換

U-SQL の式言語は C# です。 スカラー インラインの U-SQL 式の多くは、パフォーマンスを向上させるためにネイティブに実装されていますが、.NET フレームワークを呼び出すことでより複雑な式を実行できます。

Spark には、独自のスカラー式言語 (DSL または SparkSQL の一部) があり、そのホスト言語で記述されたユーザー定義関数を呼び出すことができます。

U-SQL にスカラー式が含まれている場合は、パフォーマンスを最大限に引き出すために、ネイティブに認識されている最も適切な Spark スカラー式を見つけてから、選択した Spark ホスティング言語のユーザー定義関数に他の式をマップする必要があります。

.NET と Spark C# は、Spark のホスティング言語および Spark の DSL とは異なる型セマンティクスを持つことに注意してください。 型システムの違いの詳細については、[以下](#transform-typed-values)を参照してください。

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>ユーザー定義のスカラー .NET 関数とユーザー定義のアグリゲーターを変換する

U-SQL は、任意のスカラー .NET 関数を呼び出し、.NET で記述されたユーザー定義のアグリゲーターを呼び出します。

Spark は、Spark の DSL と SparkSQL から呼び出すことができる、ほとんどのホスティング言語で記述されたユーザー定義関数およびユーザー定義のアグリゲーターもサポートしています。

### <a name="transform-user-defined-operators-udos"></a>ユーザー定義演算子 (UDO) の変換

U-SQL には、エクストラクター、アウトプッター、レジューサー、プロセッサー、アプライヤー、および .NET (若干の Python と R) で記述できるコンバイナーなど、ユーザー定義演算子 (UDO) のカテゴリがいくつか用意されています。

Spark は演算子に対して同じ機能拡張モデルを提供しませんが、一部では同等の機能を備えています。

Spark でエクストラクターとアウトプッターと同等の機能を持つのが、Spark コネクタです。 多くの U-SQL エクストラクターは、Spark コミュニティに同等のコネクタが存在する可能性があります。 その他の場合は、カスタム コネクタを作成する必要があります。 U-SQL エクストラクターが複雑で、複数の .NET ライブラリを使用している場合は、相互運用機能を使用してデータ処理を実際に行う .NET ライブラリを呼び出すコネクタを、Scala で作成することをお勧めします。 その場合は、.NET Core ランタイムを Spark クラスターにデプロイし、参照されている .NET ライブラリが .NET Standard 2.0 に準拠していることを確認する必要があります。

その他の種類の U-SQL UDO は、ユーザー定義関数とユーザー定義のアグリゲーター、および意味的に適している Spark DLS または SparkSQL 式を使用して書き換える必要があります。 たとえば、プロセッサは、データフレームを引数として受け取り、データフレームを返す関数としてパッケージ化された、さまざまな UDF 呼び出しの SELECT にマップできます。

### <a name="transform-u-sqls-optional-libraries"></a>U-SQL のオプションのライブラリを変換する

U-SQL には、[Python](data-lake-analytics-u-sql-python-extensions.md)、[R](data-lake-analytics-u-sql-r-extensions.md)、[JSON、XML、AVRO サポート](https://github.com/Azure/usql/tree/master/Examples/DataFormats)、および一部の [Cognitive Services の機能](data-lake-analytics-u-sql-cognitive.md)が利用できる一連のオプションのデモ ライブラリが用意されています。

Spark はそれぞれ独自の Python と R の統合、pySpark、SparkR を提供し、JSON、XML、AVRO の読み取りと書き込みのためのコネクタを提供します。

Cognitive Services ライブラリを参照するスクリプトを変換する必要がある場合は、Microsoft アカウント担当者に連絡することをお勧めします。

## <a name="transform-typed-values"></a>型指定された値を変換する

U-SQL の型システムは .NET 型システムに基づいており、Spark にはホスト言語のバインドに影響を受ける独自の型システムがあるため、扱っている型が近く、また一部の型では型の範囲、有効桁数、または小数点以下桁数が多少異なる場合があります。 さらに、U-SQL と Spark では `null` の値が異なる方法で処理されます。

### <a name="data-types"></a>データ型

次の表は、指定された U-SQL の型に対応する Spark、Scala、PySpark の各型を示しています。

| U-SQL | Spark |  Scala | PySpark |
| ------ | ------ | ------ | ------ |
|`byte`       ||||
|`sbyte`      |`ByteType` |`Byte` | `ByteType`|
|`int`        |`IntegerType` |`Int` | `IntegerType`|
|`uint`       ||||
|`long`       |`LongType` |`Long` | `LongType`|
|`ulong`      ||||
|`float`      |`FloatType` |`Float` | `FloatType`|
|`double`     |`DoubleType` |`Double` | `DoubleType`|
|`decimal`    |`DecimalType` |`java.math.BigDecimal` | `DecimalType`|
|`short`      |`ShortType` |`Short` | `ShortType`|
|`ushort`     ||||
|`char`   | |`Char`||
|`string` |`StringType` |`String` |`StringType` |
|`DateTime`   |`DateType`, `TimestampType` |`java.sql.Date`, `java.sql.Timestamp` | `DateType`, `TimestampType`|
|`bool`   |`BooleanType` |`Boolean` | `BooleanType`|
|`Guid`   ||||
|`byte[]` |`BinaryType` |`Array[Byte]` | `BinaryType`|
|`SQL.MAP<K,V>`   |`MapType(keyType, valueType, valueContainsNull)` |`scala.collection.Map` | `MapType(keyType, valueType, valueContainsNull=True)`|
|`SQL.ARRAY<T>`   |`ArrayType(elementType, containsNull)` |`scala.collection.Seq` | `ArrayType(elementType, containsNull=True)`|

詳細については、次を参照してください。

- [org.apache.spark.sql.types](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [Spark SQL および DataFrames 型](https://spark.apache.org/docs/latest/sql-ref-datatypes.html)
- [Scala 値の型](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark.sql.types](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>NULL の扱いについて

Spark では、既定の型ごとに NULL 値が許可されますが、その際、U-SQL では、スカラー、非オブジェクトを null 許容として明示的にマークします。 Spark では、列を null を許容しないものとして定義できますが、制約は適用されず、[これによって結果が間違ってしまう可能性があります](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836)。

Spark において、NULL は値が認識できないことを示します。 Spark NULL 値は、それ自体を含む任意の値とは異なります。 2 つの Spark NULL 値、または Spark NULL 値と他の値を比較する場合、どの NULL も認識できないので、認識できないことを示す値が返されます。  

この動作は、`null` がそれ自体以外のどの値とも異なる C#セマンティクスに従う、U-SQL とは異なります。  

したがって、`WHERE column_name = NULL` を使用する SparkSQL `SELECT` ステートメントでは、`column_name` に NULL 値がある場合でも 0 行が返され、U-SQL では、`column_name` が `null` に設定されている行が返されます。 同様に、`WHERE column_name != NULL` を使用する Spark `SELECT` ステートメントでは、`column_name` に 非null 値がある場合でも 0 行が返され、U-SQL では、非 null に設定されている行が返されます。 したがって、U-SQL の null チェック セマンティクスが必要な場合は、[isnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) および [isnotnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull) (または DSL 同等) をそれぞれ使用する必要があります。

## <a name="transform-u-sql-catalog-objects"></a>U-SQL カタログ オブジェクトを変換する

大きな違いの 1 つは、U-SQL スクリプトでは、直接的に Spark に同等のものが存在しない独自のカタログ オブジェクトを利用できることです。

Spark では、U-SQL データベースとスキーマを Hive データベースに、および U-SQL テーブルを Spark テーブルにマップできるよう、Hive メタストアの概念 (主にデータベースとテーブル) がサポートされています ([U-UQL テーブルに格納されているデータの移動](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)に関する記事を参照)。ただし、ビュー、テーブル値関数 (TVF)、ストアド プロシージャ、U-SQL アセンブリ、外部データソースなどはサポートされていません。

ビュー、TVF、ストアド プロシージャ、アセンブリなどの U-SQL コード オブジェクトは、Spark のコード関数とライブラリを使用してモデル化し、ホスト言語の関数と手続き型の抽象化メカニズムを使用して (たとえば、Python モジュールのインポートや Scala 関数の参照などを通じて) 参照できます。

プロジェクトおよびチーム間でデータとコード オブジェクトを共有するために U-SQL カタログが使用されている場合は、共有用の同等のメカニズムを使用する必要があります (たとえば、コード オブジェクトを共有するための Maven など)。

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>U-SQL 行セット式と SQL ベースのスカラー式を変換する

U-SQL のコア言語は、行セットを変換し、SQL に基づいています。 次に、U-SQL で提供される最も一般的な行セット式の一覧を示します (すべてではありません)。

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+ 集計 +`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI` `JOIN` 式
- `CROSS`/`OUTER` `APPLY` 式
- `PIVOT`/`UNPIVOT` 式
- `VALUES` 行セット コンストラクター

- 設定式 `UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

さらに、U-SQL には、次のような SQL ベースのさまざまなスカラー式が用意されています。

- `OVER` ウィンドウ化関数
- さまざまな組み込みのアグリゲーターおよび順位付け関数 (`SUM`、`FIRST` など)
- 最も一般的な SQL スカラー式の一部 (`CASE`、`LIKE`、(`NOT`) `IN`、`AND`、`OR` など)。

Spark は、これらの式のほとんどに対して、DSL と SparkSQL の両方の形式で同等の式を提供します。 Spark でネイティブにサポートされていない式の一部は、ネイティブの Spark 式と意味的に同等なパターンを組み合わせて書き換える必要があります。 たとえば、`OUTER UNION` は、プロジェクションと和集合の同等の組み合わせに変換する必要があります。

NULL 値の処理方法が異なるため、比較対象の列の両方に NULL 値が含まれていている場合 U-SQL 結合は常に行と一致し、Spark の結合は明示的な null チェックが追加されない限り、このような列に一致しません。

## <a name="transform-other-u-sql-concepts"></a>その他の U-SQL の概念を変換する

U-SQL は、SQL Server データベースに対するフェデレーションクエリ、パラメーター、スカラー、およびラムダ式の変数、システム変数、`OPTION` ヒントなど、他のさまざまな機能や概念も提供します。

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>SQL Server データベース/外部テーブルに対するフェデレーション クエリ

U-SQL では、データ ソースと外部テーブルだけでなく Azure SQL Database に対する直接クエリも可能です。 Spark は同じオブジェクトの抽象化を提供しませんが、SQL データベースのクエリに使用できる [SQL Server 用の Spark コネクタ](../azure-sql/database/spark-connector.md)を提供します。

### <a name="u-sql-parameters-and-variables"></a>U-SQL パラメーターと変数

Spark とそのホスト言語では、パラメーターとユーザー変数の概念は同じです。

たとえば、Scala では次のように `var` キーワードを使用して変数を定義できます。

```
var x = 2 * 3;
println(x)
```

U-SQL のシステム変数 (`@@` で始まる変数) は、次の 2 つのカテゴリに分けることができます。

- スクリプトの動作に影響を与えるように特定の値に設定できる設定可能なシステム変数
- システムおよびジョブ レベルの情報を照会する情報システム変数

設定可能なシステム変数のほとんどは、Spark に直接対応していません。 情報システム変数の中には、ジョブの実行中に引数として情報を渡すことによってモデル化できるものもあれば、Spark のホスト言語で同等の機能が利用できるものもあります。

### <a name="u-sql-hints"></a>U-SQL のヒント

U-SQL では、クエリ オプティマイザーと実行エンジンにヒントを与える構文方法がいくつか提供されています。  

- U-SQL システム変数を設定する
- 行セット式に関連付けることでデータまたはプランのヒントを提供する `OPTION` 句
- JOIN 式の構文の結合ヒント (`BROADCASTLEFT` など)

Spark のコストベースのクエリ オプティマイザーには、ヒントを提供し、クエリのパフォーマンスを調整するための独自の機能があります。 対応するドキュメントをご覧ください。

## <a name="next-steps"></a>次のステップ

- [U-SQL 開発者向けの Spark データ形式について](understand-spark-data-formats.md)
- [.NET for Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [ビッグ データ分析ソリューションを Azure Data Lake Storage Gen1 から Azure Data Lake Storage Gen2 にアップグレードする](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [Azure Data Factory での Spark アクティビティを使用したデータの変換](../data-factory/transform-data-using-spark.md)
- [Azure Data Factory で Hadoop Hive アクティビティを使用してデータを変換する](../data-factory/transform-data-using-hadoop-hive.md)
- [Apache Spark とは - Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)