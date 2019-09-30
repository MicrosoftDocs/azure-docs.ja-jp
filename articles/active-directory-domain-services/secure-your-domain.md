---
title: Azure AD Domain Services をセキュリティで保護する | Microsoft Docs'
description: Azure Active Directory Domain Services のマネージド ドメインの弱い暗号、古いプロトコル、および NTLM パスワード ハッシュ同期を無効にする方法を説明します。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 09/09/2019
ms.author: iainfou
ms.openlocfilehash: db086c56c9f16f4691efaade03571bf8a36c6444
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842625"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services のマネージド ドメインをセキュリティで保護するために、弱い暗号およびパスワード ハッシュ同期を無効にします

既定では、Azure Active Directory Domain Services (Azure AD DS) で、NTLM v1 や TLS v1 などの暗号が使用できます。 これらの暗号は、一部のレガシ アプリケーションで必要になる場合がありますが、脆弱と見なされており、不要であれば無効にできます。 Azure AD Connect を使用したオンプレミスのハイブリッド接続がある場合は、NTLM パスワード ハッシュ同期も無効にできます。

この記事では、NTLM v1 と TLS v1 の暗号を無効にする方法と、NTLM パスワード ハッシュ同期を無効にする方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下のリソースが必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services マネージド ドメイン。
    * 必要であれば、[Azure Active Directory Domain Services インスタンスを作成して構成][create-azure-ad-ds-instance]してください。
* Azure PowerShell のインストールおよび構成。
    * 必要であれば、手順に従って、[Azure PowerShell モジュールをインストールし、Azure サブスクリプションに接続](/powershell/azure/install-az-ps)します。
    * 必ず、[Connect-AzAccount][Connect-AzAccount] コマンドレットを使用して、Azure サブスクリプションにサインインしてください。
* Azure AD PowerShell をインストールおよび構成します。
    * 必要であれば、手順に従って、[Azure AD PowerShell モジュールをインストールして Azure AD に接続](/powershell/azure/active-directory/install-adv2)します。
    * 必ず、[Connect-AzureAD][Connect-AzureAD] コマンドレットを使用して、Azure AD テナントにサインインしてください。

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>弱い暗号と NTLM パスワード ハッシュ同期を無効にする

弱い暗号スイートと NTLM 資格情報ハッシュの同期を無効にするには、Azure アカウントにサインインしてから、[Get-AzResource][Get-AzResource] コマンドレットを使用して Azure AD DS リソースを取得します。

> [!TIP]
> [Get-AzResource][Get-AzResource] コマンドを使用して "*Microsoft.AAD/DomainServices* resource doesn't exist (Microsoft.AAD/DomainServices リソースが存在しません)" というエラーを受け取った場合は、[アクセス権を昇格して、すべての Azure サブスクリプションと管理グループを管理できるようにします][global-admin]。

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

次に、*DomainSecuritySettings* を定義して、以下のセキュリティ オプションを構成します。

1. NTLM v1 のサポートを無効にする。
2. オンプレミスの AD からの NTLM パスワード ハッシュの同期を無効にする。
3. TLS v1 を無効にする。

> [!IMPORTANT]
> Azure AD DS マネージド ドメインで NTLM パスワード ハッシュ同期を無効にしている場合、ユーザーおよびサービス アカウントは LDAP 簡易バインドを実行できません。 LDAP 簡易バインドを実行する必要がある場合は、次のコマンドで、 *"SyncNtlmPasswords"="Disabled";* セキュリティ構成オプションを設定しないでください。

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

最後に、[Set-AzResource][Set-AzResource] コマンドレットを使用して、定義済みのセキュリティ設定を Azure AD DS マネージド ドメインに適用します。 最初の手順の Azure AD DS リソースと、前の手順のセキュリティ設定を指定します。

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

セキュリティ設定が Azure AD DS マネージド ドメインに適用されるまでに、しばらく時間がかかります。

## <a name="next-steps"></a>次の手順

同期プロセスの詳細について学習するには、[Azure AD DS マネージド ドメイン内でのオブジェクトと資格情報の同期のしくみ][synchronization]に関するページを参照してください。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD