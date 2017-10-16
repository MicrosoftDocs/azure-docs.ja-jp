---
title: "Azure Service Fabric クラスターのセットアップ | Microsoft Docs"
description: "クイック スタート - Azure で Windows または Linux の Service Fabric クラスターを作成します。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/24/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ecf9554554c8b7acbd8b8f5aa9122ce1678c6502
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Azure で初めての Service Fabric クラスターを作成する
[Service Fabric クラスター](service-fabric-deploy-anywhere.md)は、ネットワークで接続された一連の仮想マシンまたは物理マシンで、マイクロサービスがデプロイおよび管理されます。 このクイック スタートでは、Windows または Linux 上で実行される 5 ノード クラスターを [Azure PowerShell](https://msdn.microsoft.com/library/dn135248) または [Azure Portal](http://portal.azure.com) を通じてほんの数分で作成できるようにします。  

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。


## <a name="use-the-azure-portal"></a>Azure ポータルの使用

Azure Portal ([http://portal.azure.com](http://portal.azure.com)) にログインします。

### <a name="create-the-cluster"></a>クラスターを作成する

1. Azure Portal の左上隅にある **[新規]** ボタンをクリックします。
2. **[新規]** ブレードで **[Compute]** を選択し、**[Compute]** ブレードで **[Service Fabric クラスター]** を選択します。
3. Service Fabric の **[基本]** フォームに入力します。 **[オペレーティング システム]** では、クラスター ノードを実行する Windows または Linux のバージョンを選択します。 ここに入力したユーザー名とパスワードが、仮想マシンへのログインに使用されます。 **[リソース グループ]** では、新しいグループを作成します。 リソース グループとは、Azure リソースの作成と一括管理に使用する論理コンテナーです。 完了したら、**[OK]** をクリックします。

    ![クラスターのセットアップに関する出力][cluster-setup-basics]

4. **[クラスター構成]** フォームに入力します。  **[ノード タイプ数]** に「1」と入力します。

5. **[ノード タイプ 1 (プライマリ)]** を選択し、**[ノード タイプの構成]** フォームに必要事項を入力します。  ノード タイプの名前を入力し、[[持続性層]](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) を "ブロンズ" に設定します。  VM サイズを選択してください。

    ノード タイプは、VM のサイズ、VM の数、カスタム エンドポイント、そのタイプの VM のその他の設定を定義します。 定義した各ノード タイプは、個別の仮想マシン スケール セットとしてセットアップされ、仮想マシンをセットとしてデプロイおよび管理するために使用されます。 各ノード タイプは、個別にスケールアップまたはスケールダウンしたり、異なるポートのセットを開いたり、別の容量メトリックを持ったりすることができます。  第 1 (プライマリ) ノード タイプは、Service Fabric システム サービスをホストし、5 個以上の VM を持つ必要があります。

    運用環境デプロイメントでは、[容量計画](service-fabric-cluster-capacity.md)が重要なステップです。  しかし、このクイック スタートではアプリケーションを実行していないので、*DS1_v2 Standard* VM サイズを選択します。  [[信頼性層]](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) には "Silver" を選択し、初期の仮想マシン スケール セット容量を 5 にします。  

    クラスターで実行されているアプリケーションに接続できるように、カスタム エンドポイントは、Azure Load Balancer でポートを開きます。  「80, 8172」と入力して、ポート 80 と 8172 を開きます。

    **[詳細設定の構成]** チェック ボックスはオンにしません。この構成は、TCP/HTTP 管理エンドポイント、アプリケーションのポート範囲、[配置の制約](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints)、および[容量プロパティ](service-fabric-cluster-resource-manager-metrics.md)をカスタマイズするために使用します。    

    **[OK]**を選択します。

6. **[クラスター構成]** フォームで、**[診断]** を **[有効]** に設定します。  このクイック スタートでは、どの[ファブリック設定](service-fabric-cluster-fabric-settings.md)プロパティも入力する必要はありません。  **[Fabric バージョン]** で **[自動]** アップグレード モードを選択し、クラスターを実行しているファブリック コードのバージョンが Microsoft によって自動的に更新されるようにします。  [サポートされているバージョンを選択](service-fabric-cluster-upgrade.md)して、そのバージョンにアップグレードする場合は、モードを **[手動]** に設定します。 

    ![ノード タイプの構成][node-type-config]

    **[OK]**を選択します。

7. **[セキュリティ]** フォームに入力します。  このクイック スタートでは、**[セキュリティ保護なし]** を選択します。  ただし、セキュリティで保護されていないクラスターでは、だれでも匿名で接続して管理操作を実行できるため、運用環境のワークロード用にはセキュリティで保護されたクラスターを作成することを強くお勧めします。  

    Service Fabric では証明書を使用して、クラスターとそのアプリケーションのさまざまな側面をセキュリティで保護するための認証および暗号化を指定します。 Service Fabric での証明書の使用方法の詳細については、「[Service Fabric クラスターのセキュリティに関するシナリオ](service-fabric-cluster-security.md)」を参照してください。  Azure Active Directory を使用したユーザー認証を有効にするか、アプリケーション セキュリティ用の証明書をセットアップするには、[Resource Manager テンプレートからクラスターを作成](service-fabric-cluster-creation-via-arm.md)します。

    **[OK]**を選択します。

8. 概要を確認します。  自分で入力した設定で構築された Resource Manager テンプレートをダウンロードする場合は、**[テンプレートとパラメーターのダウンロード]** を選択します。  **[作成]** を選択して、クラスターを作成します。

    通知には作成の進行状況が表示されます (画面の右上にあるステータス バーの近くの "ベル" アイコンをクリックします)。クラスターの作成中に **[スタート画面にピン留めする]** をクリックした場合、**[Deploying Service Fabric Cluster (Service Fabric クラスターのデプロイ)]** が **[スタート]** 画面にピン留めされます。

### <a name="connect-to-the-cluster-using-powershell"></a>PowerShell を使用してクラスターに接続する
PowerShell を使用して接続することで、クラスターが実行されていることを確認します。  ServiceFabric PowerShell モジュールは、[Service Fabric SDK](service-fabric-get-started.md) と共にインストールされます。  クラスターへの接続は、[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) コマンドレットで確立します。   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint quickstartcluster.westus2.cloudapp.azure.com:19000
```
その他の方法でクラスターに接続する例については、「[セキュリティ保護されたクラスターに接続する](service-fabric-connect-to-secure-cluster.md)」を参照してください。 クラスターに接続した後、[Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) コマンドレットを使って、クラスターに含まれている一連のノードとノードごとの状態情報を表示します。 すべてのノードで **HealthState** が *OK* になっている必要があります。

```powershell
PS C:\Users\sfuser> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
```

### <a name="remove-the-cluster"></a>クラスターの削除
Service Fabric クラスターは、クラスター リソース自体に加え、その他の Azure リソースで構成されます。 そのため、Service Fabric クラスターを完全に削除するには、構成されるすべてのリソースも削除する必要があります。 クラスターと、そのクラスターによって使用されるすべてのリソースを削除するための最も簡単な方法は、リソース グループを削除することです。 クラスターを削除したり、リソース グループ内の (すべてではなく) 一部のリソースを削除したりする他の方法については、[クラスターの削除](service-fabric-cluster-delete.md)に関するページを参照してください。

Azure Portal でリソース グループを削除するには:
1. 削除する Service Fabric クラスターに移動します。
2. クラスターの [要点] ページで**リソース グループ**の名前をクリックします。
3. リソース グループの **[要点]** ページで **[リソース グループの削除]** をクリックし、そのページの指示に従ってリソース グループの削除を完了します。
    ![リソース グループの削除][cluster-delete]


## <a name="use-azure-powershell-to-deploy-a-secure-windows-cluster"></a>Azure PowerShell を使用して、セキュリティで保護された Windows クラスターをデプロイする
1. [Azure PowerShell モジュール Version 4.0 以上](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)をコンピューターにダウンロードします。

2. Windows PowerShell ウィンドウを開き、次のコマンドを実行します。 
    
    ```powershell

    Get-Command -Module AzureRM.ServiceFabric 
    ```

    次のような出力が表示されます。

    ![ps-list][ps-list]

3. Azure にログインして、クラスターを作成するサブスクリプションを選択します。

    ```powershell

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionId "Subcription ID" 
    ```

4. 次のコマンドを実行して、セキュリティで保護されたクラスターを作成します。 パラメーターをカスタマイズすることを忘れないでください。 

    ```powershell
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
    $RDPpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 
    $RDPuser="vmadmin"
    $RGname="mycluster" # this is also the name of your cluster
    $clusterloc="SouthCentralUS"
    $subname="$RGname.$clusterloc.cloudapp.azure.com"
    $certfolder="c:\mycertificates\"
    $clustersize=1 # can take values 1, 3-99

    New-AzureRmServiceFabricCluster -ResourceGroupName $RGname -Location $clusterloc -ClusterSize $clustersize -VmUserName $RDPuser -VmPassword $RDPpwd -CertificateSubjectName $subname -CertificatePassword $certpwd -CertificateOutputFolder $certfolder
    ```

    このコマンドは、完了するのに 10 ～ 30 分かかる場合があります。終了時には、次のような出力が表示されます。 出力には、証明書、そのアップロード先の Key Vault、証明書のコピー先のローカル フォルダーに関する情報が含まれています。 

    ![ps-out][ps-out]

5. 後で参照する必要があるため、出力の全体をコピーしてテキスト ファイルに保存します。 出力の次の情報をメモしておきます。 

    - **CertificateSavedLocalPath**: c:\mycertificates\mycluster20170504141137.pfx
    - **CertificateThumbprint**: C4C1E541AD512B8065280292A8BA6079C3F26F10
    - **ManagementEndpoint**: https://mycluster.southcentralus.cloudapp.azure.com:19080
    - **ClientConnectionEndpointPort**: 19000

### <a name="install-the-certificate-on-your-local-machine"></a>証明書をローカル コンピューターにインストールする
  
クラスターに接続するには、この証明書を現在のユーザーの個人用 (マイ) ストアにインストールする必要があります。 

次の PowerShell を実行します。

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\the name of the cert.pfx `
        -Password (ConvertTo-SecureString -String certpwd -AsPlainText -Force)
```

これで、セキュリティで保護されたクラスターに接続する準備ができました。

### <a name="connect-to-a-secure-cluster"></a>セキュリティ保護されたクラスターに接続する 

次の PowerShell コマンドを実行して、セキュリティで保護されたクラスターに接続します。 証明書の詳細は、クラスターのセットアップに使用された証明書と一致する必要があります。 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```


次の例は、完全なパラメーターを示しています。 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mycluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

次のコマンドを実行して、接続できていることと、クラスターが正常なことを確認します。

```powershell

Get-ServiceFabricClusterHealth

```
### <a name="remove-the-cluster"></a>クラスターの削除
クラスターは、クラスター リソース自体に加え、その他の Azure リソースで構成されます。 クラスターと、そのクラスターによって使用されるすべてのリソースを削除するための最も簡単な方法は、リソース グループを削除することです。 

```powershell

Remove-AzureRmResourceGroup -Name $RGname -Force

```
## <a name="use-azure-cli-to-deploy-a-secure-linux-cluster"></a>Azure CLI を使用して、セキュリティで保護された Linux クラスターをデプロイする

1. コンピューターに [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) をインストールします。
2. Azure にログインして、クラスターを作成するサブスクリプションを選択します。
   ```azurecli
   az login
   az account set --subscription <GUID>
   ```
3. [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) コマンドを実行して、セキュリティで保護されたクラスターを作成します。

    ```azurecli
    #!/bin/bash

    # Variables
    ResourceGroupName="aztestclustergroup" 
    ClusterName="aztestcluster" 
    Location="southcentralus" 
    Password="q6D7nN%6ck@6" 
    Subject="aztestcluster.southcentralus.cloudapp.azure.com" 
    VaultName="aztestkeyvault" 
    VaultGroupName="testvaultgroup"
    VmPassword="Mypa$$word!321"
    VmUserName="sfadminuser"

    # Create resource groups
    az group create --name $ResourceGroupName --location $Location 
    az group create --name $VaultGroupName --location $Location

    # Create secure five node Linux cluster. Creates a key vault in a resource group
    # and creates a certficate in the key vault. The certificate's subject name must match 
    # the domain that you use to access the Service Fabric cluster.  The certificate is downloaded locally.
    az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . \
        --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName \
        --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $VaultGroupName \
        --vm-password $VmPassword --vm-user-name $VmUserName
    ```
    
### <a name="connect-to-the-cluster"></a>クラスターへの接続
次の CLI コマンドを実行して、証明書を使用してクラスターに接続します。  認証にクライアント証明書を使用した場合、証明書の詳細は、クラスター ノードにデプロイされた証明書と一致する必要があります。  自己署名証明書には `--no-verify` オプションを使用します。

```azurecli
az sf cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 --pem ./linuxcluster201709161647.pem --no-verify
```

次のコマンドを実行して、接続できていることと、クラスターが正常なことを確認します。

```azurecli
az sf cluster health
```

## <a name="next-steps"></a>次のステップ
開発クラスターをセットアップしたら、次の作業を試してみてください。
* [Service Fabric Explorer を使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)
* [クラスターの削除](service-fabric-cluster-delete.md) 
* [PowerShell を使ってアプリをデプロイ](service-fabric-deploy-remove-applications.md)します。
* [CLI を使用したアプリのデプロイ](service-fabric-application-lifecycle-sfctl.md)


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png
[ps-list]: ./media/service-fabric-get-started-azure-cluster/pslist.PNG
[ps-out]: ./media/service-fabric-get-started-azure-cluster/psout.PNG

