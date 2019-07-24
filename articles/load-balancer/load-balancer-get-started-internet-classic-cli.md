---
title: インターネットに接続するロード バランサーを作成する - Azure クラシック CLI
titlesuffix: Azure Load Balancer
description: Azure クラシック CLI を使用し、インターネットに接続するクラシック デプロイ モデルのロード バランサーを作成する方法について説明します
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
ms.openlocfilehash: fa89117e85bc3d3c9664e6aa037fac923b7432ce
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135748"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-cli"></a>Azure クラシック CLI を使用したインターネットに接続するロード バランサー (クラシック) の作成の開始

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure クラシック CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Azure リソースを使用する前に、Azure には現在、2 種類のデプロイ モデルがあることを理解しておくことが重要です。Azure Resource Manager とクラシックです。 Azure リソースを使用する前に、必ず [デプロイ モデルとツール](../azure-classic-rm.md) について知識をつけておいてください。 この記事の上部にあるタブをクリックすると、さまざまなツールについてのドキュメントを参照できます。 この記事では、クラシック デプロイ モデルについて説明します。 [Azure リソース マネージャーを使用してインターネットに接続するロード バランサーを作成する方法](load-balancer-get-started-internet-arm-ps.md)についても説明します。

[!INCLUDE [requires-classic-cli](../../includes/contains-classic-cli-content.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="create-an-internet-facing-load-balancer-using-cli"></a>CLI を使用してインターネットに接続するロード バランサーを作成する

このガイドでは、前述のシナリオに基づいてインターネット ロード バランサーを作成する方法を説明します。

1. Azure クラシック CLI を初めて使用する場合は、「 [Azure クラシック CLI のインストール](../cli-install-nodejs.md) 」を参照して、Azure のアカウントとサブスクリプションを選択する時点までの指示に従います。
2. 次に示すように、 **azure config mode** コマンドを実行して、以下に示すようにクラシック モードに切り替えます。

    ```azurecli
    azure config mode asm
    ```

    予想される出力:

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>エンドポイントとロード バランサー セットの作成

このシナリオでは、仮想マシン "web1" と "web2" が作成済みであることを前提としています。
このガイドでは、ポート 80 をパブリック ポートとして、ポート 80 をローカル ポートとして、ロード バランサー セットを作成します。 ポート 80 には、ロード バランサー セット "lbset" というプローブ ポートも構成されます。

### <a name="step-1"></a>手順 1

`azure network vm endpoint create` を使用して仮想マシン "web1" 用に最初のエンドポイントとロード バランサー セットを作成します。

```azurecli
azure vm endpoint create web1 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

### <a name="step-2"></a>手順 2.

ロード バランサー セットに 2 番目の仮想マシン "web2" を追加します。

```azurecli
azure vm endpoint create web2 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

### <a name="step-3"></a>手順 3.

`azure vm show` を使用し、ロード バランサーの構成を確認します。

```azurecli
azure vm show web1
```

次のように出力されます。

    data:    DNSName "contoso.cloudapp.net"
    data:    Location "East US"
    data:    VMName "web1"
    data:    IPAddress "10.0.0.5"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
    6-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
    data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
    /vhds/joaomatest-web1-2015-09-25.vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
    r-2012-R2-20150916-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
    data:    Network Endpoints 0 localPort 80
    data:    Network Endpoints 0 name "tcp-80-80"
    data:    Network Endpoints 0 port 80
    data:    Network Endpoints 0 loadBalancerProbe port 80
    data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
    data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 5986
    data:    Network Endpoints 1 name "PowerShell"
    data:    Network Endpoints 1 port 5986
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 3389
    data:    Network Endpoints 2 name "Remote Desktop"
    data:    Network Endpoints 2 port 58081
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>仮想マシン用のリモート デスクトップ エンドポイントの作成

`azure vm endpoint create`を使用し、特定の仮想マシンに、ネットワーク トラフィックをパブリック ポートからローカル ポートに送信するリモート デスクトップ エンドポイントを作成することができます。

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>ロード バランサーからの仮想マシンの削除

ロード バランサー セットに関連付けられているエンドポイントを仮想マシンから削除する必要があります。 エンドポイントを削除すると、その仮想マシンはそのロード バランサー セットには属さなくなります。

前述の例を使用して、`azure vm endpoint delete` コマンドを使用して、ロード バランサー "lbset" から仮想マシン "web1" に作成したエンドポイントを削除できます。

```azurecli
azure vm endpoint delete web1 tcp-80-80
```

> [!NOTE]
> `azure vm endpoint --help` コマンドを使用すると、エンドポイントを管理するオプションをさらに調べることができます。

## <a name="next-steps"></a>次の手順

[内部ロード バランサーの構成の開始](load-balancer-get-started-ilb-arm-ps.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)
