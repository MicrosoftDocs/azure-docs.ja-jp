---
title: Azure AD Domain Services に対する Kerberos の制約付き委任 | Microsoft Docs
description: Azure Active Directory Domain Services のマネージド ドメインでリソースベースの Kerberos の制約付き委任 (KCD) を有効にする方法を説明します。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 138b90a33ff1dbc4b014f17fa0098112e1da66e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619778"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services で Kerberos の制約付き委任 (KCD) を構成する

アプリケーションの実行時に、他のユーザーのコンテキストでリソースにアクセスするために、これらのアプリケーションが必要になる場合があります。 Active Directory Domain Services (AD DS) では、このユース ケースを可能にする、"*Kerberos の委任*" というメカニズムをサポートしています。 Kerberos の "*制約付き*" 委任 (KCD) は、このメカニズムに基づいて構築され、ユーザーのコンテキストでアクセスできる特定のリソースを定義します。

Azure Active Directory Domain Services (Azure AD DS) のマネージド ドメインは、従来のオンプレミス AD DS 環境よりも安全にロックダウンされているため、より安全な "*リソースベース*" の KCD を使用してください。

この記事では、Azure AD DS のマネージド ドメインでリソースベースの Kerberos の制約付き委任を構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下のリソースが必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要に応じて、[Azure Active Directory Domain Services のマネージド ドメインを作成して構成][create-azure-ad-ds-instance]します。
* Azure AD DS マネージド ドメインに参加している Windows Server 管理 VM。
    * 必要に応じて、[Windows Server VM を作成してマネージド ドメインに参加させる][create-join-windows-vm]チュートリアルと、[AD DS 管理ツールをインストールする][tutorial-create-management-vm]チュートリアルを完了します。
* Azure AD テナントの *Azure AD DC administrators* グループのメンバーであるユーザー アカウント。

## <a name="kerberos-constrained-delegation-overview"></a>Kerberos の制約付き委任の概要

Kerberos の委任を使用すると、あるアカウントが別のアカウントを偽装してリソースにアクセスできるようになります。 たとえば、バックエンド Web コンポーネントにアクセスする Web アプリケーションは、バックエンド接続を行うときに、別のユーザー アカウントとして偽装できます。 Kerberos の委任は、偽装する側のアカウントがどのリソースにアクセスできるかの制限がないため、安全ではありません。

Kerberos の "*制約付き*" 委任 (KCD) では、指定されたサーバーまたはアプリケーションが別の ID を偽装している場合に、接続できるサービスまたはリソースを制限します。 従来の KCD では、サービスのドメイン アカウントを構成するのにドメイン管理者の特権が必要です。また、そのアカウントは 1 つのドメイン上での実行だけに制限されます。

従来の KCD にも、いくつかの問題があります。 たとえば、以前のオペレーティング システムでは、サービス管理者には、所有しているリソース サービスにどのフロントエンド サービスが委任されるかを把握する便利な方法はありませんでした。 リソース サービスに委任されるフロントエンド サービスは、攻撃ポイントになる可能性がありました。 リソース サービスに委任されるように構成されているフロントエンド サービスをホストするサーバーが侵害された場合は、リソース サービスも侵害される可能性がありました。

マネージド ドメインでは、ドメイン管理者特権がありません。 そのため、マネージド ドメインでは、従来のアカウントベースの KCD は構成できません。 代わりに、リソースベースの KCD を使用できます。これも、より安全な方法です。

### <a name="resource-based-kcd"></a>リソースベースの KCD

Windows Server 2012 以降では、サービス管理者はサービスに制約付き委任を構成できるようになりました。 このモデルは、リソースベースの KCD と呼ばれます。 この方法では、バックエンド サービス管理者は特定のフロントエンド サービスによる KCD の使用を許可または拒否できます。

リソースベースの KCD は PowerShell を使用して構成します。 偽装する側のアカウントがコンピューター アカウントであるか、ユーザー アカウントまたはサービス アカウントであるかに応じて、[Set-ADComputer][Set-ADComputer] コマンドレットまたは [Set-ADUser][Set-ADUser] コマンドレットを使用します。

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>コンピューター アカウントにリソースベースの KCD を構成する

このシナリオでは、*contoso-webapp.aaddscontoso.com* というコンピューター上で実行されている Web アプリがあるとします。

この Web アプリは、*contoso-api.aaddscontoso.com* という名前のコンピューター上でドメイン ユーザーのコンテキストで実行されている Web API にアクセスする必要があります。

このシナリオを構成するには、以下の手順を実行します。

1. [カスタム OU を作成します](create-ou.md)。 このカスタム OU を管理する権限を、マネージド ドメイン内のユーザーに委任できます。
1. [仮想マシンをドメイン参加][create-join-windows-vm]させます。Web アプリを実行しているものと Web API を実行しているものの両方を、マネージド ドメインに参加させます。 前の手順のカスタム OU 内に、これらのコンピューター アカウントを作成します。

    > [!NOTE]
    > Web アプリと Web API のコンピューター アカウントは、リソースベースの KCD を構成するアクセス許可を持つカスタム OU 内にある必要があります。 組み込みの *AAD DC Computers* コンテナー内にあるコンピューター アカウントに対して、リソースベースの KCD を構成することはできません。

1. 最後に、[Set-ADComputer][Set-ADComputer] PowerShell コマンドレットを使用して、リソースベースの KCD を構成します。

    ドメインに参加している管理 VM で、"*Azure AD DC 管理者*" グループのメンバーであるユーザー アカウントとしてログインし、次のコマンドレットを実行します。 必要に応じて、独自のコンピューター名を指定します。
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>ユーザー アカウントにリソースベースの KCD を構成する

このシナリオでは、*appsvc* という名前のサービス アカウントとして実行される Web アプリがあるとします。 この Web アプリは、ドメイン ユーザーのコンテキストで *backendsvc* という名前のサービス アカウントとして実行されている Web API にアクセスする必要があります。 このシナリオを構成するには、以下の手順を実行します。

1. [カスタム OU を作成します](create-ou.md)。 このカスタム OU を管理する権限を、マネージド ドメイン内のユーザーに委任できます。
1. マネージド ドメインに、バックエンド Web API およびリソースを実行している[仮想マシンをドメイン参加][create-join-windows-vm]させます。 カスタム OU 内にそのコンピューター アカウントを作成します。
1. カスタム OU 内で Web アプリを実行するために使用するサービス アカウント (*appsvc* など) を作成します。

    > [!NOTE]
    > ここでも、Web API VM のコンピューター アカウントと、Web アプリのサービス アカウントは、リソースベースの KCD を構成するアクセス権を持つカスタム OU 内にある必要があります。 組み込みの *AAD DC Computers* または *AAD DC Users* コンテナー内にあるアカウントに対して、リソースベースの KCD を構成することはできません。 このことは、Azure AD から同期されたユーザー アカウントを、リソースベースの KCD を設定するためには使用できないことも意味しています。 Azure AD DS で特別に作成されるサービス アカウントを作成して使用する必要があります。

1. 最後に、[Set-ADUser][Set-ADUser] PowerShell コマンドレットを使用して、リソースベースの KCD を構成します。

    ドメインに参加している管理 VM で、"*Azure AD DC 管理者*" グループのメンバーであるユーザー アカウントとしてログインし、次のコマンドレットを実行します。 必要に応じて、独自のサービス名を指定します。

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>次のステップ

Active Directory Domain Services で委任がどのように機能するかの詳細については、「[Kerberos の制約付き委任の概要][kcd-technet]」を参照してください。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)