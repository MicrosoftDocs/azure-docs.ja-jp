---
title: "Azure Active Directory のアプリケーション マニフェストについて | Microsoft Docs"
description: "Azure Active Directory アプリケーション マニフェストについて詳しく説明しています。このマニフェストは、Azure AD テナントでのアプリケーションの ID 構成を表しています。また、OAuth 認証、同意エクスペリエンスなどを利用しやすくするために使用されます。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: dkershaw;bryanla
translationtype: Human Translation
ms.sourcegitcommit: 7d6525f4614c6301f0ddb621b0483da70842a71b
ms.openlocfilehash: 2dc166a346c58d43e9ed60332f47619c1de89816


---
# <a name="understanding-the-azure-active-directory-application-manifest"></a>Azure Active Directory のアプリケーション マニフェストについて
Azure Active Directory (AD) と統合するアプリケーションは、Azure AD テナントに登録され、アプリケーションの永続的な ID 構成を提供する必要があります。 この構成は実行時に参照されるため、アプリケーションが Azure AD を介して認証と承認を外部委託および仲介するシナリオが可能になります。 Azure AD のアプリケーション モデルの詳細については、「[アプリケーションの追加、更新、および削除][ADD-UPD-RMV-APP]」を参照してください。

## <a name="updating-an-applications-identity-configuration"></a>アプリケーションの ID 構成の更新
次に示すように、アプリケーションの ID 構成のプロパティを更新する方法には、現実に複数の方法があります。ただし、実行できる操作と難易度がそれぞれ異なります。

* **[Azure Portal][AZURE-PORTAL] の Web ユーザー インターフェイス**を使用すると、アプリケーションの最も一般的なプロパティを更新できます。 これは、最も手軽でミスを招きにくいアプリケーションのプロパティの更新方法ですが、次の&2; つの方法とは異なり、すべてのプロパティに完全にアクセスすることはできません。
* Azure クラシック ポータルで公開されないプロパティを更新する必要があるより高度なシナリオに対しては、 **アプリケーション マニフェスト**を変更することができます。 この記事では、この方法に話題を絞り、次のセクションで詳しく説明していきます。
* また、**[Graph API][GRAPH-API] を使用してアプリケーションを更新するアプリケーションを作成する**こともできますが、それには大きな労力が必要になります。 これは、管理ソフトウェアを作成している場合や定期的かつ自動的にアプリケーションのプロパティを更新する必要がある場合に魅力的な選択肢になる可能性があります。

## <a name="using-the-application-manifest-to-update-an-applications-identity-configuration"></a>アプリケーション マニフェストを使用してアプリケーションの ID 構成を更新する
[Azure Portal][AZURE-PORTAL] で、インライン マニフェスト エディターを使用してアプリケーション マニフェストを更新することで、アプリケーションの ID 構成を更新できます。 アプリケーション マニフェストは、JSON ファイルとしてダウンロードおよびアップロードすることもできます。 ディレクトリには実際のファイルは格納されません。 アプリケーション マニフェストは Azure AD Graph API アプリケーション エンティティに対する単なる HTTP GET 操作であり、アップロードはアプリケーション エンティティに対する HTTP PATCH 操作です。

したがって、アプリケーション マニフェストの形式とプロパティを理解するには、Graph API の[アプリケーション エンティティ][APPLICATION-ENTITY]に関するドキュメントを参照する必要があります。 アプリケーション マニフェストのアップロードによって実行できる更新操作の例を次に示します。

* **アクセス許可のスコープ (oauth2Permissions) を宣言する** 。 oauth2Permissions の委任アクセス許可のスコープを使用した偽装の実装の詳細については、「[Azure Active Directory とアプリケーションの統合][INTEGRATING-APPLICATIONS-AAD]」の、"Web API の他のアプリケーションへの公開" に関するトピックを参照してください。 既に説明したように、すべてのアプリケーション エンティティ プロパティについては、[OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION] 型のコレクションである oauth2Permissions プロパティを含む Graph API の[エンティティおよび複合型のリファレンス][APPLICATION-ENTITY]記事にドキュメント化されています。
* **アプリによって公開されるアプリケーション ロール (appRoles) を宣言する**。 アプリケーション エンティティの appRoles プロパティは、[AppRole][APPLICATION-ENTITY-APP-ROLE] 型のコレクションです。 実装例については、[Azure AD を使用したクラウド アプリケーションでのロール ベースのアクセス制御][RBAC-CLOUD-APPS-AZUREAD]に関する記事を参照してください。
* **既知のクライアント アプリケーション (knownClientApplications) を宣言する**。それらによって、指定されたクライアント アプリケーションの同意をリソースや Web API に論理的に関連付けることができます。
* **グループ メンバーシップ要求 (groupMembershipClaims) を発行するように Azure AD にリクエストする** 。  ユーザーのディレクトリ ロール メンバーシップに関する要求を発行するように構成することもできます。 実装例については、[AD グループを使用したクラウド アプリケーションでの承認][AAD-GROUPS-FOR-AUTHORIZATION]に関する記事を参照してください。
* **アプリケーションが OAuth 2.0 の暗黙的な許可フロー (oauth2AllowImplicitFlow) をサポートできるようにする** 。 この種類の許可フローは、埋め込み JavaScript Web ページまたは Single Page Applications (SPA) で使用されます。 暗黙的な認証付与の詳細については、「[Azure Active Directory での OAuth2 の暗黙的な許可フローについて][IMPLICIT-GRANT]」を参照してください。
* **X509 証明書の秘密キー (keyCredentials) としての使用を可能にする** 。 実装例については、「[Office 365 でのサービスとデーモンのアプリのビルド][O365-SERVICE-DAEMON-APPS]」と「[Azure Resource Manager API の権限付与に関する開発者ガイド][DEV-GUIDE-TO-AUTH-WITH-ARM]」を参照してください。
* アプリケーションの**新しいアプリ ID URI** (identifierURIs[]) を追加する。 アプリ ID URI は、Azure AD テナント内で (または、検証済みカスタム ドメインを使用して修飾される際のマルチ テナント シナリオの場合は、複数の Azure AD テナントで) アプリケーションを一意に識別するために使用します。 リソース アプリケーションに対するアクセス許可を要求するとき、またはリソース アプリケーションのアクセス トークンを取得するときに使用します。 この要素を更新したときに、対応するサービス プリンシパルの servicePrincipalNames[] コレクションに同じ更新が加えられます。このコレクションはアプリケーションのホーム テナントに格納されています。

