---
title: Azure HDInsight についてよく寄せられる質問
description: HDInsight についてよく寄せられる質問
keywords: よく寄せられる質問、FAQ
author: Ramakoni1
ms.author: ramakoni
ms.service: hdinsight
ms.custom: hdinsightactive,seoapr2020
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: 47f0ea34fa650371d39252ce6b0ee6a15f3124d6
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871182"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: よく寄せられる質問

この記事では、[Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) の実行方法についてよく寄せられる質問への回答を提供します。

## <a name="creating-or-deleting-hdinsight-clusters"></a>HDInsight クラスターの作成または削除

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>HDInsight クラスターをプロビジョニングするにはどうすればよいですか。

HDInsight クラスターの種類とプロビジョニング方法を確認するには、「[HDInsight で Apache Hadoop、Apache Spark、Apache Kafka などを使用してクラスターを設定する](./hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>既存の HDInsight クラスターを削除するにはどうすればよいですか。

クラスターが使用されなくなったときに削除する方法の詳細については、[HDInsight クラスターの削除](hdinsight-delete-cluster.md)に関する記事を参照してください。

作成操作と削除操作の間は、少なくとも 30 分から 60 分は空けるようにしてください。 そうしないと、次のエラー メッセージが表示され、操作が失敗する可能性があります。

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>ワークロードに適したコアまたはノードの数を選択するにはどうすればよいですか。

適切なコア数とその他の構成オプションは、さまざまな要因によって変わります。

詳細については、「[HDInsight クラスターの容量計画](./hdinsight-capacity-planning.md)」を参照してください。

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>HDInsight クラスターにはどのような種類のノードがありますか。

「[Azure HDInsight クラスターでのリソースの種類](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters)」を参照してください。

### <a name="what-are-the-best-practices-for-creating-large-hdinsight-clusters"></a>大規模な HDInsight クラスターを作成するためのベスト プラクティスとは

1. クラスターのスケーラビリティを向上させるには、[カスタム Ambari DB](./hdinsight-custom-ambari-db.md) を使用して HDInsight クラスターを設定することをお勧めします。
2. [Azure Data Lake Storage Gen2](./hdinsight-hadoop-use-data-lake-storage-gen2.md) を使用して HDInsight クラスターを作成し、Azure Data Lake Storage Gen2 のより高い帯域幅とその他のパフォーマンス特性を活用します。
3. ヘッド ノードは、これらのノードで実行されている複数のマスター サービスに対応できるように十分な大きさにする必要があります。
4. 対話型クエリなどの特定のワークロードには、より大きな Zookeeper ノードが必要になるものもあります。 8 コア以上の VM を検討してください。
5. Hive と Spark の場合は、[外部の Hive メタストア](./hdinsight-use-external-metadata-stores.md)を使用します。

## <a name="individual-components"></a>個別のコンポーネント

### <a name="can-i-install-additional-components-on-my-cluster"></a>クラスターには追加のコンポーネントをインストールできますか。

はい。 追加のコンポーネントをインストールするか、クラスター構成をカスタマイズするには、次のように使用します。

- 作成時または作成後のスクリプト。 スクリプトは、[スクリプト アクション](./hdinsight-hadoop-customize-cluster-linux.md)を使用して呼び出されます。 スクリプト アクションは、Azure portal、HDInsight Windows PowerShell コマンドレット、HDInsight .NET SDK から使用できる構成オプションです。 この構成オプションは、Azure portal、HDInsight Windows PowerShell コマンドレット、または HDInsight .NET SDK から使用できます。

- アプリケーションをインストールする [HDInsight アプリケーション プラットフォーム](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/)。

サポートされるコンポーネントの一覧については、「[HDInsight で使用できる Apache Hadoop コンポーネントおよびバージョンとは](./hdinsight-component-versioning.md)」を参照してください。

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>クラスターにプレインストールされている個々のコンポーネントをアップグレードできますか。

クラスターにプレインストールされている組み込みのコンポーネントまたはアプリケーションをアップグレードする場合、結果の構成は Microsoft によってサポートされません。 このようなシステム構成は、Microsoft によってテストされていません。 アップグレード済みのバージョンのコンポーネントがプレインストールされている可能性のある別のバージョンの HDInsight クラスターを使用してみてください。

たとえば、個々のコンポーネントとして Hive をアップグレードすることはサポートされていません。 HDInsight はマネージド サービスであり、多くのサービスは Ambari サーバーと統合され、テストされています。 Hive のみをアップグレードすると、他のコンポーネントのインデックス付きバイナリが変更され、クラスター上でコンポーネント統合の問題が発生します。

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Spark と Kafka は同じ HDInsight クラスター上で実行できますか。

いいえ。同じ HDInsight クラスター上で Apache Kafka と Apache Spark を実行することはできません。 リソース競合の問題を回避するには、Kafka と Spark 用に個別のクラスターを作成します。

### <a name="how-do-i-change-timezone-in-ambari"></a>Ambari のタイムゾーンは変更するにはどうすればよいですか。

1. [https://CLUSTERNAME.azurehdinsight.net](`https://CLUSTERNAME.azurehdinsight.net`) の Ambari Web UI を開きます。この CLUSTERNAME は実際のクラスターの名前です。
2. 右上隅の [admin | Settings]\(管理者 | 設定\) を選択します。 

   :::image type="content" source="media/hdinsight-faq/ambari-settings.png" alt-text="Ambari の設定":::

3. [User Settings]\(ユーザー設定\) ウィンドウの [Timezone]\(タイムゾーン\) ドロップ ダウンから新しいタイムゾーンを選択し、[Save]\(保存\) をクリックします。

   :::image type="content" source="media/hdinsight-faq/ambari-user-settings.png" alt-text="Ambari の [User Settings]\(ユーザー設定\)":::

## <a name="metastore"></a>メタストア

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-database"></a>既存のメタストアから Azure SQL Database に移行するにはどうすればよいですか? 

SQL Server から Azure SQL Database に移行する方法については、「[チュートリアル: DMS を使用して SQL Server を Azure SQL Database の単一データベースまたはプールされたデータベースにオフラインで移行する](../dms/tutorial-sql-server-to-azure-sql.md)」を参照してください。

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>クラスターが削除されると Hive メタストアは削除されますか。

使用するようにクラスターが構成されているメタストアの種類によって異なります。

既定のメタストアの場合:既定のメタストアは、クラスターのライフサイクルの一部です。 クラスターを削除すると、対応する metastore とメタデータも削除されます。

カスタムのメタストアの場合:メタストアのライフサイクルは、クラスターのライフサイクルに関連付けられていません。 そのため、クラスターを作成して削除してもメタデータは失われません。 Hive スキーマなどのメタデータは、HDInsight クラスターを削除して再作成した後でも保持されます。

詳細については、[Azure HDInsight での外部メタデータ ストアの使用](hdinsight-use-external-metadata-stores.md)に関する記事を参照してください。

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>Hive メタストアを移行すると、Rangers データベースの既定のポリシーも移行されますか。

いいえ。ポリシー定義は Rangers データベース内にあります。そのため、Rangers データベースを移行すると、そのポリシーも移行されます。

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-the-other-way-around"></a>Enterprise セキュリティ パッケージ (ESP) クラスターから非 ESP クラスターに (およびその逆に) Hive メタストアを移行できますか。

はい。ESP から非 ESP クラスターに Hive メタストアを移行することができます。

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Hive メタストア データベースのサイズを見積もるにはどうすればよいですか。

Hive メタストアは、Hive サーバーによって使用されるデータ ソースのメタデータを格納するために使用されます。 サイズ要件は、Hive データ ソースの数と複雑さにも左右されます。 これらの項目を事前に見積もることはできません。 [Hive メタストアのガイドライン](hdinsight-use-external-metadata-stores.md#hive-metastore-guidelines)に記載されているように、S2 レベルから開始できます。 このレベルでは 50 DTU と 250 GB のストレージが提供されます。ボトルネックが発生した場合は、データベースをスケール アップします。

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>外部メタストアとして Azure SQL Database 以外のデータベースはサポートされていますか。

いいえ。Microsoft では、外部カスタム メタストアとして Azure SQL Database のみをサポートしています。

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>複数のクラスター間でメタストアを共有することはできますか。

はい。同じバージョンの HDInsight を使用している限り、複数のクラスターでカスタムのメタストアを共有できます。

## <a name="connectivity-and-virtual-networks"></a>接続と仮想ネットワーク  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>ネットワーク上でポート 22 と 23 をブロックすると、どのような影響がありますか。

ポート 22 とポート 23 をブロックすると、クラスターに SSH でアクセスできなくなります。 これらのポートは、HDInsight サービスでは使用されません。

詳細については、以下のドキュメントをご覧ください。

- [HDInsight 上の Apache Hadoop サービスで使用されるポート](./hdinsight-hadoop-port-settings-for-services.md)

- [Secure incoming traffic to HDInsight clusters in a virtual network with private endpoint](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/) (プライベート エンドポイントを使用した、仮想ネットワーク内の HDInsight クラスターへのセキュリティで保護された着信トラフィック)

- [HDInsight の管理 IP アドレス](./hdinsight-management-ip-addresses.md)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>HDInsight クラスターと同じサブネット内に追加の仮想マシンをデプロイできますか。

はい。HDInsight クラスターと同じサブネット内に追加の仮想マシンをデプロイできます。 次の構成が可能です。

- エッジ ノード:クラスターに別のエッジ ノードを追加することができます。詳細については、「[HDInsight の Apache Hadoop クラスターで空のエッジ ノードを使用する](hdinsight-apps-use-edge-node.md)」を参照してください。

- スタンドアロン ノード:スタンドアロンの仮想マシンを同じサブネットに追加し、その仮想マシンからクラスターにアクセスするには、プライベート エンド ポイント `https://<CLUSTERNAME>-int.azurehdinsight.net` を使用します。 詳細については、[ネットワーク トラフィックのコントロール](./control-network-traffic.md)に関する記事を参照してください。

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>データをエッジ ノードのローカル ディスクに格納すべきでしょうか。

いいえ、ローカル ディスクにデータを格納することはお勧めできません。 ノードで障害が発生した場合、ローカルで格納されているすべてのデータが失われます。 Azure Data Lake Storage Gen2 または Azure Blob Storage にデータを格納するか、データを格納するために Azure Files 共有をマウントすることをお勧めします。


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>既存の HDInsight クラスターを別の仮想ネットワークに追加することはできますか。

いいえ、できません。 仮想ネットワークは、プロビジョニング時に指定する必要があります。 プロビジョニング時に仮想ネットワークが指定されていない場合は、デプロイによって、外部からアクセスできない内部ネットワークが作成されます。 詳細については、「[既存の仮想ネットワークへの HDInsight の追加](hdinsight-plan-virtual-network-deployment.md#existingvnet)」を参照してください。

## <a name="security-and-certificates"></a>セキュリティと証明書

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Azure HDInsight クラスターでのマルウェア対策の推奨事項は何ですか。

マルウェア対策の詳細については、「[Azure Cloud Services および Virtual Machines 向け Microsoft マルウェア対策](../security/fundamentals/antimalware.md)」を参照してください。

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>HDInsight ESP クラスター用にキー タブを作成するにはどうすればよいですか。

ドメイン ユーザー名の Kerberos keytab を作成します。 後でこの keytab を使用して、パスワードを入力することなく、リモートのドメインに参加しているクラスターに対する認証を行うことができます。 ドメイン名は大文字です。

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>既存の Azure Active Directory テナントを使用して、ESP がある HDInsight クラスターを作成できますか。

ESP を使用して HDInsight クラスターを作成する前に、Azure Active Directory Domain Services (Azure AD DS) を有効にします。 オープンソースの Hadoop は、(OAuth とは対照的に) 認証に Kerberos を使用します。

ドメインに VM を参加させるには、ドメイン コントローラーが必要です。 Azure AD DS はマネージド ドメイン コントローラーであり、Azure Active Directory の拡張機能と考えられます。 Azure AD DS では、セキュリティで保護された Hadoop クラスターをマネージ方式で構築するための Kerberos 要件がすべて提供されています。 マネージド サービスとしての HDInsight は、セキュリティを提供するために Azure AD DS と統合されています。

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>AAD-DS のセキュリティで保護された LDAP 設定で自己署名証明書を使用し、ESP クラスターをプロビジョニングすることはできますか。

証明機関によって発行された証明書を使用することをお勧めします。 ただし、ESP では自己署名証明書の使用もサポートされています。 詳細については、次を参照してください。

- [Azure Active Directory Domain Services を有効にする](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [チュートリアル:Azure Active Directory Domain Services のマネージド ドメイン用に Secure LDAP を構成する](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Ranger に表示されているログイン アクティビティをプルするにはどうすればよいですか。

監査の要件については、「[Azure Monitor ログを使用して HDInsight クラスターを監視する](./hdinsight-hadoop-oms-log-analytics-tutorial.md)」の説明に従って Azure Monitor ログを有効にすることをお勧めします。

### <a name="can-i-disable-clamscan-on-my-cluster"></a>クラスターで `Clamscan` を無効にすることはできますか。

`Clamscan` は HDInsight クラスター上で実行されるウイルス対策ソフトウェアであり、ウイルス攻撃からクラスターを保護するために Azure セキュリティ (azsecd) により使用されます。 Microsoft では、ユーザーが既定の `Clamscan` 構成を変更しないようにすることを強くお勧めします。

このプロセスは、他のプロセスに干渉したり、他のプロセスを中断したりすることはありません。 常に他のプロセスに生成されます。 `Clamscan` による CPU のスパイクは、システムがアイドル状態のときにのみ生じます。  

スケジュールを制御する必要があるシナリオでは、次の手順を使用できます。

1. 次のコマンドを使用して、自動実行を無効にします。
   
  sudo `usr/local/bin/azsecd config -s clamav -d Disabled` sudo service azsecd restart 
   
1. 次のコマンドをルートとして実行する Cron ジョブを追加します。
   
   `/usr/local/bin/azsecd manual -s clamav`

Cron ジョブを設定して実行する方法の詳細については、「[Cron ジョブを設定する方法](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)」を参照してください。

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>Spark ESP クラスターで LLAP を使用できるのはなぜですか。
パフォーマンスではなくセキュリティ上の理由で (Apache Ranger)、LLAP が有効になっています。 LLAP のリソース使用量に対応するには、より大きいノード VM を使用します (たとえば、D13V2 以上)。 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>ESP クラスターの作成後にさらに AAD グループを追加する方法はありますか。
この目標を達成する方法は 2 つあります。1 - クラスターを再作成して、新しいグループを追加することができます。 AAD-DS で範囲指定された同期を使用している場合は、グループ B が範囲指定された同期に含まれることを確認します。
2 - ESP クラスターの作成に使用された前のグループの入れ子になったサブグループとして、グループを追加します。 たとえば、グループ `A` で ESP クラスターを作成した場合、後でグループ `A` の入れ子になったサブグループとしてグループ `B` を追加できます。約 1 時間後に、クラスターで自動的に同期されて使用できるようになります。 

## <a name="storage"></a>ストレージ

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>既存の HDInsight クラスターに追加のストレージ アカウントとして Azure Data Lake Storage Gen2 を追加することはできますか。

いいえ。現時点では、プライマリ ストレージとして BLOB ストレージを持つクラスターに Azure Data Lake Storage Gen2 ストレージ アカウントを追加することはできません。 詳細については、[ストレージ オプションの比較](hdinsight-hadoop-compare-storage-options.md)に関する記事を参照してください。

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Data Lake ストレージ アカウントの現在リンクされているサービス プリンシパルを見つける方法を教えてください。

設定は、Azure portal のクラスター プロパティの **[Data Lake Storage Gen1 アクセス]** で確認できます。 詳細については、「[クラスターのセットアップを確認する](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up)」を参照してください。
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>HDInsight クラスターのストレージ アカウントと BLOB コンテナーの使用量を計算するにはどうすればよいですか。

次のいずれかのアクションを実行します。

- [PowerShell の使用](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- HDInsight クラスター上の */user/hive/.Trash/* フォルダーのサイズを確認するには、次のコマンド ラインを使用します。
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>BLOB ストレージ アカウントの監査を設定するにはどうすればよいですか。

BLOB ストレージ アカウントを監査するには、「[Azure ポータルでのストレージ アカウントの監視](../storage/common/manage-storage-analytics-logs.md)」の手順を使用して監視を構成します。 HDFS 監査ログでは、ローカルの HDFS ファイルシステムのみの監査情報のみが提供されます (hdfs://mycluster)。  リモート ストレージで実行される操作は含まれません。

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>BLOB コンテナーと HDInsight ヘッド ノードの間でファイルを転送する方法を教えてください。

ヘッド ノードで次のシェル スクリプトのようなスクリプトを実行します。

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> ファイル *filenames.txt* は、BLOB コンテナー内のファイルの絶対パスを持ちます。
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>ストレージ用の Ranger プラグインはありますか。

現時点では、BLOB ストレージと Azure Data Lake Storage Gen1 または Gen2 用の Ranger プラグインは存在しません。 ESP クラスターの場合は、Azure Data Lake Storage を使用する必要があります。 少なくとも、HDFS ツールを使用してファイル システム レベルで細かいアクセス許可を手動で設定できます。 また、Azure Data Lake Storage を使用する場合、ESP クラスターでは、Azure Active Directory を使用してクラスター レベルでファイル システム アクセス制御の一部が実行されます。 

Azure Storage Explorer を使用して、ユーザーのセキュリティ グループにデータ アクセス ポリシーを割り当てることができます。 詳細については、次を参照してください。

- [Hive またはその他のサービスを使用して Data Lake Storage Gen2 でデータをクエリするためのアクセス許可を Azure AD ユーザーに設定するにはどうすればよいですか。](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Azure Data Lake Storage Gen2 で Azure Storage Explorer を使用してファイルとディレクトリ レベルのアクセス許可を設定する](../storage/blobs/data-lake-storage-explorer.md)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>ワーカー ノードのディスク サイズを増やすことなく、クラスター上の HDFS ストレージを増やすことはできますか。

いいえ。 ワーカー ノードのディスク サイズを増やすことはできません。 そのため、ディスク サイズを増やす唯一の方法は、クラスターを削除し、より大きな worker VM で再作成することです。 HDInsight データを格納するために HDFS を使用しないでください。これは、クラスターを削除するとデータが削除されるためです。 代わりに、Azure にデータを格納します。 クラスターをスケールアップして、HDInsight クラスターに容量を追加することもできます。

## <a name="edge-nodes"></a>エッジ ノード

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>クラスターの作成後にエッジ ノードを追加することはできますか。

「[HDInsight の Apache Hadoop クラスターで空のエッジ ノードを使用する](hdinsight-apps-use-edge-node.md)」を参照してください。

### <a name="how-can-i-connect-to-an-edge-node"></a>エッジ ノードに接続するにはどうすればよいですか。

エッジ ノードを作成した後は、ポート 22 で SSH を使用してそれに接続できます。 エッジ ノードの名前は、クラスター ポータルから確認できます。 通常、名前は *-ed* で終わります。

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>保存されたスクリプトが新しく作成されたエッジ ノード上で自動的に実行されないのはなぜですか。

保存済みスクリプトは、スケーリング操作でクラスターに追加される新しいワーカー ノードをカスタマイズするために使用します。 保存されたスクリプトはエッジ ノードには適用されません。

## <a name="rest-api"></a>REST API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>クラスターから Tez クエリ ビューをプルする REST API 呼び出しを教えてください。

次の REST エンドポイントを使用して、JSON 形式で必要な情報をプルできます。 基本認証ヘッダーを使用して要求を行います。

- `Tez Query View`: *https:\//\<cluster name>.azurehdinsight.net/ws/v1/timeline/HIVE_QUERY_ID/*
- `Tez Dag View`: *https:\//\<cluster name>.azurehdinsight.net/ws/v1/timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Azure Active Directory ユーザーを使用して HDI クラスターから構成の詳細を取得するにはどうすればよいですか。

AAD ユーザーと適切な認証トークンをネゴシエートするには、次の形式を使用してゲートウェイを経由します。

* https://`<cluster dnsname>`.azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Ambari Restful API を使用して YARN パフォーマンスを監視するにはどうすればよいですか。

同じ仮想ネットワークまたはピアリングされた仮想ネットワークで Curl コマンドを呼び出す場合、コマンドは次のようになります。

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
仮想ネットワークの外部から、またはピアリングされていない仮想ネットワークからコマンドを呼び出すと、コマンドの形式は次のようになります。

- 非 ESP クラスターの場合:
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- ESP クラスターの場合:
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> Curl でパスワードの入力を求められます。 クラスター ログイン ユーザー名の有効なパスワードを入力する必要があります。

## <a name="billing"></a>課金

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>HDInsight クラスターのデプロイにはどのくらいのコストがかかりますか。

価格の詳細と課金に関連する FAQ については、「[Azure HDInsight の料金](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。

### <a name="when-does-hdinsight-billing-start--stop"></a>HDInsight の課金はいつ開始および停止されますか。

HDInsight クラスターの課金は、クラスターが作成されると開始し、クラスターが削除されると停止します。 課金は分単位で評価されます。

### <a name="how-do-i-cancel-my-subscription"></a>サブスクリプションを取り消すにはどうすればよいですか。

サブスクリプションを取り消す方法の詳細については、「[Azure サブスクリプションの取り消し](../cost-management-billing/manage/cancel-azure-subscription.md)」を参照してください。

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>従量課金制サブスクリプションの場合、サブスクリプションを取り消した後はどうなりますか。

取り消し後のサブスクリプションの詳細については、「[サブスクリプションの取り消し後の流れ](../cost-management-billing/manage/cancel-azure-subscription.md)」を参照してください。

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-im-running-an-hdinsight-36-cluster"></a>HDInsight 3.6 クラスターを実行している場合でも、Ambari UI で Hive バージョンが 2.1 ではなく 1.2.1000 と表示されるのはなぜですか。

Ambari UI に表示されるのは 1.2 のみですが、HDInsight 3.6 には Hive 1.2 と Hive 2.1 の両方が含まれています。

## <a name="other-faq"></a>その他の FAQ

### <a name="what-does-hdinsight-offer-for-real-time-stream-processing-capabilities"></a>リアルタイムのストリーム処理機能について、HDInsight ではどのような機能が提供されていますか。

ストリーム処理の統合機能の詳細については、[Azure でのストリーム処理テクノロジの選択](/azure/architecture/data-guide/technology-choices/stream-processing)に関するページをご覧ください。

### <a name="is-there-a-way-to-dynamically-kill-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>クラスターが一定期間アイドル状態になったときに、クラスターのヘッド ノードを動的に中止する方法はありますか。

HDInsight クラスターでこの操作を実行することはできません。 これらのシナリオには Azure Data Factory を使用できます。

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>HDInsight ではどのようなコンプライアンス認証が提供されていますか。

コンプライアンス情報については、[Microsoft セキュリティ センター](https://www.microsoft.com/trust-center)と「[Microsoft Azure コンプライアンスの概要](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)」を参照してください。