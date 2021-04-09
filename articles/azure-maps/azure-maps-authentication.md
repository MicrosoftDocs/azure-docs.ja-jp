---
title: Microsoft Azure Maps による認証
titleSuffix: Azure Maps
description: Azure Maps で要求を認証する 2 つの方法、共有キー認証と Azure Active Directory (Azure AD) 認証について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 9c973859c8b7a3d04693946f50377837c3538b85
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094106"
---
# <a name="authentication-with-azure-maps"></a>Azure Maps による認証

Azure Maps には、要求の認証方法が 2 つあります。共有キー認証と [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) 認証です。 この記事では、Azure Maps サービスの実装の参考になるように、これらの認証方法の両方について説明します。

> [!NOTE]
> Azure Maps とのセキュリティで保護された通信を強化するために、TLS (トランスポート層セキュリティ) 1.2 がサポートされるようになりました。TLS 1.0 と 1.1 のサポートは廃止されます。 現在 TLS 1.x を使用している場合は、[TLS 1.0 の問題の解決](/security/solving-tls1-problem)に関する記事で説明されているテストを使用して、TLS 1.2 の準備を評価し、移行計画を作成します。

## <a name="shared-key-authentication"></a>共有キー認証

 Azure Maps アカウントの作成後、主キーと 2 次キーが生成されます。 共有キー認証を使用して Azure Maps を呼び出す場合は、主キーをサブスクリプション キーとして使用することをお勧めします。 共有キー認証では、Azure Maps アカウントによって生成されたキーを Azure Maps サービスに渡します。 Azure Maps サービスへの要求ごとに、*サブスクリプション キー* をパラメーターとして URL に追加します。 2 次キーは、キーのローリング変更などのシナリオで使用できます。  

Azure portal でキーを表示する方法について詳しくは、[認証の管理](./how-to-manage-authentication.md#view-authentication-details)に関する記事をご覧ください。

> [!TIP]
> セキュリティ上の理由から、主キーとセカンダリ キーをローテーションすることをお勧めします。 キーをローテーションするには、セカンダリ キーを使用するようにアプリを更新してデプロイします。次に、主キーの横にある [cycle/refresh]\(サイクル/更新\) ボタンを押して、新しい主キーを生成します。 古い主キーは無効になります。 キー ローテーションの詳細については、「[キー ローテーションと監査で Azure Key Vault を設定する](../key-vault/secrets/tutorial-rotation-dual.md)」を参照してください。

## <a name="azure-ad-authentication"></a>Azure AD 認証

Azure サブスクリプションは Azure AD テナントと共に提供され、きめ細かなアクセス制御を可能にします。 Azure Maps は、Azure AD を使用する Azure Maps サービスの認証を提供します。 Azure AD は、Azure AD テナントに登録されたユーザーとアプリケーションに ID ベースの認証を提供します。

Azure Maps では、Azure Maps アカウントを含む Azure サブスクリプションに関連付けられている Azure AD テナントの **OAuth 2.0** アクセス トークンを受け付けます。 Azure Maps は、次のトークンも受け付けます。

* Azure AD ユーザー
* ユーザーによって委任されたアクセス許可を使用するパートナー アプリケーション
* Azure リソースのマネージド ID

Azure Maps では、Azure Maps アカウントごとに *一意の識別子 (クライアント ID)* を生成します。 このクライアント ID を追加のパラメーターと組み合わせると、Azure AD からトークンを要求できます。

Azure AD を構成して Azure Maps のトークンを要求する方法について詳しくは、「[Azure Maps での認証の管理](./how-to-manage-authentication.md)」をご覧ください。

Azure AD による認証に関する一般的な情報については、[認証の概要](../active-directory/develop/authentication-vs-authorization.md)に関するページを参照してください。

### <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure リソースと Azure Maps のマネージド ID

[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) により、Azure AD で認証可能な自動的に管理されるアプリケーション ベースのセキュリティ プリンシパルが Azure サービスに提供されます。 Azure ロールベースのアクセス制御 (Azure RBAC) を使用すると、マネージド ID セキュリティ プリンシパルに対して、Azure Maps サービスへのアクセスを承認することができます。 マネージド ID の例としては、次のものがあります。Azure App Service、Azure Functions、Azure Virtual Machines。 マネージド ID の一覧については、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) に関するページを参照してください。

### <a name="configuring-application-azure-ad-authentication"></a>アプリケーションの Azure AD 認証を構成する

アプリケーションは、Azure AD によって提供される 1 つ以上のサポートされるシナリオを使用して、Azure AD テナントで認証されます。 各 Azure AD アプリケーション シナリオは、ビジネス ニーズに基づいたさまざまな要件を表します。 ユーザーのサインイン エクスペリエンスを必要とするアプリケーションもあれば、アプリケーションのサインイン エクスペリエンスを必要とするアプリケーションもあります。 詳細については、「[認証フローとアプリケーションのシナリオ](../active-directory/develop/authentication-flows-app-scenarios.md)」を参照してください。

アプリケーションでアクセス トークンが受け取られた後、SDK またはアプリケーションによって HTTPS 要求が送信されます。これには、他の REST API HTTP ヘッダーに加えて次の必須 HTTP ヘッダーが含まれます。

| ヘッダー名    | 値               |
| :------------- | :------------------ |
| x-ms-client-id | 30d7cc….9f55        |
| 承認  | Bearer eyJ0e….HNIVN |

> [!NOTE]
> `x-ms-client-id` は、Azure Maps 認証ページに表示される Azure Maps アカウント ベースの GUID です。

Azure AD OAuth Bearer トークンを使用する Azure Maps ルート要求の例を次に示します。

```http
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

クライアント ID を表示する方法については、「[認証の詳細を表示する](./how-to-manage-authentication.md#view-authentication-details)」をご覧ください。

## <a name="authorization-with-role-based-access-control"></a>ロールベースのアクセス制御による認証

Azure Maps では、Azure AD ユーザー、グループ、アプリケーション、Azure リソース、Azure マネージド ID など、Azure [ロールベースのアクセス制御](../role-based-access-control/overview.md) (Azure RBAC) のすべてのプリンシパルの種類へのアクセスがサポートされます。 プリンシパルの種類には、ロールの定義とも呼ばれるアクセス許可のセットが付与されます。 ロールの定義は、REST API アクションに対するアクセス許可を提供します。 1 つ以上の Azure Maps アカウントにアクセスを適用することをスコープと呼びます。 プリンシパル、ロールの定義、スコープを適用すると、ロールの割り当てが作成されます。 

次のセクションでは、Azure Maps と Azure RBAC との統合の概念とコンポーネントについて説明します。 Azure Maps アカウントを設定するプロセスの一環として、Azure AD ディレクトリが、Azure Maps アカウントがある Azure サブスクリプションに関連付けられます。 

Azure RBAC を構成する場合、セキュリティ プリンシパルを選択して、それをロールの割り当てに適用します。 Azure portal でロールの割り当てを追加する方法については、「[Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」に関するページを参照してください。

### <a name="picking-a-role-definition"></a>ロールの定義の選択

アプリケーション シナリオがサポートされるロールの定義の種類は、次のとおりです。

| Azure ロールの定義       | 説明                                                                                              |
| :-------------------------- | :------------------------------------------------------------------------------------------------------- |
| Azure Maps データ閲覧者      | 不変の Azure Maps REST API へのアクセスを提供します。                                                       |
| Azure Maps データ共同作成者 | 変更可能な Azure Maps REST API へのアクセスを提供します。 変更可能性は、書き込みおよび削除のアクションによって定義されます。 |
| カスタム ロールの定義      | Azure Maps REST API に対する柔軟な制限付きアクセスを可能にするロールを作成します。                      |

一部の Azure Maps サービスでは、Azure Maps REST API で書き込みまたは削除アクションを実行するために、昇格された特権が必要になる場合があります。 Azure Maps データ共同作成者は、書き込みまたは削除アクションを提供するサービスで必要となります。 次の表では、そのサービスで書き込みまたは削除アクションを使用する場合に Azure Maps データ共同作成者を適用できるサービスについて説明します。 サービスで読み取りアクションのみを使用する場合は、Azure Maps データ共同作成者の代わりに Azure Maps データ閲覧者を使用することができます。

| Azure Maps サービス | Azure Maps ロールの定義  |
| :----------------- | :-------------------------- |
| Data               | Azure Maps データ共同作成者 |
| Creator            | Azure Maps データ共同作成者 |
| Spatial            | Azure Maps データ共同作成者 |

Azure RBAC 設定を確認する方法については、[Azure Maps 用に Azure RBAC を構成する方法](./how-to-manage-authentication.md)に関するページをご覧ください。

#### <a name="custom-role-definitions"></a>カスタム ロールの定義

アプリケーションのセキュリティの 1 つの側面は、最小限の特権のプリンシパルを適用することです。 この原則は、セキュリティ プリンシパルには、必須のアクセスのみを許可し、追加のアクセスは持たないようにする必要があることを意味します。 カスタム ロールの定義を作成すると、さらにきめ細かなアクセス制御を必要とするユース ケースをサポートできます。 カスタム ロールの定義を作成するには、その定義に含まれる、またはその定義から除外される特定のデータ アクションを選択できます。

カスタム ロールの定義は、セキュリティ プリンシパルのロールの割り当てで使用できます。 Azure カスタム ロールの定義の詳細については、「[Azure カスタム ロール](../role-based-access-control/custom-roles.md)」を参照してください。

カスタム ロールによってアプリケーションのセキュリティを向上できるシナリオ例をいくつか紹介します。

| シナリオ                                                                                                                                                                                                                 | カスタム ロールのデータ アクション                                                                                                                  |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| ベース マップ タイルがあり、他の REST API がない一般公開用またはインタラクティブなサインイン Web ページ。                                                                                                                              | `Microsoft.Maps/accounts/services/render/read`                                                                                              |
| リバース ジオコーディングのみを必要として、他の REST API を必要としないアプリケーション。                                                                                                                                             | `Microsoft.Maps/accounts/services/search/read`                                                                                              |
| Azure Maps ベースのマップ データの読み取りと基本マップ タイル REST API を要求するセキュリティ プリンシパルのロール。                                                                                                 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/render/read`                                                |
| 作成者ベースのマップ データの読み取り、書き込み、削除を要求するセキュリティ プリンシパルのロール。 これをマップ データ編集者として定義することはできますが、基本マップ タイルなどの他の REST API へのアクセスは許可されません。 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/data/write`, `Microsoft.Maps/accounts/services/data/delete` |

### <a name="understanding-scope"></a>スコープについて

ロールの割り当てを作成する場合、Azure リソースの階層内で定義されます。 この階層の最上位は[管理グループ](../governance/management-groups/overview.md)で、最下位は、Azure Maps アカウントなどの Azure リソースです。
ロールの割り当てをリソース グループに割り当てると、グループ内の複数の Azure Maps アカウントまたはリソースにアクセスできるようになります。

> [!TIP]
> Microsoft の一般的な推奨事項は、Azure Maps アカウント スコープへのアクセスを割り当てることです。これにより、同じ Azure サブスクリプション内にある **他の Azure Maps アカウントへの意図しないアクセス** を防止するためです。

## <a name="next-steps"></a>次のステップ

Azure RBAC の詳細については、次の記事をご覧ください。
> [!div class="nextstepaction"]
> [Azure ロールベースのアクセス制御](../role-based-access-control/overview.md)

Azure AD と Azure Maps を使用してアプリケーションを認証する方法の詳細については、次の記事を参照してください。
> [!div class="nextstepaction"]
> [Azure Maps での認証の管理](./how-to-manage-authentication.md)

Azure AD を使用した Azure Maps のマップ コントロールの認証の詳細については、次の記事を参照してください。
> [!div class="nextstepaction"]
> [Azure Maps のマップ コントロールを使用する](./how-to-use-map-control.md)