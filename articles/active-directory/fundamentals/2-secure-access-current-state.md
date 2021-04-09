---
title: Azure Active Directory を使用した外部コラボレーションの現在の状態を検出する
description: コラボレーションの現在の状態を検出する方法について説明します。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30858e9978f7e8857c5f8a2dcdfd7455f6e97b60
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553427"
---
# <a name="discover-the-current-state-of-external-collaboration-in-your-organization"></a>組織内での外部コラボレーションの現在の状態を検出する 

外部コラボレーションの現在の状態を検出する前に、[必要なセキュリティ体制を決定する](1-secure-access-posture.md)必要があります。 一元管理と委任管理、および関連するガバナンス、規制、コンプライアンスの目標について、組織のニーズを検討してください。 

組織内の個人は、既に他の組織のユーザーと共同作業している場合があります。 コラボレーションの手段としては、Microsoft 365 などの生産性アプリケーション、電子メール、またはその他の方法による外部ユーザーとのリソース共有が考えられます。 ガバナンスプランの基本方針は、以下の点を確認しながら決定していきます 
*   外部コラボレーションを開始しているユーザー。
*   現在コラボレーションを行っている外部ユーザーと外部組織。
*   外部ユーザーに付与されているアクセス権。


## <a name="users-initiating-external-collaboration"></a>外部コラボレーションを開始しているユーザー

外部コラボレーションを既に開始しているユーザーは、外部コラボレーションでよく使用されるアプリケーションや、そのアクセスが終了するタイミングについてよく理解しています。 これらのユーザーを把握することは、外部ユーザーの招待や、アクセスパッケージの作成、アクセスレビューの実施を行うためのアクセス許可を、どのユーザーに委任するか決定するうえで役立ちます。

現在コラボレーションを行っているユーザーを検索するには、 [Microsoft 365 の監査ログで、共有とアクセス要求のアクティビティ](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#sharing-and-access-request-activities)を確認します。 また、 [Azure AD の監査ログを調べて、B2B ユーザーをディレクトリに招待したユーザーの詳細](../external-identities/auditing-and-reporting.md)を確認することもできます。

## <a name="find-current-collaboration-partners"></a>現在のコラボレーションパートナーを確認する

外部ユーザーは、パートナーが管理する資格情報を持った [Azure AD B2B ユーザー](../external-identities/what-is-b2b.md)である場合と、ローカルにプロビジョニングされた資格情報を持つ外部ユーザーである場合があります。 これらのユーザーは通常、ゲストの UserType でマークされます (必ずではありません)。 ゲスト ユーザーは、[Microsoft Graph API](/graph/api/user-list?tabs=http)、[PowerShell](/graph/api/user-list?tabs=http)、または [Azure portal](../enterprise-users/users-bulk-download.md) を通じて列挙できます。

### <a name="use-email-domains-and-companyname-property"></a>電子メールドメインと companyName プロパティを使用する

外部の組織は、外部ユーザーの電子メール アドレスのドメイン名によって特定できます。 Google などのコンシューマー ID プロバイダーがサポートされている場合は、これが不可能になることがあります。 その場合は、ユーザーの外部組織を明確に識別するために、companyName 属性を記述することをお勧めします。

### <a name="use-allow-or-deny-lists"></a>許可リストまたは拒否リストを使用する

自分の組織で、特定の組織のみとのコラボレーションを許可する必要があるかどうかを検討してください。 または、特定の組織とのコラボレーションをブロックする必要があるかどうかも検討しましょう。  テナント レベルには[許可または拒否リスト](../external-identities/allow-deny-list.md)があります。これを使用すると、ソース (Teams、SharePoint、Azure Portal など) に関係なく、B2B の招待と引き換え全体を制御できます。
エンタイトルメント管理を使用している場合は、下に示すように、[Specific connected organizations]\(特定の接続済み組織\) 設定を使用して、アクセス パッケージのスコープをパートナーのサブセットに設定することもできます。


![新しいアクセス パッケージを作成する際の許可/拒否リストのスクリーンショット。](media/secure-external-access/2-new-access-package.png)


## <a name="find-access-being-granted-to-external-users"></a>外部ユーザーに付与されているアクセス権を特定する

外部ユーザーと外部組織のインベントリを取得したら、Microsoft Graph API を使用してそれらのユーザーに付与されたアクセス権を特定し、Azure AD の[グループメンバーシップ](/graph/api/resources/groups-overview)か、Azure AD での[直接アプリケーション割り当て](/graph/api/resources/approleassignment)を確認できます。


### <a name="enumerate-application-specific-permissions"></a>アプリケーション固有のアクセス許可を列挙する

アプリケーション固有のアクセス許可の列挙を実行できる場合もあります。 たとえば、[こちらのスクリプト](https://gallery.technet.microsoft.com/office/SharePoint-Online-c9ec4f64)を使用して、SharePoint Online のアクセス許可レポートをプログラムで生成することもできます。

ビジネスセンシティブ アプリやビジネスクリティカル アプリへのすべてのアクセスを厳密に調査して、あらゆる外部アクセスを完全に把握しましょう。

### <a name="detect-ad-hoc-sharing"></a>アドホック共有の検出
利用している電子メールとネットワーク プランが対応していれば、電子メールや未承認の SaaS (サービスとしてのソフトウェア) アプリによって共有されているコンテンツを調査することもできます。 [Microsoft 365 Data Loss Protection](/microsoft-365/compliance/data-loss-prevention-policies) は、Microsoft 365 のインフラストラクチャ内で機密情報が誤って共有されていないかどうかの確認や、そのような事態の回避および監視に役立ちます。 [Microsoft Cloud App Security](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/cloud-app-security) は、お使いの環境で未承認の SaaS アプリが使用されている状況を特定するのに役立ちます。

## <a name="next-steps"></a>次のステップ

リソースへの外部アクセスをセキュリティで保護する方法については、次の記事を参照してください。 アクションは、表示されている順序で実行することをお勧めします。

1. [外部アクセスに対するセキュリティ体制を決定する](1-secure-access-posture.md)

2. [現在の状態を理解する](2-secure-access-current-state.md) (このページのトピック)

3. [ガバナンス プランを作成する](3-secure-access-plan.md)

4. [セキュリティにグループを使用する](4-secure-access-groups.md)

5. [Azure AD B2B に移行する](5-secure-access-b2b.md)

6. [エンタイトルメント管理を使用してアクセスをセキュリティで保護する](6-secure-access-entitlement-managment.md)

7. [条件付きアクセス ポリシーを使用してアクセスをセキュリティで保護する](7-secure-access-conditional-access.md)

8. [秘密度ラベルを使用してアクセスをセキュリティで保護する](8-secure-access-sensitivity-labels.md)

9. [Microsoft Teams、OneDrive、SharePoint へのアクセスをセキュリティで保護する](9-secure-access-teams-sharepoint.md)