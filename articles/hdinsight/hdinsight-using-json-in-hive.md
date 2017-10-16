---
title: "Azure HDInsight での Apache Hive による JSON ドキュメントの分析および処理 | Microsoft Docs"
description: "JSON ドキュメントの使用方法および Azure HDInsight での Apache Hive による分析方法について説明します。"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e17794e8-faae-4264-9434-67f61ea78f13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: jgao
ms.openlocfilehash: 8c013937f7d3d90ed687b5ca9585f0af450774db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Azure HDInsight での Apache Hive による JSON ドキュメントの処理および分析

Azure HDInsight で Apache Hive を使用して JavaScript Object Notation (JSON) ファイルを処理および分析する方法について説明します。 このチュートリアルでは、次の JSON ドキュメントを使用します。

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
                    ]
    }

ファイルは、**wasb://processjson@hditutorialdata.blob.core.windows.net/** で参照できます。 HDInsight での Azure BLOB Storage の使用方法については、[HDInsight の Hadoop での HDFS と互換性のある Azure BLOB Storage の使用](hdinsight-hadoop-use-blob-storage.md)に関する記事をご覧ください。 クラスターの既定のコンテナーにファイルをコピーできます。

このチュートリアルでは、Hive コンソールを使用します。 Hive コンソールを開く方法については、「[リモート デスクトップによる HDInsight での Hive と Hadoop の使用](hdinsight-hadoop-use-hive-remote-desktop.md)」をご覧ください。

## <a name="flatten-json-documents"></a>JSON ドキュメントの平坦化
次のセクションで一覧表示されているメソッドでは、JSON ドキュメントが 1 行で構成されている必要があります。 このため、JSON ドキュメントを文字列にフラット化する必要があります。 JSON ドキュメントがすでにフラット化されている場合、このステップをスキップして、JSON データの分析に関する次のセクションに直接進むことができます。 JSON ドキュメントをフラット化するには、次のスクリプトを実行します。

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

未加工の JSON ファイルは **wasb://processjson@hditutorialdata.blob.core.windows.net/** にあります。 **StudentsRaw** Hive テーブルは、未加工のフラット化されていない JSON ドキュメントを指しています。

**StudentsOneLine** Hive テーブルは、**/json/students/** パスの下にある HDInsight の既定のファイル システムにデータを保存します。

**INSERT** ステートメントは、**StudentOneLine** テーブルにフラット化された JSON データを取り込みます。

**SELECT** ステートメントは 1 行のみを返します。

**SELECT** ステートメントの出力を次に示します。

![JSON ドキュメントのフラット化][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Hive での JSON ドキュメントの分析
Hive は、JSON ドキュメントに対してクエリを実行するための次の 3 つの異なるメカニズムを提供します。あるいは、独自に記述することもできます。

* get_json_object user-defined 関数 (UDF) を使用します。
* json_tuple UDF を使用します。
* カスタムのシリアライザー/デシリアライザー (SerDe) を使用します。
* Python またはその他の言語を使用して独自の UDF を作成します。 Hive で独自の Python コードを実行する方法の詳細については、「[HDInsight における Hive および Pig での Python ユーザー定義関数 (UDF) の使用][hdinsight-python]」を参照してください。

### <a name="use-the-getjsonobject-udf"></a>get_json_object UDF を使用する
Hive には、ランタイム処理中に JSON クエリを実行できる [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) という組み込み UDF があります。 このメソッドでは 2 つの引数 (テーブル名とメソッド名) を取り、解析が必要なフラット化された JSON ドキュメントと JSON フィールドが含まれます。 この UDF の動作を確認する例を見てみましょう。

次のクエリは各学生の姓と名を返します。

    SELECT
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
    FROM StudentsOneLine;

このクエリをコンソール ウィンドウで実行したときの出力を次に示します。

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

get_json_object UDF には次の制限があります。

* クエリ内の各フィールドではクエリの再解析が必要なため、パフォーマンスに影響が出ます。
* **GET\_JSON_OBJECT()** によって、配列の文字列表現が返されます。 これを Hive 配列に変換するには、正規表現を使用して、角括弧 "[" と "]" を置き換えてから、split を呼び出して配列を取得する必要があります。

このため、Hive Wiki では json_tuple の使用が推奨されています。  

### <a name="use-the-jsontuple-udf"></a>json_tuple UDF を使用する
Hive に備えられたもう 1 つの UDF は [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple) と呼ばれ、[get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) よりもパフォーマンスが優れています。 このメソッドは、一連のキーと、JSON 文字列を取り、1 つの関数を使用して値のタプルを返します。 次のクエリでは、JSON ドキュメントから、学生 ID とグレードが返されます。

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

Hive コンソールにおけるこのスクリプトの出力:

![json_tuple UDF][image-hdi-hivejson-jsontuple]

json_tuple UDF では、Hive で [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) 構文を使用します。これによって、json\_tuple は元のテーブルの各行に UDT 関数を適用して、仮想テーブルを作成することができます。 複雑な JSON では **LATERAL VIEW** が繰り返し使用されるため、処理が難しくなります。 また、**JSON_TUPLE** では入れ子になった JSON を処理できません。

### <a name="use-a-custom-serde"></a>カスタム SerDe を使用する
SerDe は、入れ子になった JSON ドキュメントの解析に最適な選択肢です。 SerDe を使用して JSON スキーマを定義し、そのスキーマを使用してドキュメントを解析することができます。 手順については、「[How to use a Custom JSON Serde with Microsoft Azure HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/)」 (Microsoft Azure HDInsight でのカスタム JSON Serde の使用方法) を参照してください。

## <a name="summary"></a>概要
結論として、Hive で選択する JSON 演算子の種類は、シナリオによって異なります。 JSON ドキュメントが単純で、検索するのが 1 つのフィールドのみの場合には、Hive UDF get_json_object を選択できます。 検索対象のキーが複数ある場合には、json_tuple を使用できます。 入れ子になったドキュメントの場合、JSON SerDe を使用する必要があります。

## <a name="next-steps"></a>次のステップ

関連記事については、次を参照してください。

* [HDInsight で Hadoop と共に Hive と HiveQL を使用して Apache log4j サンプル ファイルを分析する](hdinsight-use-hive.md)
* [HDInsight での Hive を使用したフライトの遅延データの分析](hdinsight-analyze-flight-delay-data.md)
* [HDInsight での Hive を使用した Twitter データの分析](hdinsight-analyze-twitter-data.md)
* [Azure Cosmos DB と HDInsight を使用した Hadoop ジョブの実行](../documentdb/documentdb-run-hadoop-with-hdinsight.md)

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png

