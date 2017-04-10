---
title: "HDInsight での Hive による JSON ドキュメントの分析および処理 | Microsoft Docs"
description: "HDInsight で Hive を使用した JSON ドキュメントの使用と分析方法について説明します。"
services: hdinsight
documentationcenter: 
author: rashimg
manager: mwinkle
editor: cgronlun
ms.assetid: e17794e8-faae-4264-9434-67f61ea78f13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/22/2015
ms.author: rashimg
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: c292772cb21c90bf4373803bfcaa47787c3980b5
ms.lasthandoff: 03/06/2017


---
# <a name="process-and-analyze-json-documents-using-hive-in-hdinsight"></a>HDInsight の Hive を使用した JSON ドキュメントの処理と分析
HDInsight の Hive を使用して JSON ファイルを処理および分析する方法について説明します。 このチュートリアルでは、次の JSON ドキュメントが使用されます。

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

ファイルは、wasbs://processjson@hditutorialdata.blob.core.windows.net/ で参照できます。 HDInsight での Azure BLOB Storage の使用については、[HDInsight の Hadoop での HDFS と互換性のある Azure BLOB Storage の使用](hdinsight-hadoop-use-blob-storage.md)に関する記事をご覧ください。 必要に応じて、クラスターの既定のコンテナーにファイルをコピーできます。

このチュートリアルでは、Hive コンソールを使用します。  Hive コンソールを開く手順については、「 [リモート デスクトップによる HDInsight での Hive と Hadoop の使用](hdinsight-hadoop-use-hive-remote-desktop.md)」をご覧ください。

## <a name="flatten-json-documents"></a>JSON ドキュメントの平坦化
次のセクションで一覧表示されているメソッドでは、JSON ドキュメントが&1; 行で表示されている必要があります。 このため、JSON ドキュメントを文字列に平坦化する必要があります。 JSON ドキュメントがすでにフラット化されている場合、このステップをスキップして、JSON データの分析に関する次のセクションに直接進むことができます。

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

未加工の JSON ファイルは **wasbs://processjson@hditutorialdata.blob.core.windows.net/** にあります。 *StudentsRaw* Hive テーブルは、未加工の平坦化されていない JSON ドキュメントを指しています。

