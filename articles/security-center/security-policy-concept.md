---
title: Azure Security Center のセキュリティ ポリシー、イニシアチブ、および推奨事項について
description: Azure Security Center のセキュリティ ポリシー、イニシアチブ、および推奨事項について説明します。
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 4dc29c8b52a3d0953445666672a716af013ee408
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176435"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>セキュリティ ポリシー、イニシアチブ、および推奨事項とは

Security Center は、サブスクリプションにセキュリティ イニシアチブを適用します。 これらのイニシアチブには、1 つ以上のセキュリティ ポリシーが含まれています。 各ポリシーが、セキュリティ対策を強化するための推奨事項となります。 このページでは、これらについて詳しく説明します。


## <a name="what-is-a-security-policy"></a>セキュリティ ポリシーとは何ですか。

Azure Policy で作成される Azure ポリシー定義は、制御する特定のセキュリティ条件に関するルールです。 組み込みの定義には、展開を許可するリソースの種類の制御や、すべてのリソースでのタグ使用の強制などが含まれます。 独自のカスタム ポリシー定義を作成することもできます。

これらのポリシー定義 (組み込み定義またはカスタム定義) を実装するには、それらを割り当てる必要があります。 こうしたポリシーを割り当てるには、Azure portal、PowerShell、または Azure CLI を使用します。

Azure Policy には、さまざまな種類のポリシーがあります。 Security Center は主に "監査" ポリシーを使用して、特定の条件と構成を確認した後、コンプライアンスに関するレポートを作成します。 セキュリティ設定を適用するために使用できる "強制" ポリシーもあります。

## <a name="what-is-a-security-initiative"></a>セキュリティ イニシアチブとは

Azure イニシアチブは、特定の目標や目的を実現するためにグループ化された Azure ポリシー定義またはルールのコレクションです。 Azure イニシアチブは、一連のポリシーを論理的にグループ化して単一の項目として扱うことで、ポリシーの管理を簡略化します。

セキュリティ イニシアチブは、ワークロードの必要な構成を定義し、会社や規制当局のセキュリティ要件に確実に準拠できるようにします。

Security Center のイニシアチブは、セキュリティ ポリシーと同じく Azure Policy で作成されます。 [Azure Policy](../governance/policy/overview.md) を使用すると、ポリシーを管理し、イニシアチブを構築し、複数のサブスクリプションまたは管理グループ全体に対してイニシアチブを割り当てることができます。

Azure Security Center のすべてのサブスクリプションに自動的に割り当てられる既定のイニシアチブを Azure セキュリティ ベンチマークと呼びます。 このベンチマークは Microsoft が作成したもので、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Azure 固有のガイドラインのセットです。 この広く遵守されているベンチマークは、クラウド中心のセキュリティに重点を置いた [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) と [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) の統制に基づいています。 詳細については、[Azure セキュリティ ベンチマーク](../security/benchmarks/introduction.md)に関する記事を参照してください。

Security Center では、セキュリティのイニシアチブとポリシーを操作するための次のオプションが提供されます。

- **組み込みの既定のイニシアチブの表示と編集** - Security Center を有効にすると、"Azure セキュリティ ベンチマーク" という名前のイニシアチブが、Security Center のすべての登録済みサブスクリプションに自動的に割り当てられます。 このイニシアチブをカスタマイズするには、ポリシーのパラメーターを編集して、イニシアチブ内の個々のポリシーを有効または無効にします。 [組み込みのセキュリティ ポリシー](./policy-reference.md)の一覧を参照して、すぐに使用できるオプションを確認してください。

- **独自のカスタム イニシアチブの追加** - サブスクリプションに適用されているセキュリティ イニシアチブをカスタマイズする場合は、Security Center で行うことができます。 作成したポリシーにマシンが従っていない場合は、推奨事項が提供されます。 カスタム ポリシーを作成して割り当てる手順については、[カスタム セキュリティ イニシアチブの使用](custom-security-policies.md)に関する記事をご覧ください。

