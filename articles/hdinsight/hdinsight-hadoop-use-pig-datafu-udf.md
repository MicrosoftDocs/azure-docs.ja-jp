---
title: "HDInsight の Pig で DataFu を使用する"
description: "DataFu は、Hadoop で使用するライブラリのコレクションです。 HDInsight クラスターの Pig で DataFu を使用する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 0016721a-82be-4773-88ad-91e6b2c21cbb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 2a700d04fa93e61502b2fa16ad1c7457dd8bb1e9
ms.lasthandoff: 03/25/2017


---
# <a name="use-datafu-with-pig-on-hdinsight"></a>HDInsight の Pig で DataFu を使用する

DataFu は、Hadoop で使用するオープン ソース ライブラリのコレクションです。 このドキュメントでは、HDInsight クラスターで DataFu を使用する方法、および Pig で DataFu ユーザー定義関数 (UDF) を使用する方法を説明します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。

* Azure HDInsight クラスター (Linux または Windows ベース)

  > [!IMPORTANT]
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

* [HDInsight での Pig の使用](hdinsight-use-pig.md)

## <a name="install-datafu-on-linux-based-hdinsight"></a>Linux ベースの HDInsight に DataFu をインストールする

> [!NOTE]
> DataFu は、Linux ベースのクラスター バージョン 3.3 以降と、Windows ベースのクラスター上にインストールされています。 3.3 より前の Linux ベースのクラスターにはインストールされていません。
> 
> Linux ベースのクラスター バージョン 3.3 以降、または Windows ベースのクラスターを使用している場合は、このセクションを省略できます。

DataFu は、Maven リポジトリからダウンロードしてインストールできます。 HDInsight クラスターに DataFu を追加するには、次の手順を使用します。

1. SSH を使用して、Linux ベースの HDInsight クラスターに接続します。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. 次のコマンドを使用して wget ユーティリティで DataFu jar ファイルをダウンロードするか、またはリンクをコピーしてブラウザーに貼り付けてダウンロードを開始します。
   
    ```
    wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar
    ```

3. 次に、HDInsight クラスターの既定のストレージにファイルをアップロードします。 これにより、クラスターのすべてのノードでファイルを使用できるようになり、クラスターを削除して再作成してもファイルはストレージに残っています。
   
    ```
    hdfs dfs -put datafu-1.2.0.jar /example/jars
    ```
   
    > [!NOTE]
    > 上の例では jar を `/example/jars` に格納しています。このディレクトリはクラスターのストレージに既に存在しています。 HDInsight クラスター ストレージのどこでも使用できます。

## <a name="use-datafu-with-pig"></a>Pig で DataFu を使用する

読者は HDInsight での Pig の使用に慣れていることが前提なので、このセクションの手順では Pig Latin ステートメントのみを示し、クラスターでそれを使用する方法の手順は示しません。 HDInsight で Pig を使用する方法の詳細については、 [HDInsight での Pig の使用](hdinsight-use-pig.md)に関するページをご覧ください。

> [!IMPORTANT]
> Linux ベースの HDInsight クラスター上の Pig から DataFu を使用する場合は、最初に jar ファイルを登録する必要があります。
> 
> クラスターで Azure Storage を使用する場合は、`wasb://` パスを使用する必要があります。 たとえば、「 `register wasb:///example/jars/datafu-1.2.0.jar`」のように入力します。
>
> クラスターで Azure Data Lake Store を使用する場合は、`adl://` パスを使用する必要があります。 たとえば、「 `register adl://home/example/jars/datafu-1.2.0.jar`」のように入力します。
> 
> Windows ベースの HDInsight クラスターでは、DataFu は既定で登録されます。

通常は、DataFu の関数にエイリアスを定義します。 次に例を示します。

    DEFINE SHA datafu.pig.hash.SHA();

これは、SHA ハッシュ関数に対して `SHA` というエイリアスを定義します。 その後は、Pig Latin スクリプトでこれを使用して、入力データのハッシュを生成できます。 たとえば、次の例では入力データ内の名前をハッシュ値に置き換えています。

    raw = LOAD '/data/raw/' USING PigStorage(',') AS  
        (name:chararray, 
        int1:int, 
        int2:int,
        int3:int); 
    mask = FOREACH raw GENERATE SHA(name), int1, int2, int3; 
    DUMP mask;

これを次の入力データで使用すると、

    Lana Zemljaric,5,9,1
    Qiong Zhong,9,3,6
    Sandor Harsanyi,0,7,3
    Roko Petkovic,2,6,2
    Tibor Rozsa,8,0,0
    Lea Hrastovsek,6,3,6
    Regina Toth,2,1,2
    Eva Makay,8,9,2
    Shi Liao,4,6,0
    Tjasa Zemljaric,0,2,5

これにより、次の出力が生成されます。

    (c1a743b0f34d349cfc2ce00ef98369bdc3dba1565fec92b4159a9cd5de186347,5,9,1)
    (713d030d621ab69aa3737c8ea37a2c7c724a01cd0657a370e103d8cdecac6f99,9,3,6)
    (7a5f5abdd281f68168199319d98a1a662535f988d1443b3a3c497010937bac89,0,7,3)
    (a94818e93807e12079c4b35f8f3c8c8ef8e8acd1954e7f0476bc1a3a86fc96a9,2,6,2)
    (894ead4f48af91df7e088241218a23157bede7c52115272e417e95c046d48902,8,0,0)
    (6f99f163af3448fda672087db306f363e27a98a9e49c1f274a0860e303f8aec4,6,3,6)
    (a03de92a28be3c6a984c7a153fa9ed81c0413f76a9401955b5f7e04a5dd0ab9f,2,1,2)
    (6ceab977c8fb48d9ad0dc413e6bc646cabd89f22e7ab97a6b0133f3d225c6013,8,9,2)
    (fa9c436469096ff1bd297e182831f460501b826272ae97e921f5f6e3f54747e8,4,6,0)
    (bc22db7c238b86c37af79a62c78f61a304b35143f6087eb99c34040325865654,0,2,5)

## <a name="next-steps"></a>次のステップ

DataFu または Pig の詳細については、次のドキュメントを参照してください。

* [Apache DataFu Pig ガイド](http://datafu.incubator.apache.org/docs/datafu/guide.html)。
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)


