---
title: セルフサービス パスワード リセットの連絡先情報の事前設定 - Azure Active Directory
description: Azure Active Directory セルフサービス パスワード リセット (SSPR) のユーザーの連絡先情報を事前に設定して、それらのユーザーが登録プロセスを実行せずに機能を使用できるようにする方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ed93944c2a94370250fb75a679c1ac37a2a8418c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861155"
---
# <a name="pre-populate-user-authentication-contact-information-for-azure-active-directory-self-service-password-reset-sspr"></a>Azure Active Directory セルフサービス パスワード リセット (SSPR) のユーザー認証連絡先情報の事前設定

Azure Active Directory (Azure AD) のセルフサービス パスワード リセット (SSPR) を使用するには、ユーザーの認証連絡先情報が存在する必要があります。 ユーザーに自分で認証データを登録させている組織もあります。 他に、Active Directory Domain Services (AD DS) に既に存在する認証データから同期する方法を採用している組織もあります。 ユーザーが介入しなくても、同期されたデータは Azure AD と SSPR で利用できるようになります。 ユーザーが自分のパスワードを変更またはリセットする必要がある場合、以前に連絡先情報を登録していない場合でも、ユーザーはそれを実行できます。

次の要件を満たしている場合、認証連絡先情報を事前設定することができます。

* オンプレミスのディレクトリ内のデータが正しい形式になっていること。
* Azure AD テナントの [Azure AD Connect](../hybrid/how-to-connect-install-express.md) を構成している。

電話番号の形式が *+CountryCode PhoneNumber* ( *+1 4251234567* など) である必要があります。

> [!NOTE]
> 国番号と電話番号の間にスペースが必要です。
>
> パスワードのリセットは内線番号をサポートしていません。 *+1 4251234567X12345* の形式であっても、電話がかけられる前に内線番号は削除されます。

## <a name="fields-populated"></a>取り込まれるフィールド

Azure AD Connect で既定の設定を使用する場合、SSPR の認証連絡先情報を設定するために、次のマッピングが行われます。

| オンプレミスの Active Directory | Azure AD     |
|------------------------------|--------------|
| telephoneNumber              | 会社電話 |
| mobile                       | 携帯電話 |

ユーザーが携帯電話番号を認証すると、Azure AD の **[認証の連絡先情報]** の下にある *[電話番号]* フィールドにもその番号が設定されます。

## <a name="authentication-contact-info"></a>認証の連絡先情報

Azure portal の Azure AD ユーザーの **[認証方法]** ページでは、グローバル管理者が認証の連絡先情報を手動で設定できます。 次のスクリーンショットに示すように、 *[使用可能な認証方法]* セクションまたは **[+ 認証方法の追加]** で既存の方法を確認できます。

:::image type="content" source="media/howto-sspr-authenticationdata/user-authentication-contact-info.png" alt-text="Azure portal から認証方法を管理する":::

この認証連絡先情報には、次の考慮事項が適用されます。

* *[電話番号]* フィールドに電話番号が設定され、SSPR ポリシーの *[携帯電話]* が有効になると、その番号がパスワード リセット登録ページに表示され、パスワード リセット ワークフロー中にも表示されます。
* *[電子メール]* フィールドにメール アドレスが設定され、SSPR ポリシーの *[電子メール]* が有効になると、そのメール アドレスがパスワード リセット登録ページに表示され、パスワード リセット ワークフロー中にも表示されます。

## <a name="security-questions-and-answers"></a>セキュリティの質問と回答

セキュリティの質問と回答は、Azure AD テナントに安全に格納されており、ユーザーは [SSPR 登録ポータル](https://aka.ms/ssprsetup)を介してのみアクセスできます。 管理者は、別のユーザーの質問と回答の内容を表示したり、設定したり、変更したりすることはできません。

## <a name="what-happens-when-a-user-registers"></a>ユーザーの登録時に発生すること

ユーザーが登録するとき、登録ページには次のフィールドが設定されます。

* **認証用電話**
* **認証用電子メール**
* **セキュリティの質問と回答**

*[携帯電話]* または *[連絡用メール アドレス]* に値が指定されている場合、ユーザーはそれらの値を使用してすぐにパスワードをリセットすることができます。これはユーザーがサービスに登録していない場合でも実行できます。

これらの値は、ユーザーが初めて登録するときにも表示され、ユーザーは必要に応じてそれらを変更することができます。 ユーザーが正常に登録された後、これらの値は、それぞれ *[認証用電話]* フィールドと *[認証用メール]* フィールドの固定値になります。

## <a name="set-and-read-the-authentication-data-through-powershell"></a>PowerShell を使用した認証データの設定と読み取り

PowerShell を使用して、次のフィールドを設定することができます。

* *連絡用メール アドレス*
* *携帯電話*
* *会社電話*
    * オンプレミス ディレクトリと同期していない場合にのみ設定できます。

> [!IMPORTANT]
> PowerShell v1 と PowerShell v2 間のコマンド機能には、既知のパリティの欠如があります。 [認証方法用の Microsoft Graph REST API (ベータ)](/graph/api/resources/authenticationmethods-overview) は、最新の対話を実現するための現在のエンジニアリング フォーカスです。

### <a name="use-powershell-version-1"></a>PowerShell バージョン 1 を使う

操作を開始するには、[Azure AD PowerShell モジュールをダウンロードしてインストール](/previous-versions/azure/jj151815(v=azure.100)#bkmk_installmodule)します。 それがインストールされたら、次の手順に従って各フィールドを構成します。

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>PowerShell バージョン 1 を使って認証データを設定する

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 4251234567"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 4252345678"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 4251234567" -PhoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>PowerShell バージョン 1 を使って認証データを読み取る

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>[認証用電話] と [認証用電子メール] オプションを読み取る

PowerShell バージョン 1 で **[認証用電話]** と **[認証用電子メール]** を読み取るには、次のコマンドを使います。

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>PowerShell バージョン 2 を使う

始めるには、[Azure AD バージョン 2 PowerShell モジュールをダウンロードしてインストールします](/powershell/module/azuread/)。

`Install-Module` をサポートする PowerShell の最新バージョンから簡単にインストールするには、次のコマンドを実行します。 最初の行では、モジュールが既にインストールされているかどうかを確認しています。

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

モジュールがインストールされたら、次の手順に従って各フィールドを構成します。

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>PowerShell バージョン 2 を使って認証データを設定する

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 4251234567"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 4252345678"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 4251234567" -TelephoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>PowerShell バージョン 2 を使って認証データを読み取る

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>次のステップ

ユーザーの認証連絡先情報が事前設定されたら、次のチュートリアルを実行して、セルフサービス パスワード リセットを有効にします。

> [!div class="nextstepaction"]
> [Azure AD のセルフサービス パスワード リセット を有効にする](tutorial-enable-sspr.md)
