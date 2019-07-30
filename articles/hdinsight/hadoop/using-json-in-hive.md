---
title: Azure HDInsight での Apache Hive による JSON ドキュメントの分析および処理
description: JSON ドキュメントの使用方法および Azure HDInsight での Apache Hive による分析方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 5ec766cea2135f7c00df032ad0df4ada033d6293
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461984"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Azure HDInsight での Apache Hive による JSON ドキュメントの処理および分析

Azure HDInsight で Apache Hive を使用して JavaScript Object Notation (JSON) ファイルを処理および分析する方法について説明します。 この記事では、次の JSON ドキュメントを使用します。

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

ファイルは、`wasb://processjson@hditutorialdata.blob.core.windows.net/` で参照できます。 HDInsight での Azure BLOB ストレージの使用方法については、[HDInsight の Apache Hadoop での HDFS と互換性のある Azure BLOB ストレージの使用](../hdinsight-hadoop-use-blob-storage.md)に関する記事をご覧ください。 クラスターの既定のコンテナーにファイルをコピーできます。

この記事では、Apache Hive コンソールを使用します。 Hive コンソールを開く方法については、「[HDInsight 上の Apache Hadoop で Apache Ambari Hive ビューを使用する](apache-hadoop-use-hive-ambari-view.md)」を参照してください。

## <a name="flatten-json-documents"></a>JSON ドキュメントの平坦化
次のセクションで一覧表示されているメソッドでは、JSON ドキュメントが 1 行で構成されている必要があります。 このため、JSON ドキュメントを文字列にフラット化する必要があります。 JSON ドキュメントがすでにフラット化されている場合、このステップをスキップして、JSON データの分析に関する次のセクションに直接進むことができます。 JSON ドキュメントをフラット化するには、次のスクリプトを実行します。

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

未加工の JSON ファイルは `wasb://processjson@hditutorialdata.blob.core.windows.net/` にあります。 **StudentsRaw** Hive テーブルは、未加工のフラット化されていない JSON ドキュメントを指しています。

**StudentsOneLine** Hive テーブルは、 **/json/students/** パスの下にある HDInsight の既定のファイル システムにデータを保存します。

**INSERT** ステートメントは、**StudentOneLine** テーブルにフラット化された JSON データを取り込みます。

**SELECT** ステートメントは 1 行のみを返します。

**SELECT** ステートメントの出力を次に示します。

![JSON ドキュメントのフラット化](./media/using-json-in-hive/flatten.png)

## <a name="analyze-json-documents-in-hive"></a>Hive での JSON ドキュメントの分析
Hive は、JSON ドキュメントに対してクエリを実行するための次の 3 つの異なるメカニズムを提供します。あるいは、独自に記述することもできます。

* get_json_object user-defined 関数 (UDF) を使用します。
* json_tuple UDF を使用します。
* カスタムのシリアライザー/デシリアライザー (SerDe) を使用します。
* Python またはその他の言語を使用して独自の UDF を作成します。 Hive で独自の Python コードを実行する方法の詳細については、[Apache Hive および Apache Pig での Python UDF の使用][hdinsight-python]に関するページを参照してください。

### <a name="use-the-getjsonobject-udf"></a>get_json_object UDF を使用する
Hive には、ランタイム処理中に JSON クエリを実行できる [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) という組み込み UDF があります。 このメソッドでは 2 つの引数 (テーブル名とメソッド名) を取り、解析が必要なフラット化された JSON ドキュメントと JSON フィールドが含まれます。 この UDF の動作を確認する例を見てみましょう。

次のクエリは各学生の姓と名を返します。

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

このクエリをコンソール ウィンドウで実行したときの出力を次に示します。

![get_json_object UDF](./media/using-json-in-hive/getjsonobject.png)

get_json_object UDF には次の制限があります。

* クエリ内の各フィールドではクエリの再解析が必要なため、パフォーマンスに影響が出ます。
* **GET\_JSON_OBJECT()** によって、配列の文字列表現が返されます。 これを Hive 配列に変換するには、正規表現を使用して、角括弧 "[" と "]" を置き換えてから、split を呼び出して配列を取得する必要があります。

このため、Hive Wiki では json_tuple の使用が推奨されています。  

### <a name="use-the-jsontuple-udf"></a>json_tuple UDF を使用する
Hive に備えられたもう 1 つの UDF は [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple) と呼ばれ、[get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) よりもパフォーマンスが優れています。 このメソッドは、一連のキーと、JSON 文字列を取り、1 つの関数を使用して値のタプルを返します。 次のクエリでは、JSON ドキュメントから、学生 ID とグレードが返されます。

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Hive コンソールにおけるこのスクリプトの出力:

![json_tuple UDF](./media/using-json-in-hive/jsontuple.png)

json_tuple UDF では、Hive で [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) 構文を使用します。これによって、json\_tuple は元のテーブルの各行に UDT 関数を適用して、仮想テーブルを作成することができます。 複雑な JSON では **LATERAL VIEW** が繰り返し使用されるため、処理が難しくなります。 また、**JSON_TUPLE** では入れ子になった JSON を処理できません。

### <a name="use-a-custom-serde"></a>カスタム SerDe を使用する
SerDe は、入れ子になった JSON ドキュメントの解析に最適な選択肢です。 SerDe を使用して JSON スキーマを定義し、そのスキーマを使用してドキュメントを解析することができます。 手順については、「[How to use a Custom JSON Serde with Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/)」 (Microsoft Azure HDInsight でのカスタム JSON Serde の使用方法) を参照してください。

## <a name="summary"></a>まとめ
結論として、Hive で選択する JSON 演算子の種類は、シナリオによって異なります。 JSON ドキュメントが単純で、検索するのが 1 つのフィールドのみの場合には、Hive UDF get_json_object を選択できます。 検索対象のキーが複数ある場合には、json_tuple を使用できます。 入れ子になったドキュメントの場合、JSON SerDe を使用する必要があります。

## <a name="next-steps"></a>次の手順

関連記事については、次を参照してください。

* [HDInsight で Apache Hive Hadoop と共に Apache Hive と HiveQL を使用して Apache log4j サンプル ファイルを分析する](../hdinsight-use-hive.md)
* [HDInsight の対話型クエリを使用したフライトの遅延データの分析](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [HDInsight での Apache Hive を使用した Twitter データの分析](../hdinsight-analyze-twitter-data-linux.md)
