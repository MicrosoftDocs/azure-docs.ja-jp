---
title: Azure Security Center の規制コンプライアンス ダッシュボードで規制コンプライアンスの動的監視に更新する方法 | Microsoft Docs
description: 規制コンプライアンス パッケージの更新
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 28b6a70297efb8d8237f085e1f5b08dbb6a09072
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88522608"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>規制コンプライアンス ダッシュボードでの標準セットのカスタイマイズ

Azure Security Center では、リソースの構成が業界標準、規制、ベンチマークの要件と継続的に比較されます。 **規制コンプライアンス ダッシュボード**では、特定のコンプライアンスのコントロールと要件をどのように満たしているかに基づいて、コンプライアンス体制に関する分析情報が提供されます。


## <a name="overview-of-compliance-packages"></a>コンプライアンス パッケージの概要

業界標準、規制基準、およびベンチマークは、Security Center で "*コンプライアンス パッケージ*" として表されます。  各パッケージは、Azure Policy で定義されたイニシアチブです。 評価としてマップされたコンプライアンス データをダッシュボードに表示するには、**Security Policy** ページ内から管理グループまたはサブスクリプションにコンプライアンス パッケージを追加します。 (Azure Policy とイニシアチブの詳細については、「[セキュリティ ポリシーの操作](tutorial-security-policy.md)」をご覧ください)。

選択したスコープに標準またはベンチマークをオンボードすると、イニシアチブがそのスコープに割り当てられ、評価としてマップされた、関連するすべてのコンプライアンス データと共に、標準が規制コンプライアンス ダッシュボードに表示されます。 オンボードされている標準の概要レポートをダウンロードすることもできます。

また、Microsoft では規制基準自体を追跡し、一部のパッケージの適用範囲を経時的かつ自動的に改善しています。 Microsoft がイニシアティブの新しいコンテンツ (標準のより多くのコントロールにマップされる新しいポリシー) をリリースすると、その追加のコンテンツがダッシュボードに自動的に表示されます。

> [!TIP]
> Microsoft が新しいコンテンツをリリースしたときに経時的に改善される標準の 1 つが、**Azure CIS 1.1.0 (new)** (正式名称は、[CIS Microsoft Azure Foundations Benchmark version 1.1.0](https://www.cisecurity.org/benchmark/azure/)) です。 これを Azure CIS 1.1.0 (すべての Security Center 環境で既定で構成されている Azure CIS の表現) と共にダッシュボードに追加する必要があります。 そのパッケージでは静的なルール セットが利用されます。 新しいパッケージには、より多くのポリシーが含まれており、経時的かつ自動的に更新されます。 以下の説明に従って、新しい動的パッケージに更新します。


## <a name="available-packages"></a>使用可能なパッケージ

NIST SP 800-53 R4、SWIFT CSP CSCF-v2020、UK Official および UK NHS、Canada Federal PBMM、Azure CIS 1.1.0 (new) (Azure CIS 1.1.0 のより完全な表現) などの標準を追加できます。 

さらに、**Azure セキュリティ ベンチマーク**を追加することもできます。これは Microsoft が作成したもので、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Azure 固有のガイドラインです。 (Azure セキュリティ ベンチマークの詳細については、[こちら](https://docs.microsoft.com/azure/security/benchmarks/introduction)をご覧ください)。

追加の標準が使用可能になると、ダッシュボードでサポートされます。 


## <a name="adding-a-regulatory-standard-to-your-dashboard"></a>ダッシュボードへの規制基準の追加

次の手順は、サポートされている規制基準のいずれかへの準拠を監視するためのパッケージを追加する方法を示しています。

> [!NOTE]
> 所有者またはポリシーの共同作成者であるユーザーのみが、コンプライアンス標準を追加するために必要なアクセス許可を持っています。 

1. Security Center のサイドバーで、 **[規制コンプライアンス]** を選択して、規制コンプライアンス ダッシュボードを開きます。 ここで、現在選択されているサブスクリプションに割り当てられているコンプライアンス標準を確認できます。   

1. ページの上部から、 **[コンプライアンス ポリシーの管理]** を選択します。 [ポリシー管理] ページが表示されます。

1. 規制コンプライアンス体制を管理するサブスクリプションまたは管理グループを選択します。 

    > [!TIP]
    > 入れ子になったすべてのリソースのコンプライアンス データが集計され、追跡されるように、標準の適用対象として最上位のスコープを選択することをお勧めします。 

1. 組織に関連する標準を追加するには、 **[標準をさらに追加]** をクリックします。 

1. **[規制コンプライアンス標準の追加]** ページで、使用できる標準のパッケージを検索できます。 使用できる標準の一部を次に示します。

    - **Azure セキュリティ ベンチマーク**
    - **NIST SP 800-53 R4**
    - **SWIFT CSP CSCF-v2020**
    - **UKO および UK NHS**
    - **Canada PBMM**
    
    ![Azure Security Center の規制コンプライアンス ダッシュボードへの規制パッケージの追加](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. **[追加]** を選択し、スコープ、パラメーター、修復など、特定のイニシアチブに関して必要な詳細をすべて入力します。

1. Security Center のサイドバーで、再度 **[規制コンプライアンス]** を選択して、規制コンプライアンス ダッシュボードに戻ります。
    * 業界標準および規制基準の一覧に新しい標準が表示されます。 
    * **Azure CIS 1.1.0 (New)** を追加した場合、Azure CIS 1.1.0 コンプライアンスの元の "*静的*" ビューもそのまま残されます。 これは、今後自動的に削除される可能性があります。

    > [!NOTE]
    > 新しく追加された標準がコンプライアンス ダッシュボードに表示されるまでに数時間かかることがあります。

    [![古い Azure CIS と新しい Azure CIS が表示された規制コンプライアンス ダッシュボード](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)

## <a name="next-steps"></a>次のステップ

この記事では、追加の標準への準拠を監視するための**コンプライアンス パッケージを追加**する方法について説明しました。 

その他の関連資料については、次の記事を参照してください。 

- [Azure セキュリティ ベンチマーク](https://docs.microsoft.com/azure/security/benchmarks/introduction)
- [Security Center の規制コンプライアンス ダッシュ ボード](security-center-compliance-dashboard.md)
- [セキュリティ ポリシーの操作](tutorial-security-policy.md)