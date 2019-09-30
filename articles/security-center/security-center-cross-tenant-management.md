---
title: Azure Security Center でのテナント間の管理 | Microsoft Docs
description: " Azure Security Center のデータ収集を有効にする方法について説明します。 "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: 178911390a4cb694171adf6c807369cab0c0499a
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202354"
---
# <a name="cross-tenant-management-in-security-center"></a>Security Center でのテナント間の管理

テナント間の管理を使用すると、[Azure の委任されたリソース管理](../lighthouse/concepts/azure-delegated-resource-management.md)を利用して、Security Center で複数のテナントのセキュリティ体制を表示および管理することができます。 各テナントのディレクトリにサインインすることなく、1 つのビューから複数のテナントを効率的に管理します。

- サービス プロバイダーは、独自のテナント内から、複数の顧客についてリソースのセキュリティ体制を管理できます。

- 複数のテナントを持つ組織のセキュリティ チームは、1 つの場所からセキュリティ体制を表示および管理できます。

## <a name="set-up-cross-tenant-management"></a>テナント間の管理を設定する

[Azure の委任されたリソース管理](../lighthouse/concepts/azure-delegated-resource-management.md)を使用して、管理対象テナントのリソースへのアクセスを独自のテナントに委任することによって、テナント間の管理を設定します。

> [!NOTE]
> Azure の委任されたリソース管理は、Azure Lighthouse の主要なコンポーネントの 1 つです。

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Security Center でのテナント間の管理のしくみ

複数のサブスクリプションを 1 つのテナントで管理するのと同じ方法で、複数のテナントにわたるサブスクリプションを確認し、管理することができます。

上部のメニューバーからフィルター アイコンをクリックし、表示するサブスクリプションを各テナントのディレクトリから選択します。

  ![テナントをフィルター処理する](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

ビューとアクションは基本的に同じです。 次に例をいくつか示します。

- **セキュリティ ポリシーの管理**:1 つのビューから、[ポリシー](tutorial-security-policy.md)を使用して多くのリソースのセキュリティ体制を管理し、セキュリティに関する推奨事項を使用してアクションを実行し、セキュリティに関連したデータを収集および管理します。
- **セキュリティ スコアとコンプライアンス体制の向上**:テナント間の可視性を使用すると、すべてのテナントの全体的なセキュリティ体制を表示でき、また、それぞれの[セキュリティ スコア](security-center-secure-score.md)と[コンプライアンス体制](security-center-compliance-dashboard.md)を最も改善できる場所と方法を確認できます。
- **推奨事項の修復**:さまざまなテナントからの多くのリソースの[推奨事項](security-center-recommendations.md)を一度に監視して修復します。 そして、全テナントにわたって最も高いリスクを示す脆弱性にすぐに対処できます。
- **アラートの管理**:さまざまなテナントにわたって[アラート](security-center-alerts-overview.md)を検出します。 実行可能な[修復の手順](security-center-managing-and-responding-alerts.md)に準拠していないリソースに対してアクションを実行します。

- **高度なクラウド防御機能などの管理**:[Just-in-Time (JIT) VM アクセス](security-center-just-in-time.md)、[アダプティブ ネットワークのセキュリティ強化機能](security-center-adaptive-network-hardening.md)、[適応型アプリケーション制御](security-center-adaptive-application.md)など、さまざまな脅威検出サービスや保護サービスを管理します。
 
## <a name="next-steps"></a>次の手順
この記事では、Security Center でのテナント間の管理のしくみについて説明します。 セキュリティ センターの詳細については、次を参照してください。

* 「[Azure Security Center を使用してセキュリティ体制を強化する](security-center-monitoring.md)」-- Azure リソースの正常性を監視する方法について説明しています。
* 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」-- このサービスの使用に関してよく寄せられる質問が記載されています。
