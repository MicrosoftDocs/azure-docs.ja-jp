---
title: HDInsight で Apache Hadoop サービスのヒープ ダンプを有効にする - Azure
description: デバッグと分析のために Linux ベースの HDInsight クラスターから Apache Hadoop サービスのヒープ ダンプを有効にします。
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: fe5b2a1f083e246ea61854c9cbe03932e6655fdb
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866592"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Linux ベースの HDInsight で Apache Hadoop サービスのヒープ ダンプを有効にする

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

ヒープ ダンプには、ダンプが作成された時点の変数の値を含む、アプリケーションのメモリのスナップショットが含まれています。 これらは、実行時に発生する問題を診断するのに便利です。

## <a name="services"></a>サービス

次のサービスのヒープ ダンプを有効にできます。

* **Apache hcatalog** - tempelton
* **Apache hive** - hiveserver2、metastore、derbyserver
* **mapreduce** - jobhistoryserver
* **Apache yarn** - resourcemanager、nodemanager、timelineserver
* **Apache hdfs** - datanode、secondarynamenode、namenode

HDInsight によって実行されるマップと削減のプロセスに対して、ヒープ ダンプを有効にすることもできます。

## <a name="understanding-heap-dump-configuration"></a>ヒープ ダンプ構成について

ヒープ ダンプは、サービスの開始時にオプション (opts またはパラメーターとも呼ばれる) を JVM に渡すことによって有効になります。 ほとんどの [Apache Hadoop](https://hadoop.apache.org/) サービスでは、サービスを開始するのに使用されるシェル スクリプトを変更することで、これらのオプションを渡すことができます。

各スクリプトには、JVM に渡されるオプションを含む **\*\_OPTS** のエクスポートが含まれています。 たとえば、**hadoop env.sh** スクリプトには、`export HADOOP_NAMENODE_OPTS=` で始まる行に NameNode サービスのオプションが含まれています。

これらの処理が MapReduce サービスの子プロセスである場合は、マップと削減のプロセスが多少異なります。 マップや削減のプロセスはそれぞれ子コンテナーで実行され、JVM オプションを含む 2 つのエントリがあります。 どちらも **mapred-site.xml** に含まれています。

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]  
> [Apache Ambari](https://ambari.apache.org/) がクラスター内のノード間の変更のレプリケーションを処理するように、Ambari を使用してスクリプトと mapred-site.xml 設定の両方を変更することをお勧めします。 具体的な手順については、「[Apache Ambari の使用](#using-apache-ambari)」セクションを参照してください。

### <a name="enable-heap-dumps"></a>ヒープ ダンプを有効にする

次のオプションは、OutOfMemoryError が発生したときに、ヒープ ダンプを有効にします。

`-XX:+HeapDumpOnOutOfMemoryError`

**+** は、このオプションが有効になっていることを示します。 既定では無効になっています。

> [!WARNING]  
> ヒープ ダンプは、ダンプ ファイルが大きくなる可能性があるため、既定では HDInsight の Hadoop サービスで無効になっています。 トラブルシューティングのために有効にした場合は、問題を再現してダンプ ファイルを収集したら、忘れずに無効にしてください。

### <a name="dump-location"></a>ダンプの場所

ダンプ ファイルの既定の場所は、現在の作業ディレクトリです。 次のオプションを使用して、ファイルの保存先を制御できます。

`-XX:HeapDumpPath=/path`

たとえば、`-XX:HeapDumpPath=/tmp` を使用すると、ダンプは /tmp ディレクトリに保存されます。

### <a name="scripts"></a>スクリプト

**OutOfMemoryError** が発生したときに、スクリプトをトリガーすることもできます。 たとえば、通知をトリガーすると、エラーが発生したことがわかります。 __OutOfMemoryError__ に対してスクリプトをトリガーするには、次のオプションを使用します。

`-XX:OnOutOfMemoryError=/path/to/script`

> [!NOTE]  
> Apache Hadoop は分散システムなので、使用するスクリプトは、サービスを実行しているクラスター内のすべてのノードに配置する必要があります。
> 
> また、スクリプトはサービスが実行するアカウントからアクセスできる場所にあり、実行権限を提供する必要もあります。 たとえば、スクリプトを `/usr/local/bin` に保存し、`chmod go+rx /usr/local/bin/filename.sh` を使用して読み取りと実行権限を付与できます。

## <a name="using-apache-ambari"></a>Apache Ambari の使用

サービスの構成を変更するには、次の手順を実行します。

1. Web ブラウザーから、`https://CLUSTERNAME.azurehdinsight.net` に移動します。ここで、`CLUSTERNAME` はクラスターの名前です。

2. 左側の一覧を使用して、変更するサービス領域を選択します。 たとえば、 **[HDFS]** です。 中央の領域で、 **[Configs]** タブを選択します。

    :::image type="content" source="./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-service-config-tab.png" alt-text="HDFS Configs タブが選択されている Ambari Web の画像":::

3. **[フィルター]** エントリに「**opts**」と入力します。 このテキストを含む項目のみが表示されます。

    :::image type="content" source="./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdinsight-filter-list.png" alt-text="Apache Ambari 構成のフィルター処理された一覧":::

4. ヒープ ダンプを有効にするサービスの **\*\_OPTS** エントリを検索し、有効にするオプションを追加します。 次の図では、`-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` を **HADOOP\_NAMENODE\_OPTS** エントリに追加しました。

    :::image type="content" source="./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hadoop-namenode-opts.png" alt-text="Apache Ambari の hadoop-namenode-opts":::

   > [!NOTE]  
   > マップまたは削減の子プロセスのヒープ ダンプが有効になっている場合は、**mapreduce.admin.map.child.java.opts** と **mapreduce.admin.reduce.child.java.opts** というフィールドを探します。

    **[保存]** ボタンを押して、変更を保存します。 変更を説明する短いメモを入力できます。

5. 変更が適用されると、**再起動が必要** であることを示すアイコンが 1 つ以上のサービスの横に表示されます。

    :::image type="content" source="./media/hdinsight-hadoop-collect-debug-heap-dump-linux/restart-required-icon.png" alt-text="再起動が必要アイコンと [再起動] ボタン":::

6. 再起動が必要な各サービスを選択し、**[サービス アクション]** ボタンを使用して **[メンテナンス モードの有効化]** を選択します。 メンテナンス モードは、再起動したときに、このサービスからアラートが生成されないようにします。

    :::image type="content" source="./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-maintenance-mode.png" alt-text="HDI の [メンテナンス モードの有効化] メニュー":::

7. メンテナンス モードを有効にしたら、サービスの **[再起動]** ボタンを使用して **[すべてを再起動]** を選択します。

    :::image type="content" source="./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-restart-all-button.png" alt-text="Apache Ambari の [すべてを再起動] エントリ":::

   > [!NOTE]  
   > **[再起動]** ボタンのエントリは、サービスによって異なる場合があります。

8. サービスが再開したら、**[サービス アクション]** ボタンを使用して **[メンテナンス モードの無効化]** を選択します。 これにより、Ambari はサービスのアラートの監視を再開します。