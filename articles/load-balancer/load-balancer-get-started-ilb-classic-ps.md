---
title: 内部ロード バランサーを作成する - PowerShell クラシック
titlesuffix: Azure Load Balancer
description: クラシック デプロイ モデルで PowerShell を使用して、内部ロード バランサーを作成する方法について説明します。
services: load-balancer
documentationcenter: na
author: genlin
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms:custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: ef6aac0d97c38798f826304475779ea8059875c7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097983"
---
# <a name="get-started-creating-an-internal-load-balancer-classic-using-powershell"></a>PowerShell を使用した内部ロード バランサー (クラシック) の作成の開始

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [クラウド サービス](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../azure-resource-manager/resource-manager-deployment-model.md)です。  この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。 [Resource Manager モデルを使用してこれらの手順を実行する](load-balancer-get-started-ilb-arm-ps.md)方法について説明します。

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-internal-load-balancer-set-for-virtual-machines"></a>仮想マシンの内部ロード バランサー セットの作成

内部ロード バランサー セットと、このセットにトラフィックを送信するサーバーを作成するには、次の手順を実行する必要があります。

1. 負荷分散セットのサーバー間で負荷分散される着信トラフィックのエンドポイントとなる内部負荷分散のインスタンスを作成します。
2. 着信トラフィックを受信する仮想マシンに対応するエンドポイントを追加します。
3. 内部負荷分散インスタンスの仮想 IP (VIP) アドレスにトラフィックを送信するように負荷分散されたトラフィックを送信するサーバーを構成します。

### <a name="step-1-create-an-internal-load-balancing-instance"></a>手順 1: 内部負荷分散インスタンスを作成する

既存のクラウド サービス、またはリージョンの仮想ネットワークでデプロイされたクラウド サービスでは、次の Windows PowerShell コマンドを使用して内部負荷分散インスタンスを作成できます。

```powershell
$svc="<Cloud Service Name>"
$ilb="<Name of your ILB instance>"
$subnet="<Name of the subnet within your virtual network>"
$IP="<The IPv4 address to use on the subnet-optional>"

Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP
```