- **イニシアチブとしてのコンプライアンス標準の追加** - Security Center の規制コンプライアンス ダッシュボードには、特定の標準または規制 (Azure CIS、NIST SP 800-53 R4、SWIFT CSP CSCF-v2020 など) のコンテキストにおける環境内のすべての評価の状態が表示されます。 詳細については、[規制コンプライアンスの向上](security-center-compliance-dashboard.md)に関する記事をご覧ください。

## <a name="what-is-a-security-recommendation"></a>セキュリティに関する推奨事項とは

Security Center は、ポリシーを使用して、リソースのコンプライアンス状態を定期的に分析し、潜在的なセキュリティ構成の誤りや弱点を特定します。 その後、これらの問題を修正する方法に関する推奨事項が提供されます。 推奨事項は、関連するポリシーに対してリソースを評価し、定義された要件を満たしていないリソースを特定した結果です。

Security Center は、選択したイニシアチブに基づいてセキュリティに関する推奨事項を作成します。 イニシアチブからのポリシーがリソースと比較され、対応していないものが見つかった場合は、Security Center に推奨事項として表示されます。

推奨事項とは、リソースをセキュリティで保護および強化するために実行する操作です。 各推奨事項には、次の情報が記載されています。

- 問題の簡単な説明
- 推奨事項を実装するために実行する修復手順
- 影響を受けるリソース

実際には、次のように動作します。

1. Azure セキュリティ ベンチマークは 1 つの ***イニシアチブ*** です。
1. これには、攻撃面を減らすため、すべての Azure Storage アカウントにネットワーク アクセス制限を要求する ***ポリシー*** が含まれています。 これは、"ストレージ アカウントは、仮想ネットワーク規則を使用してネットワーク アクセスを制限する必要がある" というポリシーで、Azure Policy で有効または無効にできます。
1. Azure Security Center は、保護されたサブスクリプションで Azure Storage アカウントを検出すると、仮想ネットワーク規則で保護されているかどうかについてそれらのアカウントを評価します。 そうでない場合は、その状況を修正し、それらのリソースのセキュリティを強化するための ***推奨事項*** が示されます。 

つまり、イニシアチブ (1) にはポリシーが含まれ (2)、必要に応じて推奨事項が生成されます (3)。 

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>推奨事項とポリシーの関係を表示する

前述のように、Security Center に組み込まれている推奨事項は、Azure セキュリティ ベンチマークに基づいています。 ほとんどすべての推奨事項には、ベンチマークの要件から派生した基になるポリシーがあります。

推奨事項の詳細を確認するときに、基になるポリシーを確認できると有益な場合が多くあります。 ポリシーでサポートされているすべての推奨事項について、推奨事項の詳細ページの **[ポリシー定義の表示]** リンクを使用して、関連するポリシーの Azure Policy エントリに直接アクセスします。

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="推奨事項をサポートする特定のポリシーの Azure Policy ページへのリンク":::

ポリシー定義を表示し、評価ロジックを確認するには、このリンクを使用します。 

[セキュリティの推奨事項に関するリファレンス ガイド](recommendations-reference.md)に記載されている推奨事項の一覧を確認している場合も、ポリシー定義のページへのリンクが表示されます。

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Azure Security Center 推奨事項のリファレンス ページから、特定のポリシーに関する Azure Policy ページへの直接アクセス":::


## <a name="next-steps"></a>次のステップ

このページでは、ポリシー、イニシアチブ、および推奨事項の基本的な概念と関係に関する概要を説明しています。 関連情報については、以下をご覧ください。

- [カスタム イニシアチブを作成する](custom-security-policies.md)
- [セキュリティ ポリシーを無効にして推奨事項を無効にする](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Azure Policy でセキュリティ ポリシーを編集する方法を確認する](../governance/policy/tutorials/create-and-manage.md)