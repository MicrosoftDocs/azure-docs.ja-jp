---
title: "Azure Active Directory Domain Services: Kerberos の制約付き委任を有効にする | Microsoft Docs"
description: "Azure Active Directory Domain Services の管理対象ドメインで Kerberos の制約付き委任を有効にします"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: ce5ba551f071055a9aaebb99395ada4e96ffcc76
ms.openlocfilehash: 47e81ad02c544cb269abed0a4936c30fc404f01a
ms.lasthandoff: 02/07/2017


---

# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>管理対象ドメインで Kerberos の制約付き委任 (KCD) を構成する
多くのアプリケーションは、ユーザーのコンテキストでリソースにアクセスする必要があります。 Active Directory では、このユース ケースを可能にする、Kerberos の委任というメカニズムをサポートしています。 さらに、特定のリソースのみにユーザーのコンテキストでアクセスできるように、委任を制限できます。 Azure AD Domain Services の管理対象ドメインは、従来の Active Directory ドメインとは異なっており、より安全にロックダウンされています。

この記事では、Azure AD Domain Services の管理対象ドメインで Kerberos の制約付き委任を構成する方法について説明します。

## <a name="kerberos-constrained-delegation-kcd"></a>Kerberos の制約付き委任 (KCD)
Kerberos の委任は、あるアカウントが別のセキュリティ プリンシパル (ユーザーなど) に偽装してリソースにアクセスできるようにします。 たとえば、ユーザーのコンテキストでバックエンド Web API にアクセスする Web アプリケーションがあるとします。 この例では、この Web アプリケーション (サービス アカウントまたはコンピューター/マシン アカウントのコンテキストで実行中) がリソース (バックエンド Web API) にアクセスする際に、ユーザーに偽装します。 Kerberos の委任は、偽装する側のアカウントがユーザーのコンテキストでアクセスできるリソースを制限しないため、安全ではありません。

Kerberos の制約付き委任 (KCD) は、指定したサーバーがユーザーの代理で操作できるサービス/リソースを制限します。 従来の KCD では、サービスのドメイン アカウントを構成するのにドメイン管理者の特権が必要です。また、そのアカウントは&1; つのドメインに対して制限されます。

従来の KCD に関しては、問題もいくつかあります。 以前のオペレーティング システムではドメイン管理者がサービスを構成していて、サービス管理者には、所有しているリソース サービスに委任されるフロントエンド サービスを把握する便利な方法はありませんでした。 また、リソース サービスに委任されるフロントエンド サービスは攻撃ポイントになる可能性がありました。 フロントエンド サービスをホストするサーバーが侵害され、そのフロントエンド サービスがリソース サービスに委任されるように構成されている場合、リソース サービスも侵害される可能性がありました。

> [!NOTE]
> Azure AD Domain Services の管理対象ドメインでは、ドメイン管理者特権がありません。 したがって、**管理対象ドメインで従来の KCD を構成することはできません**。 この記事で説明されているように、リソースベースの KCD を使用してください。 このメカニズムの方が安全性も高くなります。
>
>

## <a name="resource-based-kerberos-constrained-delegation"></a>リソースベースの Kerberos の制約付き委任
Windows Server 2012 R2 と Windows Server 2012 では、サービスの制約付き委任を構成する権限は、ドメイン管理者からサービス管理者に移行されています。 これにより、バックエンド サービス管理者はフロントエンド サービスを許可または拒否できます。 このモデルは、**リソースベースの Kerberos の制約付き委任**と呼ばれます。

リソースベースの KCD は PowerShell を使用して構成します。 偽装する側のアカウントがコンピューター アカウントであるか、ユーザー アカウント/サービス アカウントであるかによって、Set-ADComputer コマンドレットまたは Set-ADUser コマンドレットを使用します。

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>管理対象ドメインでコンピューター アカウントにリソースベースの KCD を構成する
コンピューター "contoso100-webapp.contoso100.com" で実行されている Web アプリがあるとします。 この Web アプリは、ドメイン ユーザーのコンテキストでリソース ("contoso100-api.contoso100.com" で実行されている Web API) にアクセスする必要があります。 このシナリオでリソースベースの KCD を設定する方法を次に示します。

```
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>管理対象ドメインでユーザー アカウントにリソースベースの KCD を構成する
サービス アカウント "appsvc" として実行されている Web アプリがあり、この Web アプリがドメイン ユーザーのコンテキストでリソース (サービス アカウント "backendsvc" として実行されている Web API) にアクセスする必要があるとします。 このシナリオでリソースベースの KCD を設定する方法を次に示します。

```
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

## <a name="related-content"></a>関連コンテンツ
* [Azure AD ドメイン サービス - 作業開始ガイド](active-directory-ds-getting-started.md)
* [Kerberos の制約付き委任の概要](https://technet.microsoft.com/library/jj553400.aspx)

