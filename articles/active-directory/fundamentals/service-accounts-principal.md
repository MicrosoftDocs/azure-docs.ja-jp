---
title: Azure Active Directory でのサービス プリンシパルのセキュリティ保護
description: サービス プリンシパルを検索し、評価して、セキュリティで保護します。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bab8e8c6dfb944e496c636d53217e63175be9fbc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587850"
---
# <a name="securing-service-principals"></a>サービス プリンシパルのセキュリティ保護

Azure Active Directory (Azure AD) [サービス プリンシパル](../develop/app-objects-and-service-principals.md)は、単一のテナントまたはディレクトリ内のアプリケーション オブジェクトをローカルに表現したものです。  これは、アプリケーション インスタンスの ID として機能します。 サービス プリンシパルは、誰がアプリケーションにアクセスできるかと、アプリケーションがどのリソースにアクセスできるかを定義するものです。 サービス プリンシパルは、アプリケーションが使用される各テナント内に作成されて、グローバルに一意なアプリケーション オブジェクトを参照します。 テナントによって、サービス プリンシパルのサインインとリソースへのアクセスがセキュリティで保護されます。  

### <a name="tenant-service-principal-relationships"></a>テナントとサービス プリンシパルの関係
シングルテナント アプリケーションには、そのホーム テナント内にサービス プリンシパルが 1 つだけあります。 マルチテナント Web アプリケーションまたは API では、各テナント内にサービス プリンシパルが 1 つ必要です。 サービス プリンシパルは、そのテナントのユーザーがアプリケーションまたは API の使用に同意したときに作成されます。 この同意によって、マルチテナント アプリケーションとそれに関連付けられたサービス プリンシパルの間に、一対多の関係が作成されます。

マルチテナント アプリケーションは、1 つのテナントに所属し、他のテナント内にインスタンスを持つように設計されています。 サービスとしてのソフトウェア (SaaS) アプリケーションのほとんどは、マルチテナント向けに設計されています。 サービス プリンシパルを使用して、シングルテナントとマルチテナントの両方のユースケースで、アプリケーションとそのユーザーにとって適切なセキュリティ体制を確保します。

## <a name="applicationid-and-objectid"></a>ApplicationID と ObjectID

特定のアプリケーション インスタンスには、ApplicationID (ClientID とも呼ばれます) と ObjectID という 2 つの別個のプロパティがあります。

> [!NOTE] 
> 認証関連タスクのコンテキストでアプリケーションをゆるやかに参照する場合、アプリケーションとサービス プリンシパルという用語は同じ意味で使用されることがあります。 ただし Azure AD では、これらはアプリケーションの 2 つの異なる表現です。
 

ApplicationID は、グローバルなアプリケーションを表し、テナント全体のすべてのアプリケーション インスタンスで同一です。 ObjectID は、アプリケーション オブジェクトの一意の値であり、サービス プリンシパルを表します。 ユーザー、グループ、その他のリソースと同様に、ObjectID は、Azure AD 内のアプリケーション インスタンスを一意に識別するのに役立ちます。

このトピックの詳細については、[アプリケーションとサービス プリンシパルの関係](../develop/app-objects-and-service-principals.md)に関するページを参照してください。

Azure PowerShell、Azure CLI、Microsoft Graph、Azure portal、およびその他のツールを使用して、テナント内にアプリケーションとそのサービス プリンシパル オブジェクト (ObjectID) を作成することもできます。 

![アプリケーション ID とオブジェクト ID のフィールドが強調表示されている、新しいアプリケーションの登録を示すスクリーンショット。](./media/securing-service-accounts/secure-principal-image-1.png)

## <a name="service-principal-authentication"></a>サービス プリンシパルの認証

サービス プリンシパルを使用した認証には、クライアント証明書とクライアント シークレットという 2 つのメカニズムがあります。 

![ 強調表示されている [証明書] と [クライアント シークレット] の領域を示している [新しいアプリ] ページのスクリーンショット。](./media/securing-service-accounts/secure-principal-certificates.png)

証明書のほうがより安全です。可能な場合はクライアント証明書を使用します。 クライアント シークレットとは異なり、クライアント証明書が誤ってコードに埋め込まれることはありません。 以下の資産を暗号化できる場合は、ハードウェア セキュリティ モジュールによって保護されているキーを使用して、証明書とシークレットの管理に Azure Key Vault を使用します。

* 認証キー

* ストレージ アカウント キー

* データ暗号化キー

* .pfx ファイル

* パスワード 

Azure Key Vault と、それを証明書とシークレットの管理に使用する方法の詳細については、「[Azure Key Vault について](../../key-vault/general/overview.md)」と「[Azure portal を使用して Key Vault アクセス ポリシーを割り当てる](../../key-vault/general/assign-access-policy-portal.md)」を参照してください。 

 ### <a name="challenges-and-mitigations"></a>課題と軽減策