アプリケーション マニフェストは、アプリケーションの登録の状態を追跡するための優れた方法でもあります。 アプリケーション マニフェストは JSON 形式で使用できるため、アプリケーションのソース コードと共にファイル表現をソース管理にチェックインできます。

## <a name="step-by-step-example"></a>詳しい手順
ここでは、アプリケーション マニフェストを使ってアプリケーションの ID 構成を更新するための手順を紹介します。 上の例のうちの&1; つに注目して、リソース アプリケーションで新しいアクセス許可スコープを宣言する方法を示します。

1. [Azure Portal][AZURE-PORTAL] にサインインします。
2. 認証が完了したら、ページの右上隅から Azure AD テナントを選択します。
3. 左側のナビゲーション パネルで **[Azure Active Directory]** 拡張機能を選択して、**[アプリの登録]** をクリックします。
4. 次に、更新するアプリケーションを一覧内で見つけてクリックします。
5. アプリケーションのページで **[マニフェスト]** をクリックして、インライン マニフェスト エディターを開きます。 
6. このエディターを使用して、マニフェストを直接編集できます。 前に述べたとおり、マニフェストは[アプリケーション エンティティ][APPLICATION-ENTITY]のスキーマに従うことに注意してください。たとえば、リソース アプリケーション (API) で "Employees.Read.All" という名前の新しいアクセス許可を実装/公開する場合は、次のように、単に新しい&2; 番目の要素を oauth2Permissions コレクションに追加します。
   
        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],
   
    エントリは一意である必要があるため、 `"id"` プロパティ用に新しいグローバル一意識別子 (GUID) を生成する必要があります。 この場合、`"type": "User"`を指定したので、このアクセス許可は、リソース/API アプリケーションが登録されている Azure AD テナントによって認証された任意のアカウントで同意することができます。 これにより、アカウントの代わりにアクセスする許可がクライアント アプリケーションに付与されます。 同意と Azure Portal での表示には、説明および表示名の文字列が使用されます。
6. マニフェストの更新が完了したら、**[保存]** をクリックしてマニフェストを保存します。  
   
マニフェストが保存されると、登録済みのクライアント アプリケーションへのアクセス権を、上で追加した新しいアクセス許可に付与することができます。 今度は、クライアント アプリケーションのマニフェストを編集する代わりに Azure Portal の Web UI を使用できます。  

1. まず、新しい API へのアクセス許可を追加するクライアント アプリケーションの **[設定]** ブレードに移動して、**[必要なアクセス許可]** をクリックし、**[API を選択します]** を選択します。
2. テナントの登録済みのリソース アプリケーション (API) の一覧が表示されます。 リソース アプリケーションをクリックして選択するか、検索ボックスにアプリケーションの名前を入力します。 アプリケーションが見つかったら、**[選択]** をクリックします。  
3. **[アクセス許可の選択]** ページが表示され、リソース アプリケーションで使用可能なアプリケーションのアクセス許可および委任されたアクセス許可が一覧表示されます。 新しいアクセス許可を選択して、クライアントのアクセス許可要求リストに追加します。 この新しいアクセス許可は、クライアント アプリケーションの ID 構成の "requiredResourceAccess" コレクション プロパティに格納されます。


これで終了です。 アプリケーションは、新しい ID 構成を使用して実行されるようになりました。

## <a name="next-steps"></a>次のステップ
* アプリケーションのアプリケーション オブジェクトとサービス プリンシパル オブジェクトの関係の詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト][AAD-APP-OBJECTS]」を参照してください。
* Azure Active Directory (AD) の開発者向けの重要な概念の定義については、「[Azure Active Directory 開発者向け用語集][AAD-DEVELOPER-GLOSSARY]」を参照してください。

Microsoft のコンテンツ改善のため、下部のコメント セクションよりご意見をお寄せください。

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/




<!--HONumber=Feb17_HO2-->


