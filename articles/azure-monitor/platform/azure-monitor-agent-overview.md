---
title: Azure Monitor エージェントの概要
description: 仮想マシンのゲスト オペレーティング システムから監視データを収集する Azure Monitor エージェント (AMA) の概要。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: e38d59ff1eb31dd5fc3ecf6b7df6b12504141d5e
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88082850"
---
# <a name="azure-monitor-agent-overview-preview"></a>Azure Monitor エージェントの概要 (プレビュー)
Azure Monitor エージェント (AMA) では、仮想マシンのゲスト オペレーティング システムから監視データが収集され、それが Azure Monitor に配信されます。 この記事では、Azure Monitor エージェントのインストール方法やデータ収集の構成方法など、Azure Monitor エージェントの概要について説明します。

## <a name="relationship-to-other-agents"></a>他のエージェントとの関係
Azure Monitor エージェントでは、Azure Monitor によって現在使用されている次のエージェントが置き換えられ、仮想マシンからゲスト データが収集されます。

- [Log Analytics エージェント](log-analytics-agent.md) - データが Log Analytics ワークスペースに送信され、Azure Monitor for VMs および監視ソリューションがサポートされます。
- [Diagnostics 拡張機能](diagnostics-extension-overview.md) - Azure Monitor メトリック (Windows のみ)、Azure Event Hubs、および Azure Storage にデータが送信されます。
- [Telegraf エージェント](collect-custom-metrics-linux-telegraf.md) - Azure Monitor メトリックにデータが送信されます (Linux のみ)。

Azure Monitor エージェントでは、この機能が 1 つのエージェントに統合されるだけでなく、既存のエージェントに勝る次の利点があります。

- 監視の範囲。 さまざまな VM セットからのさまざまなデータ セットのコレクションが一元的に構成されます。  
- Linux マルチホーム:Linux VM から複数のワークスペースにデータが送信されます。
- Windows イベント フィルター:XPATH クエリを使用して、収集される Windows イベントがフィルター処理されます。
- 拡張機能管理の強化:Azure Monitor エージェントでは、現在の Log Analytics エージェントの管理パックや Linux プラグインよりも透過的で制御可能な、拡張性を処理する新しい方法を使用しています。

### <a name="changes-in-data-collection"></a>データ収集の変更
既存のエージェントのデータ収集を定義する方法は、それぞれ明確に異なっており、それぞれ Azure Monitor エージェントによって対処される課題を抱えています。

- Log Analytics エージェントでは、Log Analytics ワークスペースからその構成が取得されます。 これを一元的に構成することは簡単ですが、異なる仮想マシンに個別の定義を定義するのは困難です。 データは、Log Analytics ワークスペースにのみ送信できます。

- 診断拡張機能では、仮想マシンごとに構成を指定します。 これにより、異なる仮想マシンに個別の定義を簡単に定義できますが、一元的に管理することは困難です。 Azure Monitor メトリック、Azure Event Hubs、または Azure Storage にのみデータを送信できます。 Linux エージェントの場合、Azure Monitor メトリックにデータを送信するために、オープン ソースの Telegraf エージェントが必要です。

Azure Monitor エージェントでは、[データ収集ルール (DCR)](data-collection-rule-overview.md) を使用して、各エージェントから収集するデータが構成されます。 データ収集ルールにより、大規模な収集設定の管理が可能になる一方、コンピューターのサブセットの一意の範囲指定された構成も可能になります。 それらはワークスペースから独立し、仮想マシンからも独立しているため、一度定義すると、コンピューターや環境間で再利用できます。 「[Azure Monitor エージェント用のデータ収集の構成 (プレビュー)](data-collection-rule-azure-monitor-agent.md)」をご覧ください。



## <a name="current-limitations"></a>現在の制限
Azure Monitor エージェントのパブリック プレビュー中は、次の制限事項が適用されます。

- Azure Monitor エージェントでは、Azure Monitor for VMs や Azure Security Center などのソリューションと分析情報がサポートされません。 現在サポートされているシナリオは、構成したデータ収集ルールを使用してデータを収集することだけです。 
- データ収集ルールは、収集先として使用する Log Analytics ワークスペースと同じリージョンに作成する必要があります。
- 現在、Azure 仮想マシンのみがサポートされています。 オンプレミスの仮想マシン、仮想マシン スケール セット、Arc for Servers、Azure Kubernetes Service、その他のコンピューティング リソースの種類は現在サポートされていません。
- 仮想マシンは、次の HTTPS エンドポイントにアクセスできる必要があります。
  - *.ods.opinsights.azure.com
  - *.ingest.monitor.azure.com
  - *.control.monitor.azure.com


## <a name="coexistence-with-other-agents"></a>他のエージェントとの共存
Azure Monitor エージェントは、既存のエージェントと共存させることができるため、評価または移行中にそれらの既存の機能を使用し続けることができます。 これは、既存のソリューションのサポートにおけるパブリック プレビューの制限のため、特に重要です。 ただし、重複データの収集では注意する必要があります。これにより、クエリ結果にずれが生じ、データ インジェストと保持に追加料金が発生する可能性があるためです。

たとえば、Azure Monitor for VMs では、Log Analytics エージェントを使用して、パフォーマンス データが Log Analytics ワークスペースに送信されます。 また、エージェントから Windows イベントと Syslog イベントを収集するようにワークスペースを構成している場合もあります。 Azure Monitor エージェントをインストールし、これらの同じイベントとパフォーマンス データに対してデータ収集ルールを作成した場合、データが重複することになります。


## <a name="costs"></a>コスト
Azure Monitor エージェントには料金はかかりませんが、取り込まれたデータの料金が発生する場合があります。 Log Analytics のデータ収集と保持の詳細についてと顧客メトリックについては、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。

## <a name="data-sources-and-destinations"></a>データ ソースと収集先
次の表に、現在 Azure Monitor エージェントでデータ収集ルールを使用して収集できるデータの種類と、そのデータを送信できる場所を示します。 分析情報、ソリューション、および Azure Monitor エージェントを使用して他の種類のデータを収集するその他のソリューションのリストについては、「[Azure Monitor で何が監視されていますか?](../monitor-reference.md)」 を参照してください。


Azure Monitor エージェントでは、Azure Monitor メトリック、または Azure Monitor ログをサポートする Log Analytics ワークスペースにデータが送信されます。

| Data Source | 変換先 | Description |
|:---|:---|:---|
| パフォーマンス        | Azure Monitor メトリック<br>Log Analytics ワークスペース | オペレーティング システムとワークロードのさまざまな側面のパフォーマンスを測定する数値。 |
| Windows イベント ログ | Log Analytics ワークスペース | Windows イベント ログ システムに送信された情報。 |
| syslog             | Log Analytics ワークスペース | Linux イベント ログ システムに送信される情報。 |


## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム
Azure Monitor エージェントでは、現在次のオペレーティング システムがサポートされています。

### <a name="windows"></a>Windows 
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012
  - Windows Server 2012 R2

### <a name="linux"></a>Linux
  - CentOS 6<sup>1</sup>、7
  - Debian 9、10
  - Oracle Linux 6<sup>1</sup>、7
  - RHEL 6<sup>1</sup>、7、8
  - SLES 11、12、15
  - Ubuntu 14.04 LTS、16.04 LTS、18.04 LTS

> [!IMPORTANT]
> <sup>1</sup>これらのディストリビューションで Syslog データを送信するには、rsyslog を削除し、syslog-ng をインストールする必要があります。


## <a name="security"></a>セキュリティ
Azure Monitor エージェントにはキーは必要ありませんが、代わりに[システム割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity) が必要です。 エージェントをデプロイする前に、各仮想マシンで、システム割り当てマネージド ID を有効にしている必要があります。


## <a name="install-the-azure-monitor-agent"></a>Azure Monitor エージェントのインストール
Azure Monitor エージェントは、次の表に示す詳細で [Azure VM 拡張機能](../../virtual-machines/extensions/overview.md)として実装されます。 

| プロパティ | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft.Azure.Monitor  | Microsoft.Azure.Monitor |
| 型      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 0.9 |

PowerShell または CLI を使用して、次のような仮想マシン エージェントをインストールするには、いずれかの方法を使用して Azure Monitor エージェントをインストールします。 または、ポータルを使用して、Azure サブスクリプションの仮想マシンでエージェントをインストールし、データ収集を構成できます。この手順については、「[Azure Monitor エージェント用のデータ収集の構成 (プレビュー)](data-collection-rule-azure-monitor-agent.md#create-using-the-azure-portal)」を参照してください。

### <a name="windows"></a>Windows

# <a name="cli"></a>[CLI](#tab/CLI1)

```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --version 1.0 --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell1)

```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -Version 1.0 -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---


### <a name="linux"></a>Linux

# <a name="cli"></a>[CLI](#tab/CLI2)

```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --version 0.9 --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell2)

```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -Version 0.9 -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---

## <a name="next-steps"></a>次のステップ

- [データ収集ルールを作成し](data-collection-rule-azure-monitor-agent.md)、エージェントからデータを収集して Azure Monitor に送信します。
