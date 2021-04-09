---
title: Linux ベースの HDInsight クラスターの Hadoop で Hue を使用する - Azure
description: HDInsight クラスターに Hue をインストールし、トンネリングを利用して Hue に要求を送信する方法について学習します。 Hue を使用して、ストレージを参照したり Hive または Pig を実行したりします。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/31/2020
ms.openlocfilehash: 97a8dd2476642e693b589b4046f612c5569b9c0b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865130"
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
> カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。 これにより問題が解決する場合もあれば、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。 たとえば、[HDInsight に関する Microsoft Q&A 質問ページ](/answers/topics/azure-hdinsight.html)、[https://stackoverflow.com](https://stackoverflow.com) などの多くのコミュニティ サイトを利用できます。 また、Apache プロジェクトには、[https://apache.org](https://apache.org) に[Hadoop](https://hadoop.apache.org/) などのプロジェクト サイトもあります。

## <a name="install-hue-using-script-actions"></a>スクリプト アクションを使用した Hue のインストール

スクリプト アクションについては、次の表の情報を参照してください。 スクリプト アクションの使用手順については、「[Script Action を使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。

> [!NOTE]  
> HDInsight クラスターに Hue をインストールするには、A4 (8 コア、14 GB メモリ) 以上のヘッドノード サイズが推奨されます。

|プロパティ |値 |
|---|---|
|スクリプトの種類:|- Custom|
|名前|Hue のインストール|
|Bash スクリプト URI|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|ノードの種類:|Head|

## <a name="use-hue-with-hdinsight-clusters"></a>HDInsight クラスターで Hue を使用する

通常のクラスターでは、Hue を使用するユーザー アカウントは 1 つしか許可されません。 マルチユーザー アクセスの場合は、クラスターで [Enterprise セキュリティ パッケージ](./domain-joined/hdinsight-security-overview.md)を有効にします。 SSH トンネリングは、実行された後のクラスターで Hue にアクセスするための唯一の方法です。 SSH によるトンネリングでは、Hue が実行されているクラスターのヘッドノードにトラフィックが直接進むことができます。 クラスターがプロビジョニングを完了したら、次の手順で HDInsight クラスターで Hue を使用します。

> [!NOTE]  
> Firefox Web ブラウザーを使用して、次の手順に従うことをお勧めします。

1. 「[SSH トンネリングを使用して Apache Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie、およびその他の Web UI にアクセスする](hdinsight-linux-ambari-ssh-tunnel.md)」の情報を使用して、クライアント システムから HDInsight クラスターへの SSH トンネルを作成し、この SSH トンネルをプロキシとして使用するように Web ブラウザーを構成します。

1. [ssh コマンド](./hdinsight-hadoop-linux-use-ssh-unix.md)を使用してクラスターに接続します。 次のコマンドを編集して CLUSTERNAME をクラスターの名前に置き換えてから、そのコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 接続後に、次のコマンドを使用してプライマリ ヘッドノードの完全修飾ドメイン名を取得します。

    ```bash
    hostname -f
    ```

    次のような名前が返されます。

    ```output
    myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    ```

    これは、Hue の Web サイトが配置されているプライマリ ヘッドノードのホスト名です。

1. ブラウザーを使用して、Hue ポータル (`http://HOSTNAME:8888`) を開きます。 HOSTNAME は前の手順で取得した名前で置き換えてください。

   > [!NOTE]  
   > 初回ログイン時、Hue ポータルにログインするためのアカウントを作成するように促されます。 ここで指定した資格情報はポータルに制限され、クラスターのプロビジョニング時に指定した管理者または SSH ユーザーの資格情報には関連しません。

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png" alt-text="HDInsight Hue ポータルのログイン ウィンドウ":::

### <a name="run-a-hive-query"></a>Hive クエリを実行する

1. Hue ポータルから、 **[クエリ エディター]** を選択し、 **[Hive]** を選択して Hive エディターを開きます。

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png" alt-text="HDInsight Hue ポータルで Hive エディターを使用する":::

2. **[支援]** タブの **[データベース]** に **hivesampletable** が表示されるはずです。 これは HDInsight のすべての Hadoop クラスターに含まれるサンプル テーブルです。 右ペインでサンプル クエリを入力します。スクリーン キャプチャに示すように、下のペインの **[結果]** タブに出力が表示されます。

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png" alt-text="HDInsight Hue ポータルの Hive クエリ":::

    **[グラフ]** タブを使用し、結果を視覚的に表示することもできます。

### <a name="browse-the-cluster-storage"></a>クラスター ストレージを参照する

1. Hue ポータルから、メニュー バーの右上隅にある **[ファイル ブラウザー]** を選択します。
2. 既定では、ファイル ブラウザーは **/user/myuser** ディレクトリで起動します。 パスのユーザー ディレクトリの直前の斜線を選択し、クラスターに関連付けられている Azure ストレージ コンテナーのルートに移動します。

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png" alt-text="HDInsight ポータル ファイル ブラウザー":::

3. ファイルまたはフォルダーを右クリックし、利用可能な操作を表示します。 現在のディレクトリにファイルをアップロードするには、右隅にある **[アップロード]** ボタンを使用します。 新しいファイルやディレクトリを作成するには、 **[新規]** ボタンを使用します。

> [!NOTE]  
> Hue ファイル ブラウザーは HDInsight クラスターに関連付けられている既定のコンテナーのコンテンツのみを表示できます。 追加のストレージ アカウント/コンテナーがクラスターに関連付けられている場合、ファイル ブラウザーではアクセスできません。 ただし、Hive ジョブの場合、クラスターに関連付けられている追加のコンテナーに常にアクセスできます。 たとえば、Hive エディターに `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` コマンドを入力すると、追加コンテナーのコンテンツを表示できます。 このコマンドで、 **newcontainer** はクラスターに関連付けられている既定のコンテナーではありません。

## <a name="important-considerations"></a>重要な考慮事項

1. Hue のインストールに使用されるスクリプトは、クラスターのプライマリ ヘッドノードにのみ Hue をインストールします。

1. インストール中、複数の Hadoop サービス (HDFS、YARN、MR2、Oozie) が再起動し、構成を更新します。 スクリプトが Hue のインストールを完了した後、他の Hadoop サービスが起動するまで少し時間がかかる場合があります。 最初はこれが Hue のパフォーマンスに影響を与えることがあります。 すべてのサービスが起動すると、Hue が完全に機能します。

1. Hue は、Hive の現在の既定値である Apache Tez ジョブを認識しません。 Hive 実行エンジンとして MapReduce を使用する場合、スクリプトで次のコマンドを使用するようにスクリプトを更新します。

   `set hive.execution.engine=mr;`

1. Linux クラスターの場合、サービスをプライマリ ヘッドノードで実行し、Resource Manager をセカンダリ ヘッドノードで実行するシナリオがありえます。 そのようなシナリオの場合、Hue を利用してクラスターで「実行中」のジョブの詳細を表示するとき、エラーが発生する可能性があります (下の画像を参照)。 ただし、ジョブが完了したときにジョブの詳細を表示できます。

   :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png" alt-text="Hue ポータルのサンプル エラー メッセージ":::

   これは既知の問題によるものです。 この問題を回避するには、アクティブな Resource Manager もプライマリ ヘッドノードで実行されるように Ambari を変更します。

1. HDInsight クラスターが `wasbs://` で Azure Storage を使用するとき、Hue は WebHDFS を認識します。 そのため、スクリプト アクションで使用されるカスタム スクリプトは WebWasb をインストールします。これは WASB と通信するための WebHDFS 互換サービスです。 そのため、Hue ポータルに HDFS と表示されている場合でも (**ファイル ブラウザー** の上にマウスを移動したときなど)、WASB として解釈するべきです。

## <a name="next-steps"></a>次のステップ

[HDInsight クラスターに R をインストールする](./r-server/r-server-overview.md)。 クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに R をインストールします。 R は、統計計算用のオープン ソースの言語および環境です。 R は、数百の組み込み統計関数と、関数型プログラミングとオブジェクト指向のプログラミングの特徴を結合した独自のプログラミング言語を提供します。 また、広範なグラフィカル機能も提供します。