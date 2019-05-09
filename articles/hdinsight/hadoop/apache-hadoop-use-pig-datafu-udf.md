---
title: HDInsight 上の Apache Pig で Apache DataFu を使用する - Azure
description: Apache DataFu Pig は、Apache Hadoop 上の Apache Pig で使用されるライブラリのコレクションです。 HDInsight クラスターの Pig で DataFu を使用する方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/16/2018
ms.author: hrasheed
ms.openlocfilehash: d67c3e452da05c626721d4c3144e612e6f9e0af4
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338446"
---
# <a name="use-apache-datafu-pig-with-apache-pig-on-hdinsight"></a>HDInsight 上の Apache Pig で Apache DataFu Pig を使用する

HDInsight で Apache DataFu Pig を使用する方法について説明します。

Apache DataFu Pig は、Apache Hadoop 上の Apache Pig で使用されるオープン ソース ライブラリのコレクションです。
DataFu Pig の詳細については、[https://datafu.apache.org/](https://datafu.apache.org/) を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。

* Azure HDInsight クラスター (Linux または Windows ベース)

  > [!IMPORTANT]  
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](../hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

* [HDInsight での Apache Pig の使用](hdinsight-use-pig.md)に関する基礎知識があること

## <a name="install-datafu-on-linux-based-hdinsight"></a>Linux ベースの HDInsight に DataFu をインストールする

> [!IMPORTANT]  
> DataFu は、Linux ベースのクラスター バージョン 3.3 以降と、Windows ベースのクラスター上にインストールされています。 3.3 より前の Linux ベースのクラスターにはインストールされていません。
>
> Windows ベースのクラスター、または Linux ベースのクラスター バージョン 3.3 以降を使用している場合は、このセクションを省略してください。

DataFu は、Maven リポジトリからダウンロードしてインストールできます。 次の手順を実行して、必要なバージョンを特定し HDInsight クラスターに追加します。

> [!WARNING]  
> DataFu のバージョンには、HDInsight では満たされていない要件がある場合があります。 たとえば、以前のバージョンの DataFu を使用すると、HDInsight に含まれていないバージョンの Pig が必要になる場合があります。

### <a name="find-a-version"></a>バージョンを特定する

1. Web ブラウザーで https://mvnrepository.com/artifact/org.apache.datafu/datafu-pig に移動し、必要なバージョンを特定します。

2. リンクされているバージョン番号を選択します。

3. __[すべて表示]__ を選択して、すべてのファイルを表示します。

4. ファイルの一覧で、.jar ファイルを特定します。 すべての依存関係が含まれているため、通常は、このファイルは一覧の中でサイズが最も大きくなります。 リンクを右クリックし、リンクのアドレスをコピーします。

### <a name="download-datafu-to-hdinsight"></a>HDInsight に DataFu をダウンロードする

1. SSH を使用して、Linux ベースの HDInsight クラスターに接続します。 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. wget ユーティリティを使用して DataFu jar ファイルをダウンロードするには、次のコマンドを使用します。

    > [!IMPORTANT]  
    > コマンド内のリンクを先ほどコピーした URL に置き換えます。

    ```
    wget https://central.maven.org/maven2/org/apache/datafu/datafu-pig/1.4.0/datafu-pig-1.4.0.jar
    ```

3. 次に、HDInsight クラスターの既定のストレージにファイルをアップロードします。 このファイルを既定のストレージに配置すると、クラスター内のすべてのノードで使用できるようになります。

    > [!IMPORTANT]  
    > ファイル名のバージョン番号を、ダウンロードしたバージョンに置き換えます。

    ```
    hdfs dfs -put datafu-pig-1.4.0.jar /example/jars
    ```

    > [!NOTE]  
    > 前のコマンドでは jar を `/example/jars` に格納しています。このディレクトリはクラスターのストレージに既に存在しています。 HDInsight クラスター ストレージのどこでも使用できます。

## <a name="use-datafu-with-pig"></a>Pig で DataFu を使用する

このセクションの手順では、HDInsight での Pig の使用について理解していることを前提としています。 HDInsight で Pig を使用する方法の詳細については、 [HDInsight での Pig の使用](hdinsight-use-pig.md)に関するページをご覧ください。

> [!IMPORTANT]  
> 前のセクションの手順を使用して DataFu を手動でインストールした場合は、使用する前に登録する必要があります。
>
> * クラスターで Azure Storage を使用する場合は、`wasb://` パスを使用します。 たとえば、「 `register wasb:///example/jars/datafu-pig-1.4.0.jar` 」のように入力します。
>
> * クラスターで Azure Data Lake Store Gen2 を使用する場合は、`abfs://` パスを使用します。 たとえば、「 `register abfs://home/example/jars/datafu-pig-1.4.0.jar` 」のように入力します。
>
> * クラスターで Azure Data Lake Store Gen1 を使用する場合は、`adl://` パスを使用します。 たとえば、「 `register adl://home/example/jars/datafu-pig-1.4.0.jar` 」のように入力します。

通常は、DataFu の関数にエイリアスを定義します。 次の例では、`SHA` のエイリアスを定義しています。

```piglatin
DEFINE SHA datafu.pig.hash.SHA();
```

その後は、Pig Latin スクリプトでこのエイリアスを使用して、入力データのハッシュを生成できます。 たとえば、次のコードでは入力データ内の場所をハッシュ値に置き換えています。

```piglatin
raw = LOAD '/HdiSamples/HdiSamples/SensorSampleData/building/building.csv' USING
    org.apache.pig.piggybank.storage.CSVExcelStorage(',', 'NO_MULTILINE', 'UNIX', 'SKIP_INPUT_HEADER') AS
    (int1:int,
     id1:chararray,
     int2:int,
     id2:chararray,
     location:chararray);
mask = FOREACH raw GENERATE int1, id1, int2, id2, SHA(location);
DUMP mask;
```

これにより、次の出力が生成されます。

    (1,M1,25,AC1000,aa5ab35a9174c2062b7f7697b33fafe5ce404cf5fecf6bfbbf0dc96ba0d90046)
    (2,M2,27,FN39TG,7a1ca4ef7515f7276bae7230545829c27810c9d9e98ab2c06066bee6270d5153)
    (3,M3,28,JDNS77,07f62b021771d3cf67e2e1faf18769cc5e5c119ad7d4d1847a11e11d6d5a7ecb)
    (4,M4,17,GG1919,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (5,M5,3,ACMAX22,1ed8c7e56c947bebc0cfcf88c4ea0f02c944568f71df893a99970e4f0c78cddc)
    (6,M6,9,AC1000,c96dd81db196cca5f57bd4270bbb9d9e9d1b242d67f9364005ee1dfdc2632523)
    (7,M7,13,FN39TG,3e049d78d958038ac6bd5dcf038075cc73362b4956aaf7308c3a69c8eca76297)
    (8,M8,25,JDNS77,c1ef40ce0484c698eb4bd27fe56c1e7b68d74f9780ed674210d0e5013dae45e9)
    (9,M9,11,GG1919,a7d355b26bda6bf1196ccffead0b2cf2b81f0a9de5b4876b44407f1dc07e51e6)
    (10,M10,23,ACMAX22,10436829032f361a3de50048de41755140e581467bc1895e6c1a17f423e42d10)
    (11,M11,14,AC1000,348841ef53dbd5a64008a86be432973db790774fb28b59b0d99702a3188b3705)
    (12,M12,26,FN39TG,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (13,M13,25,JDNS77,ed9ad13611d7164839dd3feaf9a7f6a75a4138f389e0d45f8e07fa38da1116a2)
    (14,M14,17,GG1919,80db4ccdca106d37b920206331fcfe3e9e50a9e763d89b54ce3ad5ac8cf30f03)
    (15,M15,19,ACMAX22,3552ac0c032467fbf592cb4d10c5c9267800c01e343ad8ca557256d882ae9327)
    (16,M16,23,AC1000,07c67d76ef92ac9853588e098983fefba4ba5965f8fec95f42ab0d04c27865ba)
    (17,M17,11,FN39TG,557c1599d9a04895d3817d293e0806a4419a14de31958386182798d0d2ed3a56)
    (18,M18,25,JDNS77,dbc74a36d8e7439c45c64d856388506cc9b1218619cef979c3d605115a7a4546)
    (19,M19,14,GG1919,be55ef3f4c4e6c2d9c2afe2a33ac90ad0f50d4de7f9163999877e2a9ca5a54f8)
    (20,M20,19,ACMAX22,ea0b937ea317101ee2c26b03a4843a19ceced8a2b9673c3cf409a726ca2b0fd8)

## <a name="next-steps"></a>次の手順

DataFu または Pig の詳細については、次のドキュメントを参照してください。

* [Apache DataFu Pig の概要](https://datafu.apache.org/docs/datafu/getting-started.html)
* [HDInsight での Apache Pig の使用](hdinsight-use-pig.md)
