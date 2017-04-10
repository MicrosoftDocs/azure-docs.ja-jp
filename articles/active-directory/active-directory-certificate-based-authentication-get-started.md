---
title: "Azure Active Directory の証明書ベースの認証 - 概要 | Microsoft Docs"
description: "各環境で証明書ベースの認証を構成する方法について説明します。"
author: MarkusVi
documentationcenter: na
manager: femila
ms.assetid: c6ad7640-8172-4541-9255-770f39ecce0e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: c8c6c105c2142dac1b3df6c26838ba8626161092
ms.openlocfilehash: d818cd3a243fb78228706b21a002f295782189be


---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Azure Active Directory の証明書ベースの認証の概要

証明書ベースの認証では、Exchange Online アカウントを次に接続する場合、Azure Active Directory と、Windows、Android または iOS デバイス上のクライアント証明書を使用して認証できます。 

- Microsoft Outlook や Microsoft Word などの Office モバイル アプリケーション   

- Exchange ActiveSync (EAS) クライアント 

この機能を構成すると、モバイル デバイスで特定のメールおよび Microsoft Office アプリケーションにユーザー名とパスワードの組み合わせを入力する必要がなくなります。 

このトピックの内容と前提条件:

- Office 365 Enterprise、Business、Education、および US Government の各プランのテナントのユーザー向けに証明書ベースの認証 (CBA) を構成し、使用する方法について説明します。 Office 365 China、US Government Defense、および US Government Federal の各プランでは、この機能はプレビュー版として提供されています。 

- [公開キー基盤 (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) と [AD FS](connect/active-directory-aadconnectfed-whatis.md) が構成されていることを前提としています。    


## <a name="requirements"></a>必要条件

証明書ベースの認証を構成するには、次の条件を満たしている必要があります。  

- ルート証明機関および中間証明機関は、Azure Active Directory で構成する必要があります。  

- 各証明機関には、インターネットに接続する URL から参照できる証明書失効リスト (CRL) が必要です。  

- Azure Active Directory で少なくとも&1; つの証明機関が構成されている必要があります。 この手順については、「[証明機関を構成する](#step-2-configure-the-certificate-authorities)」セクションをご覧ください。  

- Exchange ActiveSync クライアントの場合、クライアント証明書では、サブジェクト代替名フィールドのプリンシパル名または RFC822 名の値のいずれかで、Exchange Online のユーザーのルーティング可能な電子メール アドレスが必要になります。 Azure Active Directory は、ディレクトリ内のプロキシ アドレス属性に RFC822 値をマップします。  

- クライアント デバイスから、クライアント証明書を発行する証明機関の少なくとも&1; つにアクセスできる必要があります。  

- クライアント認証用のクライアント証明書が、クライアントに対して発行されている必要があります。  




## <a name="step-1-select-your-device-platform"></a>手順 1: デバイス プラットフォームを選択する

まず、対象のデバイス プラットフォームについて、次の項目を確認する必要があります。

- Office モバイル アプリケーションのサポート 
- 特別な実装要件  

関連情報については、次のリンク先をご覧ください。

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)


## <a name="step-2-configure-the-certificate-authorities"></a>手順 2: 証明機関を構成する 

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

構成には、[Azure Active Directory PowerShell バージョン 2](https://docs.microsoft.com/powershell/azuread/) を使用できます。  

1. Windows PowerShell を管理者特権で起動します。 
2. Azure AD モジュールをインストールします。 バージョン [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) 以降をインストールする必要があります。  
   
        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33 

構成の最初の手順では、テナントとの接続を確立する必要があります。 テナントへの接続が確立されるとすぐに、ディレクトリに定義されている信頼された証明機関を表示、追加、削除、および変更できるようになります。 

### <a name="connect"></a>接続

テナントとの接続を確立するには、[Connect-AzureAD](https://docs.microsoft.com/powershell/azuread/v2/connect-azuread) コマンドレットを使用します。

    Connect-AzureAD 


### <a name="retrieve"></a>デバイス ハンドルの 

ディレクトリに定義されている信頼された証明機関を取得するには、[Get-AzureADTrustedCertificateAuthority](https://docs.microsoft.com/powershell/azuread/v2/get-azureadtrustedcertificateauthority) コマンドレットを使用します。 

    Get-AzureADTrustedCertificateAuthority 
 

### <a name="add"></a>Add

信頼された証明機関を作成するには、[New-AzureADTrustedCertificateAuthority](https://docs.microsoft.com/powershell/azuread/v2/new-azureadtrustedcertificateauthority) コマンドレットを使用します。 
   
    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]" 
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation 
    $new_ca.AuthorityType=0 
    $new_ca.TrustedCertificate=$cert 
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca 


### <a name="remove"></a>Remove

信頼された証明機関を削除するには、[Remove-AzureADTrustedCertificateAuthority](https://docs.microsoft.com/powershell/azuread/v2/remove-azureadtrustedcertificateauthority) コマンドレットを使用します。
   
    $c=Get-AzureADTrustedCertificateAuthority 
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2] 


### <a name="modfiy"></a>変更

信頼された証明機関を変更するには、[Set-AzureADTrustedCertificateAuthority](https://docs.microsoft.com/powershell/azuread/v2/set-azureadtrustedcertificateauthority) コマンドレットを使用します。

    $c=Get-AzureADTrustedCertificateAuthority 
    $c[0].AuthorityType=1 
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0] 


## <a name="step-3-configure-revocation"></a>手順 3: 失効を構成する

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


## <a name="step-4-test-your-configuration"></a>手順 4: 構成をテストする

### <a name="testing-your-certificate"></a>証明書のテスト

構成の最初のテストとして、**デバイス上のブラウザー**を使用して [Outlook Web Access](https://outlook.office365.com) または [SharePoint Online](https://microsoft.sharepoint.com) へのサインインを試みます。

サインインが成功した場合、次のことがわかります。

- ユーザー証明書がテスト デバイスにプロビジョニングされている
- AD FS が正しく構成されている  


### <a name="testing-office-mobile-applications"></a>Office モバイル アプリケーションのテスト

**Office モバイル アプリケーションで証明書ベースの認証をテストするには:** 

1. テスト デバイスで、Office モバイル アプリケーション (例: OneDrive) をインストールします。
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

1. 上記の要件を満たすアプリケーションで EAS プロファイルを構成します。  
2. アプリケーションを開き、メールが同期されていることを確認します。 




<!--HONumber=Feb17_HO2-->


