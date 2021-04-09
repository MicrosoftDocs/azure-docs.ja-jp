---
title: 仮想ネットワークを使用して Kafka へ接続する - Azure HDInsight
description: Azure Virtual Network 経由で HDInsight 上の Kafka へ直接接続する方法について説明します。 VPN ゲートウェイを使用して開発環境のクライアントから、または VPN ゲートウェイ デバイスを使用してオンプレミス ネットワークから Kafka へ接続する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-python
ms.date: 03/04/2020
ms.openlocfilehash: eb97e209bc7bb9bc1a15ecadc88593e4c0573b5c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870621"
---
# <a name="connect-to-apache-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Azure Virtual Network 経由で HDInsight 上の Apache Kafka に接続する

Azure Virtual Network 経由で HDInsight 上の Apache Kafka へ直接接続する方法について説明します。 このドキュメントでは、次の構成での Kafka への接続の詳細を示します。

* オンプレミス ネットワーク内のリソースからの接続。 この接続は、ローカル ネットワーク上の VPN デバイス (ソフトウェアまたはハードウェア) を使用して確立します。
* VPN ソフトウェア クライアントを使用した開発環境からの接続。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="architecture-and-planning"></a>アーキテクチャと計画

HDInsight では、パブリック インターネット経由で Kafka に直接接続することはできません。 代わりに、Kafka クライアント (プロデューサーおよびコンシューマー) で次の接続方法のいずれかを使用する必要があります。

* HDInsight 上で Kafka と同じ仮想ネットワーク内でクライアントを実行する。 この構成は、「[HDInsight での Apache Kafka の開始](apache-kafka-get-started.md)」で使用されているものです。 クライアントは、HDInsight のクラスター上、または同じネットワーク内の別の仮想マシン上で直接実行します。

* オンプレミス ネットワークなどのプライベート ネットワークを仮想ネットワークに接続する。 この構成では、オンプレミス ネットワーク内のクライアントから直接 Kafka を操作することができます。 この構成を有効にするには、次のタスクを実行します。

  1. 仮想ネットワークを作成します。
  2. サイト間構成を使用する VPN ゲートウェイを作成します。 このドキュメントで使用する構成では、オンプレミス ネットワーク内の VPN ゲートウェイ デバイスへ接続します。
  3. 仮想ネットワーク内に DNS サーバーを作成します。
  4. 各ネットワークの DNS サーバー間の転送を構成します。
  5. 仮想ネットワーク内の HDInsight クラスターに Kafka を作成します。

     詳細については、「[オンプレミス ネットワークから Apache Kafka に接続する](#on-premises)」セクションを参照してください。

* VPN ゲートウェイと VPN クライアントを使用して、仮想ネットワークに各マシンを接続する。 この構成を有効にするには、次のタスクを実行します。

  1. 仮想ネットワークを作成します。
  2. ポイント対サイト構成を使用する VPN ゲートウェイを作成します。 この構成は、Windows と MacOS の両方のクライアントで使用することができます。
  3. 仮想ネットワーク内の HDInsight クラスターに Kafka を作成します。
  4. IP を提供するように Kafka を構成します。 この構成を行うことで、クライアントでドメイン名の代わりに ブローカー IP アドレスを使用して接続を行えるようになります。
  5. 開発システムに VPN クライアントをダウンロードして使用します。

     詳細については、「[VPN クライアントを使用して Apache Kafka に接続する](#vpnclient)」セクションを参照してください。

     > [!WARNING]  
     > この構成には次の制限があるため、推奨されるのは開発用途のみです。
     >
     > * 各クライアントは、VPN ソフトウェア クライアントを使用して接続する必要があります。
     > * この VPN クライアントは仮想ネットワークに名前解決要求を渡さないため、Kafka との通信には IP アドレスを使用する必要があります。 IP で通信を行うには、Kafka クラスターで追加の構成を行う必要があります。

仮想ネットワークにおける HDInsight の使用方法の詳細については、[Azure HDInsight クラスター用の仮想ネットワークの計画](../hdinsight-plan-virtual-network-deployment.md)に関するページを参照してください。

## <a name="connect-to-apache-kafka-from-an-on-premises-network"></a><a id="on-premises"></a> オンプレミス ネットワークから Apache Kafka に接続する

「[Connect HDInsight to your on-premises network (オンプレミス ネットワークに HDInsight を接続する)](./../connect-on-premises-network.md)」の手順に従って、オンプレミス ネットワークと通信する Kafka クラスターを作成します。

> [!IMPORTANT]  
> HDInsight クラスターの作成時には、クラスター種類で __Kafka__ を選択します。

これらの手順により、次の構成が作成されます。

* Azure Virtual Network
* サイト間 VPN ゲートウェイ
* Azure Storage アカウント (HDInsight で使用します)
* HDInsight 上の Kafka

Kafka クライアントがオンプレミスからクラスターへ接続できることを確認するには、「[例: Python クライアント](#python-client)」セクションの手順を実行します。

## <a name="connect-to-apache-kafka-with-a-vpn-client"></a><a id="vpnclient"></a> VPN クライアントを使用して Apache Kafka に接続する

このセクションの手順では、次の構成を作成します。

* Azure Virtual Network
* ポイント対サイト VPN ゲートウェイ
* Azure Storage アカウント (HDInsight で使用されます)
* HDInsight 上の Kafka

1. [ポイント対サイト接続での自己署名証明書の使用](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md)に関する記事の手順を実行します。 このドキュメントでは、ゲートウェイに必要な証明書を作成しています。

2. PowerShell プロンプトを開き、次のコードを使用して Azure サブスクリプションにサインインします。

    ```powershell
    Connect-AzAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzSubscription -SubscriptionName "name of your subscription"
    ```

3. 次のコードを使用して、構成情報を含む変数を作成します。

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.6"
    $hdiType = "Kafka"
    ```

4. 次のコードを使用して、Azure リソース グループと仮想ネットワークを作成します。

    ```powershell
    # Create the resource group that contains everything
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]  
    > このプロセスは、完了するまで数分かかる可能性があります。

5. 次のコードを使用して、Azure Storage アカウントと BLOB コンテナーを作成します。

    ```powershell
    # Create the storage account
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -SkuName Standard_GRS `
        -Location $location `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. 次のコードを使用して、HDInsight クラスターを作成します。

    ```powershell
    # Create the HDInsight cluster
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -DisksPerWorkerNode 2 `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

   > [!WARNING]  
   > このプロセスは、完了するまで約 15 分かかります。

### <a name="configure-kafka-for-ip-advertising"></a>IP を提供するように Kafka を構成する

既定では、Apache Zookeeper は、Kafka ブローカーのドメイン名をクライアントに返します。 この構成では、仮想ネットワーク内のエンティティに対して名前解決を使用できないため、VPN ソフトウェア クライアントは使用できません。 このように構成する場合は、次の手順を実行して、ドメイン名ではなく IP アドレスを提供するように Kafka を構成します。

1. Web ブラウザーを使用し、`https://CLUSTERNAME.azurehdinsight.net` にアクセスします。 `CLUSTERNAME` を HDInsight クラスター上の Kafka の名前に置き換えます。

    プロンプトが表示されたら、クラスターの HTTPS ユーザー名とパスワードを入力します。 クラスターの Ambari Web UI が表示されます。

2. Kafka に関する情報を表示するには、左にある一覧から __[Kafka]__ を選択します。

    :::image type="content" source="./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png" alt-text="Kafka が強調表示されているサービスの一覧" border="true":::

3. Kafka 構成を表示するには、上部中央の __[Configs (構成)]__ を選択します。

    :::image type="content" source="./media/apache-kafka-connect-vpn-gateway/select-kafka-config1.png" alt-text="Apache Ambari サービスの構成" border="true":::

4. __kafka-env__ 構成を検索するには、右上の __[Filter (フィルター)]__ フィールドに「`kafka-env`」と入力します。

    :::image type="content" source="./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png" alt-text="kafka-env の Kafka 構成" border="true":::

5. IP アドレスを提供するように Kafka を構成するには、次のテキストを __kafka-env-template__ フィールドの最後に追加します。

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Kafka がリッスンするインターフェイスを構成するには、右上の __[Filter (フィルター)]__ フィールドに「`listeners`」と入力します。

7. すべてのネットワーク インターフェイスをリッスンするように Kafka を構成するには、 __[listeners (リスナー)]__ フィールドの値を `PLAINTEXT://0.0.0.0:9092`に変更します。

8. 構成を保存するには、 __[Save (保存)]__ ボタンを使用します。 変更を説明するテキスト メッセージを入力します。 変更が保存されたら、 __[OK]__ を保存します。

    :::image type="content" source="./media/apache-kafka-connect-vpn-gateway/save-configuration-button.png" alt-text="Apache Ambari の保存の構成" border="true":::

9. Kafka の再起動時にエラーが発生しないようにするため、 __[Service Actions (サービス アクション)__ ] ボタンを使用して __[Turn On Maintenance Mode (メンテナンス モードの有効化)]__ を選択します。 [OK] を選択して、この操作を完了します。

    :::image type="content" source="./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png" alt-text="[Turn On Maintenance Mode (メンテナンス モードの有効化)] が強調表示されているサービス アクション" border="true":::

10. Kafka を再起動するには、 __[Restart (再起動)]__ ボタンをクリックし、 __[Restart All Affected (影響を受けるものをすべて再起動)]__ を選択します。 再起動を確認し、操作が完了したら __[OK]__ ボタンを使用します。

    :::image type="content" source="./media/apache-kafka-connect-vpn-gateway/restart-required-button.png" alt-text="[Restart All Affected (影響を受けるものをすべて再起動)] が強調表示されている [Restart (再起動)] ボタン" border="true":::

11. メンテナンス モードを無効にするには、 __[Service Actions (サービス アクション)]__ ボタンをクリックし、 __[Turn Off Maintenance Mode (メンテナンス モードの無効化)]__ を選択します。 **[OK]** を選択して、この操作を完了します。

### <a name="connect-to-the-vpn-gateway"></a>VPN ゲートウェイに接続する

VPN ゲートウェイに接続するには、[ポイント対サイト接続の構成](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect)に関するドキュメントの「__Azure への接続__」セクションに従います。

## <a name="example-python-client"></a><a id="python-client"></a>例: Python クライアント

Kafka への接続を検証するには、次の手順に従って Python プロデューサーとコンシューマーを作成します。

1. 次のいずれかの方法により、Kafka クラスター内のノードの完全修飾ドメイン名 (FQDN) と IP アドレスを取得します。

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    このスクリプトは、`$resourceGroupName` が仮想ネットワークを含む Azure リソース グループの名前であることを前提としています。

    返された情報を、次の手順で使用するために保存します。

2. 次のコマンドを使用して、[kafka-python](https://kafka-python.readthedocs.io/) クライアントをインストールします。

    ```bash
    pip install kafka-python
    ```

3. データを Kafka に送信するには、次の Python コードを使用します。

   ```python
   from kafka import KafkaProducer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # NOTE: you don't need the full list of worker nodes, just one or two.
   producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
   for _ in range(50):
      producer.send('testtopic', b'test message')
   ```

    `'kafka_broker'` エントリを、このセクションの手順 1 で返されたアドレスに置き換えます。

   * __ソフトウェア VPN クライアント__ を使用している場合、`kafka_broker` エントリはワーカー ノードの IP アドレスに置き換えます。

   * __カスタム DNS サーバー経由での名前解決を有効化__ している場合は、`kafka_broker` エントリをワーカー ノードの FQDN に置き換えます。

     > [!NOTE]
     > このコードは、文字列 `test message` をトピック `testtopic` に送信します。 HDInsight 上の Kafka の既定の構成では、トピックが存在しない場合は作成します。

4. Kafka からメッセージを取得するには、次の Python コードを使用します。

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Again, you only need one or two, not the full list.
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    `'kafka_broker'` エントリを、このセクションの手順 1 で返されたアドレスに置き換えます。

    * __ソフトウェア VPN クライアント__ を使用している場合、`kafka_broker` エントリはワーカー ノードの IP アドレスに置き換えます。

    * __カスタム DNS サーバー経由での名前解決を有効化__ している場合は、`kafka_broker` エントリをワーカー ノードの FQDN に置き換えます。

## <a name="next-steps"></a>次のステップ

仮想ネットワークでの HDInsight の使用については、[Azure HDInsight クラスター用の仮想ネットワーク デプロイの計画](../hdinsight-plan-virtual-network-deployment.md)に関するドキュメントを参照してください。

ポイント対サイト VPN ゲートウェイを使用する Azure Virtual Network の作成の詳細については、次のドキュメントを参照してください。

* [Azure Portal を使用したポイント対サイト接続の構成](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Azure PowerShell を使用したポイント対サイト接続の構成](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

HDInsight 上の Apache Kafka の操作の詳細については、次のドキュメントを参照してください。

* [HDInsight での Apache Kafka の使用](apache-kafka-get-started.md)
* [HDInsight 上の Apache Kafka でミラーリングを使用する](apache-kafka-mirroring.md)
