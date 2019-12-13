---
title: 証明書ベースの認証 - Azure Active Directory
description: 各環境で証明書ベースの認証を構成する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b57c4f474b0b9def08005f32f48225d36ea8cf1
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848835"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Azure Active Directory の証明書ベースの認証の概要

証明書ベースの認証では、Exchange Online アカウントを次に接続する場合、Azure Active Directory を Windows、Android、または iOS デバイス上のクライアント証明書と共に使用して認証できます。

- Microsoft Outlook や Microsoft Word などの Microsoft モバイル アプリケーション
- Exchange ActiveSync (EAS) クライアント

この機能を構成すると、モバイル デバイスで特定のメールおよび Microsoft Office アプリケーションにユーザー名とパスワードの組み合わせを入力する必要がなくなります。

このトピックの内容と前提条件:

- Office 365 Enterprise、Business、Education、および US Government の各プランのテナントのユーザー向けに証明書ベースの認証 (CBA) を構成し、使用する方法について説明します。 Office 365 China、US Government Defense、および US Government Federal の各プランでは、この機能はプレビュー版として提供されています。
- [公開キー基盤 (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) と [AD FS](../hybrid/how-to-connect-fed-whatis.md) が構成されていることを前提としています。

## <a name="requirements"></a>必要条件

証明書ベースの認証を構成するには、次のステートメントが当てはまる必要があります。

- 証明書ベース認証 (CBA) は、フェデレーション環境のブラウザー アプリケーション、最新の認証 (ADAL) を使用するやネイティブのクライアント、または MSAL ライブラリのみに対しサポートされます。 1 つの例外は、Exchange Online (EXO) の Exchange Active Sync (EAS) です。これは、フェデレーション アカウントや管理アカウントに使用できます。
- ルート証明機関および中間証明機関は、Azure Active Directory で構成する必要があります。
- 各証明機関には、インターネットに接続する URL から参照できる証明書失効リスト (CRL) が必要です。
- Azure Active Directory で少なくとも 1 つの証明機関が構成されている必要があります。 この手順については、「[証明機関を構成する](#step-2-configure-the-certificate-authorities)」セクションをご覧ください。
- Exchange ActiveSync クライアントの場合、クライアント証明書では、サブジェクト代替名フィールドのプリンシパル名または RFC822 名の値のいずれかで、Exchange Online のユーザーのルーティング可能な電子メール アドレスが必要になります。 Azure Active Directory は、ディレクトリ内のプロキシ アドレス属性に RFC822 値をマップします。
- クライアント デバイスから、クライアント証明書を発行する証明機関の少なくとも 1 つにアクセスできる必要があります。
- クライアント認証用のクライアント証明書が、クライアントに対して発行されている必要があります。

>[!IMPORTANT]
>正常にダウンロードしてキャッシュする Azure Active Directory の CRL の最大サイズは 20 MB であり、CRL のダウンロードに必要な時間は 10 秒以内である必要があります。  Azure Active Directory が CRL をダウンロードできない場合、対応する CA によって発行された証明書を使用する証明書ベースの認証は失敗します。 CRL ファイルがサイズ制限内に収まるようにするためのベスト プラクティスは、証明書の有効期間を妥当な制限内に保ち、期限切れの証明書をクリーンアップすることです。 

## <a name="step-1-select-your-device-platform"></a>手順 1:デバイス プラットフォームを選択する

まず、対象のデバイス プラットフォームについて、次の項目を確認する必要があります。

- Office モバイル アプリケーションのサポート
- 特別な実装要件

関連情報については、次のリンク先をご覧ください。

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>手順 2:証明機関を構成する

Azure Active Directory で証明機関を構成するには、証明機関ごとに次のものをアップロードします。

* 証明書の公開部分 ( *.cer* 形式)
* 証明書失効リスト (CRL) が存在する、インターネットに接続する URL

証明機関のスキーマは次のようになります。

    class TrustedCAsForPasswordlessAuth
    {
       CertificateAuthorityInformation[] certificateAuthorities;
    }

    class CertificateAuthorityInformation

    {
        CertAuthorityType authorityType;
        X509Certificate trustedCertificate;
        string crlDistributionPoint;
        string deltaCrlDistributionPoint;
        string trustedIssuer;
        string trustedIssuerSKI;
    }

    enum CertAuthorityType
    {
        RootAuthority = 0,
        IntermediateAuthority = 1
    }

構成には、[Azure Active Directory PowerShell バージョン 2](/powershell/azure/install-adv2?view=azureadps-2.0) を使用できます。

1. Windows PowerShell を管理者特権で起動します。
2. Azure AD モジュール バージョン [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) 以降をインストールします。

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

構成の最初の手順では、テナントとの接続を確立する必要があります。 テナントへの接続が確立されるとすぐに、ディレクトリに定義されている信頼された証明機関をレビュー、追加、削除、および変更できます。

### <a name="connect"></a>接続

テナントとの接続を確立するには、[Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0) コマンドレットを使用します。

    Connect-AzureAD

### <a name="retrieve"></a>取得

ディレクトリに定義されている信頼された証明機関を取得するには、[Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0) コマンドレットを使用します。

    Get-AzureADTrustedCertificateAuthority

### <a name="add"></a>追加

信頼された証明機関を作成するには、[New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) コマンドレットを使用し、**crlDistributionPoint** 属性に正しい値を設定します。

    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca

### <a name="remove"></a>Remove

信頼された証明機関を削除するには、[Remove-AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0) コマンドレットを使用します。

    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]

### <a name="modify"></a>[変更]

信頼された証明機関を変更するには、[Set-AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0) コマンドレットを使用します。

    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]

## <a name="step-3-configure-revocation"></a>手順 3:失効を構成する

クライアント証明書を失効させるために、Azure Active Directory は、証明機関の情報の一部としてアップロードされた URL から証明書失効リスト (CRL) をフェッチし、キャッシュします。 CRL の最後の発行タイムスタンプ (**発効日** プロパティ) を使用し、CRL がまだ有効であることを確認します。 CRL は定期的に参照されて、リストに含まれる証明書へのアクセスは無効になります。

即時の失効が必要な場合 (たとえば、ユーザーがデバイスを紛失した場合) は、ユーザーの認証トークンを無効にできます。 認証トークンを無効にするには、Windows PowerShell を使用してこの特定のユーザーの **StsRefreshTokenValidFrom** フィールドを設定します。 アクセスを無効にする各ユーザーの **StsRefreshTokenValidFrom** フィールドを更新する必要があります。

失効状態が継続していることを確認するには、CRL の**発効日**を **StsRefreshTokenValidFrom** で設定した値より後の日付に設定し、対象の証明書が CRL にあることを確認する必要があります。

次の手順は、 **StsRefreshTokenValidFrom** フィールドを設定することで認証トークンを更新し、無効にするプロセスを簡単に示したものです。

**失効を構成するには:**

1. 管理者の資格情報で MSOL サービスに接続します。

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. ユーザーの現在の StsRefreshTokensValidFrom 値を取得します。

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. 現在のタイムスタンプと等しいユーザーの新しい StsRefreshTokensValidFrom 値を構成します。

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

設定する日付は、現在より後の日付にする必要があります。 日付を現在より後の日付にしないと、 **StsRefreshTokensValidFrom** プロパティは設定されません。 日付を現在より後の日付にすると、 **StsRefreshTokensValidFrom** は、現在の時刻に設定されます (Set-MsolUser コマンドで指定した日付ではありません)。

## <a name="step-4-test-your-configuration"></a>手順 4:構成をテストする

### <a name="testing-your-certificate"></a>証明書のテスト

構成の最初のテストとして、**デバイス上のブラウザー**を使用して [Outlook Web Access](https://outlook.office365.com) または [SharePoint Online](https://microsoft.sharepoint.com) へのサインインを試みます。

サインインが成功した場合、次のことがわかります。

- ユーザー証明書がテスト デバイスにプロビジョニングされている
- AD FS が正しく構成されている

### <a name="testing-office-mobile-applications"></a>Office モバイル アプリケーションのテスト

**Office モバイル アプリケーションで証明書ベースの認証をテストするには:**

1. テスト デバイス上で、Office モバイル アプリケーション (OneDrive など) をインストールします。
3. アプリケーションを起動します。
4. ユーザー名を入力し、使用するユーザー証明書を選択します。

正常にサインインします。

### <a name="testing-exchange-activesync-client-applications"></a>Exchange ActiveSync クライアント アプリケーションのテスト

証明書ベースの認証を使用した Exchange ActiveSync (EAS) にアクセスするには、クライアント証明書を含む EAS プロファイルがアプリケーションに対して使用可能になっている必要があります。

EAS プロファイルには次の情報が表示されます。

- 認証に使用するユーザー証明書

- EAS エンドポイント (例: outlook.office365.com)

EAS プロファイルをデバイス上に構成して配置するには、Intune などのモバイル デバイス管理 (MDM) を使用するか、EAS プロファイルの証明書をデバイス上に手動で配置します。

### <a name="testing-eas-client-applications-on-android"></a>Android の EAS クライアント アプリケーションのテスト

**証明書の認証をテストするには:**

1. 前のセクションの要件を満たすアプリケーションで EAS プロファイルを構成します。
2. アプリケーションを開き、メールが同期されていることを確認します。

## <a name="next-steps"></a>次の手順

[Android デバイス上の証明書ベースの認証に関する追加情報。](active-directory-certificate-based-authentication-android.md)

[iOS デバイス上の証明書ベースの認証に関する追加情報。](active-directory-certificate-based-authentication-ios.md)
