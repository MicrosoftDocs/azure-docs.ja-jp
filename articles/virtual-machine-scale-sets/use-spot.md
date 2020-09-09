---
title: Azure スポット VM を使用するスケール セットを作成する
description: コスト削減のためにスポット VM を使う Azure 仮想マシン スケール セットを作成する方法を説明します。
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 03/25/2020
ms.reviewer: jagaveer
ms.custom: jagaveer, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: b5888000028ba87d503bb0bc690aad6628a51a37
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89072742"
---
# <a name="azure-spot-vms-for-virtual-machine-scale-sets"></a>仮想マシン スケール セット用の Azure スポット VM 

スケール セットで Azure Spot を使うと、非常に低コストで未使用の容量を利用できます。 Azure で容量の回復が必要になると常に、Azure インフラストラクチャはスポット インスタンス を排除します。 したがってスポット インスタンスは、バッチ処理ジョブ、開発/テスト環境、大規模なコンピューティング ワークロードなど、中断してもかまわないワークロードに最適です。

利用可能な容量は、サイズ、リージョン、時刻などによって異なります。 スケール セットにスポット インスタンスを展開すると、Azure は利用可能な容量がある場合のみインスタンスを割り当てますが、これらのインスタンスには SLA はありません。 スポット スケール セットは 1 つの障害ドメインに展開され、高可用性の保証はありません。


## <a name="pricing"></a>価格

スポット インスタンスの価格は、リージョンと SKU に基づいて変化します。 詳細については、[Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) および [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/) での価格を参照してください。 


可変する価格に対して、最大 5 桁の小数点以下を使用して、最大価格を米ドル (USD) で設定することができます。 たとえば、`0.98765` の値は、1 時間あたり $0.98765 米ドルの最大価格になります。 最大価格を `-1` に設定した場合、インスタンスは価格に基づいて排除されません。 インスタンスの価格は、使用可能な容量とクォータがある限り、現在のスポットの価格または標準インスタンスの価格のいずれか低い方になります。

## <a name="eviction-policy"></a>削除ポリシー

スポット スケール セットの作成では､この排除ポリシーを *[Deallocate]\(割り当て解除\)* (既定) または *[Delete]\(削除\)* のいずれかに設定できます｡ 

*[Deallocate]\(割り当て解除\)* ポリシーでは､排除されたインスタンスは [stopped-deallocated]\(停止 - 割り当て解除\) 状態に移行し､排除されたインスタンスはデプロイし直すことができます｡ ただし､割り当てが成功する保証はありません｡ 割り当てを解除された VM は、スケール セットのインスタンス クォータを基にカウントされ、構成しているディスクの料金が課金されます。 

スポット スケール セットのインスタンスが排除されたときにインスタンス を削除したい場合は、排除ポリシーを *[delete]\(削除\)* に設定できます。 排除ポリシーを削除に設定した場合、スケール セット インスタンスのカウント プロパティを増やすことで、新しい VM を作成できます。 排除された VM は基になっているディスクと共に削除されるので、ストレージが課金されることはありません。 また、スケール セットの自動スケーリング機能を使って、排除された VM の自動的な補正を試みることはできますが、割り当てが成功するという保証はありません。 ディスクのコストとクォータ制限への到達を回避するために、排除ポリシーを [delete]\(削除\) に設定しているときだけ、スポット スケール セットで自動スケーリング機能を使うことをお勧めします。 

ユーザーは、[Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md) を通じて VM 内通知を受け取ることができます。 これにより、VM が排除されつつある場合には通知が送られ、排除される前にジョブを完了し、タスクのシャットダウンを実行するために 30 秒が与えられます。 

## <a name="placement-groups"></a>配置グループ
配置グループは、Azure 可用性セットに似た構造で、独自の障害ドメインとアップグレード ドメインが備わっています。 既定では、スケール セットは、最大サイズが 100 個の VM である 1 つの配置グループで構成されます。 `singlePlacementGroup` というスケール セット プロパティが *false* に設定されている場合、そのスケール セットは、複数の配置グループで構成することができ、0 ～ 1,000 個の VM が含まれます。 

> [!IMPORTANT]
> HPC で Infiniband を使用している場合を除き、リージョンまたはゾーン全体でのスケーリングを向上させるため、スケール セットのプロパティ `singlePlacementGroup` を *false* に設定して複数の配置グループを有効にすることを強くお勧めします。 

## <a name="deploying-spot-vms-in-scale-sets"></a>スケール セットへのスポット VM のデプロイ

スケール セットにスポット VM を展開するには、新しい *Priority* フラグを *Spot* に設定します。 スケール セット内のすべての VM がスポットに設定されます。 スポット VM でスケール セットを作成するには、次のいずれかの方法を使います。
- [Azure Portal](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure リソース マネージャーのテンプレート](#resource-manager-templates)

## <a name="portal"></a>ポータル

スポット VM を使用するスケール セットを作成するプロセスは、[使用の開始に関する記事](quick-create-portal.md)で詳しく説明されているプロセスと同じです。 スケール セットをデプロイするときに､Spot フラグと排除ポリシーを設定することができます｡![スポット VM でスケール セットを作成する](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

スポット VM を使用するスケール セットを作成するプロセスは、[使用の開始に関する記事](quick-create-cli.md)で詳しく説明されているプロセスと同じです。 "--Priority Spot" を追加し、`--max-price` を追加するだけです。 この例では、`-1` を `--max-price` に使用して、インスタンスが価格に基づいて削除されないようにします。

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --single-placement-group false \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

スポット VM を使用するスケール セットを作成するプロセスは、[使用の開始に関する記事](quick-create-powershell.md)で詳しく説明されているプロセスと同じです。
"-Priority Spot" を追加し、[New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) に `-max-price` を指定するだけです。

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Resource Manager テンプレート

スポット VM を使うスケール セットを作成するプロセスは、[Linux](quick-create-template-linux.md) または [Windows](quick-create-template-windows.md) での使用の開始に関する記事で詳しく説明されているものと同じです。 

スポット テンプレートのデプロイの場合は、`"apiVersion": "2019-03-01"` 以降を使用してください。 

テンプレートで `"virtualMachineProfile":` セクションに `priority`、`evictionPolicy`、`billingProfile` の各プロパティ、および `"Microsoft.Compute/virtualMachineScaleSets"` セクションに `"singlePlacementGroup": false,` を追加します。

```json

{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  },
  "properties": {
    "singlePlacementGroup": false,
    }

        "virtualMachineProfile": {
              "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            },
```

無効にしたインスタンスを削除するには、`evictionPolicy` パラメーターを `Delete` に変更します。

## <a name="faq"></a>よく寄せられる質問

**質問:** 作成後、スポット インスタンスは標準のインスタンスと同じですか。

**A:** はい。ただし、スポット VM に対する SLA はなく、いつでも排除される可能性があります。


**質問:** 排除された後も容量が必要な場合はどうすればよいですか?

**A:** 容量がすぐに必要な場合は、スポット VM ではなく、標準の VM を使用することをお勧めします。


**質問:** スポットのクォータはどのように管理されますか。

**A:** スポット インスタンスと標準のインスタンスは、別々のクォータ プールを持ちます。 スポット クォータは、VM とスケール セット インスタンスの間で共有されます。 詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md)」をご覧ください。


**質問:** スポットに追加のクォータを要求することはできますか。

**A:** はい。[標準のクォータ要求プロセス](../azure-portal/supportability/per-vm-quota-requests.md)を通じて、スポット VM のクォータを増やす要求を送信することができます。


**質問:** 既存のスケール セットをスポット スケール セットに変換できますか。

**A:** できません｡`Spot` フラグの設定は、作成時にのみ行うことができます｡


**質問:** 優先順位の低いスケール セットに `low` を使用していた場合、代わりに `Spot` を使用する必要がありますか。

**A:** 現時点では `low` と `Spot` の両方が機能しますが、`Spot` の使用に移行し始める必要があります。


**質問:** 通常の VM とスポット VM の両方を使用してスケール セットを作成できますか。

**A:** できません｡スケール セットが複数の異なる優先順位をサポートすることはできません｡


**質問:** スポット スケール セットで自動スケールを使用できますか。

**A:** はい､できます｡スポット スケール セットに自動スケール ルールを設定できます｡ VM が排除された場合､自動スケールは新しいスポット VM の作成を試みることができます｡ ただし､この容量が保証されるわけではないことを忘れないでください｡ 


**質問:** 自動スケールは両方の排除ポリシー (割り当て解除と削除) で機能しますか ?

**A:** 自動スケールを使用する場合は排除ポリシーを削除に設定することをお勧めします｡ これは､割り当てを解除されたインスタンスが､スケール セットに対する容量数を基にカウントされるためです｡ 自動スケールを使用すると､インスタンスが割り当て解除されて排除されるため､短時間に目標インスタンス数に達する可能性があります｡ 


**質問:** どのチャネルがスポット VM をサポートしますか。

**A:** スポット VM の利用可否について、次の表を参照してください。

<a name="channel"></a>

| Azure チャンネル               | Azure スポット VM の利用可否       |
|------------------------------|-----------------------------------|
| Enterprise Agreement         | はい                               |
| 従量課金制                | はい                               |
| クラウド サービス プロバイダー (CSP) | [パートナーにお問い合わせください](/partner-center/azure-plan-get-started) |
| メリット                     | 使用不可                     |
| スポンサー                    | はい                               |
| 無料試用版                   | 使用不可                     |


**質問:** どこで質問を投稿できますか。

**A:** [Q&A](/answers/topics/azure-spot.html) で質問を投稿し、`azure-spot` のタグを付けることができます。 

## <a name="next-steps"></a>次のステップ

価格について詳しくは、[仮想マシン スケール セットの価格のページ](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)をご覧ください。
