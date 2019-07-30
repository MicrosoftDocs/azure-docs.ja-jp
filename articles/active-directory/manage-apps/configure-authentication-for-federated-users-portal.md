---
title: ホーム領域検出ポリシーを使用して、アプリケーションのサインイン自動高速化を構成する | Microsoft Docs
description: 自動高速化やドメインのヒントを含む、フェデレーション ユーザーのための Azure Active Directory 認証のホーム領域検出ポリシーを構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0534037393f4634364b927020595aa21d8e1b7b3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440366"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>ホーム領域検出ポリシーを使用して、アプリケーションの Azure Active Directory サインイン動作を構成する

この記事では、フェデレーション ユーザーに対する Azure Active Directory 認証動作の構成方法について説明します。 また、フェデレーション ドメイン内のユーザーに対する自動高速化および認証制限の構成方法についても説明します。

## <a name="home-realm-discovery"></a>ホーム領域検出
ホーム領域検出 (HRD) は、ユーザーの認証が必要な場所を、Azure Active Directory (Azure AD) がサインイン時に決定できるようにするプロセスです。  ユーザーは、Azure AD テナントにサインインしてリソース (または Azure AD の共通サインイン ページ) にアクセスするとき、ユーザー名 (UPN) を入力します。 Azure AD はそれを使用して、ユーザーがどこにサインインする必要があるかを決定します。 

ユーザーは、認証を受けるために次のいずれかの場所にアクセスする必要があります。

- ユーザーのホーム テナント (ユーザーがアクセスしようとしているリソースと同じテナントである可能性があります)。 

- Microsoft アカウント。  ユーザーはリソース テナントのゲストです。

-  Active Directory フェデレーション サービス (AD FS) など、オンプレミスの ID プロバイダー。

- Azure AD テナントとフェデレーションされた別の ID プロバイダー。

## <a name="auto-acceleration"></a>自動高速化 
組織によっては、Azure Active Directory テナント内のドメインを、ユーザー認証用の別の IdP (AD FS など) とフェデレーションするように構成することがあります。  

ユーザーがアプリケーションにサインインすると、最初に Azure AD のサインイン ページが表示されます。 UPN を入力した後、フェデレーション ドメイン内にいると、そのドメインにサービスを提供している IdP のサインイン ページが表示されます。 状況によっては、管理者は特定のアプリケーションにサインインしたユーザーに対してサインイン ページを表示したい場合があります。 

そのため、ユーザーは Azure Active Directory の最初のページをスキップできます。 このプロセスは、"サインイン自動高速化" と呼ばれます。

テナントがサインイン用の別の IdP にフェデレーションされている場合、自動高速化によりユーザー サインインがいっそう効率化されます。  自動高速化は個々 のアプリケーションに対して構成できます。

>[!NOTE]
>アプリケーションを自動高速化用に構成した場合、ゲスト ユーザーはサインインできません。 ユーザーを認証用のフェデレーション IdP に導く場合、Azure Active Directory のサインイン ページに戻ることはできません。 ゲスト ユーザーは、他のテナントや Microsoft アカウントなどの外部 IdP に移動されなければならない場合があり、ホーム領域検出の手順がスキップされるため、そのアプリケーションにサインインすることはできません。  

フェデレーション IdP への自動高速化を制御するには、2 つの方法があります。   

- アプリケーションの認証要求でドメイン ヒントを使用する。 
- ホーム領域検出ポリシーを構成して自動高速化を有効にする。

### <a name="domain-hints"></a>ドメイン ヒント    
ドメイン ヒントは、アプリケーションからの認証要求に含まれるディレクティブです。 ドメイン ヒントを使用して、ユーザーのフェデレーション IdP サインイン ページへの移動を高速化できます。 または、マルチテナント アプリケーションで使用して、テナント用にブランディングされた Azure AD サインイン ページをすぐに表示することができます。  

たとえば、"largeapp.com" というアプリケーションがあり、顧客は "contoso.largeapp.com" というカスタム URL でアプリケーションにアクセスできるものとします。 アプリは、認証要求に contoso.com へのドメイン ヒントを組み込んでいる可能性もあります。 

ドメイン ヒントの構文は、使用されているプロトコルによって異なり、通常はアプリケーション内で構成されます。

**WS-Federation**:  クエリ文字列内の whr=contoso.com。

**SAML**: ドメイン ヒントを含んだ SAML 認証要求か、クエリ文字列 whr=contoso.com。

**Open ID Connect**: クエリ文字列 domain_hint=contoso.com。 

アプリケーションからの認証要求にドメイン ヒントが含まれていて、テナントがそのドメインとフェデレーションされている場合、Azure AD は、そのドメイン用に構成された IdP にサインインをリダイレクトしようとします。 

ドメイン ヒントが確認済みのフェデレーション ドメインを参照していない場合、そのドメイン ヒントは無視され、通常のホーム領域検出が起動されます。

Azure Active Directory によってサポートされるドメイン ヒントを使った自動高速化について詳しくは、[Enterprise Mobility + Security のブログ](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/)をご覧ください。

>[!NOTE]
>認証要求にドメイン ヒントが含まれている場合は、HRD ポリシー内でアプリケーションに対して設定されている自動高速化が、そのドメイン ヒントによってオーバーライドされます。

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>自動高速化に対するホーム領域検出ポリシー
アプリケーションで、出力される認証要求を構成する手段が提供されていない場合があります。 そのような場合は、ドメイン ヒントを使って自動高速化を制御することはできません。 そのような場合は、ポリシーを使用して自動高速化を構成することで、同じ動作を実現できます。  

## <a name="enable-direct-authentication-for-legacy-applications"></a>レガシ アプリケーションに対する直接認証を有効にする
アプリケーションで AAD ライブラリと対話型サインインを使用してユーザーを認証するのが最良の方法です。 ライブラリは、フェデレーション ユーザーのフローを処理します。  レガシ アプリケーションによっては、フェデレーションを解釈するように作成されていないものがあります。 これらのアプリケーションは、ホーム領域検出を実行せず、また、目的のフェデレーション エンドポイントと対話してユーザーを認証することもしません。 選択した場合は、HRD ポリシーを使用して、ユーザー名とパスワードの資格情報を送信する特定のレガシー アプリケーションが、Azure Active Directory に対して直接認証を行うようにすることもできます。 パスワード ハッシュ同期を有効にする必要があります。 

> [!IMPORTANT]
> 直接認証を有効にするのは、パスワード ハッシュ同期がオンであり、なおかつオンプレミス IdP でポリシーを実装せずにこのアプリケーションを認証しても問題ないことがわかっている場合のみにしてください。 何らかの理由でパスワード ハッシュ同期をオフにした場合、または AD Connect によるディレクトリ同期をオフにした場合は、このポリシーを削除し、古いパスワード ハッシュを使用して直接認証が行われないようにしてください。

## <a name="set-hrd-policy"></a>HRD ポリシーを設定する
フェデレーション サインイン自動高速化用のアプリケーション、または直接的なクラウドベース アプリケーションに対して HRD ポリシーを設定するには、以下の 3 つのステップを実行します。

1. HRD ポリシーを作成します。

2. ポリシーをアタッチするサービス プリンシパルを探します。

3. サービス プリンシパルにポリシーをアタッチします。 

ポリシーは、サービス プリンシパルにアタッチされてはじめて、特定のアプリケーションに対して有効になります。 

サービス プリンシパル上でアクティブにできる HRD ポリシーは、一度に 1 つだけです。  

HRD ポリシーを作成および管理するには、Microsoft Azure Active Directory Graph API を直接使用する方法と、Azure Active Directory PowerShell コマンドレットを使用する方法があります。

ポリシーを操作する Graph API については、MSDN の記事「[Operations on policy](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations)」(ポリシーに対する操作) をご覧ください。

HRD ポリシーの定義の例を次に示します。
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":true
    }
   }
```

ポリシー タイプは、"HomeRealmDiscoveryPolicy" です。

**AccelerateToFederatedDomain** は、省略可能です。 **AccelerateToFederatedDomain** が false の場合、ポリシーが自動高速化に影響を与えることはありません。 **AccelerateToFederatedDomain** が true で、テナント内に検証およびフェデレーションされたドメインが 1 つのみある場合は、フェデレーション IdP のサインイン ページが直接表示されます。 これが true で、テナント内に検証されたドメインが複数ある場合は、**PreferredDomain** を指定する必要があります。

**PreferredDomain** は、省略可能です。 **PreferredDomain** では、高速化するドメインを指定する必要があります。 テナントのフェデレーション ドメインが 1 つだけの場合は、これを省略できます。  これを省略した場合で、確認済みのフェデレーション ドメインが 2 つ以上ある場合には、ポリシーは効力を持ちません。

 **PreferredDomain** を指定する場合は、テナントの検証済みのフェデレーション ドメインと一致している必要があります。 アプリケーションのすべてのユーザーが、そのドメインにサインインできる必要があります。

**AllowCloudPasswordValidation** は、省略可能です。 **AllowCloudPasswordValidation** が true の場合、アプリケーションは、Azure Active Directory トークン エンドポイントに対して直接ユーザー名とパスワードの資格情報を提示することにより、フェデレーション ユーザーを認証できます。 これが機能するのは、パスワード ハッシュ同期が有効の場合に限られます。

### <a name="priority-and-evaluation-of-hrd-policies"></a>HRD ポリシーの優先順位と評価
HRD ポリシーを作成し、特定の組織およびサービス プリンシパルに割り当てることができます。 つまり、複数のポリシーを特定のアプリケーションに適用できます。 HRD ポリシーは、次の規則に従って発効されます。


- 認証要求内にドメイン ヒントが存在する場合は、自動高速化に関して、すべての HRD ポリシーが無視されます。 ドメイン ヒントによって指定された動作が使われます。

- その他の場合、ポリシーがサービス プリンシパルに明示的に割り当てられていれば、そのポリシーが適用されます。 

- ドメイン ヒントが存在せず、サービス プリンシパルに明示的に割り当てられているポリシーがない場合は、サービス プリンシパルの親組織に明示的に割り当てられているポリシーが適用されます。 

- ドメイン ヒントが存在せず、サービス プリンシパルまたは組織にポリシーが割り当てられていない場合は、既定の HRD 動作が使用されます。

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>アプリケーションの HRD ポリシーの設定に関するチュートリアル 
Azure AD PowerShell コマンドレットを使って次のようなシナリオを設定する手順を示します。


- 1 つのフェデレーション ドメインを持つテナント内でアプリケーションの自動高速化を実行するように HRD ポリシーを設定する。

- テナント用に確認された複数のドメインのいずれかに対してアプリケーションの自動高速化を実行するように HRD ポリシーを設定する。

- フェデレーション ユーザー用の Azure Active Directory に対して、レガシ アプリケーションがユーザー名とパスワードの認証を直接実行できるように HRD ポリシーを設定する。

- ポリシーが構成されているアプリケーションを一覧表示する。


### <a name="prerequisites"></a>前提条件
以下の例では、Azure AD 内のアプリケーション サービス プリンシパルを対象に、ポリシーを作成、更新、リンク、および削除します。

1.  まず、最新版の Azure AD PowerShell コマンドレットのプレビューをダウンロードします。 

2.  Azure AD PowerShell コマンドレットをダウンロードした後、Connect コマンドを実行して、Azure AD に管理者アカウントでサインインします。

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  次のコマンドを実行して、組織内のすべてのポリシーを表示します。

    ``` powershell
    Get-AzureADPolicy
    ```

何も返されない場合は、テナント内にポリシーが作成されていないことを意味します。

### <a name="example-set-hrd-policy-for-an-application"></a>例:アプリケーション用の HRD ポリシーを設定する 

この例では、アプリケーションに割り当てられたときに以下を行うポリシーを作成します。 
- テナント内のドメインが 1 つのみの場合、ユーザーがアプリケーションにサインインするときに、AD FS サインイン画面への移動を自動高速化する。 
- テナント内にフェデレーション ドメインが複数あるときに、AD FS サインイン画面への移動を自動高速化する。
- ポリシー割り当て先のアプリケーションのフェデレーション ユーザー用の Azure Active Directory に対して、非対話型のユーザー名/パスワード サインインを直接行えるようにする。

#### <a name="step-1-create-an-hrd-policy"></a>手順 1:HRD ポリシーを作成する

以下のポリシーは、テナント内のドメインが 1 つのみの場合、ユーザーがアプリケーションにサインインするときに、AD FS サインイン画面への移動を自動高速化します。

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
以下のポリシーは、テナント内にフェデレーション ドメインが複数あるときに、AD FS サインイン画面への移動を自動高速化します。 アプリケーションのユーザーを認証するフェデレーション ドメインが複数ある場合は、自動高速化するドメインを指定する必要があります。

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

特定のアプリケーション用の Azure Active Directory に対し、ユーザー名とパスワードを使用してフェデレーション ユーザーを直接認証できるようにするポリシーを作成するには、以下のコマンドを実行します。

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


新しいポリシーを表示し、その **ObjectId** を取得するには、次のコマンドを実行します。

``` powershell
Get-AzureADPolicy
```


作成した HRD ポリシーを適用するには、複数のアプリケーション サービス プリンシパルに HRD ポリシーを割り当てる必要があります。

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>手順 2:ポリシーを割り当てるサービス プリンシパルを見つける  
ポリシーを割り当てるサービス プリンシパルの **ObjectID** が必要となります。 サービス プリンシパルの **ObjectID** を検索するには、複数の方法があります。    

ポータルを使うか、[Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) のクエリを行うことができます。 また、[Graph エクスプローラー ツール](https://developer.microsoft.com/graph/graph-explorer)に移動して Azure AD アカウントにサインインし、組織のすべてのサービス プリンシパルを表示することもできます。 

PowerShell を使用しているため、次のコマンドレットを使用してサービス プリンシパルとその ID を一覧表示できます。

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>手順 3:サービス プリンシパルにポリシーを割り当てる  
自動高速化を構成するアプリケーションのサービス プリンシパルの **ObjectID** を入手した後、次のコマンドを実行します。 このコマンドは、手順 1 で作成した HRD ポリシーを、手順 2 で取得したサービス プリンシパルに関連付けます。

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

このコマンドは、ポリシーを追加する各サービス プリンシパルに対して繰り返し実行できます。

アプリケーションに HomeRealmDiscovery ポリシーが既に割り当てられている場合は、さらにポリシーを追加することはできません。  その場合は、アプリケーションに割り当てられているホーム領域検出ポリシーの定義を変更して、別のパラメーターを追加します。

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>手順 4:HRD ポリシーが割り当てられているアプリケーション サービス プリンシパルを確認する
HRD ポリシーがどのアプリケーションに構成されているかを確認するには、**Get-AzureADPolicyAppliedObject** コマンドレットを使用します。 そして、確認するポリシーの **ObjectID** を渡します。

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>手順 5:以上で終わりです。
アプリケーションを実行して、新しいポリシーが機能していることを確認します。

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>例:HRD ポリシーが構成されているアプリケーションを一覧表示する

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>手順 1:組織内で作成されたすべてのポリシーを一覧表示する 

``` powershell
Get-AzureADPolicy
```

割り当てを一覧表示するポリシーの **ObjectID** をメモします。

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>手順 2:ポリシーが割り当てられているサービス プリンシパルを一覧表示する  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>例:アプリケーション用の HRD ポリシーを削除する
#### <a name="step-1-get-the-objectid"></a>手順 1:ObjectID を取得する
先述の例を使って、ポリシーの **ObjectID** と、ポリシーを削除するアプリケーション サービス プリンシパルのオブジェクト ID を取得します。 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>手順 2:アプリケーション サービス プリンシパルからポリシーの割り当てを削除する  

``` powershell
Remove-AzureADApplicationPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>手順 3:ポリシーが割り当てられているサービス プリンシパルを一覧表示して、削除を確認する 

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
## <a name="next-steps"></a>次の手順
- Azure AD での認証のしくみについて詳しくは、「[Azure AD の認証シナリオ](../develop/authentication-scenarios.md)」をご覧ください。
- ユーザー シングル サインオンについて詳しくは、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](configure-single-sign-on-portal.md)」をご覧ください。
- 開発者向けのすべての関連コンテンツの概要については、[Active Directory 開発者ガイド](../develop/v1-overview.md)を参照してください。
