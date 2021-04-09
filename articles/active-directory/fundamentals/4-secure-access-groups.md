---
title: Azure Active Directory および Microsoft 365 のグループを使用して外部アクセスをセキュリティで保護する
description: 外部ユーザーがご使用のリソースにアクセスするときにセキュリティを強化するために、Azure Active Directory および Microsoft 365 グループを使用できます。
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
ms.openlocfilehash: 2d9d63c7a703987d7b17e6e03d8b5596d5f1dfa5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560618"
---
# <a name="securing-external-access-with-groups"></a>グループを使用して外部アクセスをセキュリティで保護する 

グループは、アクセス制御戦略に不可欠な要素です。 Azure Active Directory (Azure AD) セキュリティ グループおよび Microsoft 365 (M365) グループは、リソースへのアクセスをセキュリティで保護するための基礎として使用できます。

グループは、次のアクセス制御メカニズムの基礎として使用するのに最適なオプションです。

* 条件付きアクセス ポリシー

* エンタイトルメント管理アクセス パッケージ 

* M365 リソース、Microsoft Teams、SharePoint サイトへのアクセス

グループには、次のロールがあります。

* 所有者 - グループ所有者は、グループ設定とそのメンバーシップを管理します。

* メンバー - グループに割り当てられたアクセス許可とアクセスを継承するメンバー。

* ゲスト - ゲストは、組織外部のメンバーです。 

## <a name="determine-your-group-strategy"></a>グループ戦略を決定する

リソースへの外部アクセスをセキュリティで保護するためのグループ戦略を策定する際、[必要なセキュリティ体制](1-secure-access-posture.md)を検討して、次の選択を決定してください。

* **グループを作成できる必要があるのは誰ですか?** 管理者のみにグループを作成させますか、それとも従業員や外部ユーザーにもこれらのグループを作成させますか?

   * *既定では、すべてのテナント メンバーが Azure AD セキュリティ グループを作成できます*。 

      * [管理者以外のユーザーに対して portal へのアクセスを制限](../develop/howto-restrict-your-app-to-a-set-of-users.md)でき、[PowerShell](../enterprise-users/groups-troubleshooting.md) のグループ作成機能を無効にすることができます。 

      * また、[Azure Active Directory でセルフサービス グループ管理を設定する](../enterprise-users/groups-self-service-management.md)こともできます。 

   * *既定では、すべてのユーザーが M365 グループを作成でき、グループは、テナント内のすべて (内部および外部) のユーザーが参加できるように開かれています*。 

      * [Microsoft 365 グループの作成](/microsoft-365/solutions/manage-creation-of-groups)を特定のセキュリティ グループのメンバーに制限できます。 この設定を構成するには、Windows PowerShell を使用します。 

* **ユーザーをグループに招待できる必要があるのは誰ですか?** すべてのグループ メンバーが他のメンバーを追加できますか、それともグループ所有者のみがメンバーを追加できますか?

* **誰をグループに招待できますか?** 既定では、外部ユーザーをグループに追加できます。 

### <a name="assign-users-to-groups"></a>ユーザーをグループに割り当てる

ユーザーは、ユーザー オブジェクトのユーザー属性またはその他の基準のいずれかに基づいて手動でグループに割り当てることができます。 ユーザーは、ユーザーの属性に基づいてのみ動的にグループに割り当てることができます。

たとえば、次の属性に基づいてユーザーをグループに割り当てることができます。

* 特定の役職または部署

* 所属するパートナー組織 (手動、または接続されている組織を介して)

* ユーザーの種類 (メンバーまたはゲスト)

* 特定のプロジェクトへの参加 (手動)

* location

1 つのグループに、ユーザーまたはデバイスのいずれかを含めることができますが、両方を含めることはできません。 ユーザーを動的グループに割り当てるには、ユーザー属性に基づくクエリを追加します。 次の例は、ユーザーがメンバーであり (ゲストではない)、財務部に所属している場合に、そのユーザーをグループに追加するクエリを示します。

![動的メンバーシップの規則の構成を示すスクリーンショット。](media/secure-external-access/4-dynamic-membership-rules.png)

