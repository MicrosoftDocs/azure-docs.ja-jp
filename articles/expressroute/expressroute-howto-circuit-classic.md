---
title: ExpressRoute 回線を変更する:PowerShell:Azure クラシック| Microsoft Docs
description: この記事では、ExpressRoute クラシック デプロイ モデル回線の状態確認、更新、または削除とプロビジョニング解除を行う手順について説明します。
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: ganesr
ms.reviewer: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 7468338e7bc39128564e71831abe61bb1714ff72
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849236"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>PowerShell を使用した ExpressRoute 回線の変更 (クラシック)

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager テンプレート](expressroute-howto-circuit-resource-manager-template.md)
> * [ビデオ - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (クラシック)](expressroute-howto-circuit-classic.md)
>

この記事では、ExpressRoute クラシック デプロイ モデル回線の状態確認、更新、または削除とプロビジョニング解除を行う手順について説明します。 この記事は、クラシック デプロイ モデルに適用されます。

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Azure のデプロイ モデルについて**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>開始する前に

最新バージョンの Azure Service Management (SM) PowerShell モジュールと ExpressRoute モジュールをインストールします。  次の例を使用する場合は、コマンドレットの新しいバージョンがリリースされると、バージョン番号 (この例では、5.1.1) が変わることに注意してください。

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

Azure PowerShell モジュールを使用するようにコンピューターを構成する方法の手順については、「[Azure PowerShell コマンドレットの概要](/powershell/azure/overview)」を参照してください。

Azure アカウントにサインインするには、次の例を使用します。

1. 管理者特権で PowerShell コンソールを開き、アカウントに接続します。 接続については、次の例を参考にしてください。

   ```powershell
   Connect-AzAccount
   ```
2. アカウントのサブスクリプションを確認します。

   ```powershell
   Get-AzSubscription
   ```
3. 複数のサブスクリプションがある場合は、使用するサブスクリプションを選択します。

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

4. 次に、次のコマンドレットを使用して、Azure サブスクリプションをクラシック デプロイ モデルの PowerShell に追加します。

   ```powershell
   Add-AzureAccount
   ```

## <a name="get-the-status-of-a-circuit"></a>回線の状態を取得する

この情報は、 `Get-AzureCircuit` コマンドレットを使用していつでも取得できます。 パラメーターを指定せずに呼び出しを実行すると、すべての回線が一覧表示されます。

```powershell
Get-AzureDedicatedCircuit

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled

Bandwidth                        : 1000
CircuitName                      : MyAsiaCircuit
Location                         : Singapore
ServiceKey                       : #################################
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

呼び出しに対するパラメーターとしてサービス キーを渡すことで、特定の ExpressRoute 回線に関する情報を取得できます。

```powershell
Get-AzureDedicatedCircuit -ServiceKey "*********************************"

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

次の例を実行することで、すべてのパラメーターの詳細な説明を取得できます。

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>回線の変更

ExpressRoute 回線の特定のプロパティは、接続に影響を与えることなく変更できます。

ダウンタイムなく、次のタスクを実行できます。

* ExpressRoute 回線の ExpressRoute Premium アドオンを有効または無効にします。
* ポートに使用可能な容量があれば、ExpressRoute 回線の帯域幅を増やします。 回線の帯域幅のダウングレードはサポートされていません。
* 課金プランを従量制課金データから無制限データに変更します。 無制限データから従量制課金データへの課金プランの変更はサポートされていません。
* *従来の操作の許可*を有効または無効にできます。

制限および制約事項の詳細は、「 [ExpressRoute の FAQ](expressroute-faqs.md) 」を参照してください。

### <a name="enable-the-expressroute-premium-add-on"></a>ExpressRoute Premium アドオンを有効にする

次の PowerShell コマンドレットを使用して、既存の回線の ExpressRoute Premium アドオンを有効にできます。

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Premium
Status                           : Enabled
```

これで、回線の ExpressRoute Premium アドオン機能が有効になります。 このコマンドが正常に実行されるとすぐに、Premium アドオン機能の課金が始まります。

### <a name="disable-the-expressroute-premium-add-on"></a>ExpressRoute Premium アドオンを無効にする

> [!IMPORTANT]
> 標準回線で許可されるリソースより多くのリソースを使用する場合、この操作は失敗することがあります。
>
>

#### <a name="considerations"></a>考慮事項

* Premium から Standard にダウングレードする前に、回線にリンクされている仮想ネットワークの数が 10 未満であることを確認します。 これを行わない場合、更新要求が失敗し、Premium 料金で課金されます。
* 他の地理的リージョンではすべての仮想ネットワークのリンクを解除する必要があります。 そうしない場合、更新要求が失敗し、Premium 料金で課金されます。
* プライベート ピアリングの場合、ルート テーブルのサイズを 4,000 ルート未満にする必要があります。 ルート テーブルのサイズが 4,000 ルートを超える場合、BGP セッションがドロップし、アドバタイズされたプレフィックスの数が 4,000 未満になるまで再度有効になりません。

#### <a name="to-disable-the-premium-add-on"></a>Premium アドオンを無効にするには

次の PowerShell コマンドレットを使用して、既存の回線の ExpressRoute Premium アドオンを無効にできます。

```powershell

Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

### <a name="update-the-expressroute-circuit-bandwidth"></a>ExpressRoute 回線の帯域幅を更新する

プロバイダーでサポートされている帯域幅のオプションについては、「 [ExpressRoute の FAQ](expressroute-faqs.md) 」を確認してください。 既存の回線のサイズを超えるサイズ (回線の作成先の物理ポートで許容されるだけのサイズ) を選択することができます。

> [!IMPORTANT]
> 既存のポートの容量が不十分な場合、ExpressRoute 回線の再作成が必要になる可能性があります。 その場所に使用可能な追加の容量がない場合、回路をアップグレードすることはできません。
>
> 中断せずに ExpressRoute 回線の帯域幅を減らすことはできません。 帯域幅をダウングレードするには、ExpressRoute 回線のプロビジョニングを解除してから、新しい ExpressRoute 回線を再度プロビジョニングする必要があります。
>
>

#### <a name="resize-a-circuit"></a>回線のサイズ変更

必要なサイズを決定した後、次のコマンドを使用して、回線のサイズを変更することができます。

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Microsoft 側で回線のサイズが増やされたら、接続プロバイダーに連絡し、この変更に合わせて接続プロバイダー側の構成を更新するよう依頼する必要があります。 更新された帯域幅のオプションの課金は、この時点から開始されます。

回線の帯域幅を増やしたときに次のエラーが発生した場合は、既存の回線が作成されている物理ポートに十分な帯域幅がないことを意味します。 この回線を削除し、必要なサイズの新しい回線を作成する必要があります。

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>回線のプロビジョニング解除と削除

### <a name="considerations"></a>考慮事項

* この操作を正常に行うには、ExpressRoute 回線からすべての仮想ネットワークのリンクを解除する必要があります。 この操作が失敗した場合は、回線にリンクされている仮想ネットワークがないか確認してください。
* ExpressRoute 回線サービス プロバイダーのプロビジョニング状態が**プロビジョニング中**または**プロビジョニング済み**の場合、サービス プロバイダー側の回線のプロビジョニングを解除するには、サービス プロバイダーに連絡する必要があります。 Microsoft は、サービス プロバイダーが回線のプロビジョニング解除を完了し、通知するまで、リソースの予約と課金を続行します。
* サービス プロバイダーが回線をプロビジョニング解除済み (サービス プロバイダーのプロビジョニング状態が**未プロビジョニング**に設定されている) の場合、回線を削除することができます。 これによって回線の課金が停止されます。

#### <a name="delete-a-circuit"></a>回線の削除

ExpressRoute 回線は、次のコマンドを実行して削除できます。

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
