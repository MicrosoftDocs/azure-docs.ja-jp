---
title: ハイブリッド ID 設計 - データ保護要件 - Azure | Microsoft Docs
description: ハイブリッド ID ソリューションを計画する場合、ビジネスのデータ保護要件とその要件を満たすのに最適な選択肢を特定します。
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 056d0caf2bfc6e99d5d659a2561bc41844feb79a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "64918792"
---
# <a name="plan-for-enhancing-data-security-through-a-strong-identity-solution"></a>強力な ID ソリューションによりデータ セキュリティを強化するための計画
データを保護する最初の手順は、そのデータにアクセスできるユーザーを特定することです。 また、認証および承認機能を提供するようにシステムと統合可能なIDソリューションが必要です。 認証と承認は混同され、その役割が誤解されることがよくあります。 実際には、次の図のように、大変異っています：

![モバイル デバイスのライフサイクル](./media/plan-hybrid-identity-design-considerations/mobile-devicemgt-lifecycle.png)

**モバイル デバイス管理のライフ サイクル段階**

ハイブリッド ID ソリューションを　計画する場合、ビジネスのデータ保護要件と、その要件に最適な選択肢を理解する必要があります。

> [!NOTE]
> データ セキュリティの計画を完了したら、「 [多要素認証要件の決定](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md) 」を参照して、多要素認証要件に関する選択が、このセクションで行った決定によって影響を受けていないことを確認します。
> 
> 

## <a name="determine-data-protection-requirements"></a>データ保護要件の決定
現在はモビリティの時代であり、ほとんどの企業には共通の目標があります：オンプレミスでも、自らのモバイルデバイスでも、または任意の場所のリモート環境からでも、ユーザーがモバイル デバイスを使用できるようにして、生産性を向上するという目標です。 このような要件を持つ企業は、企業のデータをセキュリティで保護し、ユーザーのプライバシーを守るために、軽減する必要がある、脅威の量についても懸念を抱きます。 この点に関する要件は、企業によって異なる可能性があります。企業が活動する業界によってコンプライアンス規則は変わり、それに応じて設計の決定も変わります。 

一方、業界に関係なく、探索し、および検証される必要があるセキュリティ上の側面がいくつかあります。

## <a name="data-protection-paths"></a>データ保護のパス
![データ保護のパス](./media/plan-hybrid-identity-design-considerations/data-protection-paths.png)

**データ保護のパス**

上図では、データにアクセスする前に、ID コンポーネントが最初に検証されます。 一方、このデータは、アクセスしている間に異なる状態になる可能性があります。 この図の各メンバーは、いずれかの時点でデータが存在する可能性があるパスを表しています。 次に、各メンバーについて説明します。

1. デバイス レベルのデータ保護。
2. 転送中のデータ保護。
3. オンプレミスに保存中のデータ保護。
4. クラウドに保存中のデータ保護。

データへのアクセスを付与する前に、ユーザーを識別するために、ハイブリッド ID ソリューションが、オンプレミスおよびクラウド ID 管理リソースの 両方を利用できることが必要です。 ハイブリッド ID ソリューションを計画する場合、組織の要件に従って、次のチェック項目に回答してください。

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
規制、法律、法令遵守の要件は、企業が属している業界によって異なります。 規制が厳しい業界の企業は、コンプライアンスの課題に関連する ID 管理の問題に対応する必要があります。 Sarbanes-Oxley (SOX: サーベンス オクスリー法)、Health Insurance Portability and Accountability Act (HIPAA: 医療保険の携行性と責任に関する法律)、Gramm-Leach-Bliley Act (GLBA: グラム リーチ ブライリー法)、Payment Card Industry Data Security Standard (PCI DSS: クレジットカード業界のセキュリティ基準) などの規制は、ID とアクセスに関して、厳しくなっています。 会社が採用するハイブリッド ID ソリューションには、これらの規制の 1 つ以上の要件を満たす中心的な機能が必要です。 この領域に関する次のチェック項目に回答してください。

* ハイブリッド ID ソリューションは、会社の規制要件に準拠していますか。
* ハイブリッド ID ソリューションには 
* 会社が規制要件に準拠できるようにする機能が組み込まれていますか? 

> [!NOTE]
> 回答をメモし、その背後にある論理的根拠を理解してください。 [データ保護戦略の定義](plan-hybrid-identity-design-considerations-data-protection-strategy.md) では、利用できる選択肢と各選択肢の長所/短所について説明します。  チェック項目に答えることで、ビジネス ニーズに最適な選択肢が見つかります。
> 
> 

## <a name="next-steps"></a>次のステップ
 [コンテンツ管理要件の決定](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>参照
[設計上の考慮事項の概要](plan-hybrid-identity-design-considerations-overview.md)

