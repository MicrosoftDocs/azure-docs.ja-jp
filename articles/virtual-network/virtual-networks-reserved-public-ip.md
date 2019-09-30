---
title: Azure 予約済み IP アドレス (クラシック) の管理 | Microsoft Docs
description: 予約済み IP アドレス (クラシック) の概要と、Azure PowerShell および Azure CLI を使用して管理する方法について説明します。
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: genli
ms.openlocfilehash: 675e7a7b557a3f19ea4d8d4960316c3859cbb9c1
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058488"
---
# <a name="reserved-ip-addresses-classic-deployment"></a>予約済み IP アドレス (クラシック デプロイ)

 Azure での IP アドレスは、動的と予約済みという 2 つのカテゴリに分類されます。 Azure で管理されるパブリック IP アドレスは、既定では動的です。 これは、リソースがシャットダウンまたは停止 (割り当て解除) されたときに、特定のクラウド サービスに使用される IP アドレス (VIP)、または VM やロール インスタンスへの直接アクセスに使用される IP アドレス (ILPIP) が変更される場合があるということです。

IP アドレスが変更されないようにするには、IP アドレスを予約します。 予約済み IP は VIP としてのみ使用できるので、リソースがシャットダウンまたは停止 (割り当て解除) された場合でも、クラウド サービスの IP アドレスが変更されることはありません。 さらに、VIP として使用されている既存の動的 IP を、予約済み IP アドレスに変換できます。

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。 [Resource Manager デプロイ モデル](virtual-network-ip-addresses-overview-arm.md)を使用して静的パブリック IP アドレスを予約する方法を確認してください。

Azure における IP アドレスの詳細については、[IP アドレス](virtual-network-ip-addresses-overview-classic.md)に関する記事をご覧ください。

## <a name="when-do-i-need-a-reserved-ip"></a>予約済み IP が必要になる場合
* **IP アドレスをサブスクリプションで確実に予約したい場合**。 どのような状況でもサブスクリプションから解放されない IP アドレスを予約する場合は、予約済みパブリック IP を使用します。  
* **クラウド サービスまたは VM が停止したり割り当て解除された場合でも、IP アドレスを保持したい場合**。 クラウド サービスの VM がシャットダウンまたは停止 (割り当て解除) された場合でも、変更されない IP アドレスを使用してサービスにアクセスする場合です。
* **Azure の出力トラフィックに予測可能な IP アドレスを使用したい場合**。 オンプレミスのファイアウォールが、特定の IP アドレスからのトラフィックのみを許可するよう構成することができます。 IP を予約すると、送信元 IP アドレスがわかるので、IP の変更に合わせてファイアウォール規則を更新する必要はありません。

## <a name="faqs"></a>FAQ
- 予約済み IP はすべての Azure サービスに使用できますか。
    いいえ。 予約済み IP は、VIP を使用して公開される VM およびクラウド サービスのインスタンス ロールに対してのみ使用できます。
- 予約済み IP は、いくつ使用できますか。
    詳細については、[Azure の制限](../azure-subscription-service-limits.md#networking-limits)に関する記事をご覧ください。
- 予約済み IP に料金はかかりますか。
    場合によります。 料金の詳細については、[予約済み IP アドレスの料金の詳細](https://go.microsoft.com/fwlink/?LinkID=398482)に関するページをご覧ください。
- どうやって IP アドレスを予約するのですか。
    PowerShell、[Azure Management REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)、または [Azure Portal](https://portal.azure.com) を使用して、Azure リージョンの IP アドレスを予約できます。 予約済み IP アドレスは、サブスクリプションに関連付けられます。
- アフィニティ グループ ベースの VNet で予約済み IP を使用できますか。
    いいえ。 予約済み IP はリージョン VNet に対してのみサポートされます。 予約済み IP は、アフィニティ グループに関連付けられている VNet ではサポートされていません。 リージョンまたはアフィニティ グループとの VNet の関連付けの詳細については、[リージョン VNet とアフィニティ グループ](virtual-networks-migrate-to-regional-vnet.md)に関する記事をご覧ください。

## <a name="manage-reserved-vips"></a>予約済み VIP を管理する

### <a name="using-azure-powershell-classic"></a>Azure PowerShell (クラシック) の使用

予約済み IP アドレスは、使用する前にサブスクリプションに追加する必要があります。 次のようにして、"*米国中部*" で使用できるパブリック IP アドレスのプールから予約済み IP を作成します。

> [!NOTE]
> クラシック デプロイ モデルの場合は、Service Management 版の Azure PowerShell をインストールする必要があります。 詳しくは、[Azure PowerShell Service Management モジュールのインストール](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0)に関するページをご覧ください。 

  ```powershell
    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
  ```
ただし、予約する IP を指定できないのでご注意ください。 サブスクリプションで予約された IP アドレスを表示するには、次の PowerShell コマンドを実行し、*ReservedIPName* と *Address* の値を確認します。

```powershell
Get-AzureReservedIP
```

予想される出力:

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

>[!NOTE]
>PowerShell を使用して予約済み IP アドレスを作成する場合、予約済み IP を作成するリソース グループを指定することはできません。 Azure では、予約済み IP は *Default-Networking* という名前のリソース グループに自動的に配置されます。 [Azure Portal](https://portal.azure.com) を使用して予約済み IP を作成する場合は、任意のリソース グループを指定できます。 ただし、*Default-Networking* 以外のリソース グループに予約済み IP を作成した場合、`Get-AzureReservedIP` や `Remove-AzureReservedIP` などのコマンドでその予約済み IP を参照するときに、*Group resource-group-name reserved-ip-name* という名前を参照する必要があります。  たとえば、*myResourceGroup* という名前のリソース グループに *myReservedIP* という予約済み IP を作成した場合、予約済み IP の名前を *Group myResourceGroup myReservedIP* として参照する必要があります。   


IP アドレスが予約されると、サブスクリプションとの関連付けは、サブスクリプションが削除されるまで維持されます。 予約済み IP アドレスを削除するには次のようにします。

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

### <a name="using-azure-cli-classic"></a>Azure CLI (クラシック) の使用
次のように Azure クラシック CLI を使用して、"*米国中部*" で使用できるパブリック IP アドレスのプールから予約済み IP を作成します。

> [!NOTE]
> クラシック デプロイの場合は、Azure クラシック CLI を使用する必要があります。 Azure クラシック CLI のインストール方法については、「[Azure クラシック CLI のインストール](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest)」をご覧ください
  
 コマンド:
 
```azurecli
azure network reserved-ip create <name> <location>
```
例:
 ```azurecli
 azure network reserved-ip create MyReservedIP centralus
 ```

次の Azure CLI を使用して、サブスクリプションで予約されている IP アドレスを表示できます。 

コマンド:
```azurecli
azure network reserved-ip list
```
IP アドレスが予約されると、サブスクリプションとの関連付けは、サブスクリプションが削除されるまで維持されます。 予約済み IP アドレスを削除するには次のようにします。

コマンド:

 ```azurecli
 azure network reserved-ip delete <name>
 ```
  例:  
 ```azurecli
 azure network reserved-ip delete MyReservedIP
 ```
## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>既存のクラウド サービスの IP アドレスを予約する
`-ServiceName` パラメーターを追加することで、既存のクラウド サービスの IP アドレスを予約できます。 次のようにして、"*米国中部*" の *TestService* クラウド サービスの IP アドレスを予約します。

- Azure PowerShell (クラシック) の使用:

  ```powershell
  New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
  ```

- Azure CLI (クラシック) の使用:
  
    コマンド:

    ```azurecli
     azure network reserved-ip create <name> <location> -r <service-name> -d <deployment-name>
    ```
    例:

    ```azurecli
      azure network reserved-ip create MyReservedIP centralus -r TestService -d asmtest8942
    ```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>予約済み IP を新しいクラウド サービスに関連付ける
次のスクリプトでは、新しい予約済み IP を作成し、*TestService* という名前の新しいクラウド サービスに関連付けます。

### <a name="using-azure-powershell-classic"></a>Azure PowerShell (クラシック) の使用
```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```
> [!NOTE]
> クラウド サービスで使用する予約済み IP を作成するときは、受信方向の通信に *VIP:&lt;ポート番号>* を使用して VM を参照します。 IP を予約しても、VM に直接接続できないためです。 予約済み IP は、VM がデプロイされたクラウド サービスに割り当てられます。 IP を使用して VM に直接接続するには、インスタンスレベル パブリック IP を構成する必要があります。 インスタンスレベル パブリック IP とは、VM に直接割り当てられている (ILPIP と呼ばれる) パブリック IP の一種です。 この IP は予約できません。 詳細については、[インスタンスレベル パブリック IP (ILPIP)](virtual-networks-instance-level-public-ip.md) に関する記事をご覧ください。
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>予約済み IP を実行中のデプロイから削除する

新しいクラウド サービスに追加された予約済み IP アドレスを削除するには次のようにします。 
### <a name="using-azure-powershell-classic"></a>Azure PowerShell (クラシック) の使用

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

### <a name="using-azure-cli-classic"></a>Azure CLI (クラシック) の使用
コマンド:

```azurecli
azure network reserved-ip disassociate <name> <service-name> <deployment-name>
```

例:

```azurecli
azure network reserved-ip disassociate MyReservedIP TestService asmtest8942
```

> [!NOTE]
> 実行中のデプロイから予約済み IP を削除しても、サブスクリプションから予約は削除されません。 単に IP が、サブスクリプション内の他のリソースで使用できるように解放されるだけだからです。
> 

サブスクリプションから予約済み IP アドレスを完全に削除するには、次のコマンドを実行します。

コマンド:

```azurecli
azure network reserved-ip delete <name>
```
例:

```azurecli
azure network reserved-ip delete MyReservedIP
```

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>予約済み IP を実行中のデプロイに関連付ける

### <a name="using-azure-powershell-classic"></a>Azure PowerShell (クラシック) の使用

次のコマンドでは、*TestVM2* という名前の新しい VM で *TestService2* という名前のクラウド サービスを作成します。 その後、*MyReservedIP* という既存の予約済み IP をクラウド サービスに関連付けます。

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

### <a name="using-azure-cli-classic"></a>Azure CLI (クラシック) の使用
次の Azure CLI を使用して、実行中のクラウド サービス デプロイに新しい予約済み IP アドレスを関連付けることができます。

コマンド:
```azurecli
azure network reserved-ip associate <name> <service-name> <deployment-name>
```
例:
```azurecli
azure network reserved-ip associate MyReservedIP TestService asmtest8942
```
## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>サービス構成ファイルを使用してクラウド サービスに予約済み IP アドレスを関連付ける
サービスの構成 (CSCFG) ファイルを使用すると、クラウド サービスに予約済み IP を関連付けることもできます。 次の xml サンプルは、*MyReservedIP* という予約済み VIP を使用するようにクラウド サービスを構成する方法を示しています。
```
   <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
```
## <a name="next-steps"></a>次の手順
* クラシック デプロイ モデルの [IP アドレス指定](virtual-network-ip-addresses-overview-classic.md) の仕組みを理解します。
* [予約済みプライベート IP アドレス](virtual-networks-reserved-private-ip.md)について理解する。
* [インスタンス レベル パブリック IP (ILPIP) アドレス](virtual-networks-instance-level-public-ip.md)について理解する。

