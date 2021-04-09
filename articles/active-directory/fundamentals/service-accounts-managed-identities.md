---
title: Azure Active Directory のマネージド ID のセキュリティ保護
description: マネージド ID のセキュリティを確認、評価、強化する方法について説明します。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4584b0a605bd63c1f71082014d3c0622ca7d2c37
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587833"
---
# <a name="securing-managed-identities"></a>マネージド ID のセキュリティ保護

異なるサービス間でやり取りされる通信のセキュリティを確保するためのシークレットと資格情報の管理は、開発者に共通の課題です。 マネージド ID は、Azure リソースに ID を提供するために作成される、セキュリティで保護された Azure Active Directory (Azure AD) の ID です。

## <a name="benefits-of-using-managed-identities-for-azure-resources"></a>Azure リソースにマネージド ID を使用する利点

マネージド ID を使用する利点を次に示します。

* 資格情報を自ら管理する必要がない。 マネージド ID を使用すると、資格情報は Azure によって完全に管理、ローテーション、保護されます。 ID は自動的に提供され、Azure リソースと共に削除されます。 マネージド ID により、Azure リソースと、Azure AD 認証をサポートするすべてのサービスとの通信が可能になります。

* 資格情報には (グローバル管理者を含めて) だれもアクセスできないため、コードに組み込まれるなどして誤って漏洩することがない。

## <a name="when-to-use-managed-identities"></a>マネージド ID を使用する場合

マネージド ID は、Azure AD 認証をサポートするサービス間の通信に最適です。 

ソース システムからターゲット サービスにアクセスを要求します。 任意の Azure リソースをソース システムにすることができます。 たとえば、Azure VM、Azure Functions インスタンス、Azure App Services インスタンスでマネージド ID がサポートされています。

[!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

### <a name="how-authentication-and-authorization-work"></a>認証と承認のしくみ

マネージド ID を使用すると、Azure AD からソース システムにトークンを取得できます。ソース所有者が資格情報を管理する必要はありません。 資格情報は Azure で管理されます。 ソース システムによって取得されたトークンは、認証のためにターゲット システムに提示されます。 

アクセスが許可される前に、ソース システムがターゲット システムで認証 (識別) され、承認される必要があります。 ターゲット サービスで Azure AD ベースの認証がサポートされている場合、Azure AD によって発行されたアクセス トークンが受け入れられます。 

Azure には、コントロール プレーンとデータ プレーンがあります。 コントロール プレーンでリソースを作成し、データ プレーンでそれにアクセスします。 たとえば、コントロール プレーンで Cosmos データベースを作成しますが、そのクエリはデータ プレーンで実行します。

ターゲット システムで認証のためにトークンが受け入れられると、コントロール プレーンとデータ プレーン用に承認のさまざまなメカニズムをサポートできるようになります。

Azure のコントロール プレーン操作はすべて [Azure Resource Manager](../../azure-resource-manager/management/overview.md) によって管理され、[Azure のロールベースのアクセス制御](../../role-based-access-control/overview.md)が適用されます。 データ プレーンには、各ターゲット システムごとに独自の承認メカニズムがあります。 Azure Storage によってデータ プレーンで Azure RBAC がサポートされています。 たとえば、Azure App Services を使用するアプリケーションで Azure Storage からデータを読み取ることができ、Azure Kubernetes Service を使用するアプリケーションで Azure Key Vault に格納されているシークレットを読み取ることができます。

コントロールおよびデータ プレーンの詳細については、[Azure Resource Manager のコントロール プレーンとデータ プレーンの概念](../../azure-resource-manager/management/control-plane-and-data-plane.md)に関するページを参照してください。

最終的にすべての Azure サービスでマネージド ID がサポートされます。 詳細については、「[Azure リソースのマネージド ID をサポートするサービス](../managed-identities-azure-resources/services-support-managed-identities.md)」を参照してください。

##  

## <a name="types-of-managed-identities"></a>マネージド ID の種類

マネージド ID には、システム割り当てとユーザー割り当ての 2 種類があります。

システム割り当てマネージド ID には次のプロパティがあります。

* Azure リソースと 1:1 の関係があります。 たとえば、各 VM に関連付けられた一意のマネージド ID があります。

* これらは、Azure リソースのライフサイクルに関連付けられています。 リソースが削除されると、そのリソースに関連付けられているマネージド ID が自動的に削除され、孤立したアカウントに関連するリスクは生じません。 

ユーザー割り当てマネージド ID には次のプロパティがあります。

* これらの ID のライフサイクルは Azure リソースとは無関係であり、ご自身でライフサイクルを管理する必要があります。 割り当て済みのユーザー割り当てマネージド ID は、Azure リソースが削除されても自動的に削除されません。

* 1 個のユーザー割り当てマネージド ID は、0 個以上の Azure リソースに割り当てることができます。

* あらかじめ作成しておいて、リソースに割り当てることができます。

## <a name="find-managed-identity-service-principals-in-azure-ad"></a>Azure AD のマネージド ID サービス プリンシパルを確認する

マネージド ID を確認するには、いくつかの方法があります。

* Azure portal の [エンタープライズ アプリケーション] ページを使用する

* Microsoft Graph を使用する

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

1. Azure AD で、[エンタープライズ アプリケーション] を選択します。

2. [マネージド ID] のフィルターを選択します。 

   ![[すべてのアプリケーション] 画面の画像。[アプリケーションの種類] ドロップダウンで [マネージド ID] が強調表示されています。](./media/securing-service-accounts/service-accounts-managed-identities.png)

 

### <a name="using-microsoft-graph"></a>Microsoft Graph を使用する

Microsoft Graph に対する次の GET 要求を使用して、ご自身のテナント内のすべてのマネージド ID の一覧を取得できます。

`https://graph.microsoft.com/v1.0/servicePrincipals?$filter=(servicePrincipalType eq 'ManagedIdentity') `

これらの要求をフィルター処理できます。 詳細については、Graph のドキュメントで「[servicePrincipal を取得する](/graph/api/serviceprincipal-get?view=)」を参照してください。

## <a name="assess-the-security-of-managed-identities"></a>マネージド ID のセキュリティを評価する 

マネージド ID のセキュリティは、次の方法で評価できます。

* 権限を調べて、最小特権モデルが確実に選択されているようにします。 次の PowerShell コマンドレットを使用して、マネージド ID に割り当てられているアクセス許可を取得します。

   ` Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

 
* マネージド ID が管理者グループなどのすべての特権グループに含まれていないことを確保します。  
これを行うには、高度な特権を持つグループのメンバーを PowerShell で列挙します。

   `Get-AzureADGroupMember -ObjectId <String> [-All <Boolean>] [-Top <Int32>] [<CommonParameters>]`

* [マネージド ID でアクセスしているリソースをご自身が確実に把握している](../../role-based-access-control/role-assignments-list-powershell.md)ようにします。

## <a name="move-to-managed-identities"></a>マネージド ID に移行する

サービス プリンシパルまたは Azure AD ユーザー アカウントを使用している場合は、代わりにマネージドを使用して資格情報の保護、ローテーション、管理を不要にすることができないか評価してください。 

## <a name="next-steps"></a>次のステップ

**マネージド ID の作成については、以下を参照してください。** 

[ユーザー割り当てマネージド ID を作成する](../managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 

[VM の作成中にシステム割り当てマネージド ID を有効にする](../managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

[既存の VM でシステム割り当てマネージド ID を有効にする](../managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**サービス アカウントの詳細については、以下を参照してください。**

[Azure Active Directory サービス アカウントの概要](service-accounts-introduction-azure.md)

[サービス プリンシパルのセキュリティ保護](service-accounts-principal.md)

[Azure サービス アカウントの管理](service-accounts-governing-azure.md)

[オンプレミス サービス アカウントの概要](service-accounts-on-premises.md)

 

 

