---
title: Azure Active Directory Domain Services のマネージド ドメインをセキュリティで保護する | Microsoft Docs
description: マネージド ドメインをセキュリティで保護します
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: e94cd9ca049cfdfd2321ce046714506ed1f23390
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483276"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services のマネージド ドメインをセキュリティで保護する
この記事は、マネージド ドメインをセキュリティで保護するときに役に立ちます。 弱い暗号スイートの使用をオフにし、NTLM 資格情報ハッシュの同期を無効にすることができます。

## <a name="install-the-required-powershell-modules"></a>必要な PowerShell モジュールをインストールする

### <a name="install-and-configure-azure-ad-powershell"></a>Azure AD PowerShell のインストールおよび構成
記事の指示に従って、 [Azure AD PowerShell モジュールをインストールして Azure AD に接続します](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)。

### <a name="install-and-configure-azure-powershell"></a>Azure PowerShell のインストールおよび構成
記事の指示に従って、 [Azure PowerShell モジュールをインストールして Azure サブスクリプションに接続します](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)。


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>弱い暗号スイートと NTLM 資格情報ハッシュの同期を無効にする
次の PowerShell スクリプトを使用して以下のことを行います。
1. マネージド ドメインで NTLM v1 のサポートを無効にする。
2. オンプレミスの AD からの NTLM パスワード ハッシュの同期を無効にする。
3. マネージド ドメインで TLS v1 を無効にする。

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

> [!IMPORTANT]
> Azure AD Domain Services インスタンスで NTLM パスワードハッシュ同期を無効にしている場合、ユーザー (およびサービス アカウント) は LDAP 簡易バインドを実行できません。  NTLM パスワード ハッシュ同期の無効化の詳細については、「[Azure AD Domain Services のマネージド ドメインをセキュリティで保護する](secure-your-domain.md)」をお読みください。
>
>

## <a name="next-steps"></a>次の手順
* [Azure AD Domain Services での同期を理解する](synchronization.md)
