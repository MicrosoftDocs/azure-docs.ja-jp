---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: bda289e73b9a782cd56c0c94b8f53e8002b1ccf4
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59532781"
---
## <a name="how-to-create-a-virtual-network-using-a-network-config-file-from-powershell"></a>PowerShell のネットワーク構成ファイルを使用して仮想ネットワークを作成する方法
Azure では、xml ファイルを使用して、サブスクリプションで使用可能なすべての仮想ネットワークを定義します。 このファイルをダウンロードし、既存の仮想ネットワークが変更または削除されるようにファイルを編集することで、新しい仮想ネットワークを作成します。 このチュートリアルでは、ネットワーク構成 (または netcgf) ファイルをダウンロードする方法と、そのファイルを編集して新しい仮想ネットワークを作成する方法を説明します。 ネットワーク構成ファイルの詳細については、「[Azure Virtual Network Configuration Schema](https://msdn.microsoft.com/library/azure/jj157100.aspx)」(Azure 仮想ネットワークの構成スキーマ) を参照してください。

PowerShell で netcfg ファイルを使用する仮想ネットワークを作成するには、次の手順に従います。

1. Azure PowerShell を初めて使用する場合は、「[Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」の手順を実行した後、Azure にサインインし、サブスクリプションを選択します。
2. Azure PowerShell コンソールから、 **Get AzureVnetConfig** コマンドレットを使用して次のコマンドを実行することで、ネットワーク構成ファイルをコンピューターのディレクトリにダウンロードします。 
   
   ```powershell
   Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
   ```
   
   予想される出力:
  
      ```
      XMLConfiguration                                                                                                     
      ----------------                                                                                                     
      <?xml version="1.0" encoding="utf-8"?>...
      ```

3. 任意の XML またはテキスト エディター アプリケーションを使用して、手順 2 で保存したファイルを開き、**\<VirtualNetworkSites>** 要素を探します。 既に作成したネットワークがある場合、各ネットワークは **\<VirtualNetworkSite>** 要素として表示されます。
4. このシナリオで説明した仮想ネットワークを作成するには、次の XML を **\<VirtualNetworkSites>** 要素のすぐ下に追加します。

   ```xml
         <?xml version="1.0" encoding="utf-8"?>
         <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
           <VirtualNetworkConfiguration>
             <VirtualNetworkSites>
                 <VirtualNetworkSite name="TestVNet" Location="East US">
                   <AddressSpace>
                     <AddressPrefix>192.168.0.0/16</AddressPrefix>
                   </AddressSpace>
                   <Subnets>
                     <Subnet name="FrontEnd">
                       <AddressPrefix>192.168.1.0/24</AddressPrefix>
                     </Subnet>
                     <Subnet name="BackEnd">
                       <AddressPrefix>192.168.2.0/24</AddressPrefix>
                     </Subnet>
                   </Subnets>
                 </VirtualNetworkSite>
             </VirtualNetworkSites>
           </VirtualNetworkConfiguration>
         </NetworkConfiguration>
   ```
   
5. ネットワーク構成ファイルを保存します。
6. Azure PowerShell コンソールから、**Set-AzureVnetConfig** コマンドレットを使用して次のコマンドを実行することで、ネットワーク構成ファイルをアップロードします。 
   
   ```powershell
   Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
   ```
   
   返される出力:
   
      ```
      OperationDescription OperationId                          OperationStatus
      -------------------- -----------                          ---------------
      Set-AzureVNetConfig  <Id>                                 Succeeded 
      ```
   
   返された出力の **OperationStatus**が *Succeeded* でない場合は、xml ファイルにエラーがないことを確認し、手順 6 をもう一度完了します。

7. Azure PowerShell コンソールから、**Get-AzureVnetSite** コマンドレットを使用して次のコマンドを実行することで、新しいネットワークが追加されたことを確認します。 

   ```powershell
   Get-AzureVNetSite -VNetName TestVNet
   ```
   
   返される (省略型の) 出力には、次のテキストが含まれます。
  
      ```
      AddressSpacePrefixes : {192.168.0.0/16}
      Location             : Central US
      Name                 : TestVNet
      State                : Created
      Subnets              : {FrontEnd, BackEnd}
      OperationDescription : Get-AzureVNetSite
      OperationStatus      : Succeeded
      ```
