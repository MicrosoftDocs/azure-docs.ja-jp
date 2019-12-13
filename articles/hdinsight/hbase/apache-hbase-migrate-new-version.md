---
title: HBase クラスターを新しいバージョンに移行する - Azure HDInsight
description: Azure HDInsight で Apache HBase クラスターを新しいバージョンに移行する方法。
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: ashishth
ms.openlocfilehash: b03bbc7aacd3bfa2a8e29296a5fafed7d4e7e37a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931526"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Apache HBase クラスターを新しいバージョンに移行する

この記事では、Azure HDInsight で Apache HBase クラスターを新しいバージョンに更新するために必要な手順について説明します。

> [!NOTE]  
> アップグレード中のダウンタイムは、分単位で最小限に抑える必要があります。 このダウンタイムは、すべてのメモリ内データをフラッシュした後、新しいクラスターでサービスを構成して再起動するための手順が原因で発生します。 結果は、ノード数、データ量、およびその他の可変要素によって異なります。

## <a name="review-apache-hbase-compatibility"></a>Apache HBase の互換性の確認

Apache HBase をアップグレードする前に、移行元クラスターと移行先クラスターの HBase バージョンの互換性を確保します。 詳しくは、[Apache HDInsight で使用できる Hadoop コンポーネントとバージョン](../hdinsight-component-versioning.md)に関するページをご覧ください。

> [!NOTE]  
> [HBase ブック](https://hbase.apache.org/book.html#upgrading)のバージョン互換性マトリックスを確認することを強くお勧めします。

バージョン互換性マトリックスの例を次に示します。 Y は互換性を示し、N は潜在的な非互換性を示します。

| 互換性の種類 | メジャー バージョン| マイナー バージョン | 修正プログラム |
| --- | --- | --- | --- |
| クライアント/サーバー間の接続の互換性 | N | Y | Y |
| サーバー/サーバー間の互換性 | N | Y | Y |
| ファイル形式の互換性 | N | Y | Y |
| クライアント API の互換性 | N | Y | Y |
| クライアント バイナリの互換性 | N | N | Y |
| **サーバー側の制限付きの API 互換性** |  |  |  |
| 安定版 | N | Y | Y |
| 進化 | N | N | Y |
| 不安定 | N | N | N |
| 依存関係の互換性 | N | Y | Y |
| 運用の互換性 | N | N | Y |

> [!NOTE]  
> 重大な非互換性は、HBase のバージョン リリース ノートに記載されています。

## <a name="upgrade-with-same-apache-hbase-major-version"></a>同じ Apache HBase メジャー バージョンでのアップグレード

Azure HDInsight で Apache HBase クラスターをアップグレードするには、次の手順を実行します。

1. HBase 互換性マトリックスおよびリリース ノートで、アプリケーションが新しいバージョンと互換性があることを確認します。 HDInsight および HBase のターゲット バージョンを実行しているクラスターでアプリケーションをテストします。

2. 同じストレージ アカウントで別のコンテナー名を使用して、[新しい移行先 HDInsight クラスターを設定](../hdinsight-hadoop-provision-linux-clusters.md)します。

    ![同じストレージ アカウントを使用して別のコンテナーを作成する](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. アップグレードするクラスターである、ソース HBase クラスターをフラッシュします。 HBase では、_memstore_ と呼ばれるメモリ内ストアに受信データが書き込まれます。 memstore が一定のサイズに達すると、HBase はクラスターのストレージ アカウントに長期保存するためにディスクにフラッシュします。 古いクラスターを削除すると、memstore がリサイクルされ、データが失われる可能性があります。 各テーブルの memstore をディスクに手動でフラッシュするには、次のスクリプトを実行します。 このスクリプトの最新バージョンは、Azure の [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh) にあります。

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1, hn2.. or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```
    
4. 古い HBase クラスターへのインジェストを停止します。
5. memstore 内の最近のデータが確実にすべてフラッシュされるようにするために、前のスクリプトをもう一度実行します。
6. 古いクラスター上の [Apache Ambari](https://ambari.apache.org/) (https://OLDCLUSTERNAME.azurehdidnsight.net) にサインインして、HBase サービスを停止します。 サービスを停止することを確認するプロンプトが表示されたら、HBase のメンテナンス モードをオンにするためのチェックボックスをオンにします。 Ambari への接続とその使用方法の詳細については、「[Ambari Web UI を使用した HDInsight クラスターの管理](../hdinsight-hadoop-manage-ambari.md)」を参照してください。

    ![Ambari で、[Service Actions]\(サービス アクション\) の下の [Services]\(サービス\) > [HBase] > [Stop]\(停止\) をクリックします](./media/apache-hbase-migrate-new-version/stop-hbase-services1.png)

    ![[Turn On Maintenance Mode for HBase]\(HBase のメンテナンス モードをオンにする\) チェックボックスをオンにし、操作を確定します](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. 新しい HDInsight クラスターの Ambari にサインインします。 `fs.defaultFS` HDFS 設定を、元のクラスターで使用されているコンテナー名を指すように変更します。 この設定は、 **[HDFS] > [Configs]\(構成\) > [Advanced]\(詳細\) > [Advanced core-site]\(高度なコアサイト\)** にあります。

    ![Ambari で、[Services]\(サービス\) > [HDFS] > [Configs]\(構成\) > [Advanced]\(詳細\) をクリックします](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![Ambari で、コンテナー名を変更する](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. **拡張書き込み機能を備えた HBase クラスターを使用していない場合は、この手順をスキップしてください。この手順は、拡張書き込み機能を備えた HBase クラスターにのみ必要です。**
   
   `hbase.rootdir` パスを、元のクラスターのコンテナーを指すように変更します。

    ![Ambari で、HBase rootdir のコンテナー名を変更する](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)
1. HDInsight 3.6 を 4.0 にアップグレードする場合は、次の手順に従います。それ以外の場合は手順 10 までスキップしてください。
    1. Ambari で必要なすべてのサービスを再起動するために、 **[Services]\(サービス\　)**  >  **[Restart All Required]\(必須をすべて再起動\)** を選択します。
    1. HBase サービスを停止します。
    1. Zookeeper ノードに SSH 接続し、[zkCli](https://github.com/go-zkcli/zkcli) コマンド `rmr /hbase-unsecure` を実行して Zookeeper から HBase ルートの znode を削除します。
    1. HBase を再起動します。
1. 4\.0 以外の HDInsight バージョンにアップグレードする場合は、次の手順に従います。
    1. 変更を保存します。
    1. Ambari の指示に従って、必要なすべてのサービスを再起動します。
1. アプリケーションが新しいクラスターを指すように設定します。

    > [!NOTE]  
    > アップグレードするとアプリケーションの静的 DNS が変更されます。 この DNS をハードコーディングする代わりに、ドメイン名の DNS 設定でクラスターの名前を指す CNAME を構成できます。 もう 1 つの選択肢は、再デプロイせずに更新できる、アプリケーション用の構成ファイルを使用することです。

12. インジェストを開始して、すべてが期待どおりに機能しているかどうかを確認します。
13. 新しいクラスターに問題がない場合は、元のクラスターを削除します。

## <a name="next-steps"></a>次の手順

[Apache HBase](https://hbase.apache.org/) の詳細と HDInsight クラスターのアップグレードについては、次の記事を参照してください。

* [HDInsight クラスターを新しいバージョンにアップグレードする](../hdinsight-upgrade-cluster.md)
* [Apache Ambari Web UI を使用して Azure HDInsight を監視および管理する](../hdinsight-hadoop-manage-ambari.md)
* [Apache Hadoop のコンポーネントとバージョン](../hdinsight-component-versioning.md)
* [Apache Ambari を使用した構成の最適化](../hdinsight-changing-configs-via-ambari.md#apache-hbase-optimization-with-the-ambari-web-ui)