*StudentsOneLine* Hive テーブルは、*/json/students/* パスの下にある HDInsight の規定のファイル システムにデータを保存します。

INSERT ステートメントは、StudentOneLine テーブルに平坦化された JSON データを取り込みます。

SELECT ステートメントは 1 行のみを返します。

SELECT ステートメントの出力を次に示します。

![JSON ドキュメントのフラット化。][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Hive での JSON ドキュメントの分析
Hive は、JSON ドキュメントに対してクエリを実行するための次の&3; つの異なるメカニズムを提供します。

* GET\_JSON\_OBJECT UDF (ユーザー定義関数) を使用する
* JSON_TUPLE UDF を使用する
* カスタム SerDe を使用する
* Python またはその他の言語を使用して独自の UDF を作成する Hive を使用した独自の Python コードの実行については、[この記事][hdinsight-python]をご覧ください。

### <a name="use-the-getjsonobject-udf"></a>GET\_JSON_OBJECT UDF を使用する
Hive には、ランタイム処理中に JSON クエリを実行できる [get json object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) という組み込み UDF があります。 このメソッドは&2; つの引数を取ります。テーブル名とメソッド名で、解析することが必要なフラット化された JSON ドキュメントと JSON フィールドが含まれます。 この UDF の動作を確認する例を見てみましょう。

各学生の姓と名を取得します。

    SELECT
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
    FROM StudentsOneLine;

このクエリをコンソール ウィンドウで実行した際の出力を次に示します。

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

get-json_object UDF にはいくつかの制限があります。

* クエリ内の各フィールドではクエリの再解析が必要なため、パフォーマンスが影響を受けます。
* GET\_JSON_OBJECT() によって、配列の文字列表現が返されます。 これを Hive 配列に変換するには、正規表現を使用して、角括弧 ‘[‘ と ‘]’ を置き換え、split を呼び出して配列を取得する必要があります。

このため、Hive Wiki では json_tuple の使用が推奨されています。  

### <a name="use-the-jsontuple-udf"></a>JSON_TUPLE UDF を使用する
Hive に備わっているもう&1; つの UDF に [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple) があります。これは、[get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) よりもパフォーマンスに優れています。 このメソッドは、一連のキーと、JSON 文字列を取り、1 つの関数を使用して値のタプルを返します。 次のクエリでは、JSON ドキュメントから、学生 ID とグレードが返されます。

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

Hive コンソールにおけるこのスクリプトの出力:

![json_tuple UDF][image-hdi-hivejson-jsontuple]

JSON\_TUPLE では Hive で [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) 構文が使用されます。この構文を使用すると、json\_tuple は UDT 関数を元のテーブルの各行に適用して仮想テーブルを作成できます。  複雑な JSON では LATERAL VIEW が繰り返し使用されるため、処理が難しくなります。 また、JSON_TUPLE では入れ子になった JSON を処理できません。

### <a name="use-custom-serde"></a>カスタム SerDe を使用する
SerDe は、入れ子になった JSON ドキュメントを解析するのに最適です。これを使用して JSON スキーマを定義し、そのスキーマを使用してドキュメントを解析できます。 このチュートリアルでは、[rcongiu](https://github.com/rcongiu) によって作成された、最も広く使用されている SerDe の&1; つを使用します。

**カスタムの SerDe を使用するには:**

1. [Java SE Development Kit 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR) をインストールします。 HDInsight の Windows デプロイメントを使用する場合は、JDK の Windows X64 バージョンを選択します。
   
   > [!WARNING]
   > JDK 1.8 はこの SerDe では機能しません。
   > 
   > 
   
    インストールが完了したら、新しいユーザーの環境変数を追加します。
   
   1. Windows 画面から **[システムの詳細設定の表示]** を開きます。
   2. **[環境変数]**をクリックします。  
   3. 新しい **JAVA_HOME** 環境変数を追加します。これは、**C:\Program Files\Java\jdk1.7.0_55** または JDK がインストールされている場所を指します。
      
      ![JDK の適切な構成値の設定][image-hdi-hivejson-jdk]
2. [Maven 3.3.1](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip)
   
    [コントロール パネル] からご使用のアカウントの環境変数の [システム環境変数の編集] に移動して、パスに bin フォルダーを追加します。 次のスクリーンショットに、この方法を示します。
   
    ![Maven のセットアップ][image-hdi-hivejson-maven]
3. [Hive-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) github サイトからプロジェクトを複製します。 そのためには、次のスクリーンショットに示すように、[Zip のダウンロード] ボタンをクリックします。
   
    ![プロジェクトの複製][image-hdi-hivejson-serde]

4: このパッケージをダウンロードしたフォルダーに移動して、「mvn package」と入力します。 これにより必要な jar ファイルが作成されるので、クラスターにコピーします。

5: パッケージをダウンロードしたルート フォルダーの下にある対象フォルダーに移動します。 クラスターのヘッド ノードに json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar ファイルをアップロードします。 通常は、Hive バイナリ フォルダーである C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin などに配置します。

6: Hive プロンプトで、「add jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar」と入力します。 この例では jar は C:\apps\dist\hive-0.13.x\bin フォルダーにあるので、次に示す名前の jar を直接追加できます。

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

   ![プロジェクトへの JAR の追加][image-hdi-hivejson-addjar]

これで、SerDe を使用して、JSON ドキュメントに対してクエリを実行する準備ができました。

次のステートメントは、定義されたスキーマでテーブルを作成します。

    DROP TABLE json_table;
    CREATE EXTERNAL TABLE json_table (
      StudentId string,
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
    LOCATION '/json/students';

学生の姓と名を一覧表示するには、次のようにします。

    SELECT StudentDetails.FirstName, StudentDetails.LastName FROM json_table;

Hive コンソールの結果を次に示します。

![SerDe クエリ 1][image-hdi-hivejson-serde_query1]

JSON ドキュメントのスコアの合計を計算するには、次のようにします。

    SELECT SUM(scores)
    FROM json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;

前述のクエリは [lateral view explode](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) UDF を使用してスコアの配列を展開し、合計を計算できます。

Hive コンソールの出力を次に示します。

![SerDe クエリ 2][image-hdi-hivejson-serde_query2]

特定の学生のスコアが 80 点を超えている科目を検索するには、次を選択します。  
      jt.StudentClassCollection.ClassId FROM json_table jt lateral view explode(jt.StudentClassCollection.Score) collection as score  where score > 80;

上記のクエリにより、Hive 配列が戻ります。文字列が返される get\_json\_object とは異なります。

![SerDe クエリ 3][image-hdi-hivejson-serde_query3]

形式が異常な JSON を除外する場合、この SerDe の [wiki ページ](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) で説明されているように以下のコードを入力します。  

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");




## <a name="summary"></a>概要
結論として、Hive で選択する JSON 演算子の種類は、シナリオによって異なります。 JSON ドキュメントが単純で、検索するのが&1; つのフィールドのみの場合には、Hive UDF get\_json\_object を選択できます。 検索対象のキーが複数ある場合には、json_tuple を使用できます。 入れ子になったドキュメントの場合、JSON SerDe を使用する必要があります。

その他の関連記事については、次を参照してください。

* [HDInsight で Hadoop と共に Hive と HiveQL を使用して Apache log4j サンプル ファイルを分析する](hdinsight-use-hive.md)
* [HDInsight での Hive を使用したフライトの遅延データの分析](hdinsight-analyze-flight-delay-data.md)
* [HDInsight での Hive を使用した Twitter データの分析](hdinsight-analyze-twitter-data.md)
* [DocumentDB と HDInsight を使用した Hadoop ジョブの実行](../documentdb/documentdb-run-hadoop-with-hdinsight.md)

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

