---
title: "Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト | Microsoft Docs"
description: "Azure Active Directory におけるアプリケーション オブジェクトとサービス プリンシパル オブジェクトのリレーションシップについての説明"
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
services: active-directory
editor: 
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2016
ms.author: bryanla;mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 8f70d9aeb0a407cdb76a5ce25eb620be58bb2659
ms.openlocfilehash: f453dcafe629c871dc29742208e4864454f4c57e


---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト
Azure Active Directory (Azure AD) の "アプリケーション" という用語の意味は誤解されやいものであり、裏付けとなるコンテキストが不足している場合は特にその可能性が高まります。 この記事の目的は、Azure AD アプリケーションの統合について概念的な側面と具体的な側面を、[マルチテナント アプリケーション](active-directory-dev-glossary.md#multi-tenant-application)の登録および同意の例を示しながら明らかにして、この用語の意味をよりわかりやすくすることです。

## <a name="overview"></a>概要
Azure AD アプリケーションとは、単なるソフトウェアのことではありません。 これは、アプリケーション ソフトウェアだけでなく、その Azure AD への登録 (ID 構成とも呼ばれる) も意味する概念的な用語です。Azure AD に登録することで、そのアプリケーション ソフトウェアは、実行時に認証および承認の "対話" に参加できるようになります。 定義上、アプリケーションは[クライアント](active-directory-dev-glossary.md#client-application) ロール (リソースの使用)、[リソース サーバー](active-directory-dev-glossary.md#resource-server) ロール (クライアントへの API の公開)、またはその両方のロールで動作できます。 対話プロトコルは [OAuth 2.0 承認付与フロー](active-directory-dev-glossary.md#authorization-grant)によって定義されており、その目的は、クライアントとリソースがそれぞれリソースのデータに対してアクセス/保護を実行できるようにすることです。 次はもう&1; 段階掘り下げ、Azure AD アプリケーション モデルにおいて、アプリケーションが内部的にはどのように表されるのかを見てみましょう。 

## <a name="application-registration"></a>アプリケーションの登録
[Azure portal][AZURE-Portal] でアプリケーションを登録すると、Azure AD テナントに、アプリケーション オブジェクトとサービス プリンシパル オブジェクトという&2; つのオブジェクトが作成されます。

#### <a name="application-object"></a>アプリケーション オブジェクト
Azure AD アプリケーションは、その唯一のアプリケーション オブジェクトによって*定義*されます。アプリケーション オブジェクトは、アプリケーションの登録先である Azure AD テナント (アプリケーションの "ホーム" テナントと呼ばれます) 内にあります。 アプリケーション オブジェクトは、ID に関連するアプリケーションの情報を提供するオブジェクトであり、対応するサービス プリンシパル オブジェクトを*派生*させて実行時に使用するためのテンプレートでもあります。 

アプリケーション オブジェクトはアプリケーションの*グローバル*な表現 (すべてのテナント用) であり、サービス プリンシパル オブジェクトはアプリケーションの*ローカル*な表現 (特定のテナント用) と考えることができます。 アプリケーション オブジェクトのスキーマは、Azure AD Graph [Application エンティティ][AAD-Graph-App-Entity]によって定義されています。 そのため、アプリケーション オブジェクトには、ソフトウェア アプリケーションとの間に 1 対 1 のリレーションシップがあり、対応する *n* 個のサービス プリンシパル オブジェクトとの間に 1 対 *n* のリレーションシップがあります。

#### <a name="service-principal-object"></a>サービス プリンシパル オブジェクト
サービス プリンシパル オブジェクトは、アプリケーション用のポリシーとアクセス許可を定義しており、実行時のリソースへのアクセスの際にアプリケーションを表す、セキュリティ プリンシパルの基となる情報を提供します。 サービス プリンシパル オブジェクトのスキーマは、Azure AD Graph [ServicePrincipal エンティティ][AAD-Graph-Sp-Entity]によって定義されています。 

Azure AD テナントで、テナントの保護対象のリソースに対してアプリケーションがアクセスできるようにするには、このテナントにサービス プリンシパルを作成する必要があります。 サービス プリンシパルは、このテナントのユーザーの所有リソースに対してのアプリケーションのアクセスを Azure AD で保護するための基盤となっています。 シングルテナント アプリケーションは、サービス プリンシパルが&1; つのみ (ホーム テナント内に) 存在します。 また、マルチテナント [Web アプリケーション](active-directory-dev-glossary.md#web-client)は各テナントにサービス プリンシパルがあります。そのテナントからの管理者やユーザーにはリソースにアクセスできるように同意が与えられています。 同意を得た後、サービス プリンシパル オブジェクトは将来の承認要求のために参照されることになります。 

> [!NOTE]
> アプリケーション オブジェクトに加えたすべての変更は、アプリケーションのホーム テナント (アプリケーションが登録されたテナント) にだけ存在するサービス プリンシパル オブジェクトにも反映されます。 マルチテナント アプリケーションの場合は、コンシューマー テナントでアクセス権を削除し、もう一度アクセス権を付与するまで、そのコンシューマー テナントのサービス プリンシパル オブジェクトにアプリケーション オブジェクトへの変更が反映されることはありません。
> 
> 

## <a name="example"></a>例
次の図は、 **HR アプリ**という名前のサンプル マルチテナント アプリケーションを基に、アプリケーションのアプリケーション オブジェクトと、対応するサービス プリンシパル オブジェクトの間のリレーションシップを表しています。 このシナリオには、次の&3; つの Azure AD テナントがあります。 

* **Adatum** - **HR アプリ**を開発した会社が使用するテナント
* **Contoso** - **HR アプリ**のコンシューマーである Contoso という組織が使用するテナント
* **Fabrikam** - Contoso と同じく **HR アプリ**のコンシューマーである Fabrikam という組織が使用するテナント

![Relationship between an application object and a service principal object](./media/active-directory-application-objects/application-objects-relationship.png)

前の図でのステップ 1. は、アプリケーションとサービス プリンシパル オブジェクトを、アプリケーションのホーム テナント内に作成するプロセスです。

ステップ 2. では、Contoso と Fabrikam の管理者が同意を終えると、それぞれの会社の Azure AD テナント内にサービス プリンシパル オブジェクトが作成され、それに管理者が付与したアクセス許可が割り当てられます。 HR アプリは、個々のユーザー用として、ユーザーによる同意を許可するように構成/設計することができる点にも注目してください。

ステップ 3 では、HR アプリケーション (Contoso と Fabrikam) のコンシューマー テナントにそれぞれ独自のサービス プリンシパル オブジェクトが作成されます。 それぞれ実行時におけるアプリケーションのインスタンスの使用を表し、それぞれの管理者によって同意されたアクセス許可によって管理されます。

## <a name="next-steps"></a>次のステップ
Azure AD Graph API を通じて、OData [Application エンティティ][AAD-Graph-App-Entity]によって表されるアプリケーションのアプリケーション オブジェクトにアクセスできます。

Azure AD Graph API を通じて、OData [ServicePrincipal エンティティ][AAD-Graph-Sp-Entity]によって表されるアプリケーションのサービス プリンシパル オブジェクトにアクセスできます。

<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Portal]: https://portal.azure.com



<!--HONumber=Feb17_HO2-->


