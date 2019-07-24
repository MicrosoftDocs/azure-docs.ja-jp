---
title: Azure Active Directory B2C のスターター パックのカスタム ポリシーについて | Microsoft Docs
description: Azure Active Directory B2C のカスタム ポリシーに関するトピック。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: dbd57e53fbaec4368e3c8ce9600bce1d37bcab8f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173209"
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Azure AD B2C カスタム ポリシー スターター パックのカスタム ポリシーについて

このセクションでは、B2C_1A_base ポリシーのすべての主要な要素を一覧で示します。このポリシーは**スターター パック**に付属しており、"*B2C_1A_base_extensions ポリシー*" の継承を通じて独自のポリシーを作成する際に利用します。

そのため、より具体的に言うと、既に定義されている要求の種類、要求の変換、コンテンツの定義、要求プロバイダーとその技術プロファイル、主要なユーザー体験について説明します。

> [!IMPORTANT]
> Microsoft は、以下の情報に対して、明示または黙示を問わず、一切の保証を行わないものとします。 一般公開の時点であるか、その前後であるかに関係なく、変更は随時行われる可能性があります。

独自のポリシーでも、B2C_1A_base_extensions ポリシーでも、必要な定義を追加することで、これらの定義をオーバーライドしてこの親ポリシーを拡張できます。

"*B2C_1A_base ポリシー*" の主要な要素は、要求の種類、要求の変換、コンテンツの定義です。 これらの要素は、独自のポリシーと "*B2C_1A_base_extensions ポリシー*" で参照できます。

## <a name="claims-schemas"></a>要求スキーマ

この要求スキーマは、次の 3 つのセクションに分かれています。

1.  最初のセクションでは、ユーザー体験を正常に機能させるのに必要な最低限の要求を一覧で示します。
2.  2 番目のセクションでは、クエリ文字列パラメーターとその他の特別なパラメーターを別の要求プロバイダー (特に認証用の login.microsoftonline.com) に渡すために必要な要求を一覧で示します。 **これらの要求は変更しないようにしてください**。
3.  最後に 3 番目のセクションでは、ユーザーから収集してディレクトリに格納し、サインイン中にトークンで送信できる省略可能な追加の要求を一覧で表示します。 ユーザーから収集され、トークンで送信される新しい要求の種類は、このセクションに追加できます。

> [!IMPORTANT]
> 要求スキーマには、パスワードやユーザー名といった特定の要求に関する制限があります。 信頼フレームワーク (TF) ポリシーでは Azure AD が他の要求プロバイダーと同様に処理され、そのすべての制限がカスタム ポリシーでモデル化されます。 ポリシーは制限をさらに追加するために修正できます。また、独自の制限のある資格情報ストレージのために別の要求プロバイダーをポリシーで使用することもできます。

使用できる要求の種類を次に一覧で示します。

### <a name="claims-that-are-required-for-the-user-journeys"></a>ユーザー体験に必要な要求

次の要求は、ユーザー体験を正常に機能させるために必要です。

| 要求の種類 | 説明 |
|-------------|-------------|
| *UserId* | ユーザー名 |
| *signInName* | サインイン名 |
| *tenantId* | Azure AD B2C におけるユーザー オブジェクトのテナント識別子 (ID) |
| *objectId* | Azure AD B2C におけるユーザー オブジェクトのオブジェクト識別子 (ID) |
| *password* | パスワード |
| *newPassword* | |
| *reenterPassword* | |
| *passwordPolicies* | パスワード強度や有効期限などを決定するために Azure AD B2C によって使用されるパスワード ポリシー。 |
| *sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | ユーザーの電話番号 |
| *Verified.strongAuthenticationPhoneNumber* | |
| *email* | ユーザーに連絡するために使用できるメール アドレス |
| *signInNamesInfo.emailAddress* | ユーザーがサインインに使用できるメール アドレス |
| *otherMails* | ユーザーに連絡するために使用できるメール アドレス |
| *userPrincipalName* | Azure AD B2C に格納されるユーザー名 |
| *upnUserName* | ユーザー プリンシパル名を作成するためのユーザー名 |
| *mailNickName* | Azure AD B2C に格納されるユーザーの電子メール ニックネーム |
| *newUser* | |
| *executed-SelfAsserted-Input* | ユーザーから属性が収集されたかどうかを示す要求 |
| *executed-PhoneFactor-Input* | ユーザーから新しい電話番号が収集されたかどうかを示す要求 |
| *authenticationSource* | ソーシャル ID プロバイダー、login.microsoftonline.com、またはローカル アカウントでユーザーが認証されたかどうかを示します |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>クエリ文字列パラメーターとその他の特別なパラメーターに必要な要求

次の要求は、(一部のクエリ文字列パラメーターを含め) 特別なパラメーターをその他の要求プロバイダーに渡すのに必要です。

| 要求の種類 | 説明 |
|-------------|-------------|
| *nux* | ローカル アカウント認証のために login.microsoftonline.com に渡される特別なパラメーター |
| *nca* | ローカル アカウント認証のために login.microsoftonline.com に渡される特別なパラメーター |
| *prompt* | ローカル アカウント認証のために login.microsoftonline.com に渡される特別なパラメーター |
| *mkt* | ローカル アカウント認証のために login.microsoftonline.com に渡される特別なパラメーター |
| *lc* | ローカル アカウント認証のために login.microsoftonline.com に渡される特別なパラメーター |
| *grant_type* | ローカル アカウント認証のために login.microsoftonline.com に渡される特別なパラメーター |
| *scope* | ローカル アカウント認証のために login.microsoftonline.com に渡される特別なパラメーター |
| *client_id* | ローカル アカウント認証のために login.microsoftonline.com に渡される特別なパラメーター |
| *objectIdFromSession* | SSO セッションからオブジェクト ID が取得されたことを示すために、既定のセッション管理プロバイダーによって提供されるパラメーター |
| *isActiveMFASession* | ユーザーがアクティブな MFA セッションを使用していることを示すために、MFA セッション管理によって提供されるパラメーター |

### <a name="additional-optional-claims-that-can-be-collected"></a>収集できる追加の (省略可能な) 要求

次の要求は、ユーザーから収集してディレクトリに格納し、トークンで送信できる追加の要求です。 前に説明したとおり、追加の要求はこの一覧に追加できます。

| 要求の種類 | 説明 |
|-------------|-------------|
| *givenName* | ユーザーの名 (または名前) |
| *surname* | ユーザーの姓 (または名字) |
| *Extension_picture* | ソーシャルから取得したユーザーの画像 |

## <a name="claim-transformations"></a>要求の変換

使用できる要求の変換を次に一覧で示します。

| 要求の変換 | 説明 |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>コンテンツの定義

このセクションでは、*B2C_1A_base* ポリシーで既に宣言されているコンテンツの定義について説明します。 これらのコンテンツの定義は、必要に応じて独自のポリシーと *B2C_1A_base_extensions* ポリシーで参照、オーバーライド、または拡張することができます。

| 要求プロバイダー | 説明 |
|-----------------|-------------|
| *Facebook* | |
| "*ローカル アカウント サインイン*" | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| "*セルフ アサート*" | |
| "*ローカル アカウント*" | |
| *セッションの管理* | |
| "*Trustframework ポリシー エンジン*" | |
| *TechnicalProfiles* | |
| "*トークン発行者*" | |

## <a name="technical-profiles"></a>技術プロファイル

このセクションでは、*B2C_1A_base* ポリシーで要求プロバイダーごとに既に宣言されている技術プロファイルについて説明します。 これらの技術プロファイルは、必要に応じて独自のポリシーと *B2C_1A_base_extensions* ポリシーでさらに参照、オーバーライド、または拡張することができます。

### <a name="technical-profiles-for-facebook"></a>Facebook の技術プロファイル

| 技術プロファイル | 説明 |
|-------------------|-------------|
| *Facebook-OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>ローカル アカウント サインインの技術プロファイル

| 技術プロファイル | 説明 |
|-------------------|-------------|
| *Login-NonInteractive* | |

### <a name="technical-profiles-for-phone-factor"></a>PhoneFactor の技術プロファイル

| 技術プロファイル | 説明 |
|-------------------|-------------|
| *PhoneFactor-Input* | |
| *PhoneFactor-InputOrVerify* | |
| *PhoneFactor-Verify* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Azure Active Directory の技術プロファイル

| 技術プロファイル | 説明 |
|-------------------|-------------|
| *AAD-Common* | その他の AAD-xxx 技術プロファイルに含まれる技術プロファイル |
| *AAD-UserWriteUsingAlternativeSecurityId* | ソーシャル ログインの技術プロファイル |
| *AAD-UserReadUsingAlternativeSecurityId* | ソーシャル ログインの技術プロファイル |
| *AAD-UserReadUsingAlternativeSecurityId-NoError* | ソーシャル ログインの技術プロファイル |
| *AAD-UserWritePasswordUsingLogonEmail* | ローカル アカウントの技術プロファイル |
| *AAD-UserReadUsingEmailAddress* | ローカル アカウントの技術プロファイル |
| *AAD-UserWriteProfileUsingObjectId* | objectId を使用してユーザー レコードを更新するための技術プロファイル |
| *AAD-UserWritePhoneNumberUsingObjectId* | objectId を使用してユーザー レコードを更新するための技術プロファイル |
| *AAD-UserWritePasswordUsingObjectId* | objectId を使用してユーザー レコードを更新するための技術プロファイル |
| *AAD-UserReadUsingObjectId* | ユーザー認証後のデータ読み取りに使用される技術プロファイル |

### <a name="technical-profiles-for-self-asserted"></a>セルフ アサートの技術プロファイル

| 技術プロファイル | 説明 |
|-------------------|-------------|
| *SelfAsserted-Social* | |
| *SelfAsserted-ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>ローカル アカウントの技術プロファイル

| 技術プロファイル | 説明 |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>セッション管理の技術プロファイル

| 技術プロファイル | 説明 |
|-------------------|-------------|
| *SM-Noop* | |
| *SM-AAD* | |
| *SM-SocialSignup* | サインアップとサインインの間に AAD セッションを特定するために使用されているプロファイル名 |
| *SM-SocialLogin* | |
| *SM-MFA* | |

### <a name="technical-profiles-for-the-trust-framework-policy-engine"></a>信頼フレームワーク ポリシー エンジンの技術プロファイル

現時点では、**Trustframework ポリシー エンジン TechnicalProfiles** 要求プロバイダーの技術プロファイルは定義されていません。

### <a name="technical-profiles-for-token-issuer"></a>トークン発行者の技術プロファイル

| 技術プロファイル | 説明 |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>ユーザー体験

このセクションでは、*B2C_1A_base* ポリシーで既に宣言されているユーザー体験について説明します。 これらのユーザー体験は、必要に応じて独自のポリシーと *B2C_1A_base_extensions* ポリシーでさらに参照、オーバーライド、または拡張することができます。

| ユーザー体験 | 説明 |
|--------------|-------------|
| *SignUp* | |
| *SignIn* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
