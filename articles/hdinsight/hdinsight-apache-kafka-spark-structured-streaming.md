---
title: "Kafka での Apache Spark 構造化ストリーミング - Azure HDInsight | Microsoft Docs"
description: "Apache Spark ストリーミング (DStreams) を使って Apache Kafka 内外でデータを取得する方法について説明します。 この例では、Jupyter Notebook を使用して HDInsight 上で Spark からデータをストリームします。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: larryfr
ms.openlocfilehash: 565f840e0ac7ec1a68361ac1613da5d42459c979
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-spark-structured-streaming-with-kafka-preview-on-hdinsight"></a>HDInsight 上で Kafka による Spark 構造化ストリーミング (プレビュー) を使用する

Spark 構造化ストリーミングを使って、Azure HDInsight 上で Apache Kafka からデータを読み込む方法を説明します。

Spark 構造化ストリーミングは、Spark SQL に組み込まれたストリーミング処理エンジンであり、 静的データに対してバッチ計算と同様にストリーミング計算を表現できるようになります。 構造化ストリーミングの詳細については、「[Structured Streaming Programming Guide [Alpha]](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)」(構造化ストリーミングのプログラミング ガイド [アルファ]) をご覧ください。

> [!IMPORTANT]
> この例では、HDInsight 3.6 上で Spark 2.1 を使用しました。 構造化ストリーミングは、Spark 2.1 では__アルファ__と見なされます。
>
> このドキュメントの手順では、HDInsight の Spark クラスターと HDInsight の Kafka クラスターの両方を含む Azure リソース グループを作成します。 これらのクラスターは両方とも、Spark クラスターが Kafka クラスターと直接通信できるように、Azure Virtual Network 内に配置します。
>
> このドキュメントの手順を完了したら、余分に課金されないようにするためにクラスターは削除してください。

## <a name="create-the-clusters"></a>クラスターの作成

HDInsight の Apache Kafka では、パブリック インターネットを介した Kafka ブローカーへのアクセスは提供されていません。 Kafka と通信するすべてのものは、Kafka クラスター内のノードと同じ Azure 仮想ネットワークに存在している必要があります。 この例では、Kafka クラスターと Spark クラスターの両方を Azure 仮想ネットワーク内に配置します。 次の図に、クラスター間の通信フローを示します。

![Azure 仮想ネットワークにおける Spark クラスターと Kafka クラスターの図](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Kafka サービスは、仮想ネットワーク内の通信に制限されます。 SSH や Ambari など、クラスター上の他のサービスは、インターネット経由でアクセスできます。 HDInsight で使用できるパブリック ポートの詳細については、「[HDInsight で使用されるポートと URI](hdinsight-hadoop-port-settings-for-services.md)」を参照してください。

Azure 仮想ネットワーク、Kafka、および Spark クラスターは手動で作成できますが、Azure Resource Manager テンプレートを使用する方が簡単です。 次の手順に従って、Azure 仮想ネットワーク、Kafka クラスター、および Spark クラスターを Azure サブスクリプションにデプロイします。

1. 次のボタンを使用して Azure にサインインし、Azure Portal でテンプレートを開きます。
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Azure Resource Manager テンプレートは **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json** にあります。

    このテンプレートでは、次のリソースを作成します。

    * HDInsight 3.5 クラスター上の Kafka
    * HDInsight 3.6 クラスター上の Spark
    * Azure Virtual Network (HDInsight クラスターを含む)

    > [!IMPORTANT]
    > この例で使用する構造化ストリーミングのノートブックでは、HDInsight 3.6 上に Spark が必要です。 HDInsight 上で以前のバージョンの Spark を使用している場合は、ノートブックを使用するとエラーを受信します。

2. 以下の情報を使用して、**[カスタム デプロイ]** セクションに各エントリを入力します。
   
    ![HDInsight のカスタム デプロイ](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **[リソース グループ]**: グループを作成するか、または既存のグループを選択します。 このグループに HDInsight クラスターが含まれます。

    * **[場所]**: 地理的に近い場所を選択します。

    * **[Base Cluster Name] \(ベース クラスター名)**: この値は、Spark クラスターと Kafka クラスターのベース名として使用されます。 たとえば、「**hdi**」と入力すると、spark-hdi__ という名前の Spark クラスターと、**kafka-hdi** という名前の Kafka クラスターが作成されます。

    * **[Cluster Login User Name] \(クラスター ログイン ユーザー名)**: Spark クラスターと Kafka クラスターの管理者のユーザー名。

    * **[クラスター ログイン パスワード]**: Spark クラスターと Kafka クラスターの管理者のユーザー パスワード。

    * **[SSH ユーザー名]**: Spark クラスターおよび Kafka クラスターの作成に使用する SSHユーザー。

    * **[SSH パスワード]**: Spark クラスターおよび Kafka クラスター用の SSH ユーザーのパスワード。

3. **使用条件**を読み、**[上記の使用条件に同意する]** をオンにします。

4. 最後に、**[ダッシュボードにピン留めする]** をオンにし、**[購入]** をクリックします。 クラスターの作成には約 20 分かかります。

リソースが作成されると、概要ページが表示されます。

![vnet とクラスターのリソース グループ情報](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> 各 HDInsight クラスターの名前が **spark-BASENAME** および **kafka-BASENAME** であることに注目してください。BASENAME はテンプレートで指定した名前です。 これらの名前は、後の手順でクラスターに接続するときに使用します。

## <a name="get-the-kafka-brokers"></a>Kafka ブローカーを取得する

この例のコードは、Kafka クラスターにある Kafka ブローカー ホストに接続します。 2 つの Kafka ブローカー ホストのアドレスを検出するには、以下の PowerShell または Bash の例を使います。

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
($brokerHosts -join ":9092,") + ":9092"
```

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

プロンプトが表示されたら、クラスター ログイン (管理者) アカウントのパスワードを入力します。

> [!NOTE]
> この例では、`$CLUSTERNAME` に Kafka クラスターの名前に含めることを前提としています。
>
> この例では、[jq](https://stedolan.github.io/jq/) ユーティリティを使って JSON ドキュメントからのデータを解析します。

出力は次のテキストのようになります。

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

このドキュメントの以降のセクションで使用するため、この情報を保存してください。

## <a name="get-the-notebooks"></a>ノートブックを取得する

このドキュメントに示す例のコードは、[https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming) で入手できます。

## <a name="upload-the-notebooks"></a>ノートブックをアップロードする

プロジェクトから HDInsight クラスター上の Spark へノートブックをアップロードするには、以下の手順を使用します。

1. Web ブラウザーで、Spark クラスターの Jupyter Notebook に接続します。 次の URL の`CLUSTERNAME` をお使いの Kafka クラスターの名前に置き換えます。

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    プロンプトが表示されたら、クラスターの作成時に使用したログイン (管理者) パスワードを入力します。

2. ページの右上隅の __[アップロード]__ ボタンを使用して、__Stream-Tweets-To_Kafka.ipynb__ ファイルをクラスターにアップロードします。 __[開く]__ を選択して、アップロードを開始します。

    ![[アップロード] ボタンを使用して、Notebook を選択してアップロード](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![KafkaStreaming.ipynb ファイルを選択する](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. ノートブックの一覧で __Stream-Tweets-To_Kafka.ipynb__ エントリを検索し、横にある __[アップロード]__ ボタンを選択します。

    ![ノートブックをアップロードするために KafkaStreaming.ipynb エントリの [アップロード] ボタンを使用する](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)

4. 手順 1 ～ 3 を繰り返して、__Spark-Structured-Streaming-From-Kafka.ipynb__ ノートブックを読み込みます。

## <a name="load-tweets-into-kafka"></a>Kafka にツイートを読み込む

ファイルをアップロードした後、__Stream-Tweets-To_Kafka.ipynb__ エントリを選択してノートブックを開きます。 ノートブックの手順に従って Kafka にツイートを読み込みます。

## <a name="process-tweets-using-spark-structured-streaming"></a>Spark 構造化ストリーミングを使用してツイートを処理する

Jupyter Notebook のホーム ページから、__Spark-Structured-Streaming-From-Kafka.ipynb__ エントリを選択します。 ノートブックの手順に従い、Spark 構造化ストリーミングを使って Kafka からツイートを読み込みます。

## <a name="next-steps"></a>次のステップ

この記事では、Spark 構造化ストリームの使用方法を説明しました。Spark および Kafka の操作に関する詳細については、以下のドキュメントをご覧ください。

* [Kafka で Spark ストリーミングを (DStream) を使用する方法](hdinsight-apache-spark-with-kafka.md)
* [HDInsight で Jupyter Notebook と Spark を使い始める](hdinsight-apache-spark-jupyter-spark-sql.md)