---
title: Azure セキュリティ ベンチマーク用の Azure Resource Graph のセキュリティ ベースライン
description: Azure Resource Graph のセキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d2ef76a054642807f0d72a758ae084a19557caf8
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009003"
---
# <a name="azure-resource-graph-security-baseline-for-azure-security-benchmark"></a>Azure セキュリティ ベンチマーク用の Azure Resource Graph のセキュリティ ベースライン

このセキュリティ ベースラインは、[Azure セキュリティ ベンチマーク](../../../security/benchmarks/overview.md)のガイダンスを Azure Resource Graph に適用します。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、**セキュリティ制御**によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Azure Resource Graph に適用できる関連ガイダンスによって定義されています。 Azure Resource Graph に適用できない**制御**は、除外されています。 Azure Resource Graph を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure Virtual Network セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。



## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](../../../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Resource Graph は、Azure ロールベースのアクセス制御 (Azure RBAC) に基づいて、リソースの種類とプロパティへのアクセスを提供します。 セキュリティ プリンシパル (ユーザー、グループ、およびサービス アカウント) に付与されたアクセス権を定期的に監査して確認し、クエリが適切なリソースの結果を返すようにします。

* [Azure Resource Graph でのアクセス許可](../overview.md#permissions-in-azure-resource-graph)

* [Azure ID アクセス レビューの使用方法](../../../active-directory/governance/access-reviews-overview.md)


**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](../../../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6:ロールベースのアクセス制御を使用してリソースへのアクセスを制御する

**ガイダンス**: ロールベースのアクセス制御 (RBAC) を使用して、データとリソースへのアクセスを制御します。 Azure Resource Graph を使用するには、クエリ対象のリソースに対する適切なアクセス権も必要です。 このアクセスは、読み取り専用にする必要があり、必要な担当者にのみ付与する必要があります。

* [Azure Resource Graph でのアクセス許可](../overview.md#permissions-in-azure-resource-graph)

* [Azure で RBAC を構成する方法](../../../role-based-access-control/role-assignments-rest.md)


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](../../../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション、管理グループ、テナント内のすべてのサポートされているリソースのクエリや検出を行います。 テナント内の適切なアクセス許可を持っていること、およびサブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

* [Azure Resource Graph を使用してクエリを作成する方法](../first-query-portal.md)

* [Azure RBAC について](../../../role-based-access-control/overview.md)


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**: 組織のニーズに応じて、承認された Azure リソースとコンピューティング リソース用に承認されたソフトウェアのインベントリを作成します。 Azure Resource Graph を使用して、承認された Azure リソースを照会し、変更履歴 (プレビュー) を使用してスナップショットを確認し、変更内容を確認します。

* [Azure Resource Graph を使用して Azure リソースにクエリを実行する](../first-query-portal.md)

* [リソースの変更の取得](../how-to/get-resource-changes.md)


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション、管理グループ、テナントのリソースのクエリや検出を行います。 環境内のすべての Azure リソースが承認されていることを確認します。

* [Azure Resource Graph を使用して Azure リソースにクエリを実行する](../first-query-portal.md)

* [サンプル:Azure Resource Graph の初歩的なクエリ](../samples/starter.md)


**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](../../../security/benchmarks/overview.md)に関するページを参照する
- [Azure セキュリティ ベースライン](../../../security/benchmarks/security-baselines-overview.md)の詳細について学習する
