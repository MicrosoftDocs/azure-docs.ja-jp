---
title: クイックスタート - Azure CLI を使用して Azure プライベート DNS ゾーンを作成する
description: このクイックスタートでは、Azure DNS でプライベート DNS ゾーンとレコードを作成してテストします。 これは、Azure CLI を使用して最初のプライベート DNS ゾーンとレコードを作成して管理するためのステップバイステップ ガイドです。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 820641af00caea4ffca450be8aa81b5357ba1261
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652965"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-cli"></a>クイック スタート:Azure CLI を使用して Azure プライベート DNS ゾーンを作成する

このクイックスタートでは、Azure CLI を使用して最初のプライベート DNS ゾーンとレコードを作成する手順について説明します。

DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。 Azure DNS でドメインのホストを開始するには、そのドメイン名用に DNS ゾーンを作成する必要があります。 ドメインの DNS レコードはすべて、この DNS ゾーン内に作成されます。 仮想ネットワークにプライベート DNS ゾーンを発行するには、そのゾーン内のレコードを解決することが認められた仮想ネットワークの一覧を指定します。  これらを "*リンクされている*" 仮想ネットワーク と呼びます。 また、自動登録を有効にすると、仮想マシンの作成または削除、あるいはその IP アドレスの変更を行うたびに、Azure DNS でそのゾーン レコードも更新されます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- また、[Azure PowerShell](private-dns-getstarted-powershell.md) を使用してこのクイックスタートを完了することもできます。

## <a name="create-the-resource-group"></a>リソース グループを作成する

最初に、DNS ゾーンが含まれるリソース グループを作成します。 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-private-dns-zone"></a>プライベート DNS ゾーンの作成

次の例では、**myAzureVNet** という名前の仮想ネットワークを作成します。 次に、**private.contoso.com** という名前の DNS ゾーンを **MyAzureResourceGroup** リソース グループに作成し、その DNS ゾーンを **MyAzureVnet** 仮想ネットワークにリンクし、自動登録を有効にします。

```azurecli
az network vnet create \
  --name myAzureVNet \
  --resource-group MyAzureResourceGroup \
  --location eastus \
  --address-prefix 10.2.0.0/16 \
  --subnet-name backendSubnet \
  --subnet-prefixes 10.2.0.0/24

az network private-dns zone create -g MyAzureResourceGroup \
   -n private.contoso.com

az network private-dns link vnet create -g MyAzureResourceGroup -n MyDNSLink \
   -z private.contoso.com -v myAzureVNet -e true
```

名前の解決のためだけにゾーンを作成する (ホスト名の自動登録を行わない) 場合は、`-e false` パラメーターを使用できます。

### <a name="list-dns-private-zones"></a>DNS プライベート ゾーンの一覧表示

DNS ゾーンを列挙するには、`az network private-dns zone list` を使用します。 `az network dns zone list --help` を使用すると、ヘルプが表示されます。

リソース グループを指定すると、リソース グループ内のゾーンのみが一覧表示されます。

```azurecli
az network private-dns zone list \
  -g MyAzureResourceGroup
```

リソース グループを省略すると、サブスクリプション内のすべてのゾーンが一覧表示されます。

```azurecli
az network private-dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>テスト用仮想マシンの作成

次に、プライベート DNS ゾーンをテストできるように 2 つの仮想マシンを作成します。

```azurecli
az vm create \
 -n myVM01 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter

az vm create \
 -n myVM02 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter
```

これが完了するまでに数分かかります。

## <a name="create-an-additional-dns-record"></a>追加の DNS レコードの作成

DNS レコードを作成するには、`az network private-dns record-set [record type] add-record` コマンドを使用します。 A レコードの追加の詳細については、たとえば `az network private-dns record-set A add-record --help` を参照してください。

 次の例では、リソース グループ **MyAzureResourceGroup** の DNS ゾーン **private.contoso.com** に、相対名が **db** のレコードを作成します。 レコード セットの完全修飾名は、**db.private.contoso.com** になります。 レコードの種類は "A"、IP アドレスは "10.2.0.4" です。

```azurecli
az network private-dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z private.contoso.com \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>DNS レコードの表示

ゾーン内の DNS レコードを一覧表示するには、次のコマンドを実行します。

```azurecli
az network private-dns record-set list \
  -g MyAzureResourceGroup \
  -z private.contoso.com
```

## <a name="test-the-private-zone"></a>プライベート ゾーンのテスト

これで、**private.contoso.com** プライベート ゾーンでの名前解決をテストできます。

### <a name="configure-vms-to-allow-inbound-icmp"></a>受信 ICMP を許可するように VM を構成する

名前解決は ping コマンドを使用してテストできます。 そこで、両方の仮想マシン上のファイアウォールを、受信 ICMP パケットを許可するように構成します。

1. myVM01 に接続し、管理者特権で Windows PowerShell ウィンドウを開きます。
2. 次のコマンドを実行します。

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

MyVM02 についても同じ手順を繰り返します。

### <a name="ping-the-vms-by-name"></a>VM を名前で ping する

1. myVM02 の Windows PowerShell コマンド プロンプトから、自動的に登録されたホスト名を使用して myVM01 を ping します。

   ```powershell
   ping myVM01.private.contoso.com
   ```

   次のような出力が表示されるはずです。

   ```output
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. 次に、前に作成した **db** 名を ping します。

   ```powershell
   ping db.private.contoso.com
   ```

   次のような出力が表示されるはずです。

   ```output
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートで作成したリソースが不要になったときに削除するには、**MyAzureResourceGroup** リソース グループを削除します。

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure DNS Private Zones のシナリオ](private-dns-scenarios.md)

