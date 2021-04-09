---
title: B2B コラボレーション ユーザーの条件付きアクセス - Azure AD
description: Azure Active Directory B2B ユーザーに多要素認証ポリシーを強制する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74bfa4987f584bbd3490bc5f4f187dee5bc1bd87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646284"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>B2B コラボレーション ユーザーの条件付きアクセス

この記事では、組織で B2B ゲスト ユーザーが自身のリソースにアクセスできるように条件付きアクセス (CA) ポリシーのスコープを設定する方法について説明します。
>[!NOTE]
>この認証または認可フローは、ゲスト ユーザーの場合とその ID プロバイダー (IdP) の既存のユーザーの場合とで少し異なります。

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>外部ディレクトリからの B2B ゲスト ユーザーの認証フロー

このフローを次の図に示します。![外部ディレクトリからの B2B ゲスト ユーザーの認証フローを示す図](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| 手順 | 説明 |
|--------------|-----------------------|
| 1. | B2B ゲスト ユーザーが、あるリソースへのアクセスを要求します。 このリソースで、ユーザーをそのリソース テナント (信頼された IdP) にリダイレクトします。|
| 2. | リソース テナントで、ユーザーを外部として識別し、B2B ゲスト ユーザーの IdP にリダイレクトします。 ユーザーは IdP でプライマリ認証を実行します。
| 3. | B2B ゲスト ユーザーの IdP で、ユーザーにトークンを発行します。 ユーザーはトークンとともにリソース テナントにリダイレクトされます。 リソース テナントで、トークンを検証した後、ユーザーをその CA ポリシーに照らして評価します。 たとえば、ユーザーはリソース テナントで Azure Active Directory (AD) Multi-Factor Authentication を実行するように求められる場合があります。
| 4. | リソース テナントのすべての CA ポリシーが満たされると、リソース テナントで独自のトークンを発行し、ユーザーをそのリソースにリダイレクトします。

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>ワンタイム パスコードによる B2B ゲスト ユーザーの認証フロー

このフローを次の図に示します。![ワンタイム パスコードによる B2B ゲスト ユーザーの認証フローを示す図](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| 手順 | 説明 |
|--------------|-----------------------|
| 1. |ユーザーが、別のテナントのリソースへのアクセスを要求します。 このリソースで、ユーザーをそのリソース テナント (信頼された IdP) にリダイレクトします。|
| 2. | リソース テナントで、ユーザーを[外部のメール ワンタイム パスコード (OTP) ユーザー](./one-time-passcode.md)として識別し、OTP を含むメールをユーザーに送信します。|
| 3. | ユーザーが OTP を取得し、コードを送信します。 リソース テナントで、ユーザーをその CA ポリシーに照らして評価します。
| 4. | すべての CA ポリシーが満たされると、リソース テナントでトークンを発行し、ユーザーをそのリソースにリダイレクトします。 |

>[!NOTE]
>ユーザーが外部のリソース テナントに属している場合、B2B ゲスト ユーザーの IdP CA ポリシーを同時に評価することはできません。 現時点では、リソース テナントの CA ポリシーのみがそのゲストに適用されます。

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>B2B ユーザーの Azure AD Multi-Factor Authentication

組織では、B2B ゲスト ユーザーに Azure AD Multi-Factor Authentication ポリシーを適用できます。 これらのポリシーは、組織のフルタイムの従業員とメンバーに対して有効にするのと同じ方法で、テナント レベル、アプリ レベル、または個々のユーザー レベルで適用できます。
ゲスト ユーザーの組織に多要素認証機能がある場合でも、ユーザーの Azure AD Multi-Factor Authentication は常にリソース テナントによって実行されます。 次に例を示します。

1. Fabrikam という会社の管理者またはインフォメーション ワーカーが、Contoso という別の会社のユーザーを自社のアプリである Woodgrove に招待します。

2. Fabrikam の Woodgrove アプリは、アクセス時に Azure AD Multi-Factor Authentication を要求するように構成されています。

3. Contoso の B2B ゲスト ユーザーが Fabrikam テナントの Woodgrove にアクセスしようとすると、Azure AD Multi-Factor Authentication で必要な情報を入力するように求められます。

4. その後、ゲスト ユーザーは Fabrikam で Azure AD Multi-Factor Authentication を設定し、オプションを選択できます。

5. このシナリオは、Azure AD や個人用 Microsoft アカウント (MSA) など、どの ID でも機能します。 たとえば、Contoso のユーザーがソーシャル ID を使用して認証するとします。

6. Fabrikam は、Azure AD Multi-Factor Authentication をサポートするのに十分な Azure AD Premium ライセンスを保有している必要があります。 その後、Contoso のユーザーはこの Fabrikam のライセンスを使用します。 B2B ライセンスの詳細については、「[Azure AD External Identities の課金モデル](./external-identities-pricing.md)」を参照してください。

>[!NOTE]
>予測可能性を確保するため、Azure AD Multi-Factor Authentication はリソース テナントで実行されます。

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>B2B ユーザーの Azure AD Multi-Factor Authentication の設定

B2B コラボレーション ユーザーの Azure AD Multi-Factor Authentication を設定するには、次のビデオをご覧ください。

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>B2B ユーザーが招待に応じるための Azure AD Multi-Factor Authentication

招待に応じるための Azure AD Multi-Factor Authentication エクスペリエンスの詳細については、次のビデオをご覧ください。

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>B2B ユーザーの Azure AD Multi-Factor Authentication リセット

現時点では、次の PowerShell コマンドレットを使用して B2B ゲスト ユーザーを証明できます。

1. Azure への接続

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. すべてのユーザーと証明方法を取得します。

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   たとえば次のようになります。

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. 特定のユーザーの Azure AD Multi-Factor Authentication 方法を、B2B コラボレーション ユーザーにもう一度証明方法の設定を要求するように再設定します。 
   たとえば次のようになります。

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>B2B ユーザーの条件付きアクセス

B2B ゲスト ユーザーの CA ポリシーに影響を与えるさまざまな要因があります。

### <a name="device-based-conditional-access"></a>デバイスベースの条件付きアクセス

CA には、ユーザーの[デバイスが準拠しているか、Hybrid Azure AD 参加済みである](../conditional-access/concept-conditional-access-conditions.md#device-state-preview)ことを要求するオプションがあります。 B2B ゲスト ユーザーは、リソース テナントが自身のデバイスを管理できる場合にのみ、コンプライアンスを満たすことができます。 デバイスを一度に複数の組織で管理することはできません。 B2B ゲスト ユーザーは、オンプレミスの AD アカウントを持っていないため、Hybrid Azure AD 参加を満たすことができません。 ゲスト ユーザーは、自身のデバイスがアンマネージドである場合にのみ、リソース テナントにデバイスを登録して、その後デバイスを準拠させることができます。 その後、ユーザーは許可制御を満たすことができます。

>[!Note]
>外部ユーザーにマネージド デバイスを要求することはお勧めしません。

### <a name="mobile-application-management-policies"></a>モバイル アプリケーション管理ポリシー

CA の **承認済みクライアント アプリを必須にする** および **アプリの保護ポリシーを必須にする** ポリシーなどの許可制御では、テナントにデバイスを登録する必要があります。 これらの制御は、[iOS および Android デバイス](../conditional-access/concept-conditional-access-conditions.md#device-platforms)にのみ適用できます。 しかし、B2B ゲスト ユーザーのデバイスが別の組織によってすでに管理されている場合は、これらの制御をユーザーに適用できません。 モバイル デバイスを一度に複数のテナントに登録することはできません。 モバイル デバイスが別の組織によって管理されている場合、ユーザーはブロックされます。 ゲスト ユーザーは、自身のデバイスがアンマネージドである場合にのみ、リソース テナントにデバイスを登録できます。 その後、ユーザーは許可制御を満たすことができます。  

>[!NOTE]
>外部ユーザーにアプリ保護ポリシーを要求することはお勧めしません。

### <a name="location-based-conditional-access"></a>場所ベースの条件付きアクセス

招待側組織でパートナー組織を定義する信頼された IP アドレス範囲を作成できる場合は、IP 範囲に基づく[場所ベースの条件付きアクセス ポリシー](../conditional-access/concept-conditional-access-conditions.md#locations)を適用できます。

**地理的な場所** に基づいてポリシーを適用することもできます。

### <a name="risk-based-conditional-access"></a>リスクベースの条件付きアクセス

B2B ゲスト ユーザーが許可制御を満たしている場合は、[サインイン リスク ポリシー](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)が適用されます。 たとえば、組織で、サインイン リスクが中または高のときに Azure AD Multi-Factor Authentication を要求できます。 ただし、ユーザーがリソース テナントで以前に Azure AD Multi-Factor Authentication に登録したことがない場合、そのユーザーはブロックされます。 これは、正当なユーザーのパスワードが侵害された場合に、悪意のあるユーザーが独自の Azure AD Multi-Factor Authentication 資格情報を登録するのを防ぐために行われます。

ただし、[ユーザー リスク ポリシー](../conditional-access/concept-conditional-access-conditions.md#user-risk)はリソース テナントで解決できません。 たとえば、リスクの高いゲスト ユーザーに対してパスワードの変更を要求した場合、リソース ディレクトリ内のパスワードをリセットできないため、そのユーザーはブロックされます。

### <a name="conditional-access-client-apps-condition"></a>条件付きアクセスのクライアント アプリの条件

[クライアント アプリの条件](../conditional-access/concept-conditional-access-conditions.md#client-apps)は、B2B ゲスト ユーザーに対しても、他の種類のユーザーの場合と同じように動作します。 たとえば、ゲスト ユーザーがレガシ認証プロトコルを使用するのを防ぐことができます。

### <a name="conditional-access-session-controls"></a>条件付きアクセのセッション制御

[セッション制御](../conditional-access/concept-conditional-access-session.md)は、B2B ゲスト ユーザーに対しても、他の種類のユーザーの場合と同じように動作します。

## <a name="next-steps"></a>次のステップ

詳細については、Azure AD B2B コラボレーションに関する以下の記事をご覧ください。

- [Azure AD B2B コラボレーションとは](./what-is-b2b.md)
- [Identity Protection と B2B ユーザー](../identity-protection/concept-identity-protection-b2b.md)
- [External Identities の価格](https://azure.microsoft.com/pricing/details/active-directory/)
- [よく寄せられる質問 (FAQ)](./faq.md)