---
title: Azure Policy 用の Azure セキュリティ ベースライン
description: Azure Policy セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: azure-policy
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9916ad6d3b6cb1a63d34004915666226b7836490
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740353"
---
# <a name="azure-security-baseline-for-azure-policy"></a>Azure Policy 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインは、[Azure セキュリティ ベンチマーク](../../../security/benchmarks/overview.md)のガイダンスを Azure Policy に適用します。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、**コンプライアンス ドメイン** および **セキュリティ制御** によってグループ化されています。これらは、Azure セキュリティ ベンチマークと、Azure Policy に適用できる関連ガイダンスによって定義されています。 Azure Policy に適用できない **制御** は、除外されています。 Azure Policy を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure Policy セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

組み込みイニシアチブによって Azure セキュリティ ベンチマーク制御を組み込みのポリシー定義にマッピングする方法については、[規制コンプライアンスの Azure セキュリティ ベンチマーク](../samples/azure-security-benchmark.md)に関するページを参照してください。

Azure Policy では、"_責任_" の代わりに "_所有権_" という用語を使用します。 "_所有権_" の詳細については、[Azure Policy のポリシー定義](./definition-structure.md#type)に関するセクションと、「[クラウドにおける共同責任](../../../security/fundamentals/shared-responsibility.md)」を参照してください。

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../../../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**:Azure Policy では、アクティビティ ログ (自動的に有効になります) を使用して、イベント ソース、日付、ユーザー、タイムスタンプ、送信元アドレス、送信先アドレス、その他の役立つ要素が記録されます。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](/azure/azure-monitor/platform/diagnostic-settings)

- [Azure でのログ記録とログのさまざまな種類について](/azure/azure-monitor/platform/platform-logs-overview)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../../../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。 また、[Azure Active Directory (Azure AD) Privileged Identity Management](../../../active-directory/privileged-identity-management/pim-configure.md) の特権ロールまたは [Azure Resource Manager](../../../azure-resource-manager/management/overview.md) を使用して、Just-In-Time または Just-Enough-Access を有効にすることもできます。

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.GuestConfiguration**:

[!INCLUDE [Resource Policy for Microsoft.GuestConfiguration 3.3](../../../../includes/policy/standards/asb/rp-controls/microsoft.guestconfiguration-3-3.md)]

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: セキュリティで保護された Azure マネージド ワークステーションを管理タスクに使用する

**ガイダンス**:多要素認証が構成された特権アクセス ワークステーション (PAW) を使用して Azure リソースにログインし、そのリソースを構成します。

- [特権アクセス ワークステーションについて](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure で多要素認証を有効にする方法](../../../active-directory/authentication/howto-mfa-getstarted.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../../../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する 

**ガイダンス**: Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure Policy へのアクセスを制御します。

- [Azure Policy における Azure RBAC アクセス許可](https://docs.microsoft.com/azure/governance/policy/overview#azure-rbac-permissions-in-azure-policy)

- [Azure RBAC を構成する方法](../../../role-based-access-control/role-assignments-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure Monitor とアクティビティ ログを使用して、Azure Policy が変更された場合のアラートを作成します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](/azure/azure-monitor/platform/alerts-activity-log)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../../../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。 Azure Policy の "_変更_" 効果を使用して、コンプライアンスおよび一貫性のあるタグ ガバナンスについてレポートし、これらを適用します。

- [チュートリアル:ポリシーを作成および管理する](../tutorials/create-and-manage.md)

- [チュートリアル:タグ ガバナンスを管理する](../tutorials/govern-tags.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**: 組織のニーズに応じて、承認されたポリシー定義およびポリシー割り当てのインベントリを作成します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。

- [Azure Policy を構成して管理する方法](../tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](/azure/security/benchmarks/overview)」を参照してください。
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
