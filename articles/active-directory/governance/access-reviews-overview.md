---
title: アクセス レビューとは - Azure Active Directory | Microsoft Docs
description: Azure Active Directory アクセス レビューを使用すると、組織のガバナンス、リスク管理、コンプライアンスの取り組みを満たすように、グループ メンバーシップとアプリケーションのアクセスを制御することができます。
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 08/05/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fe8b50775390d60f75fafeae490e0307912600f
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125277"
---
# <a name="what-are-azure-ad-access-reviews"></a>Azure AD アクセス レビューとは

組織で Azure Active Directory (Azure AD) アクセス レビューを使うことにより、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、ロールの割り当てを効率的に管理できます。 ユーザーのアクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

次のビデオでは、アクセス レビューの簡単な概要を説明します。

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>アクセス レビューが重要である理由

Azure AD を使用すると、組織内での共同作業、およびパートナーなどの外部組織のユーザーとの共同作業が可能です。 ユーザーは、グループへの参加、ゲストの招待、クラウド アプリへの接続、作業用や個人用のデバイスからのリモート作業を行うことができます。 セルフ サービスの機能を利用する便利さゆえ、よりよいアクセス管理機能が必要になっています。

- 新しい従業員が参加するとき、生産性のための適切なアクセス権をどのようにして確実に付与しますか。
- ユーザーがチームを移動したり会社を辞めたりするとき (特にゲストが関係している場合)、どのようにして古いアクセス権を確実に削除しますか。
- 過剰なアクセス権は、アクセス権の制御の欠如を示しているため、監査所見やセキュリティ侵害につながる可能性があります。
- リソースの所有者と事前に連絡を取り、リソースへのアクセス権を持つユーザーを定期的にレビューしていることを確認する必要があります。

## <a name="when-to-use-access-reviews"></a>アクセス レビューを使用すべきとき

- **特権ロールのユーザーが多すぎるとき:** 管理者アクセス権を持っているユーザーの数、その内で全体管理者の数、管理タスクに割り当てられた後で削除されていない招待ゲストまたはパートナーがいるかどうかを確認するのはよいことです。 [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) エクスペリエンスでは、全体管理者などの [Azure AD ロール](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)またはユーザー アクセス管理者などの [Azure リソース ロール](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)でロール割り当てユーザーを認定できます。
- **自動化が実行不可能なとき:** セキュリティ グループまたは Office 365 のグループで動的メンバーシップに対するルールを作成できますが、HR データが Azure AD 内にない場合や、ユーザーがグループを離れた後で後任のトレーニングのためにアクセスする必要がある場合はどうしますか。 そのようなときは、そのグループに対してレビューを作成し、まだアクセスを必要とするユーザーが継続的アクセス権を持っていることを確認することができます。
- **グループが新しい目的に使用されるとき:** Azure AD に同期されるグループがある場合、または営業チーム グループの全員に対して Salesforce アプリケーションを有効にする場合は、異なるリスク コンテンツでグループを使用する前にグループ メンバーシップを確認するようグループ所有者に依頼するとよいことがあります。
- **ビジネス クリティカルなデータ アクセス:** 特定のリソースでは、監査のため、IT 組織の外部のユーザーに対し、定期的にサインアウトして、アクセスが必要な正当な理由を示すよう依頼することが必要な場合があります。
- **ポリシーの例外リストを維持するため:** 理想的な世界では、すべてのユーザーが、組織のリソースへのアクセスをセキュリティで保護するアクセス ポリシーに従っているでしょう。 ただし、ビジネス ケースには、例外を認める必要がある場合もあります。 IT 管理者は、このタスクを管理し、ポリシー例外の見落としを防ぎ、これらの例外が定期的にレビューされている証明を監査者に提供することができます。
- **グループのゲストがまだ必要であることを確認するようグループの所有者に依頼するため:** 一部のオンプレミス IAM では従業員のアクセスは自動化される場合がありますが、招待されたゲストではされません。 グループでビジネスの重要なコンテンツへのアクセス権をゲストに付与する場合、ゲストにアクセスに対する正当なビジネス ニーズがまだあることを確認するのは、グループ所有者の責任です。
- **定期的にレビューを繰り返す場合:** 毎週、毎月、毎四半期、毎年などの設定された周期でユーザーのアクセス レビューを繰り返すよう設定することができ、レビュー担当者は各レビューの開始時に通知されます。 レビュー担当者は、使いやすいインターフェイスとスマートな推奨事項の助けを借りてアクセスを承認または拒否することができます。

## <a name="where-do-you-create-reviews"></a>レビューを作成する場所

何をレビューするかにより、Azure AD アクセス レビュー、Azure AD エンタープライズ アプリ (プレビュー)、または Azure AD PIM でアクセス レビューを作成します。

| ユーザーのアクセス権 | レビュー担当者になれるユーザー | レビューが作成される場所 | レビュー担当者のエクスペリエンス |
| --- | --- | --- | --- |
| セキュリティ グループ メンバー</br>Office グループ メンバー | 指定されたレビュー担当者</br>グループ所有者</br>自己レビュー | Azure AD アクセス レビュー</br>Azure AD グループ | アクセス パネル |
| 接続されたアプリに割り当て | 指定されたレビュー担当者</br>自己レビュー | Azure AD アクセス レビュー</br>azure AD エンタープライズ アプリ (プレビュー) | アクセス パネル |
| Azure AD ロール | 指定されたレビュー担当者</br>自己レビュー | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure ポータル |
| Azure リソース ロール | 指定されたレビュー担当者</br>自己レビュー | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure ポータル |


## <a name="onboard-access-reviews"></a>アクセス レビューの配布準備

アクセス レビューをオンボードするには、次の手順に従います。

1. グローバル管理者またはユーザー管理者として、アクセス レビューを使用する [Azure portal](https://portal.azure.com) にサインインします。

1. 左側のナビゲーションで、 **[Azure Active Directory]** をクリックします。

1. 左側のメニューで、 **[Identity Governance]** をクリックします。

1. **[アクセス レビュー]** をクリックします。
 
    ![アクセス レビューの開始ページ](./media/access-reviews-overview/access-reviews-overview-onboard.png)

1. このページで、 **[今すぐオンボード]** ボタンをクリックします。
    
      ![アクセス レビューの配布準備](./media/access-reviews-overview/access-reviews-overview-select-onboard.png)


## <a name="learn-about-access-reviews"></a>アクセス レビューの詳細情報

アクセス レビューの作成と実行の詳細については、この短いデモをご覧ください。

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

組織にアクセス レビューを展開する準備ができたら、ビデオの手順に従って、配布を準備し、管理者をトレーニングして、最初のアクセス レビューを作成してください。

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>ライセンスの要件

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="which-users-must-have-licenses"></a>ライセンスが必要なユーザー

アクセス レビューを操作する各ユーザーには、有料の Azure AD Premium P2 ライセンスが必要です。 たとえば、次のようになります。

- アクセス レビューを作成する管理者
- アクセス レビューを実行するグループ所有者
- レビュー担当者として割り当てられたユーザー
- 自己レビューを実行するユーザー

ゲスト ユーザーに各自のアクセスのレビューを求めることもできます。 自分自身の組織のユーザーの 1 人に割り当てる有料の Azure AD Premium P2 ライセンスごとに、Azure AD 企業間 (B2B) を使用して、外部ユーザー無料利用分に従って最大 5 人までのゲスト ユーザーを招待できます。 これらのゲスト ユーザーも Azure AD Premium P2 の機能を使用できます。 詳細については、[Azure AD B2B コラボレーションのライセンス ガイダンス](../b2b/licensing-guidance.md)を参照してください。

必要なライセンス数の決定に役立つ一部のシナリオ例を次に示します。

| シナリオ | 計算 | 必要なライセンスの数 |
| --- | --- | --- |
| 管理者は、ユーザーが 500 人のグループ A のアクセス レビューを作成します。 レビュー担当者として 3 人のグループ所有者を割り当てます。 | 管理者用に 1 ライセンス + レビュー担当者としての各グループ所有者用に 3 ライセンス。 | 4 |
| 管理者は、ユーザーが 500 人のグループ A のアクセス レビューを作成します。 自己レビューにします。 | 管理者用に 1 ライセンス + 自己レビュー担当者として各ユーザー用に 500 ライセンス。 | 501 |
| 管理者は、ユーザーが 5 人とゲスト ユーザーが 25 人のグループ B のアクセス レビューを作成します。 自己レビューにします。 | 管理者用に 1 ライセンス + 自己レビュー担当者として各ユーザー用に 5 ライセンス。<br/>(ゲスト ユーザーは、必須の 1:5 比率でカバーされます) | 6 |
| 管理者は、ユーザーが 5 人とゲスト ユーザーが 108 人のグループ C のアクセス レビューを作成します。 自己レビューにします。 | 管理者用に 1 ライセンス + 自己レビュー担当者として各ユーザー用に 5 ライセンス + 必要な 1:5 の比率で 108 人ゲスト ユーザー全員に対応する追加の 16 ライセンス。<br/>1 + 5 = 6 ライセンス。これは 5\*6 = 30 人のゲスト ユーザーに対応します。 残り (108 - 5\*6) = 78 人のゲスト ユーザーの場合、78/5 = 16 の追加ライセンスが必要です。 そのため、合計で 6 + 16 = 22 のライセンスが必要です。 | 22 |

お客様のユーザーにライセンスを割り当てる方法については、「[Azure Active Directory ポータルを使用したライセンスの割り当てと削除](../fundamentals/license-users-groups.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- [グループまたはアプリケーションのアクセス レビューを作成する](create-access-review.md)
- [Azure AD 管理者ロールに含まれるユーザーのアクセス レビューを作成する](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [グループまたはアプリケーションに対するアクセスのレビュー](perform-access-review.md)
- [グループまたはアプリケーションに対するアクセス レビューを完了する](complete-access-review.md)