動的グループの詳細については、「[Azure Active Directory で動的グループを作成または更新する](../enterprise-users/groups-create-rule.md)」を参照してください。

### <a name="do-not-use-groups-for-multiple-purposes"></a>複数の目的でグループを使用しない

セキュリティまたはリソース アクセスの目的でグループを使用する場合、それらの機能が単一であることが重要です。 リソースへのアクセスを許可するためにグループを使用する場合、それを他の目的で使用することはできません。 場所やチーム メンバーシップの定義などの汎用的な目的でグループを使用する場合、アクセスのセキュリティ保護のためにも使用することはできません。 

セキュリティ グループには、目的を明確にする名前付け規則を使用することをお勧めします。 例:

* *Secure_access_finance_apps*

* *Team_membership_finance_team*

* *Location_finance_building*



### <a name="types-of-groups"></a>グループの種類

Azure AD セキュリティ グループおよび Microsoft 365 グループは両方とも、Azure AD ポータルまたは M365 管理ポータルから作成できます。 どちらの種類も、外部アクセスをセキュリティで保護するための基礎として使用できます。

|考慮事項 | Azure AD セキュリティ グループ (手動および動的)| Microsoft 365 グループ |
| - | - | - |
| グループには何を含めることができるか?| ユーザー<br>グループ<br>サービス原則<br>デバイス| ユーザーのみ |
| グループをどこに作成するか?| Azure AD ポータル<br>M365 ポータル (メールが有効になっている場合)<br>PowerShell<br>Microsoft Graph<br>エンド ユーザーのポータル| M365 ポータル<br>Azure AD ポータル<br>PowerShell<br>Microsoft Graph<br>Microsoft 365 アプリケーション内 |
| 既定では、誰が作成するか?| 管理者 <br>エンド ユーザー| 管理者<br>エンド ユーザー |
| 既定では、誰を追加できるか?| 内部ユーザー (メンバー)| テナント メンバーおよび任意の組織からのゲスト |
| 何に対するアクセスを許可するか?| 割り当てられているリソースのみ。| グループ関連のすべてのリソース:<br>(グループのメールボックス、サイト、チーム、チャット、その他の含まれている M365 リソース)<br>グループが追加されるその他のリソース |
| 併用できるもの| 条件付きアクセス<br>エンタイトルメント管理<br>グループ ライセンス| 条件付きアクセス<br>エンタイトルメント管理<br>秘密度ラベル |



チーム、それに関連付けられたサイトやコンテンツなどの一連の Microsoft 365 リソースを作成および管理するには、Microsoft 365 グループを使用します。 これは、プロジェクトベースの作業に最適な選択肢です。 

 

## <a name="azure-ad-security-groups"></a>Azure AD セキュリティ グループ 

[Azure AD セキュリティ グループ](./active-directory-manage-groups.md)にはユーザーまたはデバイスを含めることができ、次のものへのアクセスを管理するために使用できます 

* Microsoft 365 アプリなどの Azure リソース、および Dropbox の ServiceNow などのサービスとしてのソフトウェア (SaaS) アプリ。

* Azure のデータとサブスクリプション。

* Azure サービス。

Azure AD セキュリティ グループは、次の目的でも使用できます。

* M365、Dynamics 365、Enterprise Mobility and Security などのサービスのライセンスを割り当てる。 詳細については、[グループベースのライセンス](./active-directory-licensing-whatis-azure-portal.md)に関する記事を参照してください。

* 昇格された権限を割り当てる。 詳細については、[クラウド グループを使用してロールの割り当てを管理する (プレビュー)](../roles/groups-concept.md) に関する記事を参照してください。 

[Azure portal](./active-directory-groups-create-azure-portal.md) でグループを作成するには、[Azure Active Directory]、[グループ] の順に移動します。 [PowerShell コマンドレット](../enterprise-users/groups-settings-v2-cmdlets.md)を使用して Azure AD セキュリティ グループを作成することもできます。 

> [!NOTE]
> セキュリティ グループは、最大 1,500 のアプリケーションの割り当てに使用できますが、それ以上には使用できません。 

![セキュリティ グループの作成のスクリーンショット。](media/secure-external-access/4-create-security-group.png)

> [!IMPORTANT]
> **メールが有効なセキュリティ グループを作成するには、[Microsoft 365 管理センター](https://admin.microsoft.com/)** にアクセスします。 Azure AD ポータルで作成することはできません。 
<br>セキュリティ グループの作成時に、メールを有効にします。 後で有効にすることはできません。

### <a name="hybrid-organizations-and-azure-ad-security-groups"></a>ハイブリッド組織と Azure AD セキュリティ グループ

ハイブリッド組織には、オンプレミスのインフラストラクチャと Azure AD クラウド インフラストラクチャの両方があります。 Active Directory を使用する多くのハイブリッド組織では、セキュリティ グループをオンプレミスで作成し、それらをクラウドに同期します。 この方法を使用すると、セキュリティ グループに追加できるのは、オンプレミス内のユーザーのみになります。

**オンプレミスの侵害を利用して Microsoft 365 テナントへのアクセスが取得される危険性があるため、オンプレミスのインフラストラクチャを侵害から保護する必要があります**。 ガイダンスについては、[オンプレミスへの攻撃から Microsoft 365 を保護する方法](./protect-m365-from-on-premises-attacks.md)に関する記事を参照してください。

## <a name="microsoft-365-groups"></a>Microsoft 365 グループ

[Microsoft 365 グループ](/microsoft-365/admin/create-groups/office-365-groups)は、M365 全体でのすべてのアクセスを管理する基本的なメンバーシップ サービスです。 これは、[Azure portal](https://portal.azure.com/)、または [M365 ポータル](https://admin.microsoft.com/)から作成できます。 M365 グループを作成すると、コラボレーションに使用されるリソース グループへのアクセスを付与することになります。 これらのリソースの完全な一覧については、「[管理者向け Microsoft 365 グループの概要](/microsoft-365/admin/create-groups/office-365-groups)」を参照してください。

M365 グループでは、ロールについて次のような違いがあります。

* **所有者** - グループ所有者は、メンバーの追加または削除を行うことができます。また、共有の受信トレイから会話を削除したり、グループ設定を変更したりできる固有のアクセス許可を持つことができます。 グループ所有者は、グループ名の変更、説明や画像の更新などを行うことができます。

* **メンバー** - メンバーは、グループ内のすべてのものにアクセスできますが、グループ設定を変更することはできません。 既定では、グループのメンバーは自分のグループにゲストを招待できますが、管理者は[この設定を制御](/microsoft-365/admin/create-groups/manage-guest-access-in-groups)できます。

* **ゲスト** - グループのゲストは、組織外部のメンバーです。 既定では、ゲストに対して、チーム内の機能が多少制限されます。

 

### <a name="m365-group-settings"></a>M365 グループの設定

設定時に、電子メール エイリアス、プライバシー、チームに対してグループを有効にするかどうかを選択します。 

![Microsoft 365 グループの設定の編集を示すスクリーンショット](media/secure-external-access/4-edit-group-settings.png)

設定後、メンバーを追加し、電子メールの使用の設定などを構成します。

### <a name="next-steps"></a>次のステップ

リソースへの外部アクセスをセキュリティで保護する方法については、次の記事を参照してください。 アクションは、表示されている順序で実行することをお勧めします。

1. [外部アクセスに必要なセキュリティ体制を決定する](1-secure-access-posture.md)

2. [現在の状態を理解する](2-secure-access-current-state.md)

3. [ガバナンス プランを作成する](3-secure-access-plan.md)

4. [セキュリティにグループを使用する](4-secure-access-groups.md) (この記事)。

5. [Azure AD B2B に移行する](5-secure-access-b2b.md)

6. [エンタイトルメント管理を使用してアクセスをセキュリティで保護する](6-secure-access-entitlement-managment.md)

7. [条件付きアクセス ポリシーを使用してアクセスをセキュリティで保護する](7-secure-access-conditional-access.md)

8. [秘密度ラベルを使用してアクセスをセキュリティで保護する](8-secure-access-sensitivity-labels.md)

9. [Microsoft Teams、OneDrive、SharePoint へのアクセスをセキュリティで保護する](9-secure-access-teams-sharepoint.md)