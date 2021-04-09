---
title: Azure Automation の新着情報
description: 毎月更新される Azure Automation に対する重要な更新。
ms.subservice: ''
ms.topic: overview
author: mgoedtel
ms.author: magoedte
ms.date: 02/23/2021
ms.custom: references_regions
ms.openlocfilehash: 899249c98c3ce0fdf061b1e689182f71c120aa13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729397"
---
# <a name="whats-new-in-azure-automation"></a>Azure Automation の新着情報

Azure Automation には継続的に改善が施されています。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

- 最新のリリース
- 既知の問題
- バグの修正

このページは毎月更新されるため、定期的にアクセスしてご確認ください。

## <a name="february-2021"></a>2021 年 2 月

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>西日本での GA を宣言された Automation と State Configuration のサポート

**種類:** 新機能

Automation アカウントと State Configuration が西日本リージョンで利用できるようになりました。 詳細については、[お知らせ](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/)を参照してください。

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>ハイブリッド worker で Runbook の実行を強制するためのカスタム Azure Policy コンプライアンスの導入

**種類:** 新機能

新しい Azure Policy コンプライアンス規則を使用すると、ハイブリッド worker グループでのみ実行されるジョブ、Webhook、ジョブ スケジュールを作成することができます。

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>米国東部、フランス中部、北ヨーロッパのリージョンで使用可能になった Update Management

**種類:** 新機能

Automation Update Management 機能を、米国東部、フランス中部、北ヨーロッパのリージョンで使用できるようになりました。 この変更を反映するドキュメントの最新版については、[サポートされるリージョン マッピング](how-to/region-mappings.md)に関するセクションを参照してください。

## <a name="january-2021"></a>2021 年 1 月

### <a name="support-for-automation-and-state-configuration-declared-ga-in-switzerland-west"></a>スイス西部での GA を宣言された Automation と State Configuration のサポート

**種類:** 新機能

Automation アカウントと State Configuration がスイス西部リージョンで利用できるようになりました。 詳細については、[お知らせ](https://azure.microsoft.com/updates/azure-automation-in-switzerland-west-region/)を参照してください。

### <a name="added-python-3-script-to-import-module-with-multiple-dependencies"></a>複数の依存関係を持つモジュールをインポートするための Python 3 スクリプトの追加

**種類:** 新機能

このスクリプトは、[GitHub リポジトリ](https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py)からダウンロードできます。 
 
### <a name="hybrid-runbook-worker-role-support-for-centos-8xrhel-8xsles-15"></a>Centos 8.x、RHEL 8.x、SLES 15 での Hybrid Runbook Worker ロールのサポート

**種類:** 新機能

Hybrid Runbook Worker 機能では、CentOS 8.x、REHL 8.x、SLES 15 の各ディストリビューションは、Hybrid Runbook Worker でのプロセスの自動化についてのみサポートされています。  これらの変更を反映するドキュメントの最新版については、[サポートされるオペレーティング システム](automation-linux-hrw-install.md#supported-linux-operating-systems)に関するセクションを参照してください。

### <a name="update-management--change-tracking-availability-in-australia-east-east-asia-west-us--central-us-regions"></a>オーストラリア東部、東アジア、米国西部、米国中部リージョンで使用できるようになった Update Management とChange Tracking

**種類:** 新機能

Automation アカウント、変更履歴とインベントリ、Update Management を、オーストラリア東部、東アジア、米国西部、米国中部の各リージョンで使用できるようになりました。 

### <a name="introduced-public-preview-of-python-3-runbooks-in-us-government-cloud"></a>US Government クラウドでの Python 3 Runbook のパブリック プレビューの導入

**種類:** 新機能 Azure Automation に、US Government クラウド リージョンでの Python 3 クラウドと Hybrid Runbook 実行のパブリック プレビュー サポートが導入されました。  詳しくは、[お知らせ](https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/)をご覧ください。

### <a name="azure-automation-runbooks-moved-from-technet-script-center-to-github"></a>Azure Automation Runbook を TechNet スクリプト センターから GitHub に移動

**種類:** 変更の計画

TechNet スクリプト センターの廃止に伴い、Runbook ギャラリーでホストされていたすべての Runbook が [Automation GitHub 組織](https://github.com/azureautomation)に移動されました。 詳細については、「[Azure Automation Runbook を GitHub に移動](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)」を参照してください。

## <a name="december-2020"></a>2020 年 12 月

### <a name="azure-automation-and-update-management-private-link-ga"></a>Azure Automation と Update Management Private Link GA

**種類:** 新機能

Azure グローバルおよび政府機関向けクラウドにおける Azure Automation と Update Management のサポートが GA としてアナウンスされました。 Azure Automation が Private Link に対応しました。Update Management を使用してマシンにパッチを適用したり、Webhook から Runbook を呼び出したり、State Configuration サービスを使用してマシンのコンプライアンスを維持したりすることで、ハイブリッド worker ロールでの Runbook の実行にセキュリティを確保することができます。 詳細については、[Azure Automation の Private Link サポート](https://azure.microsoft.com/updates/azure-automation-private-link)に関するページを参照してください。

### <a name="azure-automation-classified-as-grade-c-certified-on-accessibility"></a>Azure Automation がアクセシビリティでグレード C 認定として格付けされる

**種類:** 新機能

Microsoft 製品のアクセシビリティ機能は、グローバルなアクセシビリティ要件に対応できるよう行政機関を支援します。 [ブログのお知らせ](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/)ページで "**Azure Automation**" を検索し、Automation サービスのアクセシビリティ準拠に関するレポートをご覧ください。

### <a name="support-for-automation-and-state-configuration-ga-in-uae-north"></a>アラブ首長国連邦北部における Automation と State Configuration GA のサポート

**種類:** 新機能

Automation アカウントと State Configuration がアラブ首長国連邦北部リージョンで利用できるようになりました。 詳細については、[お知らせ](https://azure.microsoft.com/updates/azure-automation-in-uae-north-region/)を参照してください。

### <a name="support-for-automation-and-state-configuration-ga-in-germany-west-central"></a>ドイツ中西部における Automation と State Configuration GA のサポート

**種類:** 新機能

Automation アカウントと State Configuration がドイツ西部リージョンで利用できるようになりました。 詳細については、[お知らせ](https://azure.microsoft.com/updates/azure-automation-in-germany-west-central-region/)を参照してください。

### <a name="dsc-support-for-oracle-6-and-7"></a>Oracle 6 と Oracle 7 における DSC のサポート

**種類:** 新機能

Automation State Configuration を使用して Oracle Linux 6 マシンおよび Oracle Linux 7 マシンを管理できます。 これらの変更を反映するドキュメントの最新版については、[サポートされる Linux ディストリビューション](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)に関するセクションを参照してください。

### <a name="public-preview-for-python3-runbooks-in-automation"></a>Automation における Python3 Runbook のパブリック プレビュー

**種類:** 新機能

Azure Automation のパブリック プレビューで、Python 3 のクラウドおよびハイブリッド Runbook 実行が、Azure グローバル クラウドのすべてのリージョンでサポートされました。 詳細については、[お知らせ]((https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/) を参照してください。

## <a name="november-2020"></a>2020 年 11 月

### <a name="dsc-support-for-ubuntu-1804"></a>Ubuntu 18.04 の DSC サポート

**種類:** 新機能

これらの変更を反映するドキュメントの最新版については、[サポートされる Linux ディストリビューション](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)に関するセクションを参照してください。

## <a name="october-2020"></a>2020 年 10 月

### <a name="support-for-automation-and-state-configuration-ga-in-switzerland-north"></a>スイス北部における Automation と State Configuration GA のサポート

**種類:** 新機能

Automation アカウントと State Configuration がスイス北部で利用できるようになりました。 詳細については、[お知らせ](https://azure.microsoft.com/updates/azure-automation-in-switzerland-north-region/)を参照してください。

### <a name="support-for-automation-and-state-configuration-ga-in-brazil-south-east"></a>ブラジル南東部における Automation と State Configuration GA のサポート

**種類:** 新機能

Automation アカウントと State Configuration がブラジル南東部で利用できるようになりました。 詳細については、[お知らせ](https://azure.microsoft.com/updates/azure-automation-in-brazil-southeast-region/)を参照してください。

### <a name="update-management-availability-in-south-central-us"></a>米国中南部で Update Management が利用可能に

**種類:** 新機能

米国中南部リージョンで Update Management 機能をサポートするために、Azure Automation のリージョン マッピングを更新しました。 この変更を反映するドキュメントの最新版については、[サポートされるリージョン マッピング](how-to/region-mappings.md#supported-mappings)に関するセクションを参照してください。

## <a name="september-2020"></a>2020 年 9 月

### <a name="startstop-vms-during-off-hours-runbooks-updated-to-use-azure-az-modules"></a>Azure Az モジュールを使用するように Start/Stop VMs during off-hours Runbook を更新

**種類:** 新機能

Azure Resource Manager モジュールではなく Az モジュールを使用するように Start/Stop VM Runbook を更新しました。 これらの変更を反映するドキュメントの最新版については、「[Start/Stop VMs during off-hours の概要](automation-solution-vm-management.md)」を参照してください。

## <a name="august-2020"></a>2020 年 8 月

### <a name="published-the-dsc-extension-to-support-azure-arc"></a>Azure Arc をサポートする DSC 拡張機能を公開

**種類:** 新機能

Azure Automation State Configuration を使用して構成を一元的に保存し、Azure Arc 対応サーバーの DSC VM 拡張機能によって有効にされているハイブリッド接続マシンの望ましい状態を維持します。 詳細については、[Arc 対応サーバーの VM 拡張機能の概要](../azure-arc/servers/manage-vm-extensions.md)に関するページを参照してください。

### <a name="july-2020"></a>2020 年 7 月

### <a name="introduced-public-preview-of-private-link-support-in-automation"></a>Automation における Private Link サポートのパブリック プレビューを発表

**種類:** 新機能

Azure Private Link を使用すると、プライベート エンドポイントを使用して仮想ネットワークを Azure Automation に安全に接続することができます。 詳細については、[お知らせ](https://azure.microsoft.com/updates/public-preview-private-link-azure-automation-is-now-available/)を参照してください。

### <a name="hybrid-runbook-worker-support-for-windows-server-2008-r2"></a>Windows Server 2008 R2 に対する Hybrid Runbook Worker のサポート

**種類:** 新機能

Automation Hybrid Runbook Worker は、Windows Server 2008 R2 オペレーティング システムをサポートしています。 これらの変更を反映するドキュメントの最新版については、[サポートされるオペレーティング システム](automation-windows-hrw-install.md#supported-windows-operating-system)に関するセクションを参照してください。

### <a name="update-management-support-for-windows-server-2008-r2"></a>Windows Server 2008 R2 に対する Update Management のサポート

**種類:** 新機能

Update Management は、Windows Server 2008 R2 オペレーティング システムの評価とパッチ適用をサポートしています。 これらの変更を反映するドキュメントの最新版については、[サポートされるオペレーティング システム](update-management/overview.md#clients)に関するセクションを参照してください。

### <a name="automation-diagnostic-logs-schema-update"></a>Automation の診断ログのスキーマを更新

**種類:** 新機能

Log Analytics サービスにおける Azure Automation ログ データのスキーマを変更しました。 詳細については、「[Azure Monitor ログに Azure Automation のジョブ データを転送する](automation-manage-send-joblogs-log-analytics.md#filter-job-status-output-converted-into-a-json-object)」を参照してください。

### <a name="azure-lighthouse-supports-automation-update-management"></a>Azure Lighthouse が Automation Update Management をサポート

**種類:** 新機能

Update Management によるサービス プロバイダーおよび顧客のリソース管理を Azure Lighthouse で委任できるようになりました。 詳細については、[こちら](https://azure.microsoft.com/blog/how-azure-lighthouse-enables-management-at-scale-for-service-providers/)をご覧ください。

## <a name="june-2020"></a>2020 年 6 月

### <a name="automation-and-update-management-availability-in-the-us-gov-arizona-region"></a>Automation と Update Management が US Gov アリゾナ リージョンで利用可能に

**種類:** 新機能

Automation アカウントと Update Management が US Gov アリゾナで利用できるようになりました。 詳細については、[お知らせ](https://azure.microsoft.com/updates/azure-automation-generally-available-in-usgov-arizona-region/)を参照してください。

### <a name="hybrid-runbook-worker-onboarding-script-updated-to-use-az-modules"></a>Az モジュールを使用するように Hybrid Runbook Worker のオンボード スクリプトを更新

**種類:** 新機能

New-OnPremiseHybridWorker Runbook が更新されて Az モジュールに対応しました。 詳細については、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.7)のパッケージを参照してください。

### <a name="update-management-availability-in-china-east-2"></a>Update Management が中国東部 2 で利用可能に

**種類:** 新機能

中国東部 2 リージョンで Update Management 機能をサポートするために、Azure Automation のリージョン マッピングを更新しました。 この変更を反映するドキュメントの最新版については、[サポートされるリージョン マッピング](how-to/region-mappings.md#supported-mappings)に関するセクションを参照してください。

## <a name="may-2020"></a>2020 年 5 月

### <a name="updated-automation-service-dns-records-from-region-specific-to-automation-account-specific-urls"></a>Automation サービスの DNS レコードをリージョン固有の URL から Automation アカウント固有の URL に更新

**種類:** 新機能

Azure Automation の DNS レコードが更新されて Private Link に対応しました。 詳細については、[お知らせ](https://azure.microsoft.com/updates/azure-automation-updateddns-records/)を参照してください。

### <a name="added-capability-to-keep-automation-runbooks--dsc-scripts-encrypted-by-default"></a>Automation の Runbook と DSC スクリプトを既定で暗号化された状態に保つ機能を追加

**種類:** 新機能

Azure Automation のセキュリティを強化するために、資産のセキュリティが強化されるほか、Runbook と DSC スクリプトも暗号化されます。

## <a name="april-2020"></a>2020 年 4 月

### <a name="retirement-of-the-automation-watcher-task"></a>Automation Watcher タスクの廃止

**種類:** 変更の計画

イベントの監視、定期的なタスクのスケジュール設定、アクションのトリガーを行う方法として、今後は Azure Logic Apps を使用することが推奨され、サポートされます。 Watcher タスクの機能に対する投資は今後行われません。 詳細については、[Logic Apps を使用して定期的な自動化タスクをスケジュール設定および実行する方法](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)に関するページを参照してください。

## <a name="march-2020"></a>2020 年 3 月

### <a name="support-for-impact-level-5-il5-compute-isolation-in-azure-commercial-and-government-cloud"></a>Azure 商用クラウドおよび政府機関向けクラウドで影響レベル 5 (IL5) のコンピューティングの分離をサポート

**種類:**

Azure Government で Azure Automation Hybrid Runbook Worker を使用すると、影響レベル 5 のワークロードをサポートできます。 詳細については、Microsoft の[ドキュメント](automation-hybrid-runbook-worker.md#support-for-impact-level-5-il5)を参照してください。

## <a name="february-2020"></a>2020 年 2 月

### <a name="introduced-support-for-azure-virtual-network-service-tags"></a>Azure 仮想ネットワーク サービス タグを新たにサポート

**種類:** 新機能

Automation でサービス タグがサポートされ、特定のシナリオのサブセットを対象に Automation サービスのトラフィックを許可または拒否することができます。 詳細については、[このドキュメント](automation-hybrid-runbook-worker.md#service-tags)を参照してください。

### <a name="enable-tls-12-support-for-azure-automation-service"></a>Azure Automation サービスが TLS 1.2 をサポート

**種類:** 変更の計画

Azure Automation では、(Webhook、DSC ノード、ハイブリッド worker を介した) TLS 1.2 とすべてのクライアント呼び出しが完全にサポートされます。 TLS 1.2 がユーザーの間で標準的な位置付けとなり、その移行が十分に進むまでは、以前のクライアントとの下位互換性を確保するために、TLS 1.1 と TLS 1.0 は引き続きサポートされます。

## <a name="january-2020"></a>2020 年 1 月

### <a name="introduced-public-preview-of-customer-managed-keys-for-azure-automation"></a>Azure Automation のカスタマー マネージド キーのパブリック プレビューを発表

**種類:** 新機能

Azure Automation 資産の暗号化を、お客様が各自のマネージド キーを使用して管理および保護することができます。 詳細については、[カスタマー マネージド キーの使用](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)に関するセクションを参照してください。

### <a name="retirement-of-azure-service-management-asm-rest-apis-for-azure-automation"></a>Azure Automation 用の Azure Service Management (ASM) REST API の廃止

**種類:** 廃止

Azure Automation 用の Azure Service Management (ASM) REST API は、2020 年 1 月 30 日をもって廃止され、サポート終了となります。 詳細については、[お知らせ](https://azure.microsoft.com/updates/azure-automation-service-management-rest-apis-are-being-retired-april-30-2019/)を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Automation のドキュメント作成に協力していただける方は、[Docs 共同作成者ガイド](/contribute/)に関するページを参照してください。
