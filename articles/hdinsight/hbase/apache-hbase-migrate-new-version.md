---
title: HBase クラスターを新しいバージョンに移行する - Azure HDInsight
description: HBase クラスターを新しいバージョンに移行する方法。
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 3b27fe0bec4ec23739e3cff02d6aed667f1d3e1d
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226829"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Apache HBase クラスターを新しいバージョンに移行する

[Apache Spark](https://spark.apache.org/) や [Apache Hadoop](https://hadoop.apache.org/) などのジョブベースのクラスターをアップグレードするのは簡単です。「[HDInsight クラスターを新しいバージョンにアップグレードする](../hdinsight-upgrade-cluster.md)」を参照してください。

1. 一時的なデータ (ローカルに保存されているデータ) をバックアップします。
2. 既存のクラスターを削除します。
3. 同じ VNET サブネットに新しいクラスターを作成します。
4. 一時的なデータをインポートします。
5. 新しいクラスターでジョブを開始し、処理を続行します。

[Apache HBase](https://hbase.apache.org/) クラスターをアップグレードするには、この記事で説明するように、追加の手順が必要です。

> [!NOTE]  
> アップグレード中のダウンタイムは、分単位で最小限に抑える必要があります。 このダウンタイムは、すべてのメモリ内データをフラッシュした後、新しいクラスターでサービスを構成して再起動するための手順が原因で発生します。 結果は、ノード数、データ量、およびその他の可変要素によって異なります。

## <a name="review-apache-hbase-compatibility"></a>Apache HBase の互換性の確認

Apache HBase をアップグレードする前に、移行元クラスターと移行先クラスターの HBase バージョンの互換性を確保します。 詳しくは、[Apache HDInsight で使用できる Hadoop コンポーネントとバージョン](../hdinsight-component-versioning.md)に関するページをご覧ください。

> [!NOTE]  
> [HBase ブック](https://hbase.apache.org/book.html#upgrading)のバージョン互換性マトリックスを確認することを強くお勧めします。

バージョン互換性マトリックスの例を次に示します。Y は互換性を示し、N は潜在的な非互換性を示します。

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

次のシナリオでは、HBase のメジャー バージョンが同じ、HDInsight 3.4 から 3.6 (どちらにも Apache HBase 1.1.2 が付属します) にアップグレードします。 移行元と移行先のバージョン間で互換性の問題がない限り、他のバージョンのアップグレードも同様です。

1. HBase 互換性マトリックスおよびリリース ノートで、アプリケーションが新しいバージョンと互換性があることを確認します。 HDInsight および HBase のターゲット バージョンを実行しているクラスターでアプリケーションをテストします。

2. 同じストレージ アカウントで別のコンテナー名を使用して、[新しい移行先 HDInsight クラスターを設定](../hdinsight-hadoop-provision-linux-clusters.md)します。

    ![同じストレージ アカウントを使用して別のコンテナーを作成する](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. 移行元 HBase クラスターをフラッシュします。 これは、アップグレード元のクラスターです。 HBase では、_memstore_ と呼ばれるメモリ内ストアに受信データが書き込まれます。 memstore は、一定のサイズに達すると、クラスターのストレージ アカウントに長期保存するためにディスクにフラッシュされます。 古いクラスターを削除すると、memstore がリサイクルされ、データが失われる可能性があります。 各テーブルの memstore をディスクに手動でフラッシュするには、次のスクリプトを実行します。 このスクリプトの最新バージョンは、Azure の [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh) にあります。

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
    
            Provide hostname as a parameter (i.e. hn0, hn1 or wn2 etc.).
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
6. 古いクラスター上の [Apache Ambari](https://ambari.apache.org/) (https://OLDCLUSTERNAME.azurehdidnsight.net) にログインして、HBase サービスを停止します。 サービスを停止することを確認するプロンプトが表示されたら、HBase のメンテナンス モードをオンにするためのチェックボックスをオンにします。 Ambari への接続とその使用方法の詳細については、「[Ambari Web UI を使用した HDInsight クラスターの管理](../hdinsight-hadoop-manage-ambari.md)」を参照してください。

    ![Ambari で、[Services]\(サービス\) タブ、左側のメニューの [HBase]、[Service Actions]\(サービス アクション\) の [Stop]\(停止\) を順にクリックします](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![[Turn On Maintenance Mode for HBase]\(HBase のメンテナンス モードをオンにする\) チェックボックスをオンにし、操作を確定します](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. 新しい HDInsight クラスターの Ambari にログインします。 `fs.defaultFS` HDFS 設定を、元のクラスターで使用されているコンテナー名を指すように変更します。 この設定は、**[HDFS] > [Configs]\(構成\) > [Advanced]\(詳細\) > [Advanced core-site]\(高度なコアサイト\)** にあります。

    ![Ambari で、[Services]\(サービス\) タブ、左側のメニューの [HDFS]、[Configs]\(構成\) タブ、その下の [Advanced]\(詳細\) タブを順にクリックする](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![Ambari で、コンテナー名を変更する](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. **拡張書き込み機能を備えた HBase クラスターを使用していない場合は、この手順をスキップしてください。この手順は、拡張書き込み機能を備えた HBase クラスターにのみ必要です。**
   
   hbase.rootdir パスを、元のクラスターのコンテナーを指すように変更します。

    ![Ambari で、hbase rootdir のコンテナー名を変更する](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)
    
9. 変更を保存します。
10. Ambari の指示に従って、必要なすべてのサービスを再起動します。
11. アプリケーションが新しいクラスターを指すように設定します。

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
