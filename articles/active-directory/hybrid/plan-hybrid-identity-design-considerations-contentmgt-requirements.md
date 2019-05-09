---
title: ハイブリッド ID 設計 - コンテンツ管理要件 - Azure | Microsoft Docs
description: ビジネスのコンテンツ管理要件を決定する方法について説明します。 通常、ユーザーが自分のデバイスを持っている場合、使用するアプリケーションによって切り替える複数の資格情報も持っている可能性があります。 個人の資格情報を使用して作成したコンテンツと、会社の資格情報を使用して作成したコンテンツを区別することが重要です。 ID ソリューションは、クラウド サービスと対話してシームレスなエクスペリエンスをエンド ユーザーに提供すると共に、ユーザーのプライバシーを確保し、データ漏洩に対する保護を強化する必要があります。
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81a1ab0ee411f05b353317b0d781e0cb292c7d6a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56166625"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>ハイブリッド ID ソリューションのコンテンツ管理要件の決定
ビジネスのコンテンツ管理要件を理解することは、使用するハイブリッド ID ソリューションの決定に影響があります。 複数デバイスと、ユーザーが自分のデバイスを使用できる機能 ([BYOD](https://aka.ms/byodcg)) を普及する場合、社内データを保護すると共に、ユーザーのプライバシーを損ねないようにする必要があります。 通常、ユーザーが自分のデバイスを持っている場合、使用するアプリケーションによって切り替える複数の資格情報も持っている可能性があります。 個人の資格情報を使用して作成したコンテンツと、会社の資格情報を使用して作成したコンテンツを区別することが重要です。 ID ソリューションは、クラウド サービスと対話してシームレスなエクスペリエンスをエンド ユーザーに提供すると共に、ユーザーのプライバシーを確保し、データ漏洩に対する保護を強化する必要があります。 

下図のようなコンテンツ管理を提供するために、ID ソリューションはさまざまな技術制御から利用されます。

![](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**ID 管理システムを利用するセキュリティ制御**

一般に、コンテンツ管理要件は次の領域で ID 管理システムを利用します。

* プライバシー: リソースを所有するユーザーを特定し、適切な制御を適用して整合性を維持します。
* データ分類: データ分類に従って、ユーザーまたはグループのアクセス レベルを特定します。 
* データ漏えい保護: データを保護して漏えいを防ぐ処理を行うセキュリティ保護は、ユーザーの ID を検証するために、ID システムと対話する必要があります。 これは監査証跡のためにも重要です。

> [!NOTE]
> データ分類のベスト プラクティスとガイドラインの詳細については、 [クラウドに備えたデータ分類](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) に関するドキュメントを参照してください。
> 
> 

ハイブリッド ID ソリューションを計画する場合、組織の要件に従って次のチェック項目に回答してください。

* データのプライバシー保護のために、セキュリティ制御が導入されていますか。
  * 「はい」の場合、そのセキュリティ制御は、採用する予定のハイブリッド ID ソリューションと統合できますか。
* 会社はデータ分類を使用していますか。
  * 「はい」の場合、現在のソリューションは、採用する予定のハイブリッド ID ソリューションと統合できますか。
* 現在、会社にはデータ漏えいに関するソリューションがありますか。 
  * 「はい」の場合、現在のソリューションは、採用する予定のハイブリッド ID ソリューションと統合できますか。
* 会社は、リソースへのアクセスを監査する必要がありますか。
  * 「はい」の場合、どのような種類のリソースですか。
  * 「はい」の場合、どのような情報レベルが必要ですか。
  * 「はい」の場合、監査ログはどこに配置する必要がありますか。 オンプレミスですか、クラウドですか。
* 機密データ (社会保障番号、クレジット カード番号など) を含むすべての電子メールを暗号化する必要はありますか。
* 外部ビジネス パートナーと共有するすべてのドキュメント/コンテンツを暗号化する必要はありますか。
* 特定の種類の電子メールに対して会社のポリシー (全員に返信しない、転送しない) を適用する必要はありますか。

> [!NOTE]
> 回答をメモし、その背後にある論理的根拠を理解してください。 [データ保護戦略の定義](plan-hybrid-identity-design-considerations-data-protection-strategy.md) では、利用できる選択肢と各選択肢の長所/短所について説明します。  チェック項目に答えることで、ビジネス ニーズに最適な選択肢が見つかります。
> 
> 

## <a name="next-steps"></a>次の手順
[アクセス制御要件の決定](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>関連項目
[設計上の考慮事項の概要](plan-hybrid-identity-design-considerations-overview.md)

