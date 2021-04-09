---
title: 'Azure ExpressRoute: MACsec を構成する'
description: この記事では、お使いのエッジ ルーターと Microsoft のエッジ ルーターの間の接続をセキュリティで保護するように MACsec を構成する際に役立ちます。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/22/2019
ms.author: duau
ms.openlocfilehash: 8ee7f42a4b05def7c2239c7a1e5bcef54f6f7e50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89395776"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>ExpressRoute Direct ポートで MACsec を構成する

この記事では、PowerShell を利用し、お使いのエッジ ルーターと Microsoft のエッジ ルーターの間の接続をセキュリティで保護するように MACsec を構成する際に役立ちます。

## <a name="before-you-begin"></a>開始する前に

構成を開始する前に、次のことを確認してください。

* [ExpressRoute Direct のプロビジョニング ワークフロー](expressroute-erdirect-about.md)を理解していること。
* [ExpressRoute Direct ポート リソース](expressroute-howto-erdirect.md)を作成していること。
* PowerShell をローカルで実行する場合は、Azure PowerShell の最新バージョンがコンピューターにインストールされていることを確認します。

### <a name="working-with-azure-powershell"></a>Azure PowerShell を使用する

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>サインインし、適切なサブスクリプションを選択する

構成を開始するには、Azure アカウントにサインインし、使用するサブスクリプションを選択します。

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. Azure Key Vault、MACsec シークレット、ユーザー ID を作成する

1. MACsec シークレットを新しいリソース グループに格納する目的で Key Vault インスタンスを作成します。

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    キー コンテナーまたはリソース グループが既にある場合、それを再利用できます。 ただし、既存のキー コンテナーで [**soft-delete** 機能](../key-vault/general/soft-delete-overview.md)を有効にすることが非常に重要です。 soft-delete が有効になっていない場合は、次のコマンドを使用して有効にできます。

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. ユーザー ID を作成します。

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    New-AzUserAssignedIdentity が有効な PowerShell コマンドレットとして認識されない場合、(管理者モードで) 次のモジュールをインストールし、上記のコマンドを再度実行してください。

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. CAK (接続関連付けキー) と CKN (接続関連付けキー名) を作成し、それをキー コンテナーに格納します。

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. GET 権限をユーザー ID に割り当てます。

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   これでこの ID で、CAK や CKN などのシークレットをキー コンテナーから取得できるようになりました。
5. このユーザー ID を ExpressRoute で使用されるように設定します。

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. ExpressRoute Direct ポートで MACsec を構成する

### <a name="to-enable-macsec"></a>MACsec を有効にするには

各 ExpressRoute Direct インスタンスに物理ポートが 2 つあります。 両方のポートで MACsec を同時に有効にするか、一度に 1 つのポートで MACsec を有効にできます。 ExpressRoute Direct を既に利用している場合、一度に 1 つのポートで有効にすると (アクティブなポートにトラフィックを切り替える間、もう 1 つのポートでサービスを提供します)、中断が最小限に抑えられます。

1. 必要であれば、ExpressRoute 管理コードで MACsec シークレットにアクセスできるよう、MACsec のシークレットと暗号を設定し、ユーザー ID とポートを関連付けます。

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. (任意) ポートが Administrative Down の状態にあるとき、次のコマンドを実行し、ポートを有効にできます。

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    この時点で、Microsoft 側の ExpressRoute Direct ポートで MACsec が有効になります。 お使いのエッジ デバイスでそれを構成していない場合、続行し、同じ MACsec シークレット/暗号で構成できます。

### <a name="to-disable-macsec"></a>MACsec を無効にする

MACsec がお使いの ExpressRoute Direct インスタンスで不要になった場合、次のコマンドを実行して無効にできます。

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

この時点で、Microsoft 側の ExpressRoute Direct ポートで MACsec が無効になります。

### <a name="test-connectivity"></a>接続をテストする
お使いの ExpressRoute Direct ポートで (MACsec キーの更新を含め) MACsec を構成したら、回路の BGP セッションが稼働しているかどうかを[確認](expressroute-troubleshooting-expressroute-overview.md)します。 ポートにまだ何の回路もない場合、最初に 1 つ作成し、回路の Azure プライベート ペアリングまたは Microsoft ペアリングを設定してください。 お使いのネットワーク デバイスと Microsoft のネットワーク デバイスの間で MACsec キーが一致しないなど、MACsec が間違って構成された場合、レイヤー 2 で ARP 解決が表示されず、レイヤー 3 で BGP 確立が表示されません。 すべてが正しく構成されている場合、双方向で BGP ルートが正しくアドバタイズされており、ExpressRoute 経由で適宜、アプリケーション データが流れているはずです。

## <a name="next-steps"></a>次のステップ
1. [ExpressRoute Direct で ExpressRoute 回路を作成する](expressroute-howto-erdirect.md)
2. [ExpressRoute 回線を Azure 仮想ネットワークにリンクする](expressroute-howto-linkvnet-arm.md)
3. [ExpressRoute 接続を確認する](expressroute-troubleshooting-expressroute-overview.md)
