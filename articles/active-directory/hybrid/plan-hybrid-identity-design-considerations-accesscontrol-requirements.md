---
title: ハイブリッド ID 設計 - アクセス制御要件 - Azure | Microsoft Docs
description: ID の柱と、ハイブリッド環境内のユーザーのリソースのアクセス要件について説明します。
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e3b3b984-0d15-4654-93be-a396324b9f5e
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84b786a1701892823554a83fa2015ac88d6eff4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "60295145"
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>ハイブリッド ID ソリューションのアクセス制御要件の決定
組織がハイブリッド ID ソリューションを設計する場合、その機会に、ユーザーに使用できるようにする予定のリソースについて、アクセス要件を見直すこともできます。 データは、次に示す ID の 4 本の柱全体にかかっています。

* 管理
* 認証
* 承認
* 監査

ここでは、認証と承認の詳細と、管理と監査がハイブリッド ID のライフサイクルに含まれていることを説明します。 これらの機能の詳細については、「 [ハイブリッド ID 管理タスクの決定](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) 」を参照してください。

> [!NOTE]
> 個々の柱の詳細については、 [ID の 4 本の柱: ハイブリッド ID の段階における ID 管理](https://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) に関するページを参照してください。
> 
> 

## <a name="authentication-and-authorization"></a>認証と権限承認
認証と承認にはさまざまなシナリオがあり、各シナリオには異なる要件があります。会社が採用する予定のハイブリッド ID ソリューションは、その要件を満たす必要があります。 企業間 (B2B) 通信に関係するシナリオの場合、組織が使用する認証および承認の方法で、ビジネス パートナーと通信できるようにする必要があるので、IT 管理者にとって課題が増える可能性があります。 認証と承認の要件を設計するプロセスでは、次のチェック項目に回答する必要があります。

* 組織は、ID 管理システム内にあるユーザーのみを認証および承認しますか。
  * B2B シナリオの計画は何かありますか。
  * 「はい」の場合、両者の接続に使用するプロトコル (SAML、OAuth、Kerberos、または証明書) を既に把握していますか。
* 採用する予定のハイブリッド ID ソリューションは、それらのプロトコルをサポートしていますか。

考慮すべきもう 1 つの重要な点は、ユーザーとパートナーが使用する認証リポジトリがある場所と、使用する管理モデルです。 次の 2 つの主要な選択肢を検討してください。

* 一元: このモデルでは、ユーザーの資格情報、ポリシー、管理を、オンプレミスまたはクラウドで一元管理できます。
* ハイブリッド: このモデルでは、ユーザーの資格情報、ポリシー、管理がオンプレミスで一元管理され、クラウドにレプリケートされます。

組織が採用するモデルは、ビジネスの要件によって変わります。次のチェック項目に回答して、ID 管理システムを配置する場所と使用する管理モデルを特定してください。

* 現在、組織にオンプレミスの ID 管理はありますか。
  * 「はい」の場合、今後も残す予定ですか。
  * ID 管理システムを配置する場所に影響する、組織が従う必要がある規制やコンプライアンスの要件はありますか。
* 組織は、オンプレミスまたはクラウドにあるアプリケーションにシングル サインオンを使用していますか。
  * 「はい」の場合、ハイブリッド ID モデルを採用すると、このプロセスに影響がありますか。

## <a name="access-control"></a>アクセス制御
認証と承認は、ユーザーの検証によって企業データへのアクセスを可能にするための重要な要素ですが、ユーザーが持つアクセス権のレベルと、管理者が管理対象のリソースに対して持つアクセス権のレベルを制御するためにも重要です。 ハイブリッド ID ソリューションの場合、リソース、委任、ロール ベースのアクセス制御に対するアクセス権をより詳細に指定できるようにします。 アクセス制御について、次のチェック項目に回答してください。

* 社内には、ID システムを管理するための昇格された特権を持つ複数のユーザーがいますか。
  * 「はい」の場合、各ユーザーに同じアクセス レベルが必要ですか。
* 会社では、特定のリソースを管理するアクセス権をユーザーに委任する必要はありますか。
  * 「はい」の場合、その頻度はどのくらいですか。
* 会社のオンプレミスとクラウドのリソース間で、アクセス制御機能を統合する必要はありますか。
* 何らかの条件に従ってリソースに対するアクセス権を制限する必要はありますか。
* 何らかのリソースに対するカスタムの制御アクセスを必要とするアプリケーションはありますか。
  * 「はい」の場合、そのアプリケーションはどこにありますか (オンプレミスまたはクラウド)。
  * 「はい」の場合、その対象リソースはどこにありますか (オンプレミスまたはクラウド)。

> [!NOTE]
> 回答をメモし、その背後にある論理的根拠を理解してください。 [データ保護戦略の定義](plan-hybrid-identity-design-considerations-data-protection-strategy.md) では、利用できる選択肢と各選択肢の長所/短所について説明します。  チェック項目に答えることで、ビジネス ニーズに最適な選択肢が見つかります。
> 
> 

## <a name="next-steps"></a>次のステップ
[インシデント対応要件の決定](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>参照
[設計上の考慮事項の概要](plan-hybrid-identity-design-considerations-overview.md)

