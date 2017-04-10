---
title: "Hive クエリを使用して Hive テーブルのデータを探索する | Microsoft Docs"
description: "Hive クエリを使用して Hive テーブルのデータを探索します。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 0d46cea5-2b4c-4384-9bfa-fa20f6f75148
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 7795e6fd5cc175e3a0cc91e35a5b1900fbeb2fdc
ms.lasthandoff: 03/29/2017


---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Hive クエリを使用して Hive テーブルのデータを探索する
このドキュメントでは、HDInsight Hadoop クラスター内の Hive テーブルのデータを探索する Hive スクリプトの例を紹介します。

次の **メニュー** は、ツールを使用してさまざまなストレージ環境のデータを探索する方法を説明するトピックにリンクしています。

[!INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>前提条件
この記事では、以下のことを前提としています。

* Azure のストレージ アカウントが作成されている。 手順については、「[Azure ストレージ アカウントの作成](../storage/storage-create-storage-account.md#create-a-storage-account)」をご覧ください。
* HDInsight サービスでカスタマイズされた Hadoop クラスターがプロビジョニングされている。 手順については、「 [Advanced Analytics Process and Technology 向けに Azure HDInsight Hadoop クラスターをカスタマイズする](machine-learning-data-science-customize-hadoop-cluster.md)」をご覧ください。
* データが Azure HDInsight Hadoop クラスターの Hive テーブルにアップロードされている。 アップロードされていない場合は、まず「 [データを作成して Hive テーブルに読み込む](machine-learning-data-science-move-hive-tables.md) 」の指示に従って Hive テーブルにデータをアップロードします。
* クラスターへのリモート アクセスが有効になっている。 手順については、「 [Hadoop クラスターのヘッド ノードへのアクセス](machine-learning-data-science-customize-hadoop-cluster.md#headnode)」をご覧ください。
* Hive クエリの送信手順については、「 [データを作成して Azure BLOB ストレージから Hive テーブルに読み込む](machine-learning-data-science-move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>データ探索のための Hive クエリ スクリプトの例
1. パーティションごとの所見の数を取得する  `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. 1 日ごとの所見の数を取得する  `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. カテゴリ列内のレベルを取得する   
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. 2 つのカテゴリ列の組み合わせ内のレベル数を取得する  `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. 数値型列の分布を取得する   
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. 2 つのテーブルの結合からレコードを抽出する
   
        SELECT
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>タクシー乗車データ シナリオのその他のクエリ スクリプト
[NYC タクシー乗車データ](http://chriswhong.com/open-data/foil_nyc_taxi/)シナリオに固有のクエリの例も、[GitHub リポジトリ](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)に用意されています。 これらのクエリには、指定されたデータ スキーマが既にあり、すぐに送信して実行できる状態になっています。


