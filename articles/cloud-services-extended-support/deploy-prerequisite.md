---
title: Azure Cloud Services をデプロイするための前提条件 (延長サポート)
description: Azure Cloud Services をデプロイするための前提条件 (延長サポート)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 42416b1fc06ff59a68a6f5044b8bcca5dc7f035f
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880188"
---
# <a name="prerequisites-for-deploying-azure-cloud-services-extended-support"></a>Azure Cloud Services をデプロイするための前提条件 (延長サポート)

> [!IMPORTANT]
> Cloud Services (延長サポート) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Cloud Services (延長サポート) のデプロイを成功させるには、下の手順を確認し、デプロイを試行する前に各項目を完了します。 

## <a name="register-the-cloudservices-feature"></a>Cloud Services 機能を登録する
サブスクリプションに機能を登録します。 登録が完了するまでに数分かかる場合があります。 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

以下を使用して登録の状態を確認します。  
```powershell
Get-AzProviderFeature 

#Sample output
FeatureName               ProviderName      RegistrationState
CloudServices           Microsoft.Compute    Registered
```

## <a name="required-service-configuration-cscfg-file-updates"></a>必要なサービス構成 (.cscfg) ファイルの更新

### <a name="1-virtual-network"></a>1) 仮想ネットワーク
Cloud Service (延長サポート) のデプロイは、仮想ネットワーク内に存在する必要があります。 仮想ネットワークは、[Azure portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)、[PowerShell](https://docs.microsoft.com/azure/virtual-network/quick-create-powershell)、[Azure CLI](https://docs.microsoft.com/azure/virtual-network/quick-create-cli)、または [ARM テンプレート](https://docs.microsoft.com/azure/virtual-network/quick-create-template)を使用して作成できます。 また、仮想ネットワークとサブネットが、サービス構成 (.cscfg) の [NetworkConfiguration](schema-cscfg-networkconfiguration.md) セクションで参照されている必要があります。 

Cloud Service と同じリソース グループに属する仮想ネットワークの場合、サービス構成 (.cscfg) ファイルで仮想ネットワーク名を参照するだけで十分です。 仮想ネットワークと Cloud Service が 2 つの異なるリソース グループにある場合は、仮想ネットワークの完全な Azure Resource Manager ID をサービス構成 (.cscfg) ファイルで指定する必要があります。
 
#### <a name="virtual-network-located-in-same-resource-group"></a>同じリソース グループにある仮想ネットワーク
```xml
<VirtualNetworkSite name="<vnet-name>"/> 
  <AddressAssignments> 
    <InstanceAddress roleName="<role-name>"> 
     <Subnets> 
       <Subnet name="<subnet-name>"/> 
     </Subnets> 
    </InstanceAddress> 
```

#### <a name="virtual-network-located-in-different-resource-group"></a>異なるリソース グループにある仮想ネットワーク
```xml
<VirtualNetworkSite name="/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>"/> 
   <AddressAssignments> 
     <InstanceAddress roleName="<role-name>"> 
       <Subnets> 
        <Subnet name="<subnet-name>"/> 
       </Subnets> 
     </InstanceAddress> 
```
### <a name="2-remove-the-old-plugins"></a>2) 古いプラグインを削除する

古いリモート デスクトップ設定をサービス構成 (.cscfg) ファイルから削除します。  

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="gachandw" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="XXXX" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2021-12-17T23:59:59.0000000+05:30" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" /> 
```

## <a name="required-service-definition-file-csdef-updates"></a>必要なサービス定義ファイル (.csdef) の更新

### <a name="1-virtual-machine-sizes"></a>1) 仮想マシンのサイズ
次のサイズは Azure Resource Manager では非推奨です。 ただし、これらを引き続き使用する場合は、関連する Azure Resource Manager の名前付け規則に従って `vmsize` 名を更新してください。  

| 前のサイズ名 | 更新後のサイズ名 | 
|---|---|
| ExtraSmall | Standard_A0 | 
| 小 | Standard_A1 |
| Medium | Standard_A2 | 
| 大 | Standard_A3 | 
| ExtraLarge | Standard_A4 | 
| A5 | Standard_A5 | 
| A6 | Standard_A6 | 
| A7 | Standard_A7 |  
| A8 | Standard_A8 | 
| A9 | Standard_A9 |
| A10 | Standard_A10 | 
| A11 | Standard_A11 | 
| MSODSG5 | Standard_MSODSG5 | 

 たとえば、`<WorkerRole name="WorkerRole1" vmsize="Medium"` は `<WorkerRole name="WorkerRole1" vmsize="Standard_A2"` になります。
 
> [!NOTE]
> 利用可能なサイズの一覧を取得するには、「[リソース SKU - 一覧](https://docs.microsoft.com/rest/api/compute/resourceskus/list)」を参照し、次のフィルターを適用します。 <br>
`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


### <a name="2-remove-old-remote-desktop-plugins"></a>2) 古いリモート デスクトップ プラグインを削除する
古いリモート デスクトップ プラグインを使用するデプロイでは、サービス定義 (.csdef) ファイルと関連するすべての証明書からモジュールを削除する必要があります。 

```xml
<Imports> 
<Import moduleName="RemoteAccess" /> 
<Import moduleName="RemoteForwarder" /> 
</Imports> 
```

## <a name="key-vault-creation"></a>Key Vault の作成 

Key Vault は、Cloud Services (延長サポート) に関連付けられている証明書を格納するために使用されます。 証明書を Key Vault に追加してから、サービス構成ファイルでその証明書の拇印を参照します。 また、Cloud Services (延長サポート) リソースで Key Vault からシークレットとして格納されている証明書を取得できるようにするために、適切なアクセス許可に対して Key Vault を有効にする必要があります。 Key Vault は、[Azure portal](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal) および [PowerShell](https://docs.microsoft.com/azure/key-vault/general/quick-create-powershell) を使用して作成できます。 Key Vault は、Cloud Service と同じリージョンおよびサブスクリプションに作成する必要があります。 詳細については、「[Azure Cloud Services (延長サポート) で証明書を使用する](certificates-and-key-vault.md)」を参照してください。

## <a name="next-steps"></a>次のステップ 
- Cloud Services (延長サポート) の[デプロイの前提条件](deploy-prerequisite.md)を確認します。
- [Azure portal](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[テンプレート](deploy-template.md)、または [Visual Studio](deploy-visual-studio.md) を使用して Cloud Service (延長サポート) をデプロイします。
- Cloud Services (延長サポート) の[よく寄せられる質問](faq.md)を確認します。
- [Cloud Services (延長サポート) のサンプル リポジトリ](https://github.com/Azure-Samples/cloud-services-extended-support)に関する記事を確認します。