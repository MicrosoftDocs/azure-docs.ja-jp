---
title: Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト
description: Azure Active Directory におけるアプリケーション オブジェクトとサービス プリンシパル オブジェクトのリレーションシップについて説明します。
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
services: active-directory
editor: ''
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 822990ebc2eb5edbdbc6611a4f3729bc5cfadc55
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482898"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト

「アプリケーション」という用語の意味が、Azure Active Directory (Azure AD) のコンテキストで使用する場合に誤解されることがあります。 この記事では、Azure AD アプリケーション統合の概念的および具体的な側面を、[マルチテナント アプリケーション](developer-glossary.md#multi-tenant-application)の登録と同意の例を示しながら明らかにすることです。

## <a name="overview"></a>概要

Azure AD と統合されているアプリケーションは、ソフトウェアとしての側面以外の意味を持ちます。 "アプリケーション" は、アプリケーション ソフトウェアだけでなく、実行時の認証/承認の "会話" におけるその Azure AD の登録やロールも指す概念的な用語としてよく使用されます。

定義上、アプリケーションは次の役割で作動できます。

- [クライアント](developer-glossary.md#client-application)の役割 (リソースを使用する)
- [リソース サーバー](developer-glossary.md#resource-server)の役割 (クライアントに API を公開する)
- クライアントとリソース サーバー両方の役割

[OAuth 2.0 承認付与フロー](developer-glossary.md#authorization-grant)によってメッセージ交換プロトコルが定義されます。これにより、クライアントとリソースが、それぞれリソースのデータにアクセスし、保護できるようになります。

この後のセクションでは、Azure AD アプリケーション モデルにおいて、デザイン時および実行時にアプリケーションが内部的にはどのように表されるのかを見てみましょう。

## <a name="application-registration"></a>アプリケーションの登録

[Azure portal][AZURE-Portal] で Azure AD アプリケーションを登録すると、Azure AD テナントに次の 2 つのオブジェクトが作成されます。

- アプリケーション オブジェクト
- サービス プリンシパル オブジェクト

### <a name="application-object"></a>アプリケーション オブジェクト

Azure AD アプリケーションは、その唯一のアプリケーション オブジェクトによって定義されます。アプリケーション オブジェクトは、アプリケーションの登録先である Azure AD テナント (アプリケーションの "ホーム" テナントと呼ばれます) 内にあります。 アプリケーション オブジェクトのプロパティのスキーマは、Microsoft Graph [Application エンティティ][MS-Graph-App-Entity]によって定義されています。

### <a name="service-principal-object"></a>サービス プリンシパル オブジェクト

Azure AD テナントによってセキュリティ保護されているリソースにアクセスするためには、アクセスを必要とするエンティティをセキュリティ プリンシパルで表す必要があります。 これは、ユーザー (ユーザー プリンシパル) とアプリケーション (サービス プリンシパル) の両方に当てはまります。

セキュリティ プリンシパルは、その Azure AD テナント内のユーザー/アプリケーションのアクセス ポリシーとアクセス許可を定義します。 これにより、サインイン時のユーザー/アプリケーションの認証、リソースへのアクセス時の承認などのコア機能を利用できるようになります。

アプリケーションが (登録または[同意](developer-glossary.md#consent)によって) テナント内のリソースへのアクセス許可を与えられると、サービス プリンシパル オブジェクトが作成されます。 サービス プリンシパル オブジェクトのプロパティのスキーマは、Microsoft Graph [ServicePrincipal エンティティ][MS-Graph-Sp-Entity]によって定義されています。

### <a name="application-and-service-principal-relationship"></a>アプリケーションとサービス プリンシパルのリレーションシップ

アプリケーション オブジェクトはアプリケーションの*グローバル*な表現 (すべてのテナント用) であり、サービス プリンシパル オブジェクトはアプリケーションの*ローカル*な表現 (特定のテナント用) と考えることができます。

アプリケーション オブジェクトは、対応するサービス プリンシパル オブジェクトの作成に使用するために、一般的な既定のプロパティが*派生*するテンプレートとして機能します。 そのため、アプリケーション オブジェクトにはソフトウェア アプリケーションとの 1 対 1 の関係と、対応するサービス プリンシパル オブジェクトとの 1 対多の関係が存在します。

サービス プリンシパルは、テナントによってセキュリティ保護されているリソースにサインインまたはアクセスするための ID を確立できるように、アプリケーションが使用される各テナントで作成する必要があります。 シングルテナント アプリケーションには、アプリケーション登録中に作成され、使用が同意されたサービス プリンシパルが (そのホーム テナントに) 1 つだけあります。 マルチテナント Web アプリケーション/API には、そのテナントのユーザーが使用に同意した各テナントで作成されたサービス プリンシパルもあります。

> [!NOTE]
> アプリケーション オブジェクトに加えたすべての変更は、アプリケーションのホーム テナント (アプリケーションが登録されたテナント) にだけ存在するサービス プリンシパル オブジェクトにも反映されます。 マルチテナント アプリケーションの場合は、[アプリケーション アクセス パネル](https://myapps.microsoft.com)を通じてアクセス権を削除し、もう一度アクセス権を付与するまで、そのコンシューマー テナントのサービス プリンシパル オブジェクトにアプリケーション オブジェクトへの変更が反映されることはありません。
>
> 既定でネイティブ アプリケーションがマルチテナントとして登録されていることにも注意してください。

## <a name="example"></a>例

次の図は、 **HR アプリ**という名前のサンプル マルチテナント アプリケーションを基に、アプリケーションのアプリケーション オブジェクトと、対応するサービス プリンシパル オブジェクトの間のリレーションシップを表しています。 このサンプル シナリオには、次の 3 つの Azure AD テナントがあります。

- **Adatum** - **HR アプリ**を開発した会社が使用するテナント
- **Contoso** - **HR アプリ**のコンシューマーである Contoso という組織が使用するテナント
- **Fabrikam** - Contoso と同じく **HR アプリ**のコンシューマーである Fabrikam という組織が使用するテナント

![アプリ オブジェクトとサービス プリンシパル オブジェクトの間のリレーションシップ](./media/app-objects-and-service-principals/application-objects-relationship.svg)

このサンプル シナリオの内容:

| 手順 | 説明 |
|------|-------------|
| 1    | アプリケーションとサービス プリンシパル オブジェクトを、アプリケーションのホーム テナント内に作成するプロセスです。 |
| 2    | Contoso と Fabrikam の管理者が同意を終えると、それぞれの会社の Azure AD テナント内にサービス プリンシパル オブジェクトが作成され、それに管理者が付与したアクセス許可が割り当てられます。 HR アプリは、個々のユーザー用として、ユーザーによる同意を許可するように構成/設計することができる点にも注目してください。 |
| 3    | HR アプリケーション (Contoso と Fabrikam) のコンシューマー テナントにそれぞれ独自のサービス プリンシパル オブジェクトが作成されます。 それぞれ実行時におけるアプリケーションのインスタンスの使用を表し、それぞれの管理者によって同意されたアクセス許可によって管理されます。 |

## <a name="next-steps"></a>次の手順

- [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を使用して、アプリケーションとサービス プリンシパル オブジェクトの両方を照会できます。
- Microsoft Graph API、[Azure portal][AZURE-Portal] のアプリケーション マニフェスト エディター、[Azure AD PowerShell コマンドレット](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0)を使用して、その OData [Application エンティティ][MS-Graph-App-Entity]によって表されるアプリケーションのアプリケーション オブジェクトにアクセスできます。
- Microsoft Graph API または [Azure AD PowerShell コマンドレット](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0)を通じて、その OData [ServicePrincipal エンティティ][MS-Graph-Sp-Entity]によって表されるアプリケーションのサービス プリンシパル オブジェクトにアクセスできます。

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
