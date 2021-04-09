---
title: マネージド ID VM 拡張機能の使用を停止する - Azure AD
description: VM 拡張機能の使用を止めて、認証用の Azure Instance Metadata Service (IMDS) の使用を開始する手順を順を追って説明します。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2020
ms.author: barclayn
ROBOTS: NOINDEX
ms.openlocfilehash: dca5f9ed2911ae3042fb9871f849212ec18b1b58
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539385"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>仮想マシンのマネージド ID 拡張機能の使用を止めて Azure Instance Metadata Service の使用を開始する方法

## <a name="virtual-machine-extension-for-managed-identities"></a>マネージド ID の仮想マシン拡張機能

仮想マシン内のマネージド ID のトークンを要求するために、マネージド ID の仮想マシン拡張機能を使用します。 このワークフローは次のステップで構成されます。

1. 最初に、リソース内のワークロードがローカル エンドポイント `http://localhost/oauth2/token` を呼び出し、アクセス トークンを要求します。
2. 仮想マシン拡張機能は、マネージド ID の資格情報を使用して、Azure AD からアクセス トークンを要求します。 
3. アクセス トークンが呼び出し側に返されます。これは、Azure Key Vault または Azure Storage のような Azure AD 認証をサポートするサービスの認証に使用することができます。

次のセクションに示されているいくつかの制限により、マネージド ID の VM 拡張機能は非推奨になりました。その代わりに Azure Instance Metadata Service (IMDS) の同等のエンドポイントを使用します。

### <a name="provision-the-extension"></a>拡張機能のプロビジョニング 

マネージド ID を持つように仮想マシンまたは仮想マシン スケール セットを構成する場合、必要に応じて、[Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) コマンドレットで `-Type` パラメーターを使用して、Azure リソースのマネージド ID VM 拡張機能をプロビジョニングするように選択することもできます。 仮想マシンのタイプに応じて `ManagedIdentityExtensionForWindows` または `ManagedIdentityExtensionForLinux` を渡し、`-Name` パラメーターを使用して名前を付けることができます。 `-Settings` パラメーターは、トークン取得用に OAuth トークン エンドポイントによって使用されるポートを指定します。

```azurepowershell-interactive
$settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

以下の JSON をテンプレートの `resources` セクションに追加することで (Linux バージョンの場合、名前と型の要素に `ManagedIdentityExtensionForLinux` を使用します)、Azure Resource Manager デプロイ テンプレートを使用して、VM 拡張機能をプロビジョニングすることもできます。

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
    "apiVersion": "2018-06-01",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.ManagedIdentity",
        "type": "ManagedIdentityExtensionForWindows",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "port": 50342
        }
    }
}
```
    
    
仮想マシン スケール セットを使用している場合、[Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) コマンドレットを使用して、Azure リソースのマネージド ID 仮想マシン スケール セット拡張機能をプロビジョニングすることもできます。 仮想マシン スケール セットのタイプに応じて `ManagedIdentityExtensionForWindows` または `ManagedIdentityExtensionForLinux` を渡し、`-Name` パラメーターを使用して名前を付けることができます。 `-Settings` パラメーターは、トークン取得用に OAuth トークン エンドポイントによって使用されるポートを指定します。

   ```azurepowershell-interactive
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Azure Resource Manager デプロイ テンプレートを使用して、仮想マシン スケール セット拡張機能をプロビジョニングするには、以下の JSON をテンプレートの `extensionpProfile` セクションに追加します (Linux バージョンの場合、名前と型の要素に `ManagedIdentityExtensionForLinux` を使用します)。

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "ManagedIdentityWindowsExtension",
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                },
                "protectedSettings": {}
            }
        }
```

仮想マシン拡張機能のプロビジョニングは、DNS 検索エラーが原因で失敗することがあります。 この場合、仮想マシンを再起動してからやり直してください。 

### <a name="remove-the-extension"></a>拡張機能を削除する 
この拡張機能を削除するには、Azure CLI または PowerShell 用の `Remove-AzVMExtension` を使用して、仮想マシン スケール セット用の [az vm extension delete](/cli/azure/vm/) または [az vmss extension delete](/cli/azure/vmss) で、`-n ManagedIdentityExtensionForWindows` または `-n ManagedIdentityExtensionForLinux` スイッチを使用します (VM の種類に応じます)。

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```azurepowershell-interactive
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>仮想マシンの拡張機能を使用するトークンの取得

以下は、Azure リソース VM 拡張機能エンドポイントにマネージド ID を使用するサンプル要求です。

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| 要素 | 説明 |
| ------- | ----------- |
| `GET` | HTTP 動詞。エンドポイントからデータを取得する必要があることを示します。 この例では、OAuth アクセス トークンです。 | 
| `http://localhost:50342/oauth2/token` | Azure リソース エンドポイントのマネージド ID (50342 は既定のポートであり、構成可能です)。 |
| `resource` | クエリ文字列パラメーター。ターゲット リソースのアプリ ID URI です。 発行されたトークンの `aud` (audience) 要求にも表示されます。 この例では、アプリ ID URI が `https://management.azure.com/` の Azure Resource Manager にアクセスするためのトークンを要求しています。 |
| `Metadata` | HTTP 要求ヘッダー フィールド。サーバー側のリクエスト フォージェリ (SSRF) 攻撃に対する軽減策として Azure リソースのマネージド ID に必要です。 この値は、"true" に設定し、すべて小文字にする必要があります。|
| `object_id` | (省略可能) クエリの文字列パラメーター。トークン用の管理対象 ID の object_id を示します。 VM に複数のユーザーが割り当てたマネージド ID がある場合は必須です。|
| `client_id` | (省略可能) クエリの文字列パラメーター。トークン用の管理対象 ID の client_id を示します。 VM に複数のユーザーが割り当てたマネージド ID がある場合は必須です。|


応答のサンプル:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| 要素 | 説明 |
| ------- | ----------- |
| `access_token` | 要求されたアクセス トークン。 セキュリティで保護された REST API を呼び出すとき、トークンは `Authorization` 要求ヘッダー フィールドに "ベアラー" トークンとして埋め込まれ、API が呼び出し元を認証できるようにします。 | 
| `refresh_token` | Azure リソースのマネージド ID には使用されません。 |
| `expires_in` | アクセス トークンが発行されてから期限切れになるまでの有効継続時間 (秒単位)。 発行時刻はトークンの `iat` 要求で確認できます。 |
| `expires_on` | アクセス トークンが期限切れになるまでの期間。 日付は "1970-01-01T0:0:0Z UTC" からの秒数として表されます (トークンの `exp` 要求に対応)。 |
| `not_before` | アクセス トークンが有効になり、承認されるまでの期間。 日付は "1970-01-01T0:0:0Z UTC" からの秒数として表されます (トークンの `nbf` 要求に対応)。 |
| `resource` | アクセス トークンの要求対象リソース。要求の `resource` クエリ文字列パラメーターと一致します。 |
| `token_type` | トークンの種類。つまり "ベアラー" アクセス トークン。リソースが、このトークンのベアラーへのアクセスを提供できることを意味します。 |


### <a name="troubleshoot-the-virtual-machine-extension"></a>仮想マシン拡張機能のトラブルシューティング 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>障害が発生した後に仮想マシン拡張機能が再起動する

Windows と特定のバージョンの Linux で拡張機能が停止した場合は、次のコマンドレットを使用して手動で再起動できます。

```azurepowershell-interactive
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

各値の説明: 
- Windows の拡張機能の名前と種類: `ManagedIdentityExtensionForWindows`
- Linux の拡張機能の名前と種類: `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Azure リソース拡張機能のマネージド ID のスキーマ エクスポートを試行すると、"自動スクリプト" が失敗する

仮想マシンで Azure リソースのマネージド ID が有効になっている場合、その仮想マシンまたはリソース グループに対して "自動スクリプト" 機能を使用しようとすると、次のエラーが表示されます。

![Azure リソースのマネージド ID の自動スクリプトのエクスポート エラー](./media/howto-migrate-vm-extension/automation-script-export-error.png)

現時点では、Azure リソース仮想マシン拡張機能のマネージド ID では、そのスキーマをリソース グループ テンプレートにエクスポートする機能はサポートされていません。 その結果、生成されたテンプレートには、リソース上で Azure リソースのマネージド ID を有効にする構成パラメーターは表示されません。 これらのセクションは、[テンプレート を使用して Azure 仮想マシン上で Azure リソースのマネージド ID を構成する](qs-configure-template-windows-vm.md)の例に従って手動で追加できます。

Azure リソース仮想マシン拡張機能のマネージド ID (2019 年 1 月に非推奨になる予定) でスキーマのエクスポート機能が利用可能になると、[VM 拡張機能を含むリソース グループのエクスポート](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)の一覧に表示されます。

## <a name="limitations-of-the-virtual-machine-extension"></a>仮想マシン拡張機能の制限 

仮想マシン拡張機能の使用に関する重大な制限がいくつかあります。 

 * 最も重大な制限は、トークンを要求するために使用する資格情報が仮想マシンに保存されることです。 仮想マシンにうまく侵害できた攻撃者は、資格情報を抜き取るにことができてしまいます。 
 * さらに、この仮想マシン拡張機能はいくつかの Linux ディストリビューションでサポートされておらず、それらの各ディストリビューションで拡張機能の変更、ビルド、テストを行うには膨大な開発費用がかかります。 現在、次の Linux ディストリビューションのみサポートされています。 
    * CoreOS Stable
    * CentOS 7.1 
    * Red Hat 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * 仮想マシン拡張機能をプロビジョニングする必要があるため、マネージド ID を持つ仮想マシンのデプロイを行う際に、パフォーマンスに影響が生じる可能性があります。 
 * 仮想マシン拡張機能がサポートできるユーザー割り当て済みマネージド ID は、仮想マシンごとに 32 個までです。 

## <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

[Azure Instance Metadata Service (IMDS)](../../virtual-machines/windows/instance-metadata-service.md) は、実行中の仮想マシン インスタンスに関する情報を提供します。これらの情報を使用して仮想マシンの管理と構成を行うことができる REST エンドポイントです。 このエンドポイントは、仮想マシンからのみアクセスすることができる、よく知られているルーティング不可能な IP アドレス (`169.254.169.254`) で使用できます。

Azure IMDS を使用トークンを要求することには、いくつかの利点があります。 

1. サービスを仮想マシンの外部に配置できるため、マネージド ID で使用する資格情報を仮想マシン上で保管せずに済みます。 代わりに、Azure 仮想マシンのホスト コンピューター上にホストされ、保護されます。   
2. Azure IaaS でサポートされている Windows および Linux のすべてのオペレーティング システムで、マネージド ID を使用できます。
3. VM 拡張機能をプロビジョニングする必要がなくなったため、デプロイにかかる時間が短縮され、簡単になります。
4. IMDS エンドポイントでは、最大 1000 個のユーザー割り当てマネージド ID を 1 つの仮想マシンに割り当てられます。
5. 仮想マシン拡張機能の使用とは対照的に、IMDS を使用する要求に対して大きな変更を加えなくて済みます。したがって、現在仮想マシン拡張機能を使用している既存のデプロイに簡単に移植できます。

これらの理由から、仮想マシン拡張機能が非推奨になると、Azure IMDS サービスが、トークンを要求するための標準的な手段となります。 


## <a name="next-steps"></a>次の手順

* [Azure 仮想マシン上で Azure リソースのマネージド ID を使用してアクセス トークンを取得する方法](how-to-use-vm-token.md)
* [Azure Instance Metadata Service](../../virtual-machines/windows/instance-metadata-service.md)
