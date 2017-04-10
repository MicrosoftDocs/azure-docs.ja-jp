---
title: "HDInsight で R を使用してクラスターをカスタマイズする | Microsoft Docs"
description: "スクリプトの操作を使用して R をインストールする方法について説明し、HDInsight クラスターでの R を使用します。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: be851270-afa5-4af0-a69e-2d343a4deeb7
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: jgao
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: 0587dfcd6079fc8df91bad5a5f902391d3657a6b
ms.openlocfilehash: 8b74f2e4169ac97e09da12d5ed85e0e24b50e150
ms.lasthandoff: 12/08/2016


---
# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>HDInsight Hadoop クラスターに R をインストールして使用する
Script Action を使用して Windows ベースの HDInsight クラスターを R でカスタマイズする方法と、HDInsight クラスターで R を使用する方法について説明します。 [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) サービスには、HDInsight クラスターの一部として R Server が含まれています。 これにより、R スクリプトで MapReduce と Spark を使用して、分散計算を実行することができます。 詳細については、「 [Get started using R Server on HDInsight (HDInsight で R Server を使ってみる)](hdinsight-hadoop-r-server-get-started.md)」をご覧ください。 Linux ベースのクラスターでの R の操作については、「 [HDInsight Hadoop クラスターに R をインストールして使用する (Linux)](hdinsight-hadoop-r-scripts-linux.md)」を参照してください。

*Script Action*を使用し、Azure HDInsight の任意の種類のクラスター (Hadoop、Storm、HBase、Spark) に R をインストールできます。 HDInsight クラスターに R をインストールするサンプル スクリプトは、 [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1)の読み取り専用の Azure ストレージ BLOB から入手できます。

**関連記事:**

* [HDInsight Hadoop クラスターに R をインストールして使用する (Linux)](hdinsight-hadoop-r-scripts-linux.md)
* [HDInsight で Hadoop クラスターを作成する](hdinsight-provision-clusters.md): HDInsight クラスターの作成に関する一般情報。
* [スクリプト アクションを使用して HDInsight クラスターをカスタマイズする][hdinsight-cluster-customize]: スクリプト アクションを使用した HDInsight クラスターのカスタマイズに関する一般情報
* [HDInsight 用の Script Action スクリプトの開発](hdinsight-hadoop-script-actions.md)

## <a name="what-is-r"></a>R とは
<a href="http://www.r-project.org/" target="_blank">統計計算用 R プロジェクト</a>は、統計計算用のオープン ソースの言語と環境です。 R は、数百の組み込み統計関数と、関数型プログラミングとオブジェクト指向のプログラミングの特徴を結合した独自のプログラミング言語を提供します。 また、広範なグラフィカル機能も提供します。 R は、さまざまな分野におけるほぼすべての統計学専門家や科学者に推奨されるプログラミング環境です。

R は Azure BLOB Storage (WASB) と互換性があるので、そこに格納されているデータは HDInsight 上の R を使用して処理できます。  

## <a name="install-r"></a>R のインストール
HDInsight クラスターで R をインストールする [サンプル スクリプト](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) は、Azure Storage の読み取り専用 BLOB から入手できます。 このセクションでは、Azure ポータルを使用してクラスターを作成する際に、サンプル スクリプトを使用する方法について説明します。

> [!NOTE]
> サンプル スクリプトは、HDInsight クラスター バージョン 3.1 で導入されました。 HDInsight クラスター バージョンの詳細については、「[HDInsight クラスター バージョン](hdinsight-component-versioning.md)」をご覧ください。
>
>

