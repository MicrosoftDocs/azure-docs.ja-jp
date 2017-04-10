---
title: "Windows を実行する Azure VM を使用してスタンドアロン クラスターを作成する | Microsoft Docs"
description: "Windows Server を実行する Azure 仮想マシン上で Azure Service Fabric クラスターを作成および管理する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 7eeb40d2-fb22-4a77-80ca-f1b46b22edbc
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/24/2017
ms.author: ryanwi;chackdan
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: ba1f6e5662700c309fcf198a4e114fd9b2b47c5f
ms.lasthandoff: 03/29/2017


---
# <a name="create-a-three-node-standalone-service-fabric-cluster-with-azure-virtual-machines-running-windows-server"></a>Windows Server を実行する Azure 仮想マシンを使用して、3 ノードのスタンドアロン Service Fabric クラスターを作成する
この記事では、Windows Server のスタンドアロン Service Fabric インストーラーを使用して、Windows ベースの Azure 仮想マシン (VM) でクラスターを作成する方法について説明します。 このシナリオは、[Windows Server を実行する Azure VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) が VM となっている [Windows Server 上で実行されるクラスターを作成および管理する](service-fabric-cluster-creation-for-windows-server.md)特殊なケースですが、[Azure クラウド ベースの Service Fabric クラスター](service-fabric-cluster-creation-via-portal.md)は作成しません。 このパターンに従うときの相違点は、Azure クラウド ベースの Service Fabric クラスターは Service Fabric リソースプロバイダーによって管理およびアップグレードされるのに対して、次の手順で作成するスタンドアロン Service Fabric クラスターは、完全に自分で管理するということです。

## <a name="steps-to-create-the-standalone-cluster"></a>スタンドアロン クラスターを作成する手順
1. Azure Portal にサインインし、リソース グループに新しい Windows Server 2012 R2 Datacenter または Windows Server 2016 Datacenter VM を作成します。 詳細については、[Azure Portal での Windows VM の作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事を参照してください。
2. いくつかの VM を同じリソース グループに追加します。 作成時に各 VM には同じ管理者ユーザー名とパスワードを使用するようにしてください。 3 つの VM は、作成されると、すべて同じ仮想ネットワーク内に表示されます。
3. 各 VM に接続し、 [サーバー マネージャーの [ローカル サーバー] ダッシュボード](https://technet.microsoft.com/library/jj134147.aspx)を使用して Windows ファイアウォールを無効にします。 これにより、ネットワーク トラフィックをマシン間でやり取りできるようになります。 各マシンに接続中に、コマンド プロンプトを開いて `ipconfig`と入力し、IP アドレスを取得します。 また、リソース グループの仮想ネットワーク リソースを選択し、マシンごとに作成されたネットワーク インターフェイスを確認することで、ポータル上の各マシンの IP アドレスを確認することもできます。
4. いずれかの VM に接続し、他の 2 台の VM に正常に ping を実行できるかどうかをテストします。
5. いずれかの VM に接続し、 [Windows Server 用のスタンドアロン Service Fabric パッケージをマシンの新しいフォルダーにダウンロードして](http://go.microsoft.com/fwlink/?LinkId=730690) 、そのパッケージを解凍します。
6. メモ帳で *ClusterConfig.Unsecure.MultiMachine.json* ファイルを開き、3 つのマシンの IP アドレスで各ノードを編集します。 上部にあるクラスター名を変更し、ファイルを保存します。  クラスター マニフェストの部分的な例を次に示します。
   
    ```
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "01-2017",
        "nodes": [
        {
            "nodeName": "standalonenode0",
            "iPAddress": "10.1.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "standalonenode1",
            "iPAddress": "10.1.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "standalonenode2",
            "iPAddress": "10.1.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
        ],
    ```
7. このクラスターをセキュリティで保護する場合は、使用するセキュリティ対策 ([X509 証明書](service-fabric-windows-cluster-x509-security.md)または [Windows セキュリティ](service-fabric-windows-cluster-windows-security.md)) を決定し、リンク先の手順を実行します。 Windows セキュリティを使用してクラスターを設定する場合は、Active Directory を管理するためにドメイン コントローラーを設定する必要があります。 Service Fabric ノードとしてドメイン コントローラー コンピューターを使用することはできません。
8. [PowerShell ISE ウィンドウ](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise)を開きます。 ダウンロードしたスタンドアロンのインストーラー パッケージを展開して、クラスター構成ファイルを保存したフォルダーに移動します。 次の PowerShell コマンドを実行し、クラスターをデプロイします。
   
    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
    ```

このスクリプトは、Service Fabric クラスターをリモートで構成し、デプロイの進行状況を報告します。

9. 約 1 分後に、いずれかのマシンの IP アドレス (例: `http://10.1.0.5:19080/Explorer/index.html`) を使用して Service Fabric Explorer に接続すると、クラスターが動作可能かどうかを確認できます。 

## <a name="next-steps"></a>次のステップ
* [Windows Server または Linux でのスタンドアロン Service Fabric クラスターの作成](service-fabric-deploy-anywhere.md)
* [スタンドアロン Service Fabric クラスターでノードを追加または削除する](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [スタンドアロン Windows クラスターの構成設定](service-fabric-cluster-manifest.md)
* [Windows 上のスタンドアロン クラスターを Windows セキュリティで保護する](service-fabric-windows-cluster-windows-security.md)
* [X.509 証明書を使用した Windows でのスタンドアロン クラスターの保護](service-fabric-windows-cluster-x509-security.md)


