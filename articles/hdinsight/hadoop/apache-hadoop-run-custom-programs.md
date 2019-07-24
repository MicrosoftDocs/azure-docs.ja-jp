---
title: カスタムの MapReduce プログラムの実行 - Azure HDInsight
description: HDInsight でカスタムの MapReduce プログラムを実行するタイミングと方法。
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: ashishth
ms.openlocfilehash: c9cbea913a86a681620eea4adc0a5c99cc84f920
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53433867"
---
# <a name="run-custom-mapreduce-programs"></a>カスタムの MapReduce プログラムを呼び出す

Apache HDInsight などの Hadoop ベースのビッグ データ システムにより、さまざまなツールとテクノロジを使用したデータ処理が可能になります。 次の表では、主な利点とそれぞれの考慮事項について説明します。

| クエリ メカニズム | 長所 | 考慮事項 |
| --- | --- | --- |
| **HiveQL を使用した Apache Hive** | <ul><li>バッチ処理と大量の変更不可データの分析、データの集計、オンデマンド クエリを行うための優れたソリューションです。 使い慣れた SQL に似た構文を使用します。</li><li>パーティション分割やインデックス作成を簡単に行うことができるデータの永続テーブルを生成するのに使用できます。</li><li>同じデータに対して複数の外部テーブルおよびビューを作成できます。</li><li>大規模なスケールアウトとフォールト トレランスの機能をデータ ストレージと処理で実現する、シンプルなデータ ウェアハウスの実装をサポートします。</li></ul> | <ul><li>少なくともいくつかの特定可能な構造を持つソース データが必要です。</li><li>リアルタイム クエリや行レベルの更新には適していません。 大規模なデータ セットに対するバッチ ジョブに最適です。</li><li>いくつかの種類の複雑な処理タスクを実行できない場合があります。</li></ul> |
| **Pig Latin を使用した Apache Pig** | <ul><li>セットでのデータ操作、データセットのマージとフィルタリング、レコードまたはレコード グループへの関数の適用を行うためのソリューションであり、列の定義、値のグループ化、または列から行への変換によるデータの再構築を行うための優れたソリューションです。</li><li>データ操作のシーケンスとしてワークフロー ベースのアプローチを使用できます。</li></ul> | <ul><li>SQL ユーザーは Pig Latin を使い慣れていなかったり、HiveQL よりも使いづらいと感じたりする可能性があります。</li><li>通常、既定の出力はテキスト ファイルであるため、Excel などの視覚化ツールを使用するよりも難しく感じられる可能性があります。 通常、出力に対して Hive テーブルをレイヤーします。</li></ul> |
| **カスタムの map/reduce** | <ul><li>map および reduce のフェーズと実行に対するフル コントロールが可能です。</li><li>これにより、クエリを最適化して、クラスターから最大のパフォーマンスを引き出したり、サーバーとネットワークの負荷を最小限に抑えたりすることができます。</li><li>よく使われるさまざまな言語でコンポーネントを記述できます。</li></ul> | <ul><li>独自の map および reduce コンポーネントを作成する必要があるため、Pig や Hive を使用するよりも難易度が高くなります。</li><li>データ セットの結合を必要とする処理は、実装の難度が上がります。</li><li>テスト フレームワークを使用できますが、Hadoop ジョブ スケジューラの制御下でバッチ ジョブとしてコードが実行されるため、コードのデバッグは通常のアプリケーションよりも複雑です。</li></ul> |
| **Apache HCatalog** | <ul><li>ストレージのパスの詳細を抽象化して管理を容易にし、ユーザーがデータの保存場所を知る必要がなくなります。</li><li>データ可用性などのイベント通知を有効にし、操作発生時に Oozie などの他のツールによる検出が可能になります。</li><li>キーによるパーティション分割を含めたデータのリレーショナル ビューを公開し、データにアクセスしやすくします。</li></ul> | <ul><li>既定で RCFile、CSV テキスト、JSON テキスト、SequenceFile、ORC の各ファイル形式がサポートされていますが、他の形式用にカスタムの SerDe を記述する必要がある場合があります。</li><li>HCatalog はスレッドセーフではありません。</li><li>Pig スクリプト内で HCatalog ローダーを使用するときに、列のデータ型にいくつかの制限が適用されます。 詳細については、Apache HCatalog ドキュメント内の「[HCatLoader Data Types (HCatLoader のデータ型)](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes)」をご覧ください。</li></ul> |

通常、必要な結果を実現するこれらのアプローチのうち、最もシンプルなものを使用します。 たとえば、Hive だけを使用して必要な結果を得ることはできるかもしれませんが、より複雑なシナリオでは Pig を使用したり、独自の map および reduce コンポーネントを記述したりする必要がある場合もあります。 Hive や Pig を試したあと、カスタムの map および reduce コンポーネントのほうが処理を微調整したり最適化したりできることでより高いパフォーマンスを実現できると思うこともあるかもしれません。

## <a name="custom-mapreduce-components"></a>カスタムの map/reduce コンポーネント

map/reduce コードは、**map** および **reduce** コンポーネントとして実装されている 2 つの個別の関数で構成されています。 **map** コンポーネントは複数のクラスター ノード上で並列実行され、各ノードはそのマッピングをそのノードのデータのサブセットに適用します。 **reduce** コンポーネントは、すべての map 関数からの結果を照合して集計します。 これら 2 つのコンポーネントの詳細については、「[HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)」をご覧ください。

HDInsight で処理する場合のほとんどのシナリオでは、Pig や Hive などのより高度な抽象化を使用するほうがシンプルで効率的です。 Hive スクリプト内で使用するカスタムの map および reduce コンポーネントを作成して、より高度な処理を実行することもできます。

カスタムの map および reduce コンポーネントは通常 Java で記述されます。 Hadoop は、C#、F#、Visual Basic、Python、JavaScript などの言語で開発されたコンポーネントを使用することもできるストリーミング インターフェイスを提供します。

* カスタムの Java MapReduce プログラム開発のチュートリアルについては、「[HDInsight での Hadoop 用 Java MapReduce プログラムの開発](apache-hadoop-develop-deploy-java-mapreduce-linux.md)」をご覧ください。
* Python の使用例については、「[HDInsight 用 Python ストリーミング MapReduce プログラムの開発](apache-hadoop-streaming-python.md)」をご覧ください。

独自の map および reduce コンポーネントを作成する場合は、次の条件を考慮に入れてください。

* データを解析し、カスタム ロジックを使用することで、完全には構造化されていないデータから構造化された情報を取得して、そのデータを処理する必要がある。
* Pig や Hive で表現することが困難 (または不可能) である複雑なタスクを、UDF を作成せずに実行する必要がある。 たとえば、緯度・経度座標またはソース データ内の IP アドレスを地理的な場所の名前に変換するには、外部のジオコーディング サービスを使用する必要があるかもしれません。
* Hadoop ストリーミング インターフェイスを使用して、map/reduce コンポーネント内の既存の .NET、Python、または JavaScript コードを再利用する必要がある。

## <a name="upload-and-run-your-custom-mapreduce-program"></a>カスタム MapReduce プログラムのアップロードと実行

最も一般的な MapReduce プログラムは Java で記述され、jar ファイルにコンパイルされます。

1. MapReduce プログラムを開発、コンパイル、テストしたあとに、`scp` コマンドを使用して jar ファイルをヘッドノードにアップロードします。

    ```bash
    scp mycustomprogram.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    **USERNAME** を、使用するクラスターの SSH ユーザー アカウントに置き換えます。 **CLUSTERNAME** をクラスター名に置き換えます。 SSH アカウントをセキュリティ保護するためにパスワードを使用している場合は、そのパスワードの入力を求められます。 証明書を使用している場合は、 `-i` パラメーターを使用して、秘密キー ファイルを指定することが必要な場合があります。

2. [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) を使用してクラスターに接続します。

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. SSH セッションから、YARN を介して MapReduce プログラムを実行します。

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    このコマンドにより、MapReduce ジョブが YARN に送信されます。 入力ファイルは `/example/data/sample.log`、出力ディレクトリは `/example/data/logoutput` です。 この入力ファイルとすべての出力ファイルは、クラスターの既定のストレージに格納されます。

## <a name="next-steps"></a>次の手順

* [HDInsight 上の Apache Hadoop で C# と MapReduce ストリーミングを使用する](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [HDInsight 上の Apache Hadoop 用の Java MapReduce プログラムを開発する](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [HDInsight 用 Python ストリーミング MapReduce プログラムの開発](apache-hadoop-streaming-python.md)
* [Azure Toolkit for Eclipse を使用して HDInsight クラスター向けの Apache Spark アプリケーションを作成する](../spark/apache-spark-eclipse-tool-plugin.md)
* [HDInsight 上の Apache Hive と Apache Pig で Python ユーザー定義関数 (UDF) を使用する](python-udf-hdinsight.md)