この [Add-azureendpoint](https://msdn.microsoft.com/library/dn495300.aspx) Windows PowerShell コマンドレットでは、DefaultProbe パラメーター セットを使用します。 追加のパラメーター セットの詳細については、「 [Add-azureendpoint](https://msdn.microsoft.com/library/dn495300.aspx)」をご覧ください。

### <a name="step-2-add-endpoints-to-the-internal-load-balancing-instance"></a>手順 2: 内部負荷分散インスタンスにエンドポイントを追加する

たとえば次のようになります。

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
$lbsetname="lbset"
$prot="tcp"
$locport=1433
$pubport=1433
$ilb="ilbset"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

### <a name="step-3-configure-your-servers-to-send-their-traffic-to-the-new-internal-load-balancing-endpoint"></a>手順 3:新しい内部負荷分散エンドポイントにトラフィックを送信するようにサーバーを構成する

トラフィックを負荷分散するサーバーを、内部負荷分散インスタンスの新しい IP アドレス (VIP) を使用するように構成する必要があります。 このアドレスは、内部負荷分散インスタンスがリッスンしているアドレスです。 多くの場合、必要な操作は、内部負荷分散インスタンスの VIP の DNS レコードの追加または変更のみです。

内部負荷分散インスタンスの作成時に IP アドレスを指定した場合は、既に VIP があります。 それ以外の場合は、次のコマンドから VIP を確認できます。

```powershell
$svc="<Cloud Service Name>"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

これらのコマンドを使用するには、値を入力して < と > を削除します。 たとえば次のようになります。

```powershell
$svc="mytestcloud"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

Get-azureinternalloadbalancer コマンドの表示から IP アドレスをメモし、サーバーまたは DNS レコードに必要な変更を加えて、トラフィックが VIP に送信されることを確認します。

> [!NOTE]
> Microsoft Azure Platform は、さまざまな管理シナリオに静的でパブリックにルーティング可能な IPv4 アドレスを使用します。 IP アドレスは 168.63.129.16 です。 この IP アドレスはファイアウォールによってブロックされないように設定しておく必要があります。ブロックされると、予期しない動作が発生する可能性があるためです。
> Azure 内部負荷分散については、この IP アドレスはロード バランサーからの監視プローブによって使用され、負荷分散セットでの仮想マシンの正常性状態が判別されます。 ネットワーク セキュリティ グループが、内部負荷分散セットで Azure の仮想マシンへのトラフィックを制限するために使用されている場合、または仮想ネットワーク サブネットに適用されている場合、168.63.129.16 からのトラフィックを許可するネットワーク セキュリティの規則が追加されていることを確認します。

## <a name="example-of-internal-load-balancing"></a>内部負荷分散の例

2 つのサンプル構成に負荷分散セットを作成するエンド ツー エンドのプロセスの手順の説明については、次のセクションをご覧ください。

### <a name="an-internet-facing-multi-tier-application"></a>インターネットに接続された多層アプリケーション

インターネットに接続された一連の Web サーバーに負荷分散データベース サービスを提供します。 両方のサーバー セットは、1 つの Azure クラウド サービスでホストされます。 TCP ポート 1433 への Web サーバーのトラフィックは、データベース層の 2 つの仮想マシンに分散される必要があります。 図 1 に、この構成を示します。

![このデータベース層の内部負荷分散セット](./media/load-balancer-internal-getstarted/IC736321.png)

この構成は次のとおりです。

* 仮想マシンをホストする既存のクラウド サービスの名前は mytestcloud です。
* 既存の 2 つのデータベース サーバーの名前は DB1 と DB2 です。
* Web 層の Web サーバーは、プライベート IP アドレスを使用してデータベース層のデータベース サーバーに接続します。 仮想ネットワークに独自の DNS を使用し、内部ロード バランサー セットの A レコードを手動で登録することもできます。

次のコマンドは、 **ILBset** という名前の新しい内部ロード バランサー インスタンスを構成し、2 つのデータベース サーバーに対応する仮想マシンにエンドポイントを追加します。

```powershell
$svc="mytestcloud"
$ilb="ilbset"
Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb
$prot="tcp"
$locport=1433
$pubport=1433
$epname="TCP-1433-1433"
$lbsetname="lbset"
$vmname="DB1"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

$epname="TCP-1433-1433-2"
$vmname="DB2"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

## <a name="remove-an-internal-load-balancing-configuration"></a>内部負荷分散の構成を削除する

内部ロード バランサー インスタンスからエンドポイントとしての仮想マシンを削除するには、次のコマンドを使用します。

```powershell
$svc="<Cloud service name>"
$vmname="<Name of the VM>"
$epname="<Name of the endpoint>"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

これらのコマンドを使用するには、値を入力して < と > を削除します。

たとえば次のようになります。

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

クラウド サービスから内部ロード バランサー インスタンスを削除するには、次のコマンドを使用します。

```powershell
$svc="<Cloud service name>"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

これらのコマンドを使用するには、値を入力して < と > を削除します。

たとえば次のようになります。

```powershell
$svc="mytestcloud"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

## <a name="additional-information-about-internal-load-balancer-cmdlets"></a>内部ロード バランサーのコマンドレットに関する追加情報

内部負荷分散のコマンドレットに関する追加情報を取得するには、Windows PowerShell プロンプトで、次のコマンドを実行します。

```powershell
Get-Help New-AzureInternalLoadBalancerConfig -full
Get-Help Add-AzureInternalLoadBalancer -full
Get-Help Get-AzureInternalLoadbalancer -full
Get-Help Remove-AzureInternalLoadBalancer -full
```

## <a name="next-steps"></a>次の手順

[ソース IP アフィニティを使用したロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)