次の表に、サービス プリンシパルの使用時に発生する可能性がある課題の軽減策を示します。


| 課題| 軽減策 |
| - | - |
| 特権ロールに割り当てられたサービス プリンシパルのアクセス レビュー。| この機能はプレビュー段階であり、まだ広く提供されていません。 |
| サービス プリンシパルのアクセス レビュー| Azure portal を使用したリソースのアクセス制御リストの手動チェック |
| 過剰なアクセス許可が付与されたサービス プリンシパル| オートメーション サービス アカウントまたはサービス プリンシパルを作成するときには、そのタスクに必要なアクセス許可だけを提供します。 既存のサービス プリンシパルを評価して、特権を減らすことができるかどうかを確認します。 |
|サービス プリンシパルの資格情報または認証方法に対する ID の変更 |この問題を軽減するのに役立つ Sensitive Operations Report ワークブックを使用します。 [このブログ記事の説明を参照してください](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718)。|

## <a name="find-accounts-using-service-principals"></a>サービス プリンシパルを使用してアカウントを見つける
サービス プリンシパルを使用してアカウントを見つけるには、次のコマンドを実行します。

Azure CLI の使用


`az ad sp list`

PowerShell の使用

`Get-AzureADServicePrincipal -All:$true` 


詳細については、[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) を参照してください。

## <a name="assess-service-principal-security"></a>サービス プリンシパルのセキュリティを評価する

サービス プリンシパルのセキュリティを評価するには、必ず、特権と資格情報の保存について評価してください。

以下の情報を使用して、潜在的な課題を軽減します。
|課題 | 軽減策|
| - | - |
| マルチテナント アプリに同意したユーザーを検出し、マルチテナント アプリに対する不正な同意の付与を検出します | 次の PowerShell を実行して、マルチテナント アプリを見つけます。<br>`Get-AzureADServicePrincipal -All:$true ? {$_.Tags -eq WindowsAzureActiveDirectoryIntegratedApp"}`<br>ユーザーの同意を無効にします。 <br>選択されたアクセス許可について、確認済みの発行元からのユーザーの同意を許可する (推奨) <br> 条件付きアクセスを使用して、信頼されていない場所からのサービス プリンシパルをブロックします。 これらをユーザー コンテキストのもとで構成します。また、それらのトークンを使用してサービス プリンシパルをトリガーする必要があります。|
|サービス プリンシパルを使用した、スクリプト内でのハードコーディングされた共有シークレットの使用。|証明書または Azure Key Vault を使用します。|
|誰が証明書またはシークレットを使用しているかの追跡| Azure AD サインイン ログを使用して、サービス プリンシパルのサインインを監視します。|
条件付きアクセスではサービス プリンシパルのサインインを管理できません。| Azure AD サインイン ログを使用してサインインを監視します。
| 既定の Azure RBAC の役割は共同作成者です。 |ニーズを評価し、そのニーズを満たすために最小限のアクセス許可を持つ役割を適用します。|

## <a name="move-from-a-user-account-to-a-service-principal"></a>ユーザー アカウントからサービス プリンシパルに移行する  
‎サービス プリンシパルとして Azure ユーザー アカウントを使用している場合は、[マネージド ID](../../app-service/overview-managed-identity.md?tabs=dotnet) またはサービス プリンシパルに移行できるかどうかを評価します。 マネージド ID を使用できない場合は、必要なタスクを実行するのに十分なアクセス許可とスコープだけを持つサービス プリンシパルをプロビジョニングします。 サービス プリンシパルは、[アプリケーションを登録する](../develop/howto-create-service-principal-portal.md)か、[PowerShell](../develop/howto-authenticate-service-principal-powershell.md) を使用して作成できます。

Microsoft Graph の使用時は、[この例のように](/powershell/azure/create-azure-service-principal-azureps)、特定の API のドキュメントを確認し、‎アプリケーションのアクセス許可の種類がサポート対象として表示されていることを確認してください。

## <a name="next-steps"></a>次のステップ

**サービス プリンシパルに関する詳細情報を確認する場合:**

[サービス プリンシパルの作成](../develop/howto-create-service-principal-portal.md)

 [サービス プリンシパルのサインインの監視](../reports-monitoring/concept-sign-ins.md#sign-ins-report)

**サービス アカウントのセキュリティ保護の詳細を確認する場合:**

[Azure サービス アカウントの概要](service-accounts-introduction-azure.md)

[マネージド ID のセキュリティ保護](service-accounts-managed-identities.md)

[Azure サービス アカウントの管理](service-accounts-governing-azure.md)

[オンプレミス サービス アカウントの概要](service-accounts-on-premises.md)