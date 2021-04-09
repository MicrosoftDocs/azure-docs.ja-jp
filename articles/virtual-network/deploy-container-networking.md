---
title: Azure Virtual Network コンテナー ネットワークのデプロイ |Microsoft Docs
description: Kubernetes クラスター用の Azure Virtual Network コンテナー ネットワーク インターフェイス (CNI) プラグインのデプロイ方法について説明します。
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/18/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: b95b3cfdf8fea6e31015d945566803569b4ba064
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222923"
---
# <a name="deploy-the-azure-virtual-network-container-network-interface-plug-in"></a>Azure Virtual Network コンテナー ネットワーク インターフェイス プラグインのデプロイ

Azure Virtual Network コンテナー ネットワーク インターフェイス (CNI) プラグインを Azure 仮想マシンにインストールして、Kubernetes ポッドと Docker コンテナーに仮想ネットワーク機能をもたらします。 プラグインの詳細については、「[Enable containers to use Azure Virtual Network capabilities](container-networking-overview.md)」 (コンテナーで Azure Virtual Network 機能を使用できるようにする) を参照してください。 さらに、Azure Kubernetes Service (AKS) コンテナーを仮想ネットワークに自動的に配置する [[高度なネットワーク]](../aks/configure-azure-cni.md?toc=%2fazure%2fvirtual-network%2ftoc.json) オプションを選択することで、AKS でプラグインで使用することができます。

## <a name="deploy-plug-in-for-acs-engine-kubernetes-cluster"></a>ACS エンジン Kubernetes クラスター用プラグインのデプロイ

ACS エンジンは、Azure Resource Manager テンプレートを使用して Kubernetes クラスターをデプロイします。 クラスターの構成は、テンプレートを生成するときにツールに渡される JSON ファイルで指定されます。 サポートされているクラスターの設定とその説明の完全なリストについては、「[Microsoft Azure Container Service Engine - Cluster Definition](https://github.com/Azure/acs-engine/blob/master/docs/clusterdefinition.md)」 (Microsoft Azure Container Service エンジン - クラスター定義) を参照してください。 このプラグインは、ACS エンジンを使用して作成されたクラスターの既定のネットワーク プラグインです。 プラグインを構成するときには、次のネットワーク構成設定が重要です。

  | 設定                              | 説明                                                                                                           |
  |--------------------------------------|------------------------------------------------------------------------------------------------------                 |
  | firstConsecutiveStaticIP             | マスター ノードに割り当てられる IP アドレス。 これは必須の設定です。                                     |
  | kubernetesConfig の下の clusterSubnet | クラスターのデプロイ先で、ポッドへの IP アドレスの割り当て元の仮想ネットワーク サブネットの CIDR   |
  | masterProfile の下の vnetSubnetId     | クラスターのデプロイ先のサブネットの Azure Resource Manager リソース ID を指定します                    |
  | vnetCidr                             | クラスターのデプロイ先の仮想ネットワークの CIDR                                                             |
  | kubeletConfig 下の max-Pods         | すべてのエージェント仮想マシン上のポッドの最大数。 プラグインの場合、既定値は 30 です。 最大 250 を指定できます。  |

### <a name="example-configuration"></a>構成例

次の json の例は、次のプロパティを持つクラスター用です。
-   1 つのマスター ノードと 2 つのエージェント ノード 
-   マスター ノードとエージェント ノードの両方が存在する *KubeClusterSubnet* (10.0.0.0/20) という名前のサブネットにデプロイされる

```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
        "clusterSubnet": "10.0.0.0/20" --> Subnet allocated for the cluster
      }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "ACSKubeMaster",
      "vmSize": "Standard_A2",
      "vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<Vnet Name>/subnets/KubeClusterSubnet",
      "firstConsecutiveStaticIP": "10.0.1.50", --> IP address allocated to the Master node
"vnetCidr": "10.0.0.0/16" --> Virtual network address space
    },
    "agentPoolProfiles": [
      {
        "name": "k8sagentpoo1",
        "count": 2,
        "vmSize": "Standard_A2_v2",
"vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<VNet Name>/subnets/KubeClusterSubnet",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
    "linuxProfile": {
      "adminUsername": "KubeServerAdmin",
      "ssh": {
        "publicKeys": [
          {…}
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "dd438987-aa12-4754-b47d-375811889714",
      "secret": "azure123"
    }
  }
}
```

## <a name="deploy-plug-in-for-a-kubernetes-cluster"></a>Kubernetes クラスター用プラグインのデプロイ

Kubernetes クラスター内のすべての Azure 仮想マシンにプラグインをインストールするには、次の手順を行います。

1. [プラグインをダウンロードしてインストールします](#download-and-install-the-plug-in)。
2. ポッドへの IP アドレスの割り当て元となるすべての仮想マシンで、仮想ネットワークの IP アドレス プールを事前に割り当てます。 すべての Azure 仮想マシンには、ネットワーク インターフェイスごとにプライマリ仮想ネットワークのプライベート IP アドレスが付属しています。 ポッド用の IP アドレスのプールは、次のいずれかのオプションを使用して、仮想マシンのネットワーク インターフェイス上にセカンダリ アドレス (*ipconfigs*) として追加されます。

   - **CLI**: [Azure CLI を使用して複数の IP アドレスを割り当てる](virtual-network-multiple-ip-addresses-cli.md)
   - **PowerShell**: [PowerShell を使用して複数の IP アドレスを割り当てる](virtual-network-multiple-ip-addresses-powershell.md)
   - **ポータル**: [Azure portal を使用して複数の IP アドレスを割り当てる](virtual-network-multiple-ip-addresses-portal.md)
   - **Azure Resource Manager テンプレート**: [テンプレートを使用して複数の IP アドレスを割り当てる](./template-samples.md)

   仮想マシンでの起動が想定されるすべてのポッドに十分な IP アドレスを確実に追加します。

3. クラスターの作成時に `–network-plugin=cni` コマンド ライン オプションを Kubelet に渡すことによって、ご使用のクラスターのネットワークに提供するプラグインを選択します。 既定では、Kubernetes は、プラグインおよび構成ファイルをそれらが既にインストールされているディレクトリで検索します。
4. ポッドをインターネットにアクセスさせるには、ご使用の Linux 仮想マシン上の次の *iptables* 規則を source-NAT インターネット トラフィックに追加します。 次の例では、指定された IP 範囲は、10.0.0.0/8 です。

   ```bash
   iptables -t nat -A POSTROUTING -m iprange ! --dst-range 168.63.129.16 -m
   addrtype ! --dst-type local ! -d 10.0.0.0/8 -j MASQUERADE
   ```

   指定した IP 範囲に宛先がない NAT トラフィックの規則。 前の範囲外のすべてのトラフィックがインターネット トラフィックであることを前提としています。 仮想マシンの仮想ネットワーク、ピアリングされた仮想ネットワーク、およびオンプレミス ネットワークの IP 範囲を指定できます。

   Windows 仮想マシンは、サブネット外の宛先を持つ NAT トラフィックを仮想マシンが属するサブネットに自動的に送信元を明らかにします。 カスタムの IP 範囲を指定することはできません。

前の手順を完了すると、Kubernetes エージェント仮想マシンで起動されたポッドに、仮想ネットワークからプライベート IP アドレスが自動的に割り当てられます。

## <a name="deploy-plug-in-for-docker-containers"></a>Docker コンテナー用プラグインのデプロイ

1. [プラグインをダウンロードしてインストールします](#download-and-install-the-plug-in)。
2. 次のコマンドを使用して、Docker コンテナーを作成します。

   ```
   ./docker-run.sh \<container-name\> \<container-namespace\> \<image\>
   ```

コンテナーは、割り当てられたプールから IP アドレスの受信を自動的に開始します。 トラフィックを Docker コンテナーに負荷分散する場合は、Docker コンテナーをソフトウェア ロード バランサーの背後に配置して、仮想マシンのポリシーとプローブを作成するのと同じ方法でロード バランサー プローブを構成する必要があります。

### <a name="cni-network-configuration-file"></a>CNI ネットワーク構成ファイル

CNI ネットワーク構成ファイルは、JSON 形式で記述されています。 このファイルは、Linux では `/etc/cni/net.d` に、Windows では `c:\cni\netconf` に既定で存在しています。 このファイルは、プラグインの構成を指定し、Windows と Linux で異なります。 次の json は、サンプルの Linux 構成ファイルで、その後にいくつかの重要な設定について説明します。 このファイルに変更を加える必要はありません。

```json
{
       "cniVersion":"0.3.0",
       "name":"azure",
       "plugins":[
          {
             "type":"azure-vnet",
             "mode":"bridge",
             "bridge":"azure0",
             "ipam":{
                "type":"azure-vnet-ipam"
             }
          },
          {
             "type":"portmap",
             "capabilities":{
                "portMappings":true
             },
             "snat":true
          }
       ]
}
```

#### <a name="settings-explanation"></a>設定の説明

- **cniVersion**: Azure Virtual Network CNI プラグインは、[CNI 仕様](https://github.com/containernetworking/cni/blob/master/SPEC.md)のバージョン 0.3.0 および 0.3.1 をサポートしています。
- **name**:ネットワークの名前。 このプロパティは、任意の一意の値に設定できます。
- **type**: ネットワーク プラグインの名前。 *azure-vnet* に設定します。
- **mode**: 操作モード。 このフィールドは省略可能です。 サポートされているモードは、"bridge" だけです。 詳細については、[操作モード](https://github.com/Azure/azure-container-networking/blob/master/docs/network.md)を参照してください。
- **bridge**: コンテナーを仮想ネットワークに接続するために使用されるブリッジの名前。 このフィールドは省略可能です。 省略した場合、マスター インターフェイスのインデックスに基づいて、一意の名前がプラグインによって自動的に選択されます。
- **ipam type**: IPAM プラグインの名前。 常に *azure-vnet-ipam* に設定します。

## <a name="download-and-install-the-plug-in"></a>プラグインのダウンロードとインストール

[GitHub](https://github.com/Azure/azure-container-networking/releases) からプラグインをダウンロードします。 ご使用のプラットフォームの最新バージョンをダウンロードします。

- **Linux**: [azure-vnet-cni-linux-amd64-\<version no.\>.tgz](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-linux-amd64-v1.0.12-rc3.tgz)
- **Windows**: [azure-vnet-cni-windows-amd64-\<version no.\>.zip](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-windows-amd64-v1.0.12-rc3.zip)

[Linux](https://github.com/Azure/azure-container-networking/blob/master/scripts/install-cni-plugin.sh) または [Windows](https://github.com/Azure/azure-container-networking/blob/master/scripts/Install-CniPlugin.ps1) 用のインストール スクリプトをご使用のコンピューターにコピーします。 スクリプトをご使用のコンピューターの `scripts` ディレクトリに保存し、Linux の場合は `install-cni-plugin.sh`、Windows の場合は `install-cni-plugin.ps1` とファイルに名前を付けます。 プラグインをインストールするには、ご使用のプラットフォームに該当するスクリプトを、使用するプラグインのバージョンを指定して実行します。 たとえば、次のように *v1.0.12-rc3* を指定できます。

   ```bash
   \$scripts/install-cni-plugin.sh [version]
   ```

   ```powershell
   scripts\\ install-cni-plugin.ps1 [version]
   ```

スクリプトにより、Linux の場合は `/opt/cni/bin` の下に、Windows の場合は `c:\cni\bin` の下に、プラグインがインストールされます。 インストールされたプラグインには、インストール後に機能する単純なネットワーク構成ファイルが付属しています。 これは更新する必要はありません。 ファイルの設定の詳細については、「[CNI ネットワーク構成ファイル](#cni-network-configuration-file)」を参照してください。