---
title: "Azure Active Directory ハイブリッド ID の設計上の考慮事項 - データ保護要件の決定 | Microsoft Docs"
description: "ハイブリッド ID ソリューションを計画する場合、ビジネスのデータ保護要件と、その要件に最適な選択肢を特定します。"
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: d971823ecd237c90787d4b923b5f9aad08fff4ee
ms.lasthandoff: 12/29/2016


---
# <a name="plan-for-enhancing-data-security-through-strong-identity-solution"></a>強力な ID ソリューションによりデータ セキュリティを強化するための計画を立てる
データを保護するための最初の手順は、データにアクセスできるユーザーを特定することです。また、そのプロセスの一環で、認証機能と承認機能を提供するシステムと統合できる ID ソリューションが必要になります。 認証と承認は混同され、その役割が誤解されることがよくあります。 次の図のように、実際はまったく異なります。

![](./media/hybrid-id-design-considerations/mobile-devicemgt-lifecycle.png)

**モバイル デバイス管理のライフ サイクル段階**

ハイブリッド ID ソリューションを計画する場合、ビジネスのデータ保護要件と、その要件に最適な選択肢を理解する必要があります。

> [!NOTE]
> データ セキュリティの計画を完了したら、「 [多要素認証要件の決定](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md) 」を参照して、多要素認証要件に関する選択が、このセクションで行った決定によって影響を受けていないことを確認します。
> 
> 

## <a name="determine-data-protection-requirements"></a>データ保護要件の決定
現在はモビリティの時代であり、ほとんどの企業には共通の目標があります。オンプレミスでも任意の場所のリモート環境からでも、ユーザーが自分のモバイル デバイスを使用できるようにして、生産性を向上するという目標です。 これは共通する目標かもしれませんが、このような要件を持つ企業は、企業のデータをセキュリティで保護し、ユーザーのプライバシーを守るために、軽減する必要がある脅威の量についても不安を持ちます。 この点に関する要件は、企業によって異なる可能性があります。企業が活動する業界によってコンプライアンス規則は変わり、それに応じて設計の決定も変わります。 

一方、業界に関係なく、探索し、検証する必要があるセキュリティ上の側面がいくつかあります。その側面について、次のセクションで説明します。

## <a name="data-protection-paths"></a>データ保護のパス
![](./media/hybrid-id-design-considerations/data-protection-paths.png)

**データ保護のパス**

上図では、データにアクセスする前に、ID コンポーネントが最初に検証されます。 一方、このデータは、アクセスしている間に異なる状態になる可能性があります。 この図の各メンバーは、いずれかの時点でデータが存在する可能性があるパスを表しています。 次に、各メンバーについて説明します。

1. デバイス レベルのデータ保護。
2. 転送中のデータ保護。
3. オンプレミスに保存中のデータ保護。
4. クラウドに保存中のデータ保護。

IT が各段階でデータ自体を保護できるようにする技術制御は、ハイブリッド ID ソリューションから直接提供されませんが、ハイブリッド ID ソリューションは、データに対するアクセス権をユーザーに付与する前に、オンプレミスとクラウド両方の ID 管理リソースを利用してユーザーを特定できる必要があります。 ハイブリッド ID ソリューションを計画する場合、組織の要件に従って次のチェック項目に回答してください。

## <a name="data-protection-at-rest"></a>保存中のデータ保護
データが保存されている場所 (デバイス、クラウド、またはオンプレミス) に関係なく、評価を実行して、この点について組織のニーズを理解することが重要です。 この領域に関する次のチェック項目に回答してください。

* 会社は保存中のデータを保護する必要がありますか。
  * 「はい」の場合、ハイブリッド ID ソリューションは現在のオンプレミス インフラストラクチャと統合できますか。
  * 「はい」の場合、ハイブリッド ID はクラウド内にあるワークロードと統合できますか。
* クラウド ID 管理は、クラウドに格納されているユーザーの資格情報や他のデータを保護できますか。

## <a name="data-protection-in-transit"></a>転送中のデータ保護
デバイスとデータセンター間、またはデバイスとクラウド間で転送中のデータを保護する必要があります。 ただし、転送中とは、クラウド サービス以外のコンポーネントとの通信プロセスを意味するとは限りません。2 つの仮想ネットワーク間など、内部的な移動の場合もあります。 この領域に関する次のチェック項目に回答してください。

* 会社は転送中のデータを保護する必要がありますか。
  * 「はい」の場合、ハイブリッド ID ソリューションは SSL/TLS などのセキュア制御と統合できますか。
* クラウド ID 管理は、署名されたディレクトリ ストアに対するトラフィックと内部のトラフィック (データセンター内およびデータセンター間) を維持していますか。

## <a name="compliance"></a>コンプライアンス
規制、法律、法令遵守の要件は、企業が属している業界によって変わります。 規制が厳しい業界の企業は、コンプライアンスの課題に関連する ID 管理の問題に対応する必要があります。 Sarbanes-Oxley (SOX: サーベンス オクスリー法)、Health Insurance Portability and Accountability Act (HIPAA: 医療保険の携行性と責任に関する法律)、Gramm-Leach-Bliley Act (GLBA: グラム リーチ ブライリー法)、Payment Card Industry Data Security Standard (PCI DSS: クレジットカード業界のセキュリティ基準) などの規制は、ID とアクセスについて非常に厳格です。 会社が採用するハイブリッド ID ソリューションには、これらの規制の 1 つ以上の要件を満たす中心的な機能が必要です。 この領域に関する次のチェック項目に回答してください。

* ハイブリッド ID ソリューションは、会社の規制要件に準拠していますか。
* ハイブリッド ID ソリューションには、会社が規制要件に準拠できるようにする機能が組み込まれていますか。 

> [!NOTE]
> 回答をメモし、その背後にある論理的根拠を理解してください。 [データ保護戦略の定義](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) では、利用できる選択肢と各選択肢の長所/短所について説明します。  チェック項目に答えることで、ビジネス ニーズに最適な選択肢が見つかります。
> 
> 

## <a name="next-steps"></a>次のステップ
 [コンテンツ管理要件の決定](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>関連項目
[設計上の考慮事項の概要](active-directory-hybrid-identity-design-considerations-overview.md)


