---
title: Azure HDInsight でのスクリプト アクションのトラブルシューティング
description: Azure HDInsight でのスクリプト アクションの一般的なトラブルシューティングの手順。
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 73b958964db2d0b308dd6dfc34024d61ce5ad8af
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871437"
---
# <a name="troubleshoot-script-actions-in-azure-hdinsight"></a>Azure HDInsight でのスクリプト アクションのトラブルシューティング

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="viewing-logs"></a>ログの表示

Apache Ambari Web UI を使用すると、スクリプト アクションによってログに記録された情報を表示できます。 クラスターの作成中にスクリプトでエラーが発生した場合、ログは既定のストレージ アカウントにあります。 このセクションでは、これら両方のオプションを使用してログを取得する方法について説明します。

### <a name="apache-ambari-web-ui"></a>Apache Ambari Web UI

1. Web ブラウザーから、`https://CLUSTERNAME.azurehdinsight.net` に移動します。ここで、`CLUSTERNAME` はクラスターの名前です。

1. ページ上部のバーから **[OPS]** エントリを選択します。 これにより、Ambari を使用してクラスターで実行される、現在と過去の操作の一覧が表示されます。

    :::image type="content" source="./media/troubleshoot-script-action/hdi-apache-ambari-nav.png" alt-text="Ambari Web UI バーで OPS を選択" border="true":::

1. **[Operations]** 列で **run\_customscriptaction** エントリを探します。 これらのエントリは、スクリプト アクションの実行時に作成されます。

    :::image type="content" source="./media/troubleshoot-script-action/ambari-script-action.png" alt-text="Apache Ambari スクリプト アクションの操作" border="true":::

    **STDOUT** と **STDERR** の出力を表示するには、**run\customscriptaction** エントリを選択してリンクをたどります。 この出力結果はスクリプトの実行時に生成され、有益な情報が含まれていることがあります。

### <a name="default-storage-account"></a>既定のストレージ アカウント

スクリプト エラーのためにクラスターの作成が失敗した場合、クラスター ストレージ アカウントにログが保持されます。

* ストレージ ログは、 `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`にあります。

    :::image type="content" source="./media/troubleshoot-script-action/script-action-logs-in-storage.png" alt-text="スクリプト アクション ログ" border="true":::

    このディレクトリの下で、**ヘッド ノード**、**ワーカー ノード**、および **zookeeper ノード** ごとにログが整理されています。 次の例を参照してください。

    * **ヘッド ノード**: `<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **ワーカー ノード**: `<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Zookeeper ノード**: `<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* 対応するホストのすべての **stdout** と **stderr** が、ストレージ アカウントにアップロードされます。 各スクリプト アクションに対して、**output-\*.txt** と **errors-\*.txt** が 1 つずつあります。 **output-*.txt** ファイルには、ホストで実行されたスクリプトの URI に関する情報が含まれます。 次のテキストはこの情報の例です。

    ```output
    'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'
    ```

* 同じ名前のスクリプト アクション クラスターを繰り返し作成できます。 そのような場合は、**DATE** フォルダー名に基づいて適切なログを識別できます。 たとえば、異なる日付で作成されるクラスターのフォルダー構造 **mycluster** は、ログ エントリには次のように表示されます。

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* 同じ日に同じ名前のスクリプト アクション クラスターを作成する場合は、一意のプレフィックスを使用して該当するログ ファイルを識別できます。

* 12:00 AM (深夜 0 時) 近くにクラスターを作成すると、ログ ファイルが 2 日間にまたがる可能性があります。 そのような場合は、同じクラスターに日付が異なる 2 つのフォルダーが作成されます。

* 既定のコンテナーへのログ ファイルのアップロードは、特に大きなクラスターの場合、最大 5 分かかることがあります。 そのため、ログにアクセスする必要がある場合は、スクリプト アクションが失敗したときにクラスターをすぐに削除しないでください。

## <a name="ambari-watchdog"></a>Ambari ウォッチドッグ

Linux ベースの HDInsight クラスターでは、Ambari ウォッチドッグ hdinsightwatchdog のパスワードは変更しないでください。 パスワードを変更すると、HDInsight クラスターで新しいスクリプト アクションを実行できなくなります。

## <a name="cant-import-name-blobservice"></a>名前 BlobService をインポートできない

__現象__。 スクリプト操作が失敗します。 Ambari で操作を表示すると、次のエラーに似たテキストが表示されます。

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__原因__。 このエラーは、HDInsight クラスターに含まれている Python Azure Storage クライアントをアップグレードする場合に発生します。 HDInsight は、Azure Storage クライアント 0.20.0 を予期しています。

__解決策__。 このエラーを解決するには、`ssh` を使用して各クラスター ノードを手動で接続します。 次のコマンドを実行して、ストレージ クライアントの正しいバージョンを再インストールします。

```bash
sudo pip install azure-storage==0.20.0
```

SSH を使用してクラスターに接続する方法については、「[SSH を使用して HDInsight (Apache Hadoop) に接続する](hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。

## <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>クラスターの作成時に使用されたスクリプトが履歴に表示されない

クラスターが 2016 年 3 月 15 日より前に作成された場合、スクリプト アクション履歴にエントリが表示されない可能性があります。 クラスターのサイズ変更を行うと、スクリプト アクション履歴にスクリプトが表示されます。

ただし、例外が 2 つあります。

* クラスターが 2015 年 9 月 1 日より前に作成された場合。 この日付は、スクリプト アクションが導入された日付です。 この日付より前に作成されたクラスターに関しては、クラスター作成にスクリプト アクションを使用できませんでした。

* クラスターを作成するときに、複数のスクリプト アクションを使用した場合。 または、複数のスクリプトに対して同じ名前を使用したか、複数のスクリプトに対して同じ名前と URI、異なるパラメーターを使用した場合。 この場合は、次のエラーが発生します。

    ```
    No new script actions can be run on this cluster because of conflicting script names in existing scripts. Script names provided at cluster creation must be all unique. Existing scripts are run on resize.
    ```

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]