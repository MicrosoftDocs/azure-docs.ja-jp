---
title: Azure HDInsight で Grafana を使用する
description: Azure HDInsight で Apache Hadoop クラスターを使用して Grafana ダッシュボードにアクセスする方法について学習します
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/27/2019
ms.openlocfilehash: 81fd3b368f9405192c164ed7a0638caad0cd75fc
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869754"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Azure HDInsight で Grafana にアクセスする

[Grafana](https://grafana.com/) は、人気のあるオープン ソースのグラフとダッシュボードのビルダーです。 Grafana は豊富な機能を備えています。カスタマイズ可能で共有可能なダッシュボードを作成できるだけでなく、テンプレート化/スクリプト化されたダッシュボード、LDAP の統合、複数のデータ ソースなどの機能があります。

現在、Azure HDInsight では、Grafana は Spark、HBase、Kafka、および Interactive Query のクラスターの種類でサポートされています。 Enterprise Security Pack が有効なクラスターではサポートされていません。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="create-an-apache-hadoop-cluster"></a>Apache Hadoop クラスターを作成する

[Azure portal を使用した Apache Hadoop クラスターの作成](../hdinsight-hadoop-create-linux-clusters-portal.md)に関するページを参照してください。 **[クラスターの種類]** では、 **[Spark]** 、 **[Kafka]** 、 **[HBase]** 、または **[Interactive Query]** を選択します。

## <a name="access-the-grafana-dashboard"></a>Grafana ダッシュボードにアクセスする

1. Web ブラウザーから、`https://CLUSTERNAME.azurehdinsight.net/grafana/` に移動します。ここで、CLUSTERNAME はクラスターの名前です。

1. Hadoop クラスター ユーザーの資格情報を入力します。

1. Grafana ダッシュボードが表示され、次の例のようになります:

    :::image type="content" source="./media/hdinsight-grafana/hdinsight-grafana-dashboard.png " alt-text="HDInsight Grafana Web ダッシュボード" border="true":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順で作成したクラスターを削除します。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. 上部の **検索** ボックスに「**HDInsight**」と入力します。

1. **[サービス]** の下の **[HDInsight クラスター]** を選択します。

1. 表示される HDInsight クラスターの一覧で、作成したクラスターの横にある **[...]** を選択します。

1. **[削除]** を選択します。 **[はい]** を選択します。

## <a name="next-steps"></a>次のステップ

HDInsight でデータを分析する方法の詳細については、次の記事を参照してください。

* [HDInsight での Apache Hive の使用](../hadoop/hdinsight-use-hive.md)

* [HDInsight での MapReduce の使用](../hadoop/hdinsight-use-mapreduce.md)

* [HDInsight 用の Visual Studio Hadoop ツールの使用について](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
