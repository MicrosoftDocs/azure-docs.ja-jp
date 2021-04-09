---
title: Azure AD Domain Services のグループの管理されたサービス アカウント | Microsoft Docs
description: Azure Active Directory Domain Services マネージド ドメインで使用する、グループで管理されるサービス アカウント (gMSA) を作成する方法について説明します。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: f975d3e0e605b7c24b9fd31dc8fc78f0f37bb6b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619986"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services でグループで管理されるサービス アカウント (gMSA) を作成する

多くの場合、アプリケーションとサービスは、他のリソースで自身を認証するために ID を必要とします。 たとえば、Web サービスは、データベース サービスで認証を行う必要がある可能性があります。 アプリケーションまたはサービスに複数のインスタンス (Web サーバー ファームなど) がある場合、それらのリソースの ID を手動で作成して構成すると時間がかかります。

代わりに、グループの管理されたサービス アカウント (gMSA) を Azure Active Directory Domain Services (Azure AD DS) マネージド ドメインで作成することができます。 Windows OS は、gMSA の資格情報を自動的に管理します。これにより、大規模なリソース グループの管理が簡素化されます。

この記事では、Azure PowerShell を使用してマネージド ドメインで gMSA を作成する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要に応じて、[Azure Active Directory Domain Services マネージド ドメインを作成して構成する][create-azure-ad-ds-instance]チュートリアルを完了します。
* Azure AD DS マネージド ドメインに参加している Windows Server 管理 VM。
    * 必要に応じて、[管理 VM を作成する][tutorial-create-management-vm]チュートリアルを完了します。

## <a name="managed-service-accounts-overview"></a>管理されたサービス アカウントの概要

スタンドアロンの管理されたサービス アカウント (sMSA) とは、パスワードが自動的に管理されるドメイン アカウントです。 このアプローチでは、サービス プリンシパル名 (SPN) の管理が簡素化され、他の管理者への委任管理が可能になります。 アカウントの資格情報を手動で作成したりローテーションしたりする必要はありません。

グループの管理されたサービス アカウント (gMSA) は、同じ管理の簡素化をドメイン内の複数のサーバーに対して提供します。 gMSA を使用すると、サーバー ファームでホストされているサービスのすべてのインスタンスが、相互認証プロトコルを機能させるために同じサービス プリンシパルを使用できます。 gMSA がサービス プリンシパルとして使用されると、Windows オペレーティング システムは、管理者に依存する代わりに、再びアカウントのパスワードを管理します。

詳細については、[グループの管理されたサービス アカウントの (gMSA) 概要][gmsa-overview]に関する記事を参照してください。

## <a name="using-service-accounts-in-azure-ad-ds"></a>Azure AD DS でのサービス アカウントの使用

マネージド ドメインは、Microsoft によってロックダウンおよび管理されるため、サービス アカウントを使用する際の考慮事項がいくつかあります。

* マネージド ドメインのカスタム組織単位 (OU) 内にサービス アカウントを作成する。
    * 組み込みの *AADDC ユーザー* または *AADDC コンピューター* の OU には、サービス アカウントを作成できません。
    * 代わりに、マネージド ドメインに[カスタム OU を作成][create-custom-ou]し、そのカスタム OU 内にサービス アカウントを作成します。
* キー配布サービス (KDS) ルート キーが事前に作成されている。
    * KDS ルート キーは、gMSA のパスワードの生成と取得に使用されます。 Azure AD DS では、KDS ルートは自動的に作成されます。
    * 別のものを作成したり、既定の KDS ルート キーを表示したりする権限はありません。

## <a name="create-a-gmsa"></a>gMSA を作成する

まず、[New-ADOrganizationalUnit][New-AdOrganizationalUnit] コマンドレットを使用してカスタム OU を作成します。 カスタム OU の作成と管理について詳しくは、[Azure AD DS のカスタム OU][create-custom-ou] に関する記事を参照してください。

> [!TIP]
> これらの手順を完了して gMSA を作成するには、[管理 VM 使用します][tutorial-create-management-vm]。 この管理 VM には、必要な AD PowerShell コマンドレットと管理対象ドメインへの接続が既に存在している必要があります。

次の例では、*aaddscontoso.com* という名前のマネージド ドメインで、*myNewOU* という名前のカスタム OU が作成されます。 独自の OU とマネージド ドメイン名を使用します。

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

次に、[New-ADServiceAccount][New-ADServiceAccount] コマンドレットを使用して gMSA を作成します。 次のパラメーターの例が定義されています。

* **-Name** は *WebFarmSvc* に設定されます
* **-Path** パラメーターは、前の手順で作成された gMSA のカスタム OU を指定します。
* DNS エントリとサービス プリンシパル名が *WebFarmSvc.aaddscontoso.com* に対して設定されます。
* *AADDSCONTOSO-SERVER$* のプリンシパルでは、パスワードを取得して ID を使用することが可能です。

独自の名前とドメイン名を指定します。

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.aaddscontoso.com `
    -Path "OU=MYNEWOU,DC=aaddscontoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.aaddscontoso.com/aaddscontoso.com, `
        http/WebFarmSvc.aaddscontoso.com/aaddscontoso, `
        http/WebFarmSvc/aaddscontoso.com, `
        http/WebFarmSvc/aaddscontoso `
    -PrincipalsAllowedToRetrieveManagedPassword AADDSCONTOSO-SERVER$
```

これで、必要に応じて gMSA を使用するようにアプリケーションとサービスを構成できるようになりました。

## <a name="next-steps"></a>次のステップ

gMSA の詳細については、「[グループの管理されたサービス アカウントの概要][gmsa-start]」を参照してください。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
