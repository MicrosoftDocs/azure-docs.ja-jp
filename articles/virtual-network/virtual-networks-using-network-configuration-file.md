---
title: Azure Virtual Network (クラシック) の構成 - ネットワーク構成ファイル | Microsoft Docs
description: ネットワーク構成ファイルをエクスポート、変更、インポートして、仮想ネットワーク (クラシック) を作成、変更する方法を説明します。
services: virtual-network
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: genli
ms.custom: ''
ms.openlocfilehash: e26ec4d268b9bd8852ef8cd2c522995902e15923
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57992384"
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>ネットワーク構成ファイルを使用した仮想ネットワーク (クラシック) の構成
> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)です。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは Resource Manager デプロイ モデルを使用することをお勧めします。

仮想ネットワーク (クラシック) は、Azure クラシック コマンド ライン インターフェイス (CLI) または Azure PowerShell でネットワーク構成ファイルを使用して作成および構成することができます。 ネットワーク構成ファイルを使用して、Azure Resource Manager デプロイ モデルを介して仮想ネットワークを作成または変更することはできません。 Azure Portal でネットワーク構成ファイルを使って仮想ネットワーク (クラシック) の作成と変更を行うことはできませんが、Azure Portal では、ネットワーク構成ファイルを使わなくても仮想ネットワーク (クラシック) を作成できます。

ネットワーク構成ファイルを使った仮想ネットワーク (クラシック) の作成と構成には、そのファイルのエクスポート、変更、インポートが伴います。

## <a name="export"></a>ネットワーク構成ファイルのエクスポート

ネットワーク構成ファイルをエクスポートするには、PowerShell または Azure クラシック CLI を使用できます。 PowerShell では XML ファイルがエクスポートされるのに対し、Azure クラシック CLI では json ファイルがエクスポートされます。

### <a name="powershell"></a>PowerShell
 
1. [Azure PowerShell をインストールして Azure にサインイン](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install)します。
2. 次のコマンドを実行してネットワーク構成ファイルをエクスポートします。ディレクトリとファイル名は必要に応じて変更してください。またディレクトリは、存在することをあらかじめ確認してください。

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Azure クラシック CLI

1. [Azure クラシック CLI をインストールします](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 残りの手順は、クラシック CLI のコマンド プロンプトから行います。
2. `azure login` コマンドを入力して Azure にログインします。
3. `azure config mode asm` コマンドを入力して asm モードになっていることを確認します。
4. 次のコマンドを実行してネットワーク構成ファイルをエクスポートします。ディレクトリとファイル名は必要に応じて変更してください。またディレクトリは、存在することをあらかじめ確認してください。
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>ネットワーク構成ファイルの作成または変更

ネットワーク構成ファイルは、XML ファイル (PowerShell を使用した場合) または json ファイル (クラシック CLI を使用した場合) です。 任意のテキスト エディターまたは XML/json エディターでファイルを編集することができます。 [ネットワーク構成ファイルのスキーマ設定](https://msdn.microsoft.com/library/azure/jj157100.aspx)に関する記事に、すべての設定の詳細が記載されています。 設定の詳しい説明については、「[仮想ネットワークと設定の表示](manage-virtual-network.md#view-virtual-networks-and-settings)」を参照してください。 このファイルに変更を加えるときは、次の点に注意してください。

- スキーマに準拠する必要があります。準拠していないと、ネットワーク構成ファイルのインポートに失敗します。
- ご利用のサブスクリプションの既存のネットワーク設定がすべて上書きされます。変更する際は、十分に注意してください。 実際の例については、以下に掲載されているネットワーク構成ファイルのサンプルを参照してください。 たとえば、元のファイルに 2 つの **VirtualNetworkSite** インスタンスが含まれていて、それを例のように変更したとします。 そのファイルで削除した **VirtualNetworkSite** インスタンスの仮想ネットワークは、ファイルをインポートすると削除されます。 このシナリオは簡略化のため、仮想ネットワークにリソースが存在しないことを前提としています。仮にリソースが存在していた場合、仮想ネットワークを削除できず、インポートでエラーが発生するためです。

> [!IMPORTANT]
> Azure では、何かがデプロイされているサブネットは **使用中**と見なされます。 使用中のサブネットは変更することができません。 ネットワーク構成ファイル内のサブネット情報に変更を加える前に、そのサブネットにデプロイしたすべてのものを、変更対象ではない別のサブネットに移動してください。 詳細については、[VM またはロール インスタンスの異なるサブネットへの移動](virtual-networks-move-vm-role-to-subnet.md)に関するページを参照してください。

### <a name="example-xml-for-use-with-powershell"></a>PowerShell で使用する XML の例

次のネットワーク構成ファイルの例では、*10.0.0.0/16* をアドレス空間とする *myVirtualNetwork* という名前の仮想ネットワークを Azure リージョン "*米国東部*" に作成します。 この仮想ネットワークには、アドレス プレフィックス *10.0.0.0/24* の *mySubnet* という名前のサブネットが 1 つ存在します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <Dns />
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myVirtualNetwork" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="mySubnet">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

エクスポートしたネットワーク構成ファイルにコンテンツがない場合は、前の例の XML をコピーして新しいファイルに貼り付けてください。

### <a name="example-json-for-use-with-the-classic-cli"></a>クラシック CLI で使用する JSON の例

次のネットワーク構成ファイルの例では、*10.0.0.0/16* をアドレス空間とする *myVirtualNetwork* という名前の仮想ネットワークを Azure リージョン "*米国東部*" に作成します。 この仮想ネットワークには、アドレス プレフィックス *10.0.0.0/24* の *mySubnet* という名前のサブネットが 1 つ存在します。

```json
{
   "VirtualNetworkConfiguration" : {
      "Dns" : "",
      "VirtualNetworkSites" : [
         {
            "AddressSpace" : [ "10.0.0.0/16" ],
            "Location" : "East US",
            "Name" : "myVirtualNetwork",
            "Subnets" : [
               {
                  "AddressPrefix" : "10.0.0.0/24",
                  "Name" : "mySubnet"
               }
            ]
         }
      ]
   }
}
```

エクスポートしたネットワーク構成ファイルにコンテンツがない場合は、前の出の例の json をコピーして新しいファイルに貼り付けてください。

## <a name="import"></a>ネットワーク構成ファイルのインポート

ネットワーク構成ファイルをインポートするには、PowerShell またはクラシック CLI を使用できます。 PowerShell では XML ファイルがインポートされるのに対し、クラシック CLI では json ファイルがインポートされます。 インポートに失敗する場合は、ファイルが[ネットワーク構成スキーマ](https://msdn.microsoft.com/library/azure/jj157100.aspx)に準拠していることを確認してください。 

### <a name="powershell"></a>PowerShell
 
1. [Azure PowerShell をインストールして Azure にサインイン](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install)します。
2. 次のコマンドを実行してネットワーク構成ファイルをインポートします。ディレクトリとファイル名は必要に応じて変更してください。
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Azure クラシック CLI

1. [Azure クラシック CLI をインストールします](/cli/azure/install-classic-cli)。 残りの手順は、クラシック CLI のコマンド プロンプトから行います。
2. `azure login` コマンドを入力して Azure にログインします。
3. `azure config mode asm` コマンドを入力して asm モードになっていることを確認します。
4. 次のコマンドを実行してネットワーク構成ファイルをインポートします。ディレクトリとファイル名は必要に応じて変更してください。

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```