1. HDInsight クラスターをポータルから作成する場合は、**[オプションの構成]**、**[スクリプト アクション]** の順にクリックします。
2. **[スクリプト アクション]** ページで、次の値を入力します。

    ![スクリプト アクションを使ってクラスターをカスタマイズする](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "スクリプト アクションを使ってクラスターをカスタマイズする")

    <table border='1'>
        <tr><th>プロパティ</th><th>値</th></tr>
        <tr><td>名前</td>
            <td>「<b>R のインストール</b>」などスクリプト アクションの名前を指定します。</td></tr>
        <tr><td>スクリプト URI</td>
            <td>たとえば、<i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i> のように、クラスターをカスタマイズするために呼び出されるスクリプトへの URI を指定します。</td></tr>
        <tr><td>ノードの種類</td>
            <td>カスタマイズ スクリプトが実行されるノードを指定します。 <b>[すべてのノード]</b>、<b>[ヘッド ノードのみ]</b>、<b>[ワーカー ノードのみ]</b> から選択できます。
        <tr><td>parameters</td>
            <td>スクリプトで必要な場合は、パラメーターを指定します。 ただし、R をインストールするスクリプトではパラメーターが必要ないため、空白のままにすることができます。</td></tr>
    </table>

    複数のスクリプト操作を追加して、クラスターに複数のコンポーネントをインストールすることができます。 スクリプトを追加した後、チェック マークをオンにして、クラスターの作成を開始します。

Azure PowerShell や HDInsight .NET SDK を使用して、HDInsight に R をインストールするためにスクリプトを使用することもできます。 これらの手順については、この記事の後半で説明します。

## <a name="run-r-scripts"></a>R スクリプトの実行
このセクションでは、HDInsight で Hadoop クラスター上の R スクリプトを実行する方法について説明します。

1. **クラスターへのリモート デスクトップ接続の確立**: ポータルから、R をインストールして作成したクラスターに対してリモート デスクトップを有効にし、クラスターに接続します。 手順については、「 [RDP を使用した HDInsight クラスターへの接続](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)」をご覧ください。
2. **Rコンソールを開く**: R をインストールすると、ヘッドノードのデスクトップに R コンソールへのリンクが設定されます。 それをクリックして、R コンソールを開きます。
3. **R スクリプトの実行**: R スクリプトは、R コンソールに貼り付け、選択して、ENTER を押すことで、直接実行できます。 1 ～ 100 の数値を生成し、その結果に 2 を乗算する、単純なスクリプト例を示します。

        library(rmr2)
        library(rhdfs)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
        from.dfs(calc)

最初の 2 行は、R と共にインストールされる RHadoop ライブラリを呼び出します。最後の行は、結果をコンソールに出力します。 出力は次のようになります。

    [1,]  1 2
    [2,]  2 4
    .
    .
    .
    [98,]  98 196
    [99,]  99 198
    [100,] 100 200


## <a name="install-r-using-aure-powershell"></a>Azure PowerShell を使用した R のインストール
「[スクリプト アクションを使用して HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell)」を参照してください。  このサンプルでは、Azure PowerShell を使用して Spark をインストールする方法を示します。 スクリプトをカスタマイズし、 [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1)を使用する必要があります。

## <a name="install-r-using-net-sdk"></a>.NET SDK を使用した R のインストール
「[スクリプト アクションを使用して HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell)」を参照してください。 このサンプルでは、.NET SDK を使用して Spark をインストールする方法を示します。 スクリプトをカスタマイズし、 [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11)を使用する必要があります。

## <a name="see-also"></a>関連項目
* [HDInsight Hadoop クラスターに R をインストールして使用する (Linux)](hdinsight-hadoop-r-scripts-linux.md)
* [HDInsight で Hadoop クラスターを作成する](hdinsight-provision-clusters.md): HDInsight クラスターの作成に関する一般情報。
* [スクリプト アクションを使用して HDInsight クラスターをカスタマイズする][hdinsight-cluster-customize]: スクリプト アクションを使用した HDInsight クラスターのカスタマイズに関する一般情報
* [HDInsight 用の Script Action スクリプトの開発](hdinsight-hadoop-script-actions.md)
* [HDInsight クラスターに Spark をインストールし、使用する][hdinsight-install-spark]: Spark のインストールに関する Script Action サンプル
* [HDInsight クラスターに Giraph をインストールし、使用する](hdinsight-hadoop-giraph-install.md): Giraph のインストールに関する Script Action サンプル。
* [HDInsight クラスターに Solr をインストールし、使用する](hdinsight-hadoop-solr-install-linux.md): Solr のインストールに関する Script Action サンプル。

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-apache-spark-jupyter-spark-sql.md

