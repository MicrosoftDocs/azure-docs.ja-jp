---
title: "Azure AD ドメイン サービス: パスワード同期を有効にする | Microsoft Docs"
description: "Azure Active Directory ドメイン サービスの概要"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/17/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 4969b43831a3813a4e76c6447c252a9c458f371a
ms.lasthandoff: 03/18/2017


---
# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Azure AD Domain Services とのパスワード同期を有効にする
ここまでの作業では、自分の Azure AD テナントの Azure AD Domain Services を有効にしました。 次に、Azure AD Domain Services とのパスワードの同期を有効にします。 資格情報の同期が設定されると、ユーザーは企業の資格情報を使用して、管理対象ドメインにサインインできます。

実行する手順は、組織がクラウド専用 Azure AD テナントであるか、Azure AD Connect を使用してオンプレミスのディレクトリに同期するように設定されているかによって異なります。

<br>

> [!div class="op_single_selector"]
> * [クラウド専用 Azure AD テナント](active-directory-ds-getting-started-password-sync.md)
> * [同期された Azure AD テナント](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

<br>

## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-synced-azure-ad-tenant"></a>タスク 5: AAD ドメイン サービスとのパスワード同期を有効にする (同期された Azure AD テナントの場合)
同期対象の Azure AD テナントは、Azure AD Connect を使用して組織のオンプレミス ディレクトリと同期するように設定されます。 Azure AD Connect は、既定では NTLM および Kerberos 資格情報ハッシュを Azure AD と同期しません。 Azure AD Domain Services を使用するには、NTLM および Kerberos 認証に必要な資格情報ハッシュを同期するように Azure AD Connect を構成する必要があります。 

> [!WARNING]
> AAD Domain Services とのパスワード同期は、Azure AD Domain Services を有効にするたびに有効にする必要があります。 以前、Azure AD ディレクトリを使用するために Azure AD Domain Services を有効にし、その後無効にしたことがあるかもしれませんが、 パスワード同期はその場合も、次回ディレクトリを使用するために Azure AD Domain Services を有効にする際に有効にする必要があります。
>
>

以下の手順では、必要な資格情報ハッシュの Azure AD テナントとの同期を有効にします。

### <a name="install-or-update-azure-ad-connect"></a>Azure AD Connect のインストールまたは更新
Azure AD Connect の最新の推奨リリースをドメイン参加コンピューターにインストールします。 Azure AD Connect の既存のインスタンスが設定されている場合は、Azure AD Connect の最新バージョンを使用するように更新する必要があります。 既に修正されている既知の問題/バグを回避するために、常に最新バージョンの Azure AD Connect を使用してください。

**[Azure AD Connect のダウンロード](http://www.microsoft.com/download/details.aspx?id=47594)**

推奨バージョン: **1.1.281.0** - 2016 年 9 月 7 日公開

> [!WARNING]
> 従来のパスワードの資格情報 (NTLM/Kerberos 認証で必要) で Azure AD テナントとの同期を有効にするには、Azure AD Connect の最新の推奨リリースをインストールする必要があります。 この機能は、旧リリースの Azure AD Connect または従来の DirSync ツールでは使用できません。
>
>

Azure AD Connect のインストール手順については、[Azure AD Connect の概要](../active-directory/active-directory-aadconnect.md)に関する記事をご覧ください。

### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>NTLM と Kerberos の資格情報ハッシュの Azure AD との同期を有効にする
各 AD フォレストで以下の PowerShell スクリプトを実行して、完全パスワード同期を強制的に適用し、すべてのオンプレミス ユーザーの資格情報ハッシュが Azure AD テナントと同期できるようにします。 このスクリプトは、NTLM/Kerberos 認証に必要な資格情報ハッシュが Azure AD テナントと同期されるようにします。

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

ディレクトリのサイズ (ユーザーやグループの数など) によっては、資格情報ハッシュが Azure AD と同期されるまでに時間がかかります。 資格情報ハッシュが Azure AD と同期されるとすぐに、Azure AD ドメイン サービスでパスワードを使用できるようになります。

<br>

## <a name="related-content"></a>関連コンテンツ
* [AAD ドメイン サービスとのパスワード同期を有効にする (クラウド専用 Azure AD ディレクトリの場合)](active-directory-ds-getting-started-password-sync.md)
* [Azure AD ドメイン サービスで管理されているドメインの管理](active-directory-ds-admin-guide-administer-domain.md)
* [Azure AD ドメイン サービスで管理されているドメインに Windows 仮想マシンを参加させる](active-directory-ds-admin-guide-join-windows-vm.md)
* [Azure AD ドメイン サービスで管理されているドメインに Red Hat Enterprise Linux 仮想マシンを参加させる](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

