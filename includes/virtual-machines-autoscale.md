---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 3c008e77116a9b42a2ea137069529c5e241adddd
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56213099"
---
[仮想マシン スケール セット](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)と [Azure Monitor の自動スケール機能](../articles/azure-monitor/platform/autoscale-overview.md)を使うと、[仮想マシン (VM)](../articles/virtual-machines/windows/overview.md) を簡単に[自動スケール](../articles/azure-monitor/platform/autoscale-best-practices.md)できます。 自動スケールを適用するには、VM がスケール セットのメンバーになっている必要があります。 この記事では、垂直方向、水平方向を問わず自動または手動で VM をスケールする方法について理解を深めることができるよう、各種の情報を紹介します。

## <a name="horizontal-or-vertical-scaling"></a>水平スケーリングと垂直スケーリング

Azure Monitor の自動スケール機能は、水平スケーリングにのみ対応しています。"水平スケーリング" とは、VM の数を増やしたり ("スケールアウト")、減らしたり ("スケールイン") することを指します。 クラウド環境では数千の VM を使って負荷を処理することもできるため、水平スケールの方が柔軟性に優れていると言えます。 水平スケーリングの方法には、スケール セットの容量 (インスタンス数) を自動で変更する方法と、手動で変更する方法があります。 

"垂直スケーリング" とは、VM の数を変えずに VM の性能を上げたり ("スケールアップ")、下げたり (スケールダウン) することを指します。 性能は、メモリ、CPU 速度、ディスク領域などの属性を指標として計測します。 垂直スケーリングが可能かどうかは、現行のものよりも大きなハードウェアが使えるかどうかによって左右されますが、その上限に達するのはあっという間です。また、そのようなハードウェアが使えるかどうかは、リージョンによって異なります。 垂直スケーリングでは、多くの場合、VM の停止と再起動も必要になります。 垂直スケーリングを実行するには、スケール セットに含まれる VM の構成で新しいサイズを設定します。

[Azure Automation](../articles/automation/automation-intro.md) で Runbook を使えば、[スケール セット内の VM のスケールアップとスケールダウン](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision.md)を簡単に行うことができます。

## <a name="create-a-virtual-machine-scale-set"></a>仮想マシン スケール セットを作成する

スケール セットを使用すると、まったく同じ VM をまとめて簡単にデプロイし、管理することができます。 Linux または Windows のスケール セットを作成するには、[Azure Portal](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md)、[Azure PowerShell](../articles/virtual-machines/windows/tutorial-create-vmss.md)、または [Azure CLI](../articles/virtual-machines/linux/tutorial-create-vmss.md) を使用します。 また、[Python](https://azure.microsoft.com/develop/python/)、[Node.js](/nodejs/azure) などの SDK や、[REST API](/rest/api/compute/virtualmachinescalesets) でも、スケール セットを作成および管理できます。 こうして作成したスケール セットにメトリックとルールを適用することによって、VM に自動スケールを実現します。

## <a name="configure-autoscale-for-a-scale-set"></a>スケール セットの自動スケールを構成する

自動スケールは、アプリケーションに対する負荷の処理に必要な数だけ VM を利用できる機能です。 VM を追加して負荷の増大に対処したり、アイドル状態の VM があれば削除してコストを節約したりできます。 実行する VM の数は、ルールで最小値と最大値を指定します。 最小値を指定すると、負荷のない状況でも、アプリケーションが常に実行されます。 最大値を指定すると、時間単位の合計コストが制限されます。

[Azure PowerShell](../articles/azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings) または [Azure CLI](https://docs.microsoft.com/cli/azure/monitor/autoscale-settings) を使ってスケール セットを作成する場合には、スケール セットの作成時に自動スケールを有効にできます。 このほか、スケール セットを作成した後に自動スケールを有効にすることもできます。 [Azure Resource Manager テンプレート](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)では、スケール セットの作成から拡張機能のインストール、自動スケールの構成までが可能です。 Azure Portal の場合には、Azure Monitor かスケール セットの設定から自動スケールを有効にできます。

![自動スケールの有効化](./media/virtual-machines-autoscale/virtual-machines-autoscale-enable.png)
 
### <a name="metrics"></a>メトリック

Azure Monitor の自動スケール機能では、実行する VM の数を[メトリック](../articles/azure-monitor/platform/autoscale-common-metrics.md)に基づいて調節することができます。 既定ではディスク、ネットワーク、CPU 使用率について、VM からホストレベルの基本的なメトリックが提供されます。 診断拡張機能を使って収集対象となる診断データを構成した場合には、ディスク、CPU、メモリに関して追加のゲスト OS パフォーマンス カウンターが利用できるようになります。

![メトリックの条件](./media/virtual-machines-autoscale/virtual-machines-autoscale-criteria.png)

ホストでは利用できないメトリックに基づいてアプリケーションをスケールすることが必要な場合には、スケール セット内の VM に [Linux 診断拡張機能](../articles/virtual-machines/linux/diagnostic-extension.md)と [Windows 診断拡張機能](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)のいずれかがインストールされている必要があります。 Azure Portal を使ってスケール セットを作成した場合には、ほかにも Azure PowerShell または Azure CLI を使用して、必要な診断構成を備えた拡張機能をインストールする必要があります。
 
### <a name="rules"></a>ルール

[ルール](../articles/monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md)とは、メトリックと実行するアクションを組み合わせたものを指します。 ルール条件が満たされると、1 つまたは複数の自動スケール アクションがトリガーされます。 たとえば、CPU の平均使用率が 85% を超えた場合に VM を 1 つ増やすというルールが考えられます。

![自動スケール アクション](./media/virtual-machines-autoscale/virtual-machines-autoscale-actions.png)
 
### <a name="notifications"></a>通知

[トリガーを設定](../articles/azure-monitor/platform/autoscale-webhook-email.md)し、作成した自動スケール ルールに基づいて特定の Web URL を呼び出したり、メールを送信したりすることができます。 Webhook を使用すると、後処理やカスタム通知のために、Azure アラート通知を他のシステムにルーティングすることができます。

## <a name="manually-scale-vms-in-a-scale-set"></a>スケール セット内の VM を手動でスケールする

### <a name="horizontal"></a>水平

スケール セットの容量を変更すると、VM を追加または削除できます。 Azure Portal では [スケーリング] 画面の [Override condition]\(オーバーライド条件\) バーを左右に動かすことによって、スケール セットの VM の数 (表示は **[インスタンス数]**) を調節できます。

Azure PowerShell を使用する場合には、[Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) を使ってスケール セット オブジェクトを取得する必要があります。 オブジェクトを取得したら **sku.capacity** プロパティに必要な VM の数を設定し、[Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) を使ってスケール セットを更新します。 Azure CLI を使用する場合には、**--new-capacity** パラメーターを使用して [az vmss scale](/cli/azure/vmss?view=azure-cli-latest#az-vmss-scale) コマンドを実行すると、容量を変更できます。

### <a name="vertical"></a>Vertical

Azure Portal では、スケール セットの [サイズ] 画面で VM のサイズを手動で変更できます。 Azure PowerShell の場合には、Get-AzVmss を使用し、イメージ参照 SKU のプロパティを設定したうえで、[Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) と [Update-AzVmssInstance](https://docs.microsoft.com/powershell/module/az.compute/update-azvmssinstance) を使用します。

## <a name="next-steps"></a>次の手順

- スケール セットの詳細については、「[スケール セットの設計上の考慮事項](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md)」を参照してください。

