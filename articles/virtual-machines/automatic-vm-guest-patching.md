---
title: Azure VM での VM ゲストの自動パッチ適用
description: Azure の仮想マシンに自動的にパッチを適用する方法について説明します。
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-guest-patching
ms.collection: windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/17/2021
ms.author: manayar
ms.openlocfilehash: 276762bc2b8624f687cbb77e1af771478791a57b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101678725"
---
# <a name="preview-automatic-vm-guest-patching-for-azure-vms"></a>プレビュー: Azure VM での VM ゲストの自動パッチ適用

Azure VM での VM ゲストの自動パッチ適用を有効にすると、安全かつ自動的に仮想マシンにパッチを適用することで、容易に更新を管理し、セキュリティ コンプライアンスを維持することができます。

VM ゲストの自動パッチ適用には、次の特性があります。
- *[重大]* または *[セキュリティ]* に分類されているパッチは、自動的にダウンロードされ、VM に適用されます。
- パッチは、VM のタイムゾーンでピーク外の時間帯に適用されます。
- パッチ オーケストレーションが Azure によって管理され、[可用性優先の原則](#availability-first-patching)に従ってパッチが適用されます。
- プラットフォーム正常性シグナルによって特定された仮想マシンの正常性は、パッチ適用の失敗を検出するために監視されます。
- すべての VM サイズで機能します。

> [!IMPORTANT]
> 現在、VM ゲストの自動パッチ適用はパブリック プレビュー段階にあります。 後述するパブリック プレビュー機能を使用するためには、オプトイン手順が必要です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="how-does-automatic-vm-guest-patching-work"></a>VM ゲストの自動パッチ適用はどのように機能しますか?

VM で VM ゲストの自動パッチ適用が有効になっている場合、使用可能な *[重大]* および *[セキュリティ]* パッチが自動的にダウンロードされて VM に適用されます。 このプロセスは、毎月新しいパッチがリリースされるたびに自動的に開始されます。 パッチの評価とインストールは自動的に実行され、このプロセスでは必要に応じて VM の再起動が行われます。

VM は、その VM に適用可能なパッチを確認するため、数日ごと、または 30 日以内に複数回、定期的に評価されます。 パッチは、VM のピーク外の時間帯であればいつでも VM にインストールされる可能性があります。 この自動評価により、不足しているパッチができるだけ早く検出されるようになります。

次に説明する可用性優先のオーケストレーションに従って、パッチは毎月のパッチ リリースから 30 日以内にインストールされます。 パッチは、VM のタイムゾーンに応じて、VM のピーク外の時間帯にのみインストールされます。 パッチが自動的にインストールされるようにするには、ピーク外の時間帯に VM が実行されている必要があります。 定期的な評価の際に VM の電源がオフになっている場合、VM の電源がオンになっている次の定期的な評価中 (通常は数日以内) に VM が自動的に評価され、該当するパッチが自動的にインストールされます。

"*重大*" または "*セキュリティ*" として分類されていない定義の更新プログラムや他のパッチは、VM ゲストの自動パッチ適用ではインストールされません。 他のパッチの分類が適用されたパッチをインストールしたり、独自のカスタム メンテナンス期間内でパッチをインストールするようスケジュールしたりするには、[Update Management](./windows/tutorial-config-management.md#manage-windows-updates) を使用します。

### <a name="availability-first-patching"></a>可用性優先のパッチ適用

パッチのインストール プロセスは、VM ゲストの自動パッチ適用が有効になっているすべての VM で、Azure によってグローバルにオーケストレーションされています。 このオーケストレーションは、Azure で提供されるさまざまなレベルの可用性において、可用性優先の原則に従います。

更新中の仮想マシンのグループについては、Azure プラットフォームで更新がオーケストレーションされます。

**複数のリージョン間:**
- 毎月の更新プログラムは、グローバル展開の失敗を防ぐために、Azure 全体で段階的にオーケストレーションされます。
- 1 つのフェーズには 1 つ以上のリージョンを含めることができ、更新は、フェーズ内の対象となる VM が正常に更新された場合にのみ、次のフェーズに移動します。
- geo ペア リージョンは同時に更新されず、同じリージョンのフェーズに置くことはできません。
- 更新の成功は、更新後に VM の正常性を追跡することによって測定できます。 VM の正常性は、VM のプラットフォーム正常性インジケーターによって追跡されます。

**リージョン内:**
- 異なる Availability Zones の VM は同時に更新されません。
- 可用性セットの一部ではない VM は、サブスクリプション内のすべての VM が同時に更新されることを回避するために、ベストエフォート方式でバッチ処理されます。

**可用性セット内:**
- 共通の可用性セット内のすべての VM が同時に更新されることはありません。
-   共通の可用性セット内の VM は更新ドメインの境界内で更新され、複数の更新ドメインにまたがる VM は同時に更新されません。

特定の VM のパッチのインストール日は、月ごとに異なる可能性があります。これは、特定の VM が毎月のパッチ適用サイクルの異なるバッチで選択される可能性があるためです。

### <a name="which-patches-are-installed"></a>インストールされるパッチ
インストールされるパッチは、VM のロールアウト ステージによって異なります。 毎月、新しいグローバル ロールアウトが開始され、個々の VM に対して評価されたすべてのセキュリティおよび重大パッチが、その VM 用にインストールされます。 ロールアウトは、バッチ内のすべての Azure リージョン間でオーケストレーションされます (可用性優先のパッチ適用に関する前のセクションを参照)。

インストールされるパッチの正確なセットは、OS の種類や評価のタイミングなどの VM の構成によって異なります。 パッチ オーケストレーションが異なるタイミングで異なるリージョンに達すると、程度の差はあってもパッチが利用可能な場合、異なるリージョンにある 2 つの同一の VM に異なるパッチがインストールされる可能性があります。 頻度は低くなりますが、同じように、同じリージョン内の VM であっても、(可用性ゾーンや可用性セットのバッチが異なるために) 評価されるタイミングが異なると、異なるパッチを適用される可能性があります。

VM ゲストの自動パッチ適用でパッチのソースは構成されないため、2 つの類似した VM に異なるパッチ ソースが構成されていると (パブリック リポジトリとプライベート リポジトリなど)、やはりインストールされるパッチの正確なセットが異なることがあります。

一定の周期でパッチがリリースされる OS の種類の場合、OS のパブリック リポジトリに構成されている VM は、ある月の異なるロールアウト フェーズで同じパッチのセットを受け取ることが予想されます。 たとえば、パブリック Windows Update リポジトリに構成されている Windows VM などです。

毎月新しいロールアウトがトリガーされるので、オフピーク時間帯に VM の電源がオンになっている場合、VM は毎月少なくとも 1 つのパッチ ロールアウトを受け取ります。 これにより、VM には、使用可能な最新のセキュリティおよび重大パッチが毎月適用されます。 インストールされるパッチのセットの整合性を保証するには、独自のプライベート リポジトリにあるパッチを評価してダウンロードするように、VM を構成することができます。

## <a name="supported-os-images"></a>サポート対象の OS イメージ
現在プレビューでは、特定の OS プラットフォーム イメージから作成された VM のみがサポートされています。 プレビューでは、カスタム イメージは現在サポートされていません。

現時点では、以下のプラットフォーム SKU がサポートされています (定期的に追加されます)。

| Publisher               | OS 製品      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical  | UbuntuServer | 18.04-LTS |
| Redhat  | RHEL | 7.x |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Server-Core |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Core |

## <a name="patch-orchestration-modes"></a>パッチ オーケストレーションのモード
Azure 上の VM により、次のパッチ オーケストレーション モードがサポートされるようになりました。

**AutomaticByPlatform:**
- このモードは、Linux と Windows の両方の VM でサポートされています。
- このモードを使用すると、仮想マシンで VM ゲストの自動パッチ適用が有効になり、それ以降のパッチのインストールは Azure によってオーケストレーションされます。
- このモードは、可用性優先のパッチを適用するために必要です。
- このモードは、上記のサポートされている OS プラットフォーム イメージを使用して作成された VM でのみサポートされています。
- Windows VM の場合、このモードを設定すると、重複を避けるために、Windows 仮想マシンのネイティブ自動更新も無効になります。
- Linux VM でこのモードを使用するには、VM テンプレートでプロパティ `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform` を設定します。
- Windows VM でこのモードを使用するには、プロパティ `osProfile.windowsConfiguration.enableAutomaticUpdates=true` を設定し、VM テンプレートでプロパティ `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` を設定します。

**AutomaticByOS:**
- このモードは、Windows VM についてのみサポートされます。
- このモードでは、Windows 仮想マシンで自動更新が有効になり、自動更新によってパッチが VM にインストールされます。
- このモードは、可用性優先のパッチ適用をサポートしていません。
- Windows VM で他のパッチ モードが指定されていない場合は、このモードが既定で設定されます。
- Windows VM でこのモードを使用するには、プロパティ `osProfile.windowsConfiguration.enableAutomaticUpdates=true` を設定し、VM テンプレートでプロパティ `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` を設定します。

**Manual:**
- このモードは、Windows VM についてのみサポートされます。
- このモードでは、Windows 仮想マシンの自動更新が無効になります。
- このモードは、可用性優先のパッチ適用をサポートしていません。
- カスタム パッチ適用ソリューションを使用する場合は、このモードに設定する必要があります。
- Windows VM でこのモードを使用するには、プロパティ `osProfile.windowsConfiguration.enableAutomaticUpdates=false` を設定し、VM テンプレートでプロパティ `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` を設定します。

**ImageDefault:**
- このモードは、Linux VM についてのみサポートされます。
- このモードは、可用性優先のパッチ適用をサポートしていません。
- このモードを指定すると、VM の作成に使用されたイメージでの既定のパッチ構成が使用されます。
- Linux VM で他のパッチ モードが指定されていない場合は、このモードが既定で設定されます。
- Linux VM でこのモードを使用するには、VM テンプレートでプロパティ `osProfile.linuxConfiguration.patchSettings.patchMode=ImageDefault` を設定します。

> [!NOTE]
>Windows VM の場合、現在、プロパティ `osProfile.windowsConfiguration.enableAutomaticUpdates` は VM の最初の作成時にのみ設定できます。 手動モードから自動モードへの切り替え、またはどちらかの自動モードから手動モードへの切り替えは、現在サポートされていません。 AutomaticByOS モードから AutomaticByPlatfom モードへの切り替えがサポートされています。

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>VM ゲストの自動パッチ適用を有効にするための要件

- 仮想マシンには、[Windows](./extensions/agent-windows.md) または [Linux](./extensions/agent-linux.md) 用の Azure VM エージェントがインストールされている必要があります。
- Linux VM の場合、Azure Linux エージェントはバージョン 2.2.53.1 以降である必要があります。 現在のバージョンが必要なバージョンより古い場合は、[Linux エージェントを更新します](./extensions/update-linux-agent.md)。
- Windows VM の場合、Windows Update サービスが仮想マシンで実行されている必要があります。
- 仮想マシンから構成されている更新エンドポイントにアクセスできる必要があります。 Linux 用のプライベート リポジトリまたは Windows VM 用の Windows Server Update Services (WSUS) を使用するように仮想マシンが構成されている場合は、関連する更新エンドポイントにアクセスできる必要があります。
- Compute API バージョン 2020-12-01 以降を使用します。 Windows VM で使用できる Compute API バージョン 2020-06-01 は、機能が制限されています。

プレビュー機能を有効にするには、次のセクションで詳しく説明するように、サブスクリプションごとに機能 **InGuestAutoPatchVMPreview** と **InGuestPatchVMPreview** に 1 回だけオプトインする必要があります。

### <a name="rest-api"></a>REST API
次の例では、お使いのサブスクリプションでプレビューを有効にする方法について説明します。

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```
```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

機能の登録には最大で 15 分かかる場合があります。 登録状態を確認するには:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```
```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```
サブスクリプションに対してこの機能が登録されたら、変更をコンピューティング リソース プロバイダーに伝達することによって、オプトイン プロセスを完了します。

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
[Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) コマンドレットを使用して、サブスクリプションでのプレビューを有効にします。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

機能の登録には最大で 15 分かかる場合があります。 登録状態を確認するには:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

サブスクリプションに対してこの機能が登録されたら、変更をコンピューティング リソース プロバイダーに伝達することによって、オプトイン プロセスを完了します。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
[az feature register](/cli/azure/feature#az-feature-register) を使用して、サブスクリプションでのプレビューを有効にします。

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

機能の登録には最大で 15 分かかる場合があります。 登録状態を確認するには:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

サブスクリプションに対してこの機能が登録されたら、変更をコンピューティング リソース プロバイダーに伝達することによって、オプトイン プロセスを完了します。

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="enable-automatic-vm-guest-patching"></a>VM ゲストの自動パッチ適用を有効化する
Windows VM で VM ゲストの自動パッチ適用を有効にするには、プロパティ *osProfile.windowsConfiguration.enableAutomaticUpdates* が VM テンプレート定義で *true* に設定されていることを確認します。 このプロパティは、VM の作成時にのみ設定できます。 この追加のプロパティは、Linux VM には適用されません。

### <a name="rest-api-for-linux-vms"></a>Linux VM 用の REST API
次の例では、VM ゲストの自動パッチ適用を有効にする方法について説明します。

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "properties": {
    "osProfile": {
      "linuxConfiguration": {
        "provisionVMAgent": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="rest-api-for-windows-vms"></a>Windows VM 用の REST API
次の例では、VM ゲストの自動パッチ適用を有効にする方法について説明します。

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-06-01`
```

```json
{
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="azure-powershell-for-windows-vms"></a>Windows VM 用 Azure PowerShell
VM を作成または更新する際に VM ゲストの自動パッチ適用を有効にするには、[Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) コマンドレットを使用します。

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-for-windows-vms"></a>Windows VM 用の Azure CLI
新しい VM を作成するときに VM ゲストの自動パッチ適用を有効にするには、[az vm create](/cli/azure/vm#az-vm-create) を使用します。 次の例では、*myResourceGroup* という名前のリソース グループ内の *myVM* という名前の VM に対して、VM ゲストの自動パッチ適用を構成します。

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

既存の VM を変更するには、[az vm update](/cli/azure/vm#az-vm-update) を使用します

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>有効化と評価

> [!NOTE]
>VM で VM のゲストの自動更新を有効にするには 3 時間以上かかることがあります。これは、有効化が VM のピーク外の時間帯に行われるためです。 評価とパッチのインストールは、ピーク外の時間帯にのみ行われるため、パッチを適用するには、ピーク外の時間帯でも VM が実行されている必要があります。

VM で VM ゲストの自動パッチ適用が有効になっていると、Linux VM には種類 `Microsoft.CPlat.Core.LinuxPatchExtension` の VM 拡張機能が、また Windows VM には種類 `Microsoft.CPlat.Core.WindowsPatchExtension` の VM 拡張機能が、それぞれインストールされます。 この拡張機能は、VM ゲストの自動パッチ適用プロセスの一環として Azure プラットフォームによって管理されているため、手動でインストールまたは更新する必要はありません。

VM で VM のゲストの自動更新を有効にするには 3 時間以上かかることがあります。これは、有効化が VM のピーク外の時間帯に行われるためです。 この拡張機能も、VM のピーク外の時間帯にインストールおよび更新されます。 有効化が完了する前に VM のピーク外の時間が終了した場合、有効化プロセスは、次に利用可能なピーク外の時間帯に再開されます。

自動更新はほとんどのシナリオで無効になっており、パッチのインストールは今後の拡張機能によって行われます。 次の条件が適用されます。
- Windows VM でこれまで AutomaticByOS パッチ モードにより Automatic Windows Update がオンになっていた場合、拡張機能がインストールされると VM の Automatic Windows Update はオフになります。
- Ubuntu VM の場合、VM ゲストの自動パッチ適用の有効化が完了すると、既定の自動更新は自動的に無効になります。
- RHEL の場合は、自動更新を手動で無効にする必要があります (これはプレビューの制限です)。 次のように実行します。

```
systemctl stop packagekit
```

```
systemctl mask packagekit
```

VM ゲストの自動パッチ適用が完了していて、パッチ拡張が VM にインストールされているかどうかを確認するには、VM のインスタンス ビューを確認します。 有効化プロセスが完了すると、拡張機能がインストールされ、`patchStatus` の下に VM の評価結果が表示されます。 VM のインスタンス ビューには、次に示すように複数の方法でアクセスできます。

### <a name="rest-api"></a>REST API

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-12-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
`-Status` パラメーターを指定して [Get-AzVM](/powershell/module/az.compute/get-azvm) コマンドレットを使用して、VM のインスタンス ビューにアクセスします。

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

現在、PowerShell ではパッチの拡張機能に関する情報のみが提供されています。 `patchStatus` に関する情報は、PowerShell を通じて間もなく提供されます。

### <a name="azure-cli"></a>Azure CLI
[az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view) を使用して、VM のインスタンス ビューにアクセスします。

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>VM のパッチの状態について理解する

インスタンス ビューの応答の `patchStatus` セクションでは、最新の評価と VM の最後のパッチのインストールに関する詳細が提供されます。

VM の評価結果は、`availablePatchSummary` セクションで確認できます。 評価は、VM ゲストの自動パッチ適用が有効になっている VM に対して定期的に実行されます。 評価後に使用可能なパッチの数は `criticalAndSecurityPatchCount` と `otherPatchCount` の結果として表示されます。 VM ゲストの自動パッチ適用により、 *[重大]* および *[セキュリティ]* パッチの分類で評価されたすべてのパッチがインストールされます。 その他の評価済みのパッチはスキップされます。

VM のパッチのインストール結果は、`lastPatchInstallationSummary` セクションで確認できます。 このセクションでは、インストール済み、保留中、失敗、またはスキップされたパッチの数などを含む、VM で最後に試行されたパッチのインストールの詳細について説明します。 パッチは、VM のピーク外の時間帯のメンテナンス期間中にのみインストールされます。 保留中および失敗したパッチは、次のピーク外の時間帯のメンテナンス期間中に自動的に再試行されます。

## <a name="on-demand-patch-assessment"></a>オンデマンドのパッチ評価
VM に対して VM ゲストの自動パッチ適用が既に有効になっている場合は、VM のピーク外の時間帯に定期的なパッチの評価が実行されます。 このプロセスは自動的に行われ、このドキュメントで既に説明したように、VM のインスタンス ビューで最新の評価の結果を確認できます。 また、いつでも VM に対してオンデマンドのパッチ評価をトリガーすることもできます。 パッチの評価が完了するまで数分かかることがあります。また、最新の評価の状態は、VM のインスタンス ビューに更新されます。

プレビュー機能を有効にするには、サブスクリプションごとに機能 **InGuestPatchVMPreview** のワンタイム オプトインが必要になります。 この機能プレビューは、**InGuestAutoPatchVMPreview** で以前実行された、VM ゲストの自動パッチ適用機能とは異なります。 追加の機能プレビューを有効にすることは、別の要件となります。 オンデマンド パッチ評価の機能プレビューは、自動 VM ゲスト パッチについて前に説明した[プレビュー有効化プロセス](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching)に従うことで有効にすることができます。

> [!NOTE]
>オンデマンドのパッチ評価では、パッチ インストールは自動的にトリガーされません。 VM ゲストの自動パッチ適用を有効にすると、評価された適用可能な VM のパッチは、このドキュメントで既に説明した可用性優先のパッチ適用プロセスに従って、VM のピーク外の時間帯にインストールされます。

### <a name="rest-api"></a>REST API
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
[Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) コマンドレットを使用して、仮想マシンで利用可能なパッチを評価します。

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli"></a>Azure CLI
[az vm assess-patches](/cli/azure/vm#az-vm-assess-patches) を使用して、仮想マシンで利用可能なパッチを評価します。

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Windows 仮想マシンの作成と管理について説明します](./windows/tutorial-manage-vm.md)
