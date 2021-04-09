---
title: Azure Automation でのネットワーク構成の詳細
description: この記事では、Azure Automation State Configuration、Azure Automation Hybrid Runbook Worker、Update Management、および変更履歴とインベントリに必要なネットワーク情報の詳細について説明します
ms.author: magoedte
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 36331e9c07926d4d3ffff136aefa2f9a77d47cb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708885"
---
# <a name="azure-automation-network-configuration-details"></a>Azure Automation でのネットワーク構成の詳細

このページでは、[Hybrid Runbook Worker と State Configuration](#hybrid-runbook-worker-and-state-configuration)、および [Update Management と変更履歴とインベントリ](#update-management-and-change-tracking-and-inventory)に必要なネットワークの詳細について説明します。

## <a name="hybrid-runbook-worker-and-state-configuration"></a>Hybrid Runbook Worker と State Configuration

次のポートと URL は、Hybrid Runbook Worker、および [Automation State Configuration](automation-dsc-overview.md) が Azure Automation と通信するために必要です。

* ポート: 送信インターネット アクセスには 443 のみが必要です
* グローバル URL: `*.azure-automation.net`
* US Gov バージニアのグローバル URL: `*.azure-automation.us`
* エージェント サービス: `https://<workspaceId>.agentsvc.azure-automation.net`

### <a name="network-planning-for-hybrid-runbook-worker"></a>Hybrid Runbook Worker のネットワーク計画

システムまたはユーザー Hybrid Runbook Worker が Azure Automation に接続して登録するには、このセクションで説明されているポート番号と URL へのアクセスが必要です。 worker には、Azure Monitor Log Analytics ワークスペースに接続するために、[Log Analytics エージェントに必要なポートと URL](../azure-monitor/agents/agent-windows.md) へのアクセスも必要です。

特定のリージョンに対して定義された Automation アカウントがある場合は、そのリージョン データセンターへの Hybrid Runbook Worker の通信を制限できます。 必要な DNS レコードについては、[Azure Automation によって使用される DNS レコード](how-to/automation-region-dns-records.md)に関するページを参照してください。

### <a name="configuration-of-private-networks-for-state-configuration"></a>State Configuration 用のプライベート ネットワークの構成

ノードがプライベート ネットワークに配置されている場合は、上に定義されているポートと URL が必要です。 これらのリソースにより、マネージド ノードに対するネットワーク接続が提供され、DSC で Azure Automation と通信できます。

ノード間で通信する DSC リソース ([WaitFor * リソース](/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)など) を使用している場合、ノード間のトラフィックを許可する必要もあります。 これらのネットワーク要件を理解するには、各 DSC リソースのドキュメントを参照してください。

TLS 1.2 のクライアント要件を理解するには、「[Azure Automation に対する TLS 1.2 の強制](automation-managing-data.md#tls-12-enforcement-for-azure-automation)」のセクションを参照してください。

## <a name="update-management-and-change-tracking-and-inventory"></a>Update Management および変更履歴とインベントリ

この表に記載されているアドレスは、Update Management および変更履歴とインベントリの両方に必要です。 この表の後の段落も両方に適用されます。

これらのアドレスへの通信には、**ポート 443** が使用されます。

|Azure Public  |Azure Government  |
|---------|---------|
|\*.ods.opinsights.azure.com    | \*.ods.opinsights.azure.us         |
|\*.oms.opinsights.azure.com     | \*.oms.opinsights.azure.us        |
|\*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net|
|\*.azure-automation.net | \*.azure-automation.us|

ネットワーク グループのセキュリティ規則を作成する場合、または Automation サービスと Log Analytics ワークスペースへのトラフィックを許可するように Azure Firewall を構成する場合は、[サービス タグ](../virtual-network/service-tags-overview.md#available-service-tags)の **GuestAndHybridManagement** と **AzureMonitor** を使用します。 これにより、ネットワーク セキュリティ規則の継続的な管理が簡単になります。 Azure VM から安全かつプライベートに Automation サービスに接続するには、[Azure Private Link の使用](./how-to/private-link-security.md)に関するページを確認してください。 現在のサービス タグと範囲情報を、オンプレミスのファイアウォール構成の一部として取得して含めるには、[ダウンロード可能な JSON ファイル](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [Automation の Update Management の概要](update-management\overview.md)について説明します。
* [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) について説明します。
* [変更履歴とインベントリ](change-tracking\overview.md)について説明します。
* [Automation State Configuration](automation-dsc-overview.md) について説明します。