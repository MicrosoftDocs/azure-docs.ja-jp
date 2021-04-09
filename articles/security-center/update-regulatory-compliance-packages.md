---
title: Azure Security Center の規制コンプライアンス ダッシュボードの使用
description: Security Center の規制コンプライアンス ダッシュボードで規制基準を追加および削除する方法について説明します
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 768f686889663d9b1af4b88d84b361ac9460a5a0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381734"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>規制コンプライアンス ダッシュボードで標準セットをカスタイマイズする

Azure Security Center では、リソースの構成が業界標準、規制、ベンチマークの要件と継続的に比較されます。 **規制コンプライアンス ダッシュボード** では、特定のコンプライアンス要件をどのように満たしているかに基づいて、コンプライアンス体制に関する分析情報が提供されます。


## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>Security Center では、規制コンプライアンス標準はどのように表されますか?

Security Center の規制コンプライアンス ダッシュボードでは、業界標準、規制基準、ベンチマークが表されます。 各標準は、Azure Policy で定義されたイニシアチブです。

評価としてマップされたコンプライアンス データをダッシュボードに表示するには、**Security Policy** ページ内から管理グループまたはサブスクリプションにコンプライアンス標準を追加します。 Azure Policy とイニシアチブの詳細については、「[セキュリティ ポリシーの操作](tutorial-security-policy.md)」を参照してください。

標準またはベンチマークを選択したスコープに割り当てた場合、評価としてマップされた関連するすべてのコンプライアンス データと共に、標準が規制コンプライアンス ダッシュボードに表示されます。 割り当てられている標準の概要レポートをダウンロードすることもできます。

Microsoft では規制基準自体を追跡し、一部のパッケージの適用範囲を経時的かつ自動的に改善しています。 Microsoft がイニシアティブの新しいコンテンツをリリースすると、そのコンテンツは、標準のコントロールにマップされた新しいポリシーとしてダッシュボードに自動的に表示されます。


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>Security Center では、どのような規制コンプライアンス標準を使用できますか?

既定で、すべてのサブスクリプションに **Azure セキュリティ ベンチマーク** が割り当てられています。 これは、Microsoft が作成した、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Azure 固有のガイドラインです。 [Azure セキュリティ ベンチマークの詳細を確認してください](../security/benchmarks/introduction.md)。

次のような標準も追加できます。

- NIST SP 800-53 R4
- SWIFT CSP CSCF-v2020
- UK OFFICIAL および UK NHS
- カナダ連邦の PBMM
- Azure CIS 1.1.0

標準は、使用可能になるとダッシュボードに追加されます。


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>規制基準をダッシュボードへ追加する

次の手順は、サポートされている規制基準のいずれかへの準拠を監視するためのパッケージを追加する方法を示しています。

> [!NOTE]
> ダッシュボードに標準を追加するには、サブスクリプションで Azure Defender が有効になっている必要があります。 また、所有者またはポリシーの共同作成者であるユーザーのみが、コンプライアンス標準を追加するために必要なアクセス許可を持っています。 

1. Security Center のサイドバーで、 **[規制コンプライアンス]** を選択して、規制コンプライアンス ダッシュボードを開きます。 ここで、現在選択されているサブスクリプションに割り当てられているコンプライアンス標準を確認できます。   

1. ページの上部から、 **[コンプライアンス ポリシーの管理]** を選択します。 [ポリシー管理] ページが表示されます。

1. 規制コンプライアンス体制を管理するサブスクリプションまたは管理グループを選択します。 

    > [!TIP]
    > 入れ子になったすべてのリソースのコンプライアンス データが集計され、追跡されるように、標準の適用対象として最上位のスコープを選択することをお勧めします。 

1. 組織に関連する標準を追加するには、 **[標準をさらに追加]** をクリックします。 

1. **[規制コンプライアンス標準の追加]** ページで、以下を含む使用可能な標準を検索できます。

    - **NIST SP 800-53 R4**
    - **NIST SP 800 171 R2**
    - **SWIFT CSP CSCF v2020**
    - **UKO および UK NHS**
    - **カナダ連邦の PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.1.0**
    
    ![Azure Security Center の規制コンプライアンス ダッシュボードへの規制基準の追加](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. **[追加]** を選択し、スコープ、パラメーター、修復など、特定のイニシアチブに関して必要な詳細をすべて入力します。

1. Security Center のサイドバーで、再度 **[規制コンプライアンス]** を選択して、規制コンプライアンス ダッシュボードに戻ります。

    業界標準および規制基準の一覧に新しい標準が表示されます。 

    > [!NOTE]
    > 新しく追加された標準がコンプライアンス ダッシュボードに表示されるまでに数時間かかることがあります。

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="規制コンプライアンス ダッシュボード" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

## <a name="remove-a-standard-from-your-dashboard"></a>ダッシュボードから標準を削除する

提供されている規制基準に組織に関連していないものが含まれている場合、簡単なプロセスで UI からそれらを削除できます。 これにより、規制へのコンプライアンス ダッシュボードをさらにカスタマイズし、ユーザーに該当する標準にのみ焦点を当てることができます。

標準を削除するには:

1. Security Center のメニューから、 **[セキュリティ ポリシー]** を選択します。

1. 標準を削除する、該当するサブスクリプションを選択します。

    > [!NOTE]
    > サブスクリプションから標準を削除することはできますが、管理グループからは削除できません。 

    [セキュリティ ポリシー] ページが開きます。 選択したサブスクリプションの、既定のポリシー、業界標準と規制基準、および作成したカスタム イニシアチブが表示されます。

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Azure Security Center の規制へのコンプライアンス ダッシュボードから規制基準を削除する":::

1. 削除する標準に対応する **[無効]** を選択します。 確認ウィンドウが表示されます。

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="選択した規制基準を本当に削除するかどうかを確認します":::

1. **[はい]** を選択します。 標準が削除されます。 


## <a name="next-steps"></a>次のステップ

この記事では、規制基準と業界標準への準拠を監視するための **コンプライアンス標準を追加** する方法について説明しました。

関連資料については、次のページを参照してください。

- [Azure セキュリティ ベンチマーク](../security/benchmarks/introduction.md)
- [Security Center の規制コンプライアンス ダッシュボード](security-center-compliance-dashboard.md) - Security Center および外部のツールを使用して規制コンプライアンス データを追跡およびエクスポートする方法について説明します。
- [セキュリティ ポリシーの操作](tutorial-security-policy.md)