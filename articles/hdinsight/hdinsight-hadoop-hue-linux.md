---
title: Linux ベースの HDInsight クラスターの Hadoop で Hue を使用する - Azure
description: HDInsight クラスターに Hue をインストールし、トンネリングを利用して Hue に要求を送信する方法について学習します。 Hue を使用して、ストレージを参照したり Hive または Pig を実行したりします。
keywords: hue hadoop
author: hrasheed-msft
ms.date: 12/11/2017
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.openlocfilehash: 8795d28cb36bacf4f57dba2048e30219047a9ac5
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098633"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>HDInsight Hadoop クラスターに Hue をインストールして使用する

HDInsight クラスターに Hue をインストールし、トンネリングを利用して Hue に要求を送信する方法について学習します。

## <a name="what-is-hue"></a>Hue とは

Hue は Apache Hadoop クラスターとの情報のやりとりに使用される一連の Web アプリケーションです。 Hue を使用すると、Hadoop クラスターに関連付けられているストレージ (HDInsight クラスターでは WASB) を参照したり、Hive ジョブや Pig スクリプトを実行したりすることができます。 HDInsight Hadoop クラスターにインストールした Hue では次のコンポーネントが利用可能です。

* Beeswax Hive エディター
* Apache Pig
* メタストア マネージャー
* Apache Oozie
* FileBrowser (WASB の既定のコンテナーと通信)
* ジョブ ブラウザー

> [!WARNING]  
> HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
>
> カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。 これにより問題が解決する場合もあれば、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。 たとえば、[HDInsight についての MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight)や [https://stackoverflow.com](https://stackoverflow.com) などの数多くのコミュニティ サイトを利用できます。 また、Apache プロジェクトには、[https://apache.org](https://apache.org) に[Hadoop](https://hadoop.apache.org/) などのプロジェクト サイトもあります。

## <a name="install-hue-using-script-actions"></a>スクリプト アクションを使用した Hue のインストール

Linux ベースの HDInsight クラスターに Hue をインストールするスクリプトは https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh にあります。このスクリプトを使用すると、既定のストレージとして Azure Storage Blob (WASB) または Azure Data Lake Storage を使用してクラスターに Hue をインストールできます。

このセクションでは、Azure Portal を使ってクラスターをプロビジョニングする際にこのスクリプトを使用する方法について説明します。

> [!NOTE]  
> スクリプト アクションは、Azure PowerShell、Azure クラシック CLI、HDInsight .NET SDK、または Azure Resource Manager テンプレートを使用して適用することもできます。 既に実行しているクラスターにスクリプト アクションを適用することもできます。 詳細については、 [スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md)に関する記事を参照してください。

1. 「[Linux の HDInsight クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md)」に記載されている手順を使用してクラスターのプロビジョニングを開始します。ただし、プロビジョニングを完了しないでください。

   > [!NOTE]  
   > HDInsight クラスターに Hue をインストールするには、A4 (8 コア、14 GB メモリ) 以上のヘッドノード サイズが推奨されます。

1. **[オプションの構成]** ブレードで **[スクリプト アクション]** を選択し、以下のように情報を指定します。

    ![Hue のスクリプト アクション パラメーターを指定する](./media/hdinsight-hadoop-hue-linux/hdi-hue-script-action.png "Hue のスクリプト アクション パラメーターを指定する")

   * **[名前]** :スクリプト アクションのフレンドリ名を入力します。
   * **[スクリプト URI]** : https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **[ヘッド]** :このオプションをオンにします。
   * **[ワーカー]** :空白のままにします。
   * **[Zookeeper]** :空白のままにします。
   * **[パラメーター]** :空白のままにします。

1. **[スクリプト アクション]** の下部で、 **[選択]** を使用して構成を保存します。 最後に、 **[オプションの構成]** ブレードの下部にある **[選択]** を使用して、オプションの構成情報を保存します。

1. 「[Linux の HDInsight クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md)」の説明に従って、クラスターのプロビジョニングを続行します。

## <a name="use-hue-with-hdinsight-clusters"></a>HDInsight クラスターで Hue を使用する

SSH トンネリングは、実行後、クラスターの Hue にアクセスする唯一の方法です。 SSH によるトンネリングでは、Hue が実行されているクラスターのヘッドノードにトラフィックが直接進むことができます。 クラスターがプロビジョニングを完了したら、次の手順で HDInsight Linux クラスターで Hue を使用します。

> [!NOTE]  
> Firefox Web ブラウザーを使用して、次の手順に従うことをお勧めします。

1. 「[SSH トンネリングを使用して Apache Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie、およびその他の Web UI にアクセスする](hdinsight-linux-ambari-ssh-tunnel.md)」の情報を使用して、クライアント システムから HDInsight クラスターへの SSH トンネルを作成し、この SSH トンネルをプロキシとして使用するように Web ブラウザーを構成します。

2. SSH トンネルを作成し、これをトラフィックのプロキシとして使用するようにブラウザーを構成したら、プライマリ ヘッド ノードのホスト名を見つける必要があります。 そのためには、ポート 22 で SSH を使用してクラスターに接続します。 たとえば、`ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` を使用して、**USERNAME** に SSH ユーザー名を、**CLUSTERNAME** にクラスターの名前を指定します。

    詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

3. 接続後に、次のコマンドを使用してプライマリ ヘッドノードの完全修飾ドメイン名を取得します。

        hostname -f

    次のような名前が返されます。

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    これは、Hue の Web サイトが配置されているプライマリ ヘッドノードのホスト名です。

4. ブラウザーを使用して、Hue ポータル (http:\//HOSTNAME:8888) を開きます。 HOSTNAME は前の手順で取得した名前で置き換えてください。

   > [!NOTE]  
   > 初回ログイン時、Hue ポータルにログインするためのアカウントを作成するように促されます。 ここで指定した資格情報はポータルに制限され、クラスターのプロビジョニング時に指定した管理者または SSH ユーザーの資格情報には関連しません。

    ![HDInsight Hue ポータルのログイン ウィンドウ](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Hue ポータルの資格情報を指定する")

### <a name="run-a-hive-query"></a>Hive クエリを実行する

1. Hue ポータルから、 **[クエリ エディター]** をクリックし、 **[Hive]** をクリックして Hive エディターを開きます。

    ![HDInsight Hue ポータルで Hive エディターを使用する](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Hive を使用する")
2. **[支援]** タブの **[データベース]** に **hivesampletable** が表示されるはずです。 これは HDInsight のすべての Hadoop クラスターに含まれるサンプル テーブルです。 右ペインでサンプル クエリを入力します。スクリーン キャプチャに示すように、下のペインの **[結果]** タブに出力が表示されます。

    ![HDInsight Hue ポータルの Hive クエリ](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Hive クエリを実行する")

    **[グラフ]** タブを使用し、結果を視覚的に表示することもできます。

### <a name="browse-the-cluster-storage"></a>クラスター ストレージを参照する

1. Hue ポータルから、メニュー バーの右上隅にある **[ファイル ブラウザー]** をクリックします。
2. 既定では、ファイル ブラウザーは **/user/myuser** ディレクトリで起動します。 パスのユーザー ディレクトリの直前の斜線をクリックし、クラスターに関連付けられている Azure ストレージ コンテナーのルートに移動します。

    ![HDInsight Hue ポータルのファイル ブラウザー](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "ファイル ブラウザーを使用する")

3. ファイルまたはフォルダーを右クリックし、利用可能な操作を表示します。 現在のディレクトリにファイルをアップロードするには、右隅にある **[アップロード]** ボタンを使用します。 新しいファイルやディレクトリを作成するには、 **[新規]** ボタンを使用します。

> [!NOTE]  
> Hue ファイル ブラウザーは HDInsight クラスターに関連付けられている既定のコンテナーのコンテンツのみを表示できます。 追加のストレージ アカウント/コンテナーがクラスターに関連付けられている場合、ファイル ブラウザーではアクセスできません。 ただし、Hive ジョブの場合、クラスターに関連付けられている追加のコンテナーに常にアクセスできます。 たとえば、Hive エディターに `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` コマンドを入力すると、追加コンテナーのコンテンツを表示できます。 このコマンドで、 **newcontainer** はクラスターに関連付けられている既定のコンテナーではありません。

## <a name="important-considerations"></a>重要な考慮事項

1. Hue のインストールに使用されるスクリプトは、クラスターのプライマリ ヘッドノードにのみ Hue をインストールします。

2. インストール中、複数の Hadoop サービス (HDFS、YARN、MR2、Oozie) が再起動し、構成を更新します。 スクリプトが Hue のインストールを完了した後、他の Hadoop サービスが起動するまで少し時間がかかる場合があります。 最初はこれが Hue のパフォーマンスに影響を与えることがあります。 すべてのサービスが起動すると、Hue が完全に機能します。
3. Hue は Hive の現在の既定である Apache Tez ジョブを認識しません。 Hive 実行エンジンとして MapReduce を使用する場合、スクリプトで次のコマンドを使用するようにスクリプトを更新します。

        set hive.execution.engine=mr;

4. Linux クラスターの場合、サービスをプライマリ ヘッドノードで実行し、Resource Manager をセカンダリ ヘッドノードで実行するシナリオがありえます。 そのようなシナリオの場合、Hue を利用してクラスターで「実行中」のジョブの詳細を表示するとき、エラーが発生する可能性があります (下の画像を参照)。 ただし、ジョブが完了したときにジョブの詳細を表示できます。

   ![Hue ポータルのサンプル エラー メッセージ](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Hue ポータル エラー")

   これは既知の問題によるものです。 この問題を回避するには、アクティブな Resource Manager もプライマリ ヘッドノードで実行されるように Ambari を変更します。
5. HDInsight クラスターが `wasb://` で Azure Storage を使用するとき、Hue は WebHDFS を認識します。 そのため、スクリプト アクションで使用されるカスタム スクリプトは WebWasb をインストールします。これは WASB と通信するための WebHDFS 互換サービスです。 そのため、Hue ポータルに HDFS と表示されている場合でも (**ファイル ブラウザー**の上にマウスを移動したときなど)、WASB として解釈するべきです。

## <a name="next-steps"></a>次の手順

* [HDInsight クラスターに Apache Giraph をインストールする](hdinsight-hadoop-giraph-install-linux.md)。 クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Giraph をインストールします。 Giraph は、Hadoop でグラフの処理を実行するために使用でき、Azure HDInsight で使用できます。
* [HDInsight クラスターに R をインストールする](hdinsight-hadoop-r-scripts-linux.md)。 クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに R をインストールします。 R は、統計計算用のオープン ソースの言語および環境です。 R は、数百の組み込み統計関数と、関数型プログラミングとオブジェクト指向のプログラミングの特徴を結合した独自のプログラミング言語を提供します。 また、広範なグラフィカル機能も提供します。

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
