---
title: Virtual Network での HBase クラスターの作成 - Azure
description: Azure HDInsight での HBase の使用の概要 Azure Virtual Network での HDInsight HBase クラスターの作成方法について説明します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: 9d81e5e69837f6074d94278f4e54f9178a656335
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67433790"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Azure 仮想ネットワーク内の HDInsight 上に Apache HBase クラスターを作成する
[Azure 仮想ネットワーク][1]内に Azure HDInsight Apache HBase クラスターを作成する方法について説明します。

アプリケーションが HBase と直接通信できるように、仮想ネットワーク統合を使用して、Apache HBase クラスターをアプリケーションと同じ仮想ネットワークにデプロイできます。 次の利点があります。

* Web アプリケーションと HBase クラスターのノード間の直接接続により、HBase Java リモート プロシージャ コール (RPC) API を使用した通信が可能になります。
* トラフィックが複数のゲートウェイやロード バランサーを経由しないためパフォーマンスが向上します。
* 機密情報は、パブリック エンドポイントに公開されることなく、より安全な方法で処理できます。

### <a name="prerequisites"></a>前提条件
この記事の操作を始める前に、以下を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* **Azure PowerShell を実行できるワークステーション**。 [Azure PowerShell のインストールおよび使用](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)に関するページを参照してください。

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>仮想ネットワークに Apache HBase クラスターを作成する
このセクションでは、[Azure Resource Manager テンプレート](../../azure-resource-manager/resource-group-template-deploy.md)を使用して Azure 仮想ネットワーク内の依存する Azure ストレージ アカウントで Linux ベースの Apache HBase クラスターを作成します。 その他のクラスター作成方法と設定の詳細については、「 [HDInsight での Linux ベースの Hadoop クラスターの作成](../hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。 テンプレートを利用して HDInsight で Apache Hadoop クラスターを作成する方法の詳細については、「[Resource Manager テンプレートを使用して HDInsight で Apache Hadoop クラスターを作成する](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)」をご覧ください。

> [!NOTE]  
> 一部のプロパティは、テンプレートにハードコーディングされています。 例:
>
> * **場所**:米国東部 2
> * **クラスターのバージョン**:3.6
> * **クラスターの worker ノードの数**:2
> * **既定のストレージ アカウント**: 一意の文字列
> * **仮想ネットワーク名**:&lt;クラスター名>-vnet
> * **仮想ネットワークのアドレス空間**:10.0.0.0/16
> * **サブネット名**: subnet1
> * **サブネットのアドレス範囲**:10.0.0.0/24
>
> &lt;クラスター名> は、テンプレートを使用する際に指定するクラスター名に置き換えられます。
>
>

1. 次の画像をクリックして Azure ポータルでテンプレートを開きます。 テンプレートは [Azure クイック スタート テンプレート集](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/)にあります。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. **[カスタム デプロイ]** ブレードで以下のプロパティを入力します。

   * **サブスクリプション**:HDInsight クラスター、依存するストレージ アカウント、Azure 仮想ネットワークの作成に使用した Azure サブスクリプションを選択します。
   * **リソース グループ**: **[新規作成]** を選択し、新しいリソース グループの名前を指定します。
   * **場所**:リソース グループの場所を選びます。
   * **ClusterName**:作成される Hadoop クラスターの名前を入力します。
   * **クラスター ログイン名とパスワード**:既定のログイン名は **admin** です。
   * **SSH ユーザー名とパスワード**:既定のユーザー名は **sshuser** です。  この名前は変更できます。
   * **上記の使用条件に同意する**:(選択)
3. **[購入]** をクリックします。 クラスターの作成には約 20 分かかります。 クラスターが作成されたら、ポータルのクラスター ブレードをクリックして開きます。

記事を完了したら、必要に応じてクラスターを削除できます。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。 クラスターの削除手順については、「[Azure portal を使用して HDInsight の Apache Hadoop クラスターを管理する](../hdinsight-administer-use-portal-linux.md#delete-clusters)」を参照してください。

新しい HBase クラスターの使用を開始する場合は、[HDInsight の Apache Hadoop での Apache HBase の使用開始](./apache-hbase-tutorial-get-started-linux.md)に関する記事に記載されている手順を使用できます。

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Apache HBase Java RPC API を使用して Apache HBase クラスターに接続する
1. サービスとしてのインフラストラクチャ (IaaS) 仮想マシンを同じ Azure 仮想ネットワークと同じサブネットに対して作成します。 新しい IaaS 仮想マシンの作成手順については、「[Create a Virtual Machine Running Windows Server (Windows Server を実行する仮想マシンの作成)](../../virtual-machines/windows/quick-create-portal.md)」をご覧ください。 このドキュメントの手順に従う場合は、ネットワーク構成に以下の値を使用する必要があります。

   * **仮想ネットワーク**:&lt;クラスター名>-vnet
   * **サブネット**: subnet1

   > [!IMPORTANT]  
   > &lt;クラスター名> を、前の HDInsight クラスターの作成手順で使用した名前に置き換えます。

   これらの値を使用すると、仮想マシンは、構HDInsight クラスターと同じ仮想ネットワークとサブネットに配置されます。 この構成によって、互いに直接通信することができます。 空のエッジ ノードがある HDInsight クラスターを作成する方法があります。 このエッジ ノードを使用して、クラスターを管理できます。  詳細については、「 [Use empty edge nodes in HDInsight](../hdinsight-apps-use-edge-node.md)」(HDInsight で空のエッジ ノードを使用する) を参照してください。

2. Java アプリケーションを使用して HBase にリモートで接続する場合は、完全修飾ドメイン名 (FQDN) を使用する必要があります。 これを確認するには、HBase クラスターの接続固有の DNS サフィックスを取得する必要があります。 そのためには、次のいずれかの方法を実行します。

   * Web ブラウザーを使用した [Apache Ambari](https://ambari.apache.org/) の呼び出し:

     https://&lt;ClusterName>.azurehdinsight.net/api/v1/clusters/&lt;ClusterName>/hosts?minimal_response=true にアクセスします。 JSON ファイルに DNS サフィックスが付きます。
   * Ambari Web サイトの使用

     1. https://&lt;ClusterName>.azurehdinsight.net にアクセスします。
     2. ページの上部にある **[ホスト]** をクリックします。
   * Curl を使用した REST の呼び出し:

     ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
     ```

     返された JavaScript Object Notation (JSON) データで、"host_name" エントリを見つけます。 これには、クラスターのノードの FQDN が含まれています。 例:

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     クラスター名で始まるドメイン名の部分は、DNS サフィックスです。 たとえば、mycluster.b1.cloudapp.net です。
   * Azure PowerShell の使用

     次の Azure PowerShell スクリプトを使用して、DNS サフィックスを返すことができる **Get-ClusterDetail** 関数を登録します。

     ```powershell
        function Get-ClusterDetail(
            [String]
            [Parameter( Position=0, Mandatory=$true )]
            $ClusterDnsName,
            [String]
            [Parameter( Position=1, Mandatory=$true )]
            $Username,
            [String]
            [Parameter( Position=2, Mandatory=$true )]
            $Password,
            [String]
            [Parameter( Position=3, Mandatory=$true )]
            $PropertyName
            )
        {
        <#
            .SYNOPSIS
            Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
            .Description
            This command shows the following 4 properties of an HDInsight cluster:
            1. ZookeeperQuorum (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.quorum".
            2. ZookeeperClientPort (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.property.clientPort".
            3. HBaseRestServers (supports only HBase type cluster)
                Shows a list of host FQDNs that run the HBase REST server.
            4. FQDNSuffix (supports all cluster types)
                Shows the FQDN suffix of hosts in the cluster.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
            This command shows the value of HBase property "hbase.zookeeper.quorum".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
            This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
            This command shows a list of host FQDNs that run the HBase REST server.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
            This command shows the FQDN suffix of hosts in the cluster.
        #>

            $DnsSuffix = ".azurehdinsight.net"

            $ClusterFQDN = $ClusterDnsName + $DnsSuffix
            $webclient = new-object System.Net.WebClient
            $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

            if($PropertyName -eq "ZookeeperQuorum")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
            }
            if($PropertyName -eq "ZookeeperClientPort")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
            }
            if($PropertyName -eq "HBaseRestServers")
            {
                $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                $Response1 = $webclient.DownloadString($Url1)
                $JsonObject1 = $Response1 | ConvertFrom-Json
                $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                $Response2 = $webclient.DownloadString($Url2)
                $JsonObject2 = $Response2 | ConvertFrom-Json
                foreach ($host_component in $JsonObject2.host_components)
                {
                    $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                    Write-host $ConnectionString
                }
            }
            if($PropertyName -eq "FQDNSuffix")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                $pos = $FQDN.IndexOf(".")
                $Suffix = $FQDN.Substring($pos + 1)
                Write-host $Suffix
            }
        }
     ```

     Azure PowerShell スクリプトを実行した後、次のコマンドで **Get-ClusterDetail** 関数を使用して DNS サフィックスを返します。 このコマンドを使用する場合は、HDInsight HBase のクラスター名、管理者名、および管理者のパスワードを指定します。

     ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
     ```

     このコマンドは、DNS サフィックスを返します。 たとえば、 **yourclustername.b4.internal.cloudapp.net**です。


<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/PrimaryDNSSuffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

仮想マシンが HBase クラスターと通信できることを確認するには、仮想マシンからコマンド `ping headnode0.<dns suffix>` を使用します。 たとえば、「ping headnode0.mycluster.b1.cloudapp.net」のように入力します。

Java アプリケーションでこの情報を使用するには、[HDInsight (Hadoop) で Apache HBase を使用する Java アプリケーションを構築するための Apache Maven の使用](./apache-hbase-build-java-maven-linux.md)に関する記事の手順に従って、アプリケーションを作成します。 アプリケーションをリモート HBase サーバーに接続するには、Zookeeper の FQDN を使用するように、この例の **hbase-site.xml** ファイルを変更します。 例:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> 独自の DNS サーバーの使用方法を含め、Azure Virtual Network の名前解決の詳細については、「 [名前解決 (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)」をご覧ください。

## <a name="next-steps"></a>次の手順
この記事では、Apache HBase クラスターの作成方法を学習しました。 詳細については、次を参照してください。

* [HDInsight の概要](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight での空のエッジ ノードの使用](../hdinsight-apps-use-edge-node.md)
* [HDInsight での Apache HBase レプリケーションの構成](apache-hbase-replication.md)
* [HDInsight で Apache Hadoop クラスターを作成する](../hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight の Apache Hadoop での Apache HBase の概要](./apache-hbase-tutorial-get-started-linux.md)
* [Virtual Network の概要](../../virtual-network/virtual-networks-overview.md)

[1]: https://azure.microsoft.com/services/virtual-network/
[2]: https://technet.microsoft.com/library/ee176961.aspx
[3]: https://technet.microsoft.com/library/hh847889.aspx

