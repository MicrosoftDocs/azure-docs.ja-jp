---
title: Azure Active Directory Domain Services:Kerberos の制約付き委任を有効にする | Microsoft Docs
description: Azure Active Directory Domain Services のマネージド ドメインで Kerberos の制約付き委任を有効にします
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 5569344a2df560036b99dea40c466302f5e6fe4c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159283"
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>マネージド ドメインで Kerberos の制約付き委任 (KCD) を構成する
多くのアプリケーションは、ユーザーのコンテキストでリソースにアクセスする必要があります。 Active Directory では、このユース ケースを可能にする、Kerberos の委任というメカニズムをサポートしています。 さらに、特定のリソースのみにユーザーのコンテキストでアクセスできるように、委任を制限できます。 Azure AD Domain Services のマネージド ドメインは、従来の Active Directory ドメインとは異なっており、より安全にロックダウンされています。

この記事では、Azure AD Domain Services のマネージド ドメインで Kerberos の制約付き委任を構成する方法について説明します。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="kerberos-constrained-delegation-kcd"></a>Kerberos の制約付き委任 (KCD)
Kerberos の委任は、あるアカウントが別のセキュリティ プリンシパル (ユーザーなど) に偽装してリソースにアクセスできるようにします。 たとえば、ユーザーのコンテキストでバックエンド Web API にアクセスする Web アプリケーションがあるとします。 この例では、この Web アプリケーション (サービス アカウントまたはコンピューター/マシン アカウントのコンテキストで実行中) がリソース (バックエンド Web API) にアクセスする際に、ユーザーに偽装します。 Kerberos の委任は、偽装する側のアカウントがユーザーのコンテキストでアクセスできるリソースを制限しないため、安全ではありません。

Kerberos の制約付き委任 (KCD) は、指定したサーバーがユーザーの代理で操作できるサービス/リソースを制限します。 従来の KCD では、サービスのドメイン アカウントを構成するのにドメイン管理者の特権が必要です。また、そのアカウントは 1 つのドメインに対して制限されます。

従来の KCD に関しては、問題もいくつかあります。 以前のオペレーティング システムでは、ドメイン管理者がサービスにアカウント ベースの KCD を構成した場合、サービス管理者には、所有しているリソース サービスに委任されるフロントエンド サービスを把握する便利な方法はありませんでした。 また、リソース サービスに委任されるフロントエンド サービスは攻撃ポイントになる可能性がありました。 フロントエンド サービスをホストするサーバーが侵害され、そのフロントエンド サービスがリソース サービスに委任されるように構成されている場合、リソース サービスも侵害される可能性がありました。

> [!NOTE]
> Azure AD Domain Services のマネージド ドメインでは、ドメイン管理者特権がありません。 したがって、**マネージド ドメインで従来のアカウントベースの KCD を構成することはできません**。 この記事で説明されているように、リソースベースの KCD を使用してください。 このメカニズムの方が安全性も高くなります。
>
>

## <a name="resource-based-kcd"></a>リソースベースの KCD
Windows Server 2012 以降では、サービス管理者はサービスに制約付き委任を構成できるようになりました。 このモデルでは、バックエンド サービス管理者は特定のフロントエンド サービスによる KCD の使用を許可または拒否できます。 このモデルは、**リソースベースの KCD**と呼ばれます。

リソースベースの KCD は PowerShell を使用して構成します。 偽装する側のアカウントがコンピューター アカウントであるか、ユーザー アカウント/サービス アカウントであるかによって、`Set-ADComputer` コマンドレットまたは `Set-ADUser` コマンドレットを使います。

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>マネージド ドメインでコンピューター アカウントにリソースベースの KCD を構成する
コンピューター "contoso100-webapp.contoso100.com" で実行されている Web アプリがあるとします。 この Web アプリは、ドメイン ユーザーのコンテキストでリソース ("contoso100-api.contoso100.com" で実行されている Web API) にアクセスする必要があります。 このシナリオでリソースベースの KCD を設定する方法を次に示します。

1. [カスタム OU を作成します](active-directory-ds-admin-guide-create-ou.md)。 このカスタム OU を管理する権限を、マネージド ドメイン内のユーザーに委任できます。
2. 両方の仮想マシン (Web アプリを実行しているものと、Web API を実行しているもの) をマネージド ドメインに参加させます。 カスタム OU 内にこれらのコンピューター アカウントを作成します。
3. ここで、次の PowerShell コマンドを使用して、リソースベースの KCD を構成します。

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Web アプリと Web API のコンピューター アカウントは、ユーザーがリソースベースの KCD を構成する権限を持つカスタム OU 内にある必要があります。 組み込みの "AAD DC Computers" コンテナー内にあるコンピューター アカウントに対して、リソースベースの KCD を構成することはできません。
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>マネージド ドメインでユーザー アカウントにリソースベースの KCD を構成する
サービス アカウント "appsvc" として実行されている Web アプリがあり、この Web アプリがドメイン ユーザーのコンテキストでリソース (サービス アカウント "backendsvc" として実行されている Web API) にアクセスする必要があるとします。 このシナリオでリソースベースの KCD を設定する方法を次に示します。

1. [カスタム OU を作成します](active-directory-ds-admin-guide-create-ou.md)。 このカスタム OU を管理する権限を、マネージド ドメイン内のユーザーに委任できます。
2. バックエンド Web API/リソースを実行している仮想マシンをマネージド ドメインに参加させます。 カスタム OU 内にそのコンピューター アカウントを作成します。
3. カスタム OU 内で Web アプリを実行するために使用するサービス アカウント ("appsvc" など) を作成します。
4. ここで、次の PowerShell コマンドを使用して、リソースベースの KCD を構成します。

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> バックエンド Web API のコンピューター アカウントとサービス アカウントはいずれも、ユーザーがリソースベースの KCD を構成する権限を持つカスタム OU 内にある必要があります。 組み込みの "AAD DC Computers" コンテナーのコンピューター アカウントまたは組み込みの 'AAD DC Users' コンテナーのユーザー アカウントに対して、リソースベースの KCD を構成することはできません。 したがって、リソースベースの KCD を設定するために Azure AD から同期されているユーザー アカウントを使用することはできません。
>

## <a name="related-content"></a>関連コンテンツ
* [Azure AD ドメイン サービス - 作業開始ガイド](active-directory-ds-getting-started.md)
* [Kerberos の制約付き委任の概要](https://technet.microsoft.com/library/jj553400.aspx)
