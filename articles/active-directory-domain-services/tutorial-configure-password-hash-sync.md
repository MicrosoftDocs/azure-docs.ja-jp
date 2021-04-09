---
title: Azure AD Domain Services でパスワード ハッシュ同期を有効にする | Microsoft Docs
description: このチュートリアルでは、Azure AD Connect を使用して Azure Active Directory Domain Services マネージド ドメインへのパスワード ハッシュ同期を有効にする方法について説明します。
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: e83d8941d1be7fd36f53a881a21716252ad01954
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618350"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>チュートリアル:ハイブリッド環境の Azure Active Directory Domain Services でパスワード同期を有効にする

ハイブリッド環境では、Azure AD Connect を使用して、Azure Active Directory (Azure AD) テナントをオンプレミスの Active Directory Domain Services (AD DS) 環境と同期するように構成できます。 既定では、Azure AD Connect は、Azure Active Directory Domain Services (Azure AD DS) に必要となる従来の NT LAN Manager (NTLM) および Kerberos のパスワード ハッシュを同期しません。

オンプレミスの AD DS 環境から同期されるアカウントで Azure AD DS を使用するには、NTLM および Kerberos 認証に必要なこれらのパスワード ハッシュを同期するよう Azure AD Connect を構成する必要があります。 Azure AD Connect の構成後は、オンプレミスのアカウント作成またはパスワード変更イベントによって従来のパスワード ハッシュも Azure AD に同期されます。

オンプレミスの AD DS 環境を持たないクラウド専用アカウントを使用する場合、または "*リソース フォレスト*" を使用する場合は、これらの手順を実行する必要はありません。 リソース フォレストを使用するマネージド ドメインの場合、オンプレミスのパスワード ハッシュが同期されることはありません。 オンプレミスのアカウントの認証では、独自の AD DS ドメイン コントローラーに対するフォレストの信頼を使用します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 従来の NTLM および Kerberos パスワード ハッシュが必要な理由
> * Azure AD Connect に対して従来のパスワード ハッシュ同期を構成する方法

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のリソースが必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* Azure AD Connect を使用してオンプレミスのディレクトリと同期される、ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
    * 必要に応じて、[Azure AD Connect でパスワード ハッシュの同期を有効にします][enable-azure-ad-connect]。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要に応じて、[Azure Active Directory Domain Services のマネージド ドメインを作成して構成][create-azure-ad-ds-instance]します。

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>Azure AD Connect を使用したパスワード ハッシュの同期

Azure AD Connect は、ユーザー アカウントやグループなどのオブジェクトをオンプレミスの AD DS 環境から Azure AD テナントに同期するために使用します。 プロセスの一環として、パスワード ハッシュの同期により、アカウントはオンプレミスの AD DS 環境と Azure AD で同じパスワードを使用できます。

Azure AD DS では、マネージド ドメインのユーザーを認証するために、NTLM および Kerberos 認証に適した形式のパスワード ハッシュが必要になります。 NTLM または Kerberos 認証に必要な形式のパスワード ハッシュは、ご利用のテナントに対して Azure AD DS を有効にするまで、Azure AD で保存されることはありません。 また、セキュリティ上の理由から、クリアテキスト形式のパスワード資格情報が Azure AD に保存されることもありません。 そのため、こうした NTLM または Kerberos のパスワード ハッシュをユーザーの既存の資格情報に基づいて Azure AD が自動的に生成することはできません。

Azure AD Connect は、Azure AD DS に必要な NTLM または Kerberos のパスワード ハッシュを同期するよう構成できます。 [Azure AD Connect でパスワード ハッシュの同期を有効にする][enable-azure-ad-connect]ための手順を完了していることを確認します。 Azure AD Connect の既存のインスタンスがある場合は、[最新バージョンをダウンロードして更新][azure-ad-connect-download]し、NTLM と Kerberos の従来のパスワード ハッシュを同期できるようにします。 この機能は、初期リリースの Azure AD Connect または従来の DirSync ツールでは使用できません。 Azure AD Connect バージョン *1.1.614.0* 以降が必要です。

> [!IMPORTANT]
> Azure AD Connect は、オンプレミスの AD DS 環境との同期のためにのみインストールおよび構成する必要があります。 オブジェクトを Azure AD に同期するために、Azure AD DS マネージド ドメインに Azure AD Connect をインストールすることはサポートされていません。

## <a name="enable-synchronization-of-password-hashes"></a>パスワード ハッシュの同期を有効にする

Azure AD Connect がインストールされ、Azure AD と同期するよう構成された状態で、NTLM と Kerberos の従来のパスワード ハッシュの同期を構成します。 PowerShell スクリプトは、必要な設定を構成してから Azure AD への完全なパスワード同期を開始する際に使用されます。 その Azure AD Connect のパスワード ハッシュの同期プロセスが完了すると、ユーザーは、従来の NTLM または Kerberos のパスワード ハッシュを使用する Azure AD DS を介してアプリケーションにサインインできます。

1. Azure AD Connect がインストールされているコンピューターで、[スタート] メニューから **[Azure AD Connect] > [Synchronization Service]\(同期サービス\)** を開きます。
1. **[コネクタ]** タブを選択します。オンプレミスの AD DS 環境と Azure AD の間で同期を確立するために使用される接続情報が一覧表示されます。

    **[種類]** では、 *[Microsoft Azure の Active Directory (Microsoft)]* (Azure AD コネクタの場合) または *[Active Directory Domain Services]* (オンプレミスの AD DS コネクタの場合) が示されます。 次の手順でこの PowerShell スクリプトで使用するコネクタ名をメモしておきます。

    ![Sync Service Manager でコネクタ名を一覧表示する](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    このスクリーンショットの例では、次のコネクタが使用されています。

    * Azure AD コネクタの名前は *contoso.onmicrosoft.com - AAD* です
    * オンプレミスの AD DS コネクタの名前は *onprem.contoso.com* です

1. Azure AD Connect がインストールされているコンピューターに、次の PowerShell スクリプトをコピーして貼り付けます。 このスクリプトにより、従来のパスワード ハッシュを含む完全なパスワード同期がトリガーされます。 前の手順のコネクタ名を使用して、`$azureadConnector` および `$adConnector` 変数を更新します。

    各 AD フォレストでこのスクリプトを実行して、オンプレミス アカウントの NTLM および Kerberos のパスワード ハッシュを Azure AD と同期します。

    ```powershell
    # Define the Azure AD Connect connector names and import the required PowerShell module
    $azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
    $adConnector = "<CASE SENSITIVE AD DS CONNECTOR NAME>"
    
    Import-Module "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1"
    Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

    # Create a new ForceFullPasswordSync configuration parameter object then
    # update the existing connector with this new configuration
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c

    # Disable and re-enable Azure AD Connect to force a full password synchronization
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
    ```

    アカウントとグループの数の観点からご利用のディレクトリのサイズに応じて、従来のパスワード ハッシュを Azure AD と同期するには時間がかかる場合があります。 このパスワードは、Azure AD と同期された後、マネージド ドメインと同期されます。

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 従来の NTLM および Kerberos パスワード ハッシュが必要な理由
> * Azure AD Connect に対して従来のパスワード ハッシュ同期を構成する方法

> [!div class="nextstepaction"]
> [Azure AD Domain Services のマネージド ドメインにおける同期の動作を確認する](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
