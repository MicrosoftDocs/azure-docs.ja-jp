---
title: Azure Active Directory のセキュリティ デフォルト
description: 組織を一般的な攻撃から保護するために設計されたセキュリティ デフォルトのポリシー
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: d899f477612e4c738314187f61551fe5c0b17f8d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932164"
---
# <a name="what-are-security-defaults"></a>セキュリティ デフォルトとは

ID 関連の一般的な攻撃がますます広まる中で、セキュリティの管理に困難をきたす場合があります。 このような攻撃として、パスワード スプレー、リプレイ、フィッシングなどがあります。

Azure Active Directory (Azure AD) のセキュリティ既定値は、セキュリティの実現をいっそう容易にし、組織を保護するために役立ちます。 セキュリティ既定値には、一般的な攻撃に対して事前に構成されたセキュリティ設定が含まれています。 

Microsoft では、誰もがセキュリティ既定値を利用できるようにしています。 目標は、すべての組織が追加の費用なしで基本レベルのセキュリティを確実に有効にできるようにすることです。 セキュリティ既定値は、Azure portal で有効にします。

![セキュリティ デフォルトを有効にするためのトグルがある Azure portal のスクリーンショット](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
テナントでは、次のセキュリティ構成が有効になります。 

## <a name="unified-multi-factor-authentication-registration"></a>多要素認証の登録手続きの統一

テナント内のすべてのユーザーは、Azure Multi-Factor Authentication サービスのフォームを使用して多要素認証 (MFA) に登録する必要があります。 ユーザーは 14 日以内に Microsoft Authenticator アプリを使用して Multi-Factor Authentication に登録する必要があります。 14 日が経過すると、ユーザーは Multi-Factor Authentication への登録が完了するまでサインインできなくなります。

セキュリティ デフォルトを有効にした直後の 14 日以内に出勤またはログインしない場合があることは理解しています。 ユーザー全員に Multi-Factor Authentication の登録のための十分な時間を与えられるように、14 日の期間はユーザーごとに固有です。 14 日の期間は、セキュリティ デフォルトが有効になった後、それぞれのユーザーの対話型サインインが最初に成功した時点から始まります。

## <a name="multi-factor-authentication-enforcement"></a>Multi-Factor Authentication の強制

### <a name="protecting-administrators"></a>管理者の保護

特権アカウントにアクセスできるユーザーは、より自由に環境にアクセスできます。 これらのアカウントには権限があるので、特別な注意を払って対処する必要があります。 特権アカウントの保護を強化するための一般的な方法の 1 つは、サインイン時に、強力な形式のアカウント検証を必須にすることです。 Azure AD では、Multi-Factor Authentication を必須にすることで、アカウント検証を強力にすることができます。

次の 9 つの Azure AD 管理者ロールについては、Multi-Factor Authentication への登録が完了した後、サインインのたびに追加の認証を実行する必要があります。

- 全体管理者
- SharePoint 管理者
- Exchange 管理者
- 条件付きアクセス管理者
- セキュリティ管理者
- ヘルプデスク管理者またはパスワード管理者
- 課金管理者
- ユーザー管理者
- 認証管理者

### <a name="protecting-all-users"></a>すべてのユーザーの保護

認証の追加のレイヤーが必要なアカウントは管理者アカウントだけであると考えがちです。 管理者は、機密情報への広範なアクセス権を持ち、サブスクリプション全体の設定に変更を加えることができます。 しかし、攻撃者はエンド ユーザーをターゲットにする傾向があります。 

これらの攻撃者は、アクセス権を取得した後、元のアカウント所有者に代わって機密性の高い情報へのアクセスを要求できます。 ディレクトリ全体をダウンロードして、組織全体に対してフィッシング攻撃を実行することさえできます。 

すべてのユーザーを対象にした保護を向上させるための一般的な方法の 1 つは、全員に Multi-Factor Authentication を要求するなど、より強力な形式のアカウント検証を要求することです。 ユーザーが Multi-Factor Authentication の登録を完了すると、必要に応じて追加の認証を求められるようになります。

### <a name="blocking-legacy-authentication"></a>レガシ認証をブロックする

ユーザーがクラウド アプリに簡単にアクセスできるように、Azure AD ではレガシ認証を含め、幅広い認証プロトコルがサポートされています。 "*レガシ認証*" は、以下のものによって行われる認証要求を指す用語です。

- 先進認証を使用していない古い Office クライアント (Office 2010 クライアントなど)。
- IMAP、SMTP、POP3 などの古いメール プロトコルを使用しているクライアント。

今日では、不正侵入を意図したサインイン試行の大部分がレガシ認証によるものです。 レガシ認証では、Multi-Factor Authentication がサポートされていません。 ディレクトリで Multi-Factor Authentication ポリシーが有効になっている場合でも、攻撃者は古いプロトコルを使用して認証を受け、Multi-Factor Authentication をバイパスできます。 

テナントでセキュリティ デフォルトが有効になった後は、古いプロトコルによるすべての認証要求がブロックされます。 Exchange ActiveSync は、セキュリティ既定値ではブロックされません。

### <a name="protecting-privileged-actions"></a>特権アクションの保護

組織では、Azure Resource Manager API によって管理される、次のような各種の Azure サービスを使用します。

- Azure ポータル 
- Azure PowerShell 
- Azure CLI

Azure Resource Manager を使用してご自身のサービスを管理する操作は、高い権限が与えられているアクションです。 Azure Resource Manager では、サービス設定、サブスクリプションの課金など、テナント全体の構成を変更できます。 単一要素認証は、フィッシング、パスワード スプレーなどの各種の攻撃に対して脆弱です。 

Azure Resource Manager にアクセスして構成を更新しようとするユーザーの ID を検証することが重要です。 アクセスを許可する前に、追加の認証を要求して ID を検証します。

テナントでセキュリティ デフォルトを有効にすると、Azure portal、Azure PowerShell、または Azure CLI にアクセスしようとしているユーザーがいずれも、追加の認証を完了しなければならなくなります。 このポリシーは、Azure Resource Manager にアクセスしようとしているユーザーであれば、管理者であるかユーザーであるかに関係なく全員に適用されます。 

ユーザーが Multi-Factor Authentication に登録していない場合、そのユーザーが操作を続行するには、Microsoft Authenticator アプリを使用してユーザー登録を行う必要があります。 Multi-Factor Authentication に登録するための 14 日の期間は提供されません。

## <a name="deployment-considerations"></a>デプロイに関する考慮事項

テナントに対するセキュリティ既定値のデプロイに関連したその他の考慮事項を次に示します。

### <a name="older-protocols"></a>古いプロトコル

メール クライアントでは、認証要求に際して古い認証プロトコル (IMAP、SMTP、POP3 など) が使用されます。 これらのプロトコルでは、Multi-Factor Authentication がサポートされていません。 Microsoft が認識しているアカウント侵害のほとんどは、Multi-Factor Authentication をバイパスしようとする、古いプロトコルに対する攻撃によるものです。 

管理者アカウントへのサインインで確実に Multi-Factor Authentication を要求し、攻撃者がこれをバイパスできないようにするために、セキュリティ既定値では、古いプロトコルから管理者アカウントに対して行われる認証要求がすべてブロックされます。

> [!WARNING]
> この設定を有効にする前に、管理者が古い認証プロトコルを使用していないことを確認してください。 詳細については、[レガシ認証から移行する方法](concept-fundamentals-block-legacy-authentication.md)に関するページを参照してください。

### <a name="conditional-access"></a>条件付きアクセス

条件付きアクセスを使用してポリシーを構成し、セキュリティ既定値を使った動作と同じ動作を実現することもできます。 条件付きアクセスを使用しており、環境で条件付きアクセス ポリシーを有効にしている場合、セキュリティ既定値は使用できません。 条件付きアクセスが利用できるライセンスを持っていても、環境で条件付きアクセス ポリシーが有効になっていない場合には、条件付きアクセス ポリシーを有効にしない限り、セキュリティ既定値を使用できます。

![セキュリティ既定値と条件付きアクセスは併用不可であるという警告メッセージ](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

以下は、条件付きアクセスを使用して同等のポリシーを構成する方法のステップバイステップ ガイドです。

- [管理者に対して MFA を必須にする](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Azure 管理のために MFA を必須にする](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [レガシ認証をブロックする](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [すべてのユーザーに対して MFA を必須にする](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Azure MFA への登録を必須とする](../identity-protection/howto-identity-protection-configure-mfa-policy.md) - Azure AD Identity Protection が必要です

## <a name="enabling-security-defaults"></a>セキュリティ デフォルトの有効化

ディレクトリでセキュリティ デフォルトを有効にするには、次のようにします。

1. セキュリティ管理者、条件付きアクセス管理者、またはグローバル管理者として、 [Azure portal](https://portal.azure.com)  にサインインします。
1.  **[Azure Active Directory]**  > **[プロパティ]** の順に移動します。
1. **[セキュリティの既定値の管理]** を選択します。
1. **[Enable security defaults]\(セキュリティ デフォルトを有効にする\)** トグルを **[はい]** に設定します。
1. **[保存]** を選択します。

## <a name="disabling-security-defaults"></a>セキュリティの既定値を無効にする

セキュリティの既定値を置き換える条件付きアクセス ポリシーを実装する組織では、セキュリティの既定値を無効にする必要があります。 

![条件付きアクセス ポリシーを有効にするためにセキュリティの既定値を無効にする警告メッセージ](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

ディレクトリでセキュリティの既定値を無効にするには、次のようにします。

1. セキュリティ管理者、条件付きアクセス管理者、またはグローバル管理者として、 [Azure portal](https://portal.azure.com)  にサインインします。
1.  **[Azure Active Directory]**  > **[プロパティ]** の順に移動します。
1. **[セキュリティの既定値の管理]** を選択します。
1. **[セキュリティの既定値の有効化]** トグルを **[いいえ]** に設定します。
1. **[保存]** を選択します。

## <a name="next-steps"></a>次の手順

[一般的な条件付きアクセス ポリシー](../conditional-access/concept-conditional-access-policy-common.md)
