---
title: B2B コラボレーション ユーザーの条件付きアクセス - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B コラボレーションでは、会社のアプリケーションへの選択的なアクセスのために、多要素認証 (MFA) をサポートしています
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: c012ebfb00b57079e43b74b4f6570744db980860
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113154"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>B2B コラボレーション ユーザーの条件付きアクセス

## <a name="multi-factor-authentication-for-b2b-users"></a>B2B ユーザーの多要素認証
Azure AD B2B コラボレーションでは、組織は B2B ユーザー向けの多要素認証 (MFA) ポリシーを適用することができます。 これらのポリシーは、組織のフルタイムの従業員とメンバーに対して有効にするのと同じ方法で、テナント レベル、アプリ レベル、または個々のユーザー レベルで適用できます。 MFA ポリシーは、リソース組織で適用します。

例:
1. 会社 A の管理者またはインフォメーション ワーカーが、会社 B のユーザーを会社 A のアプリケーションである *Foo* に招待します。
2. 会社 A のアプリケーション *Foo* は、アクセス時に MFA を要求するように構成されています。
3. 会社 B のユーザーが会社 A テナントのアプリ *Foo* にアクセスしようとすると、MFA チャレンジを完了するように求められます。
4. ユーザーは、会社 A で MFA をセットアップし、MFA オプションを選択することができます。
5. このシナリオは、どの ID でも機能します (会社 B のユーザーがソーシャル ID を使用して認証を行う場合は、Azure AD や MSA など)。
6. 会社 A は、MFA をサポートしている Premium Azure AD ライセンスを所有している必要があります。 会社 B のユーザーは、A 社のこのライセンスを使用します。

パートナー組織が MFA 機能を持っている場合でも、パートナー組織のユーザーに対する MFA は、常に招待側テナントが責任をもって実行します。

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>B2B コラボレーション ユーザー用の MFA のセットアップ
B2B コラボレーション ユーザー用の MFA のセットアップがいかに簡単かについては、次のビデオをご覧ください。

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>B2B ユーザーがオファーに応じるための MFA エクスペリエンス
次のアニメーションで、招待に応じるためのエクスペリエンスをご覧ください。

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>B2B コラボレーション ユーザーの MFA リセット
現時点では、管理者は次の PowerShell コマンドレットを使用することによってのみ、B2B コラボレーション ユーザーにもう一度証明を要求できます。

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

3. 特定のユーザーの MFA 方法を、B2B コラボレーション ユーザーにもう一度証明方法の設定を要求するように再設定します。 例:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>リソース テナントで MFA を実行する理由

現在のリリースでは、MFA は常にリソース テナントで実行されます。その理由は予測可能性にあります。 たとえば、Contoso ユーザー (Sally) が Fabrikam に招待され、Fabrikam が B2B ユーザーの MFA を有効にしたとします。

Contoso に App1 が有効で App2 が有効ではない MFA ポリシーが設定されている場合、トークン内の Contoso の MFA 要求を調べると、次のような問題が考えられます。

* ある日: Contoso の MFA に登録し、App1 にアクセスできるユーザーは、Fabrikam で追加の MFA プロンプトが表示されることはありません。

* 別の日: Contoso で App 2 にアクセスできるユーザーが Fabrikam にアクセスした場合、Fabrikam の MFA に登録する必要があります。

このプロセスは、混乱を招く可能性があり、サインインが完了しない場合があります。

さらに、Contoso に MFA の機能があっても、Fabrikam が Contoso の MFA ポリシーを信頼するとは限りません。

最後に、リソース テナントの MFA は、MSA とソーシャル ID、および MFA をセットアップしていないパートナー組織でも機能します。

したがって、B2B ユーザーの MFA では、常に招待側テナントで MFA を要求することをお勧めします。 この要件によって MFA が重複する可能性がありますが、招待側テナントにアクセスするときのエンドユーザーのエクスペリエンスは常に予測可能になり、Sally は招待側テナントで MFA の登録を行う必要があります。

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>B2B ユーザーのデバイス、場所、およびリスクに基づく条件付きアクセス

Contoso が会社のデータに対するデバイス ベースの条件付きアクセス ポリシーを有効にした場合、Contoso の管理対象外であり、Contoso のデバイス ポリシーに準拠していないデバイスからのアクセスは禁止されます。

B2B ユーザーのデバイスが Contoso によって管理されていない場合、このポリシーが適用される環境では、常にパートナー組織の B2B ユーザーのアクセスはブロックされます。 ただし、Contoso は、デバイス ベースの条件付きアクセス ポリシーから除外する特定のパートナー ユーザーを含む除外リストを作成できます。

#### <a name="location-based-conditional-access-for-b2b"></a>B2B 向けの場所ベースの条件付きアクセス

招待側組織がパートナー組織を定義する信頼できる IP アドレス範囲を作成できる場合は、B2B ユーザーに対して場所ベースの条件付きアクセス ポリシーを適用できます。

#### <a name="risk-based-conditional-access-for-b2b"></a>B2B 向けのリスクベースの条件付きアクセス

現時点では、リスク評価は B2B ユーザーのホーム組織で実行されるため、B2B ユーザーに対してリスクベースのサインイン ポリシーを適用することはできません。

## <a name="next-steps"></a>次の手順

Azure AD B2B コラボレーションに関する以下の記事を参照してください。

* [Azure AD B2B コラボレーションとは](what-is-b2b.md)
* [Azure AD B2B コラボレーションのライセンス](licensing-guidance.md)
* [Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)](faq.md)
