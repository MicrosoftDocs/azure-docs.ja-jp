---
title: Azure Resource Graph 用の Azure セキュリティ ベースライン
description: Azure Resource Graph のセキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e5e01c8d1ac16e5e8be405660a0726796789e645
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738975"
---
# <a name="azure-security-baseline-for-azure-resource-graph"></a>Azure Resource Graph 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインでは、[Azure セキュリティ ベンチマーク バージョン 1.0](../../../security/benchmarks/overview-v1.md) のガイダンスが Azure Resource Graph に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。
内容は、**セキュリティ制御** によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Azure Resource Graph に適用できる関連ガイダンスによって定義されています。 Azure Resource Graph に適用できない **制御** は、除外されています。

 
Azure Resource Graph を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure Resource Graph セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../../../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Resource Graph では、Azure ロールベースのアクセス制御 (Azure RBAC) に基づいて、リソースの種類とプロパティへのアクセスが提供されます。 セキュリティ プリンシパル (ユーザー、グループ、およびサービス アカウント) に付与されたアクセス権を定期的に監査して確認し、クエリが適切なリソースの結果を返すようにします。

- [Azure Resource Graph でのアクセス許可](https://docs.microsoft.com/azure/governance/resource-graph/overview#permissions-in-azure-resource-graph)

- [Azure ID アクセス レビューの使用方法](../../../active-directory/governance/access-reviews-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../../../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する 

**ガイダンス**: Azure RBAC を使用して、データとリソースへのアクセスを制御します。 Azure Resource Graph を使用するには、クエリ対象のリソースに対する適切なアクセス権も必要です。 このアクセスは、読み取り専用にする必要があり、必要な担当者にのみ付与する必要があります。

- [Azure Resource Graph でのアクセス許可](https://docs.microsoft.com/azure/governance/resource-graph/overview#permissions-in-azure-resource-graph)

- [Azure RBAC を構成する方法](../../../role-based-access-control/role-assignments-rest.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../../../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション、管理グループ、テナント内のすべてのサポートされているリソースのクエリや検出を行います。 テナント内の適切なアクセス許可を持っていること、およびサブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

- [Azure Resource Graph を使用してクエリを作成する方法](../first-query-portal.md)

- [Azure RBAC について](../../../role-based-access-control/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**: 組織のニーズに応じて、承認された Azure リソースとコンピューティング リソース用に承認されたソフトウェアのインベントリを作成します。 Azure Resource Graph を使用して、承認された Azure リソースを照会し、変更履歴 (プレビュー) を使用してスナップショットを確認し、変更内容を確認します。

- [Azure Resource Graph を使用して Azure リソースにクエリを実行する](../first-query-portal.md)

- [リソースの変更の取得](../how-to/get-resource-changes.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション、管理グループ、テナントのリソースのクエリや検出を行います。 環境内のすべての Azure リソースが承認されていることを確認します。

- [Azure Resource Graph を使用して Azure リソースにクエリを実行する](../first-query-portal.md)

- [サンプル:Azure Resource Graph の初歩的なクエリ](../samples/starter.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](/azure/security/benchmarks/overview)」を参照してください。
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
