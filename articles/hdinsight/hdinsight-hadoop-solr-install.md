---
title: "Script Action を使用して Hadoop クラスターに Solr をインストールする | Microsoft Docs"
description: "Script Action を使って HDInsight クラスターを Solr でカスタマイズする方法について説明します。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b1e6f338-8ac1-4b38-bbb5-2f7388b9de3b
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2016
ms.author: nitinme
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: c9e3c1d2a1f5b83c59fa2a22f3cb4d89df203384
ms.openlocfilehash: 010a8a377150292fcb533a1af99adcc7c782bceb
ms.lasthandoff: 01/19/2017


---
# <a name="install-and-use-solr-on-windows-based-hdinsight-clusters"></a>Windows ベースの HDInsight クラスターに Solr をインストールして使用する

スクリプト アクションを使用して Windows ベースの HDInsight クラスターを Solr でカスタマイズする方法と、Solr を使用してデータを検索する方法について説明します。 

> [!IMPORTANT]
> このドキュメントの手順は、Windows ベースの HDInsight クラスターに対してのみ機能します。 Windows では、バージョン 3.4 より前の HDInsight のみを使用できます。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。 Linux ベースのクラスターでの Solr の操作については、「 [HDInsight Hadoop クラスターに Solr をインストールして使用する (Linux)](hdinsight-hadoop-solr-install-linux.md)」を参照してください。


*Script Action*を使用し、Azure HDInsight の任意の種類のクラスター (Hadoop、Storm、HBase、Spark) に Solr をインストールできます。 HDInsight クラスターに Solr をインストールするためのサンプル スクリプトは、 [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1)の読み取り専用 Azure ストレージ BLOB から入手できます。

サンプル スクリプトは、HDInsight クラスター version 3.1 でのみ機能します。 HDInsight クラスター バージョンの詳細については、「 [HDInsight クラスター バージョン](hdinsight-component-versioning.md)」をご覧ください。

このトピックで使用するサンプル スクリプトは、既定の構成で Windows ベースの Solr クラスターを作成します。 この構成とは異なるコレクションやシャード、スキーマ、レプリカなどで Solr クラスターを構成する場合には、それに応じてスクリプトと Solr バイナリを変更する必要があります。

**関連記事:**

* [HDInsight Hadoop クラスターに Solr をインストールして使用する (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [HDInsight で Hadoop クラスターを作成する](hdinsight-provision-clusters.md): HDInsight クラスターの作成に関する一般情報。
* [スクリプト アクションを使用して HDInsight クラスターをカスタマイズする][hdinsight-cluster-customize]: スクリプト アクションを使用した HDInsight クラスターのカスタマイズに関する一般情報。
* [HDInsight 用のスクリプト アクションのスクリプトを開発する](hdinsight-hadoop-script-actions.md)。

## <a name="what-is-solr"></a>Solr とは何か
<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> は、データに対して強力なフルテキスト検索ができるエンタープライズ検索プラットフォームです。 Hadoop が大量のデータの保存と管理を可能にするのに対し、Apache Solr は迅速にデータを取得するための検索機能を提供します。

## <a name="install-solr-using-portal"></a>ポータルを使用して Solr をインストールする
1. 「[HDInsight で Hadoop クラスターを作成する](hdinsight-provision-clusters.md)」の説明に基づき、**CUSTOM CREATE** オプションを使用してクラスターの作成を開始します。
2. ウィザードの **[スクリプトのアクション]** ページで、**[スクリプト アクションの追加]** をクリックし、次に示すように、スクリプト アクションの詳細を指定します。

    ![スクリプト アクションを使ってクラスターをカスタマイズする](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "スクリプト アクションを使ってクラスターをカスタマイズする")

    <table border='1'>
        <tr><th>プロパティ</th><th>値</th></tr>
        <tr><td>名前</td>
            <td>スクリプト アクションの名前を指定します。 たとえば、<b>Install Solr</b> のような名前にします。</td></tr>
        <tr><td>スクリプト URI</td>
            <td>クラスターをカスタマイズするために呼び出すスクリプトの Uniform Resource Identifier (URI) を指定します。 例: <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
        <tr><td>ノードの種類</td>
            <td>カスタマイズ スクリプトが実行されるノードを指定します。 <b>[すべてのノード]</b>、<b>[ヘッド ノードのみ]</b>、<b>[ワーカー ノードのみ]</b> から選択できます。
        <tr><td>parameters</td>
            <td>スクリプトで必要な場合は、パラメーターを指定します。 Solr をインストールするスクリプトにパラメーターは必要ないため、この部分は空白のままにすることもできます。</td></tr>
    </table>

    複数のスクリプト操作を追加して、クラスターに複数のコンポーネントをインストールすることができます。 スクリプトの追加後、チェックマークをクリックしてクラスターの作成を開始します。

## <a name="use-solr"></a>Solr を使用する
最初に、いくつかのデータ ファイルに対し Solr のインデックスを作成する必要があります。 これにより、インデックス付きデータに対して、Solr を使用して検索クエリを実行できます。 HDInsight クラスターで Solr を使用するには、次の手順を実行します。

1. **リモート デスクトップ プロトコル (RDP) を使用してインストールされている Solr で HDInsight クラスターにリモート接続します**。 Solr のインストールによって作成したクラスターに対し、Azure ポータルでリモート デスクトップを有効にし、クラスターにリモート接続します。 手順については、「 [RDP を使用した HDInsight クラスターへの接続](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)」をご覧ください。
2. **データ ファイルをアップロードして Solr のインデックスを作成します**。 Solr のインデックスを作成する場合、検索が必要なドキュメントをその中に配置します。 Solr のインデックスを作成するには、クラスターに RDP をリモート接続してデスクトップに移動します。次に、Hadoop コマンド ラインを開いて **C:\apps\dist\solr-4.7.2\example\exampledocs** に移動します。 次のコマンドを実行します。

        java -jar post.jar solr.xml monitor.xml

    コンソールで、次の出力が表示します。

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    post.jar ユーティリティは、**solr.xml** と **monitor.xml** という&2; つのサンプル ドキュメントで Solr のインデックスを作成します。 post.jar ユーティリティとサンプル ドキュメントは Solr のインストールで利用できるようになります。
3. **Solr ダッシュボードを使用して、インデックス付きドキュメント内を検索します**。 HDInsight クラスターへの RDP セッションで Internet Explorer を開き、**http://headnodehost:8983/solr/#/** で Solr のダッシュ ボードを起動します。 左側のウィンドウの **[Core Selector]** ボックスから、**[collection1]** を選択し、メニューの中から **[クエリ]** をクリックします。 例として、Solr 内のすべてのドキュメントを選択して返すために、次の値を指定します。

   * **[q]** ボックスに「**\*:**\*」を入力します。 これにより、Solr でインデックス付けされたすべてのドキュメントが返されます。 ドキュメント内の特定の文字列を検索する場合には、ここにその文字列を入力することができます。
   * **[wt]** ボックスでは、出力形式を選択します。 既定値は、 **json**です。 **[Execute Query]**をクリックします。

     ![スクリプト アクションを使ってクラスターをカスタマイズする](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Solr ダッシュボードでクエリを実行する")

     これによる出力は、Solr のインデックス作成のために使用した&2; つのドキュメントを返します。 出力結果は、以下のようになります。

           "response": {
               "numFound": 2,
               "start": 0,
               "maxScore": 1,
               "docs": [
                 {
                   "id": "SOLR1000",
                   "name": "Solr, the Enterprise Search Server",
                   "manu": "Apache Software Foundation",
                   "cat": [
                     "software",
                     "search"
                   ],
                   "features": [
                     "Advanced Full-Text Search Capabilities using Lucene",
                     "Optimized for High Volume Web Traffic",
                     "Standards Based Open Interfaces - XML and HTTP",
                     "Comprehensive HTML Administration Interfaces",
                     "Scalability - Efficient Replication to other Solr Search Servers",
                     "Flexible and Adaptable with XML configuration and Schema",
                     "Good unicode support: héllo (hello with an accent over the e)"
                   ],
                   "price": 0,
                   "price_c": "0,USD",
                   "popularity": 10,
                   "inStock": true,
                   "incubationdate_dt": "2006-01-17T00:00:00Z",
                   "_version_": 1486960636996878300
                 },
                 {
                   "id": "3007WFP",
                   "name": "Dell Widescreen UltraSharp 3007WFP",
                   "manu": "Dell, Inc.",
                   "manu_id_s": "dell",
                   "cat": [
                     "electronics and computer1"
                   ],
                   "features": [
                     "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                   ],
                   "includes": "USB cable",
                   "weight": 401.6,
                   "price": 2199,
                   "price_c": "2199,USD",
                   "popularity": 6,
                   "inStock": true,
                   "store": "43.17614,-90.57341",
                   "_version_": 1486960637584081000
                 }
               ]
             }
4. **インデックス付きデータを、Solr から HDInsight クラスターに関連付けられている Azure BLOB ストレージにバックアップすることをお勧めします**。 インデックス付きデータを Solr のクラスター ノードから Azure BLOB ストレージ にバックアップすることをお勧めします。 そのために、次の手順を実行してください。

   1. RDP セッションから Internet Explorer を開き、次の URL をポイントします。

           http://localhost:8983/solr/replication?command=backup

       次のように、応答が表示されます。

           <?xml version="1.0" encoding="UTF-8"?>
           <response>
             <lst name="responseHeader">
               <int name="status">0</int>
               <int name="QTime">9</int>
             </lst>
             <str name="status">OK</str>
           </response>
   2. リモート セッションで、{SOLR_HOME}\{Collection}\data に移動します。 サンプル スクリプトを通じて作成したクラスターの場合、この部分は **C:\apps\dist\solr-4.7.2\example\solr\collection1\data** となります。 この場所には、**snapshot.*timestamp*** のような名前でスナップショット フォルダーが作成されています。
   3. スナップショット フォルダーを zip 圧縮して Azure BLOB ストレージにアップロードします。 Hadoop コマンド ラインで、次のコマンドを使用して、スナップショット フォルダーの場所に移動します。

             hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

       このコマンドは、クラスターに関連付けられている既定のストレージ アカウント内のコンテナーの下にある /example/data/ にスナップショットをコピーします。

## <a name="install-solr-using-aure-powershell"></a>Aure PowerShell を使用して Solr をインストールする
「[Script Action を使用して HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell)」を参照してください。  このサンプルでは、Azure PowerShell を使用して Spark をインストールする方法を示します。 スクリプトをカスタマイズし、 [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1)を使用する必要があります。

## <a name="install-solr-using-net-sdk"></a>.NET SDK を使用して Solr をインストールする
「[Script Action を使用して HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell)」を参照してください。 このサンプルでは、.NET SDK を使用して Spark をインストールする方法を示します。 スクリプトをカスタマイズし、 [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1)を使用する必要があります。

## <a name="see-also"></a>関連項目
* [HDInsight Hadoop クラスターに Solr をインストールして使用する (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [HDInsight で Hadoop クラスターを作成する](hdinsight-provision-clusters.md): HDInsight クラスターの作成に関する一般情報。
* [スクリプト アクションを使用して HDInsight クラスターをカスタマイズする][hdinsight-cluster-customize]: スクリプト アクションを使用した HDInsight クラスターのカスタマイズに関する一般情報。
* [HDInsight 用のスクリプト アクションのスクリプトを開発する](hdinsight-hadoop-script-actions.md)。
* [HDInsight クラスターに Spark をインストールし、使用する][hdinsight-install-spark]: Spark のインストールに関する Script Action サンプル。
* [HDInsight クラスターに R をインストールし、使用する][hdinsight-install-r]: R のインストールに関する Script Action サンプル。
* [HDInsight クラスターに Giraph をインストールし、使用する](hdinsight-hadoop-giraph-install.md): Giraph のインストールに関する Script Action サンプル。

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md

