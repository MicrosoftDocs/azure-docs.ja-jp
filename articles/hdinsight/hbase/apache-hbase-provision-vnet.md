---
title: Virtual Network での HBase クラスターの作成 - Azure
description: Azure HDInsight での HBase の使用の概要 Azure Virtual Network での HDInsight HBase クラスターの作成方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: 82e3374491aa119d9985ea7ef31e180c920511d3
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86087743"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Azure 仮想ネットワーク内の HDInsight 上に Apache HBase クラスターを作成する

[Azure 仮想ネットワーク](https://azure.microsoft.com/services/virtual-network/)内に Azure HDInsight Apache HBase クラスターを作成する方法について説明します。

アプリケーションが HBase と直接通信できるように、仮想ネットワーク統合を使用して、Apache HBase クラスターをアプリケーションと同じ仮想ネットワークにデプロイできます。 次の利点があります。

* Web アプリケーションと HBase クラスターのノード間の直接接続により、HBase Java リモート プロシージャ コール (RPC) API を使用した通信が可能になります。
* トラフィックが複数のゲートウェイやロード バランサーを経由しないためパフォーマンスが向上します。
* 機密情報は、パブリック エンドポイントに公開されることなく、より安全な方法で処理できます。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>仮想ネットワークに Apache HBase クラスターを作成する

このセクションでは、[Azure Resource Manager テンプレート](../../azure-resource-manager/templates/deploy-powershell.md)を使用して Azure 仮想ネットワーク内の依存する Azure ストレージ アカウントで Linux ベースの Apache HBase クラスターを作成します。 その他のクラスター作成方法と設定の詳細については、「 [HDInsight での Linux ベースの Hadoop クラスターの作成](../hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。 テンプレートを利用して HDInsight で Apache Hadoop クラスターを作成する方法の詳細については、「[Resource Manager テンプレートを使用して HDInsight で Apache Hadoop クラスターを作成する](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)」をご覧ください。

> [!NOTE]  
> 一部のプロパティは、テンプレートにハードコーディングされています。 次に例を示します。
>
> * **[場所]** :米国東部 2
> * **クラスターのバージョン**:3.6
> * **クラスターの worker ノードの数**:2
> * **既定のストレージ アカウント**: 一意の文字列
> * **仮想ネットワーク名**:CLUSTERNAME-vnet
> * **仮想ネットワークのアドレス空間**:10.0.0.0/16
> * **サブネット名**: subnet1
> * **[サブネットのアドレス範囲]** : 10.0.0.0/24
>
> `CLUSTERNAME` は、テンプレートを使用する際に指定するクラスター名に置き換えられます。

1. 次の画像を選択して Azure Portal でテンプレートを開きます。 テンプレートは [Azure クイック スタート テンプレート集](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/)にあります。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. **[カスタム デプロイ]** ダイアログで **[テンプレートの編集]** を選択します。

1. 行 165 で、値 `Standard_A3` を `Standard_A4_V2` に変更します。 次に、 **[保存]** を選択します。

1. 次の情報を利用して残りのテンプレートを完成させます。

    |プロパティ |値 |
    |---|---|
    |サブスクリプション|HDInsight クラスター、依存するストレージ アカウント、Azure 仮想ネットワークの作成に使用した Azure サブスクリプションを選択します。|
    Resource group|**[新規作成]** を選択し、新しいリソース グループの名前を指定します。|
    |場所|リソース グループの場所を選びます。|
    |クラスター名|作成される Hadoop クラスターの名前を入力します。|
    |クラスター ログイン ユーザー名とパスワード|既定のユーザー名は **admin** です。パスワードを指定します。|
    |SSH のユーザー名とパスワード|既定のユーザー名は **sshuser** です。  パスワードを指定します。|

    **[上記の使用条件に同意する]** を選択します。

1. **[購入]** を選択します。 クラスターの作成には約 20 分かかります。 クラスターが作成されたら、ポータルのクラスターを選択して開くことができます。

記事を完了したら、必要に応じてクラスターを削除できます。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。 クラスターの削除手順については、「[Azure portal を使用して HDInsight の Apache Hadoop クラスターを管理する](../hdinsight-administer-use-portal-linux.md#delete-clusters)」を参照してください。

新しい HBase クラスターの使用を開始する場合は、[HDInsight の Apache Hadoop での Apache HBase の使用開始](./apache-hbase-tutorial-get-started-linux.md)に関する記事に記載されている手順を使用できます。

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Apache HBase Java RPC API を使用して Apache HBase クラスターに接続する

### <a name="create-a-virtual-machine"></a>仮想マシンの作成

サービスとしてのインフラストラクチャ (IaaS) 仮想マシンを同じ Azure 仮想ネットワークと同じサブネットに対して作成します。 新しい IaaS 仮想マシンの作成手順については、「[Create a Virtual Machine Running Windows Server (Windows Server を実行する仮想マシンの作成)](../../virtual-machines/windows/quick-create-portal.md)」をご覧ください。 このドキュメントの手順に従う場合は、ネットワーク構成に以下の値を使用する必要があります。

* **仮想ネットワーク**:CLUSTERNAME-vnet
* **サブネット**: subnet1

> [!IMPORTANT]  
> `CLUSTERNAME` を、前の HDInsight クラスターの作成手順で使用した名前に置き換えます。

これらの値を使用すると、仮想マシンは、構HDInsight クラスターと同じ仮想ネットワークとサブネットに配置されます。 この構成によって、互いに直接通信することができます。 空のエッジ ノードがある HDInsight クラスターを作成する方法があります。 このエッジ ノードを使用して、クラスターを管理できます。  詳細については、「 [Use empty edge nodes in HDInsight](../hdinsight-apps-use-edge-node.md)」(HDInsight で空のエッジ ノードを使用する) を参照してください。

### <a name="obtain-fully-qualified-domain-name"></a>完全修飾ドメイン名を取得する

Java アプリケーションを使用して HBase にリモートで接続する場合は、完全修飾ドメイン名 (FQDN) を使用する必要があります。 これを確認するには、HBase クラスターの接続固有の DNS サフィックスを取得する必要があります。 そのためには、次のいずれかの方法を実行します。

* Web ブラウザーを使用した [Apache Ambari](https://ambari.apache.org/) の呼び出し:

    `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts?minimal_response=true` を参照します。 DNS サフィックスが付いた JSON ファイルが返されます。

* Ambari Web サイトの使用

    1. `https://CLUSTERNAME.azurehdinsight.net` を参照します。
    2. 上部のメニューで **[ホスト]** を選択します。

* Curl を使用した REST の呼び出し:

    ```bash
    curl -u <username>:<password> -k https://CLUSTERNAME.azurehdinsight.net/ambari/api/v1/clusters/CLUSTERNAME.azurehdinsight.net/services/hbase/components/hbrest
    ```

返された JavaScript Object Notation (JSON) データで、"host_name" エントリを見つけます。 これには、クラスターのノードの FQDN が含まれています。 次に例を示します。

```
"host_name" : "hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net"
```

クラスター名で始まるドメイン名の部分は、DNS サフィックスです。 たとえば、「 `hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net` 」のように入力します。

<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/hdi-primary-dns-suffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

### <a name="verify-communication-inside-virtual-network"></a>仮想ネットワーク内の通信を確認する

仮想マシンが HBase クラスターと通信できることを確認するには、仮想マシンからコマンド `ping headnode0.<dns suffix>` を使用します。 たとえば、「 `ping hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net` 」のように入力します。

Java アプリケーションでこの情報を使用するには、[HDInsight (Hadoop) で Apache HBase を使用する Java アプリケーションを構築するための Apache Maven の使用](./apache-hbase-build-java-maven-linux.md)に関する記事の手順に従って、アプリケーションを作成します。 アプリケーションをリモート HBase サーバーに接続するには、Zookeeper の FQDN を使用するように、この例の **hbase-site.xml** ファイルを変更します。 次に例を示します。

```xml
<property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
</property>
```

> [!NOTE]  
> 独自の DNS サーバーの使用方法を含め、Azure Virtual Network の名前解決の詳細については、「 [名前解決 (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

この記事では、Apache HBase クラスターの作成方法を学習しました。 詳細については、次を参照してください。

* [HDInsight の概要](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight での空のエッジ ノードの使用](../hdinsight-apps-use-edge-node.md)
* [HDInsight での Apache HBase レプリケーションの構成](apache-hbase-replication.md)
* [HDInsight で Apache Hadoop クラスターを作成する](../hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight の Apache Hadoop での Apache HBase の概要](./apache-hbase-tutorial-get-started-linux.md)
* [Virtual Network の概要](../../virtual-network/virtual-networks-overview.md)
