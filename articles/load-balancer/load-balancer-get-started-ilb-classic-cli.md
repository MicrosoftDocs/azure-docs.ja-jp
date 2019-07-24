---
title: 内部ロード バランサーを作成する - Azure クラシック CLI
titlesuffix: Azure Load Balancer
description: クラシック デプロイ モデルで Azure クラシック CLI を使用して、内部ロード バランサーを作成する方法について説明します。
services: load-balancer
documentationcenter: na
author: genlin
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 991e6554df62591dea5c126f8ea82704373d6ffd
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084925"
---
# <a name="get-started-creating-an-internal-load-balancer-using-the-azure-classic-cli"></a>Azure クラシック CLI を使用した内部ロード バランサーの作成の概要

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [クラウド サービス](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../azure-resource-manager/resource-manager-deployment-model.md)です。  この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。 [Resource Manager モデルを使用してこれらの手順を実行する](load-balancer-get-started-ilb-arm-cli.md)方法について説明します。

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>仮想マシンの内部ロード バランサー セットを作成するには

内部ロード バランサー セットと、このセットにトラフィックを送信するサーバーを作成するには、次の手順を実行する必要があります。

1. 負荷分散セットのサーバー間で負荷分散される着信トラフィックのエンドポイントとなる内部負荷分散のインスタンスを作成します。
2. 着信トラフィックを受信できる仮想マシンに対応するエンドポイントを追加します。
3. 内部負荷分散インスタンスの仮想 IP (VIP) アドレスにトラフィックを送信するようにサーバーを構成します。

## <a name="step-by-step-creating-an-internal-load-balancer-using-classic-cli"></a>クラシック CLI を使用した内部ロード バランサーの作成手順

このガイドでは、前述のシナリオに基づいてインターネット ロード バランサーを作成する方法を説明します。

1. クラシック CLI を初めて使用する場合は、「[Azure CLI のインストールと構成](../cli-install-nodejs.md)」を参照し、Azure のアカウントとサブスクリプションを選択する時点までの指示に従ってください。
2. 次に示すように、 **azure config mode** コマンドを実行して、以下に示すようにクラシック モードに切り替えます。

    ```azurecli
    azure config mode asm
    ```

    予想される出力:

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>エンドポイントとロード バランサー セットの作成

シナリオでは、mytestcloud と呼ばれるクラウド サービス内に仮想マシン DB1 および DB2 があることを前提としています。 両方の仮想マシンは、サブネット subnet-1 付きの testvnet と呼ばれる仮想ネットワークを使用します。

このガイドでは、ポート 1433 をプライベート ポートとして、また 1433 をローカル ポートとして、内部ロード バランサー セットを作成します。

これは、内部ロード バランサーを使用して SQL 仮想マシンをバック エンドに配置し、パブリック IP アドレスを直接使用してデータベース サーバーが公開されないようにするための一般的なシナリオです。

### <a name="step-1"></a>手順 1

`azure network service internal-load-balancer add`を使用して、内部ロード バランサー セットを作成します。

```azurecli
azure service internal-load-balancer add --serviceName mytestcloud --internalLBName ilbset --subnet-name subnet-1 --static-virtualnetwork-ipaddress 192.168.2.7
```

詳細については、「 `azure service internal-load-balancer --help` 」を参照してください。

内部ロード バランサーのプロパティを確認するには、コマンド `azure service internal-load-balancer list` *クラウド サービス名*を使用します。

出力の例を次に示します。

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


### <a name="step-2"></a>手順 2.

最初のエンドポイントの追加時に、内部ロード バランサー セットを構成します。 この手順では、エンドポイント、仮想マシン、およびプローブ ポートを内部ロード バランサーに関連付けることができます。

```azurecli
azure vm endpoint create db1 1433 --local-port 1433 --protocol tcp --probe-port 1433 --probe-protocol tcp --probe-interval 300 --probe-timeout 600 --internal-load-balancer-name ilbset
```

### <a name="step-3"></a>手順 3.

 `azure vm show` *仮想マシン名*

```azurecli
azure vm show DB1
```

出力は次のようになります。

    azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>仮想マシン用のリモート デスクトップ エンドポイントの作成

`azure vm endpoint create`を使用し、特定の仮想マシンに、ネットワーク トラフィックをパブリック ポートからローカル ポートに送信するリモート デスクトップ エンドポイントを作成することができます。

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>ロード バランサーからの仮想マシンの削除

仮想マシンを内部ロード バランサー セットから削除するには、関連付けられたエンドポイントを削除します。 エンドポイントを削除すると、その仮想マシンはそのロード バランサー セットには属さなくなります。

前述の例を使用して、 `azure vm endpoint delete`コマンドを使用して、内部ロード バランサー ilbset から仮想マシン "DB1" に作成したエンドポイントを削除できます。

```azurecli
azure vm endpoint delete DB1 tcp-1433-1433
```

詳細については、「 `azure vm endpoint --help` 」を参照してください。

## <a name="next-steps"></a>次の手順

[ソース IP アフィニティを使用したロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)
