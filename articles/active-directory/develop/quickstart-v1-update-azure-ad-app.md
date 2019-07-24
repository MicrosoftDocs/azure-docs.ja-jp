---
title: Azure Active Directory とアプリケーションの統合
description: Azure Active Directory (Azure AD) でアプリケーションを更新する方法について説明します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: bee16ed8205453546702946628c98c73b0f34b15
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58103810"
---
# <a name="quickstart-update-an-application-in-azure-active-directory"></a>クイック スタート:Azure Active Directory のアプリケーションを更新する

Azure Active Directory (Azure AD) にアプリケーションを登録したエンタープライズ開発者および SaaS (サービスとしてのソフトウェア) プロバイダーは、Web API などの他のリソースにアクセスするようにアプリケーションを構成して、他の組織などでアプリケーションを利用できるようにすることが必要な場合があります。

このクイック スタートでは、自他組織の要件またはニーズを満たすためにアプリケーションを構成または更新するさまざまな方法について学習します。

## <a name="prerequisites"></a>前提条件

開始するには、次の手順を完了していることを確認します。

* [Azure AD 同意フレームワーク](consent-framework.md)の概要を読みます。他のユーザーまたはアプリケーションが使用する必要があるアプリケーションを構築する場合には、このフレームワークについて理解しておくことが重要です。
* アプリケーションが登録されている Azure AD テナントがあること。
  * テナントを所有していない場合は、「 [How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](quickstart-create-new-tenant.md)」を参照して取得してください。
  * テナントにアプリが登録されていない場合は、[アプリケーションを Azure AD に追加する方法を学びます](quickstart-v1-integrate-apps-with-azure-ad.md)。

## <a name="configure-a-client-application-to-access-web-apis"></a>Web API にアクセスするためのクライアント アプリケーションの構成

Web/confidential クライアント アプリケーションが認証を必要とする認可付与フローに参加 (し、アクセス トークンを取得) できるようにするには、セキュリティで保護された資格情報を確立する必要があります。 Azure portal でサポートされている既定の認証方法は、クライアント ID と秘密鍵の組み合わせです。 このセクションでは、クライアントの資格情報に秘密鍵を追加するために必要な構成手順について説明します。

同意フレームワークでは、リソース アプリケーションによって公開されている Web API (Microsoft Graph API など) にクライアントがアクセスできるようになる前に、要求されたアクセス許可に基づいてクライアントに必要なアクセス許可が付与されていることを確認します。 既定では、すべてのアプリケーションが **Azure Active Directory** (Graph API) および Azure クラシック デプロイ モデルのアクセス許可を選択できます。 また、[Graph API の [サインインとユーザー プロファイルの読み取り] アクセス許可](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails)が既定で選択されています。 Office 365 のサブスクリプションを保有しているアカウントが存在するテナントにクライアントを登録しようとしている場合には、SharePoint と Exchange Online 用の Web API とアクセス許可を選択できます。 目的の Web API ごとに 2 種類のアクセス許可から選択できます。

- アプリケーションのアクセス許可: クライアント アプリケーションは、アプリケーション自体として (ユーザー コンテキストなしで) Web API に直接アクセスする必要があります。 この種類のアクセス許可には管理者の同意が必要であるほか、ネイティブ クライアント アプリケーションでは使用できません。
- 委任されたアクセス許可: クライアント アプリケーションは、サインインしているユーザーとして Web API にアクセスする必要があります。アクセスにあたっては、選択されているアクセス許可に応じて制限が適用されます。 この種類のアクセス許可は、管理者の同意が必要でない限り、ユーザーが付与できます。

  > [!NOTE]
  > 委任されたアクセス許可をアプリケーションに追加しても、テナント内のユーザーに対して自動的に同意が与えられるわけではありません。 管理者がすべてのユーザーに代わって同意を許可しない限り、ユーザーはやはり追加された委任されたアクセス許可に実行時に手動で同意する必要があります。

### <a name="add-application-credentials-or-permissions-to-access-web-apis"></a>Web API にアクセスするためのアプリケーションの資格情報またはアクセス許可を追加する

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ご利用のアカウントで複数の Azure AD テナントにアクセスできる場合は、右上隅でアカウントを選択し、ポータルのセッションを目的のテナントに設定します。
3. 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスを選択し、**[アプリの登録]** を選択して、構成するアプリケーションを検索/選択します。

   ![アプリケーションの登録の更新](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. アプリケーションのメイン登録ページが表示され、そのアプリケーションの **[設定]** ページがそこに含まれています。 Web アプリケーションの資格情報を追加するには:
   1. **[設定]** ページの **[キー]** セクションを選択します。
   1. 証明書を追加するには:
      - **[公開キーのアップロード]** を選択します。
      - アップロードするファイルを選択します。 ファイルの種類は .cer、.pem、.crt のいずれかである必要があります。
   1. パスワードを追加するには:
      - 鍵の説明を追加します。
      - 期間を選択します。
      - **[保存]** を選択します。 構成の変更を保存すると、右端の列に鍵の値が格納されます。 クライアント アプリケーションのコードで使用するので、**必ず鍵をコピーしておいてください**。このページを一度閉じると、鍵にアクセスできなくなります。

5. 次の手順に従い、クライアントからリソース API にアクセスするためのアクセス許可を追加します。
   1. **[設定]** ページで **[必要なアクセス許可]** セクションを選択し、**[追加]** を選択します。
   1. **[API を選択します]** を選択し、選択するリソースの種類を選択します。
   1. 利用可能な API の一覧を参照するか、検索ボックスを使用して、ディレクトリ内にあって Web API を公開している利用可能なリソース アプリケーションの中から選択します。 目的のリソースを選択し、**[選択]** をクリックします。
   1. **[アクセスの有効化]** ページで、アプリケーションのアクセス許可や委任されたアクセス許可のうち、アプリケーションが API にアクセスするうえで必要なものを選択します。
   
   ![アプリケーションの登録の更新 - アクセス許可 API](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-api.png)

   ![アプリケーションの登録の更新 - アクセス許可](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms.png)

6. 完了したら、**[アクセスの有効化]** ページで **[選択]** ボタンを選択し、**[API アクセスの追加]** ページ で **[完了]** ボタンを選択します。 **[必要なアクセス許可]** ページに戻ります。ページでは API の一覧に新しいリソースが追加されています。

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Web API を公開するためのリソース アプリケーションの構成

自ら Web API を開発し、クライアント アプリケーションで利用できるようにすることもできます。そのためには、アクセスの[スコープ](developer-glossary.md#scopes)と[ロール](developer-glossary.md#roles)を公開する必要があります。 Web API を適切に構成すれば、Graph API や Office 365 API など、他の Microsoft Web API と同じように利用できるようになります。 アクセスのスコープとロールを公開するには、[アプリケーション マニフェスト](developer-glossary.md#application-manifest)を使用します。アプリケーション マニフェストは、アプリケーションの ID 構成を示した JSON ファイルです。

以下のセクションでは、リソース アプリケーションのマニフェストを変更してアクセス スコープを公開する方法について説明します。

### <a name="add-access-scopes-to-your-resource-application"></a>リソース アプリケーションにアクセス スコープを追加する

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ご利用のアカウントで複数の Azure AD テナントにアクセスできる場合は、右上隅でアカウントを選択し、ポータルのセッションを目的のテナントに設定します。
3. 左側のナビゲーション ウィンドウで、**[Azure Active Directory] > [アプリの登録]** を選択し、構成するアプリケーションを検索/選択します。

   ![アプリケーションの登録の更新](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. アプリケーションのメイン登録ページが表示され、そのアプリケーションの **[設定]** ページが開きます。 アプリケーションの登録ページで **[マニフェスト]** をクリックして、**[マニフェストの編集]** ページに切り替えます。 Web ベースのマニフェスト エディターが開き、ポータルでマニフェストを**編集**できます。 必要があれば、**[ダウンロード]** をクリックしてローカルで編集します。編集を終えたら **[アップロード]** をクリックすると、アプリケーションに編集後のマニフェストが適用されます。
5. この例では、`oauth2Permissions` コレクションに以下の JSON 要素を追加して、リソース/API で `Employees.Read.All` という新しいスコープを公開します。 既存の `user_impersonation` スコープは、登録時に既定で提供されます。 `user_impersonation` は、クライアント アプリケーションがリソースに対するアクセス許可を要求するにあたり、サインインしたユーザーの ID を使えるようにするためのものです。 既存の `user_impersonation` スコープ要素の後にコンマを追加したうえで、リソースのニーズに応じてプロパティの値を変更してください。 

   ```json
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
   ```

   > [!NOTE]
   > `id` の値は、プログラムによって、または [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) などの GUID 生成ツールを使って作成する必要があります。 `id` は、Web API によって公開されるスコープの一意の識別子となります。 クライアントに対して Web API にアクセスするためのアクセス許可が適切に構成されていると、そのクライアントには、Azure AD によって OAuth 2.0 アクセス トークンが発行されます。 クライアントが Web API を呼び出すときには、このアクセス トークンを提示することになります。そして、このアクセス トークンでは、スコープ (scp) 要求がアプリケーションの登録時に要求されたアクセス許可のとおりに設定されています。
   >
   > 公開するスコープは、必要に応じて後から追加することもできます。 たとえば、Web API で、さまざまな機能が関連付けられたスコープをいくつも公開しているとします。 リソースでは、受け取った OAuth 2.0 アクセス トークンのスコープ (`scp`) 要求を評価することによって、実行時に Web API へのアクセスを制御します。

6. 完了したら、**[保存]** をクリックします。 これで Web API の構成が終わり、ディレクトリ内の他のアプリケーションが利用できるようになりました。

   ![アプリケーションの登録の更新](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-manifest.png)

### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>テナント内の他のアプリケーションに Web API が公開されているかどうかの確認

1. Azure AD テナントに戻って、**[アプリの登録]** をもう一度選択し、構成するクライアント アプリケーションを検索/選択します。

   ![アプリケーションの登録の更新](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

2. 「[Web API にアクセスするためのクライアント アプリケーションの構成](#configure-a-client-application-to-access-web-apis)」の手順 5. をもう一度実行します。 **[API を選択します]** の手順まで進んだら、検索フィールドにアプリケーション名を入力してリソースを検索し、**[選択]** をクリックします。

3. **[アクセスの有効化]** ページに新しいスコープが表示されていれば、クライアントのアクセス許可の要求で利用できる状態です。

   ![新しいアクセス許可の表示](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms-newscopes.png)

### <a name="more-on-the-application-manifest"></a>アプリケーション マニフェストの詳細

アプリケーション マニフェストは、アプリケーション エンティティを更新するためのメカニズムとしての役割を果たすものです。ここまでに取り上げた API のアクセス スコープをはじめ、Azure AD アプリケーションの ID 構成の属性はすべて、このアプリケーション エンティティで定義されています。 アプリケーション エンティティとそのスキーマの詳細については、[Graph API のアプリケーション エンティティに関するドキュメント](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)を参照してください。 このドキュメントでは、API のアクセス許可を指定するために使用するアプリケーション エンティティのメンバーについて、詳細な参照情報を確認できます。たとえば、次のような情報です。  

- appRoles メンバー: Web API の[アプリケーションのアクセス許可](developer-glossary.md#permissions)を定義するときに使用する [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) エンティティのコレクションです。 
- oauth2Permissions メンバー: Web API の[委任されたアクセス許可](developer-glossary.md#permissions)を定義するときに使用する [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) エンティティのコレクションです。

アプリケーション マニフェストの概念全般については、[Azure AD app manifest](reference-app-manifest.md)\(Azure AD アプリ マニフェスト\) を参照してください。

## <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Microsoft Graph API による Azure AD Graph と Office 365 へのアクセス  

前に説明したように、独自のアプリケーションのための API を公開してそれにアクセスするだけでなく、クライアント アプリケーションを登録して、Microsoft のリソースにより公開されている API にアクセスすることもできます。 Microsoft Graph API (ポータルのリソース/API 一覧では "Microsoft Graph") は、Azure AD に登録されているすべてのアプリケーションが利用できます。 Office 365 サブスクリプションにサインアップしたアカウントが存在するテナントにクライアント アプリケーションを登録した場合には、さまざまな Office 365 リソースにより公開されているスコープにもアクセスできます。

Microsoft Graph API により公開されているスコープの全容については、[Microsoft Graph のアクセス許可に関するリファレンス](https://docs.microsoft.com/graph/permissions-reference)を参照してください。

> [!NOTE]
> 現時点では、ネイティブ クライアント アプリケーションに [Access your organization's directory]\(組織のディレクトリにアクセスする\) アクセス許可を使用した場合、そのアプリケーションで Azure AD Graph API しか呼び出すことができなくなるという制約があります。 この制約は、Web アプリケーションには適用されません。

## <a name="configuring-multi-tenant-applications"></a>マルチテナント アプリケーションの構成

Azure AD にアプリケーションを登録するときには、自分が所属する組織のユーザーだけがそのアプリケーションにアクセスできるようにすることもあれば、 反対に、外部組織のユーザーがアプリケーションにアクセスできるようにすることもあります。 前者をシングルテナント アプリケーション、後者をマルチテナント アプリケーションと呼びます。 このセクションでは、シングルテナント アプリケーションの構成を変更してマルチテナント アプリケーションにする方法を説明します。

シングルテナント アプリケーションとマルチテナント アプリケーションの違いに注意することが重要です。  

- シングルテナント アプリケーションは、1 つの組織で使用することを想定しています。 典型例としては、エンタープライズ開発者が作成した基幹業務 (LOB) アプリケーションが挙げられます。 シングルテナント アプリケーションには、アプリケーションを登録したテナントと同じテナントに存在するアカウントのユーザーのみがアクセスできます。 このため、1 つのディレクトリにプロビジョニングするだけで済みます。
- マルチテナント アプリケーションは、多数の組織で使用することを想定しています。 サービスとしてのソフトウェア (SaaS) Web アプリケーションとも呼ばれ、独立系ソフトウェア ベンダー (ISV) が作成したアプリケーションがその典型例です。 マルチテナント アプリケーションは、ユーザーがアクセスする必要があるテナントそれぞれにプロビジョニングする必要があります。 アプリケーションが登録されているテナント以外のテナントの場合、登録にはユーザーまたは管理者の同意が必要です。 ネイティブ クライアント アプリケーションは、リソース所有者のデバイスにインストールされるので、既定でマルチテナントになります。 同意フレームワークの詳細については、前の「同意フレームワークの概要」セクションを参照してください。

アプリケーションをマルチテナントにするには、アプリケーション登録の変更と、Web アプリケーション自体に対する変更の両方が必要です。 以下のセクションでは、そのどちらについても説明します。

### <a name="changing-the-application-registration-to-support-multi-tenant"></a>マルチテナントのサポートのためのアプリケーション登録の変更

組織の外部の顧客やパートナーが利用できるアプリケーションを作成する場合には、Azure Portal でアプリケーションの定義を更新する必要があります。

> [!IMPORTANT]
> Azure AD では、マルチテナント アプリケーションのアプリ ID URI がグローバルで一意になっている必要があります。 アプリ ID URI は、プロトコル メッセージでアプリケーションを識別する手段の 1 つです。 シングル テナント アプリケーションの場合、アプリ ID URI はそのテナント内で一意であれば十分です。 これに対してマルチテナント アプリケーションの場合、Azure AD が全テナントから該当するアプリケーションを特定できるように、アプリ ID URI がグローバルで一意になっている必要があります。
> グローバルな一意性を確保するため、アプリ ID URI には Azure AD テナントの検証済みドメインと一致するホスト名が含まれていなければならないという条件が存在します。 たとえば、テナントの名前が contoso.onmicrosoft.com の場合、有効なアプリ ID URI は https://contoso.onmicrosoft.com/myapp のようになります。 また、テナントの検証済みドメインが contoso.com の場合、有効なアプリ ID URI は https://contoso.com/myapp のようになります。 アプリ ID URI がこのパターンに従っていないと、アプリケーションのマルチテナントとしての設定が失敗します。

外部ユーザーがアプリケーションにアクセスできるようにするには、次の操作を行います。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ご利用のアカウントで複数の Azure AD テナントにアクセスできる場合は、右上隅でアカウントをクリックし、ポータルのセッションを目的のテナントに設定します。
3. 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスをクリックし、**[アプリの登録]** をクリックして、構成するアプリケーションを検索/クリックします。 アプリケーションのメイン登録ページが表示され、そのアプリケーションの **[設定]** ページが開きます。
4. **[設定]** ページで **[プロパティ]** をクリックし、**[マルチテナント]** スイッチを **[はい]** に変更します。

ここに挙げた変更が終わると、他の組織のユーザーと管理者からその組織のユーザーに対して、アプリケーションへのサインインを許可できるようになります。その結果、アプリケーションがそのユーザーのテナントで保護されているリソースにアクセスできるようになります。

### <a name="changing-the-application-to-support-multi-tenant"></a>マルチテナントのサポートのためのアプリケーションの変更

マルチテナント アプリケーションのサポートは、Azure AD の同意フレームワークに大きく依存しています。 同意とは、他のテナントのユーザーからアプリケーションに対して、そのユーザーのテナントで保護されているリソースへのアクセスを許可できるようにするメカニズムです。 このエクスペリエンスを "ユーザーの同意" と呼びます。

Web アプリケーションではほかにも、次のものが提供されることがあります。

- 管理者による "会社のサインアップ"。 このエクスペリエンスは "管理者の同意" とも呼ばれ、管理者が組織内の "*すべてのユーザー*" に代わって同意を与えることができるようにするものです。 管理者の同意を与えることができるのは、グローバル管理者ロールに属するアカウントで認証したユーザーのみです。それ以外のユーザーが同意しようとしても、エラーが発生します。
- ユーザーのサインアップ エクスペリエンス。 ユーザーの画面に [サインアップ] ボタンが表示されます。このボタンを押すと、ブラウザーが Azure AD OAuth2.0 の `/authorize` エンドポイントまたは OpenID Connect の `/userinfo` エンドポイントにリダイレクトされます。 これらのエンドポイントでは、アプリケーションが id_token を調べて、新しいユーザーに関する情報を取得できます。 サインアップ フェーズの後は、「同意フレームワークの概要」セクションで示したものとよく似た同意プロンプトがユーザーに表示されます。

マルチテナント アクセスとサインイン/サインアップ エクスペリエンスのサポートに必要なアプリケーションの変更の詳細については、次を参照してください。

- [マルチテナント アプリケーション パターンを使用してすべての Azure Active Directory (AD) ユーザーがサインインできるようにする方法](howto-convert-app-to-be-multi-tenant.md)
- [マルチテナント コード サンプル](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant)の一覧。
- [クイック スタート:Azure AD のサインイン ページに会社のブランドを追加する](../fundamentals/customize-branding.md)

## <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>シングル ページ アプリケーションでの OAuth 2.0 暗黙的な許可の有効化

シングル ページ アプリケーション (SPA) は、ブラウザー上で実行される JavaScript ヘビーなフロント エンドを使用して通常構成されています。これがアプリケーションの Web API バックエンドを呼び出してビジネス ロジックを実行します。 Azure AD でホストされている SPA では、Azure AD によるユーザー認証と、アプリケーションの JavaScript クライアントによるバックエンド Web API の呼び出しを保護するためのトークンの取得に、OAuth 2.0 Implicit Grant を使用します。

この認証プロトコルはほかにも、ユーザーが同意した後、クライアントとアプリケーション用に構成されている他の Web API リソースとの間に発生する呼び出しを保護するトークンを取得するときにも利用できます。 暗黙的な認可付与の詳細と、それが自身のアプリケーションのシナリオに適しているかどうかの判断に役立つ情報は、「[Azure Active Directory (AD) での OAuth2 の暗黙的な許可フローについて](v1-oauth2-implicit-grant-flow.md)」を参照してください。

既定では、アプリケーションに対して OAuth 2.0 Implicit Grant が無効になっています。 アプリケーションに対して OAuth 2.0 Implicit Grant を有効にするには、[アプリケーション マニフェスト](reference-app-manifest.md)で `oauth2AllowImplicitFlow` の値を設定します。

### <a name="to-enable-oauth-20-implicit-grant"></a>OAuth 2.0 Implicit Grant を有効にするには

> [!NOTE]
> アプリケーション マニフェストを編集する方法の詳細については、前の「[Web API を公開するためのリソース アプリケーションの構成](#configuring-a-resource-application-to-expose-web-apis)」セクションを参照してください。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ご利用のアカウントで複数の Azure AD テナントにアクセスできる場合は、右上隅でアカウントをクリックし、ポータルのセッションを目的のテナントに設定します。
3. 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスをクリックし、**[アプリの登録]** をクリックして、構成するアプリケーションを検索/クリックします。 アプリケーションのメイン登録ページが表示され、そのアプリケーションの **[設定]** ページが開きます。
4. アプリケーションの登録ページで **[マニフェスト]** をクリックして、**[マニフェストの編集]** ページに切り替えます。 Web ベースのマニフェスト エディターが開き、ポータルでマニフェストを**編集**できます。 "oauth2AllowImplicitFlow" を探し、値を "true" に設定します。 既定値は "false" です。
   
   ```json
   "oauth2AllowImplicitFlow": true,
   ```
5. 更新したマニフェストを保存します。 保存が完了すると、ユーザー認証に OAuth 2.0 Implicit Grant を使用するように Web API が構成されます。

## <a name="next-steps"></a>次の手順

Azure AD v1.0 エンドポイントを使用するアプリに関連した、その他のアプリ管理クイック スタートについて学びます。
- [アプリケーションを Azure AD に追加する](quickstart-v1-integrate-apps-with-azure-ad.md)
- [アプリケーションを Azure AD から削除する](quickstart-v1-remove-azure-ad-app.md)

登録されたアプリケーションを表す 2 つの Azure AD オブジェクトと、両者間の関係については、[Application objects and service principal objects](app-objects-and-service-principals.md)\(アプリケーション オブジェクトとサービス プリンシパル オブジェクト\) を参照してください。

Azure Active Directory でアプリケーションを開発するときに使用するブランド化ガイドラインについては、[アプリケーションのブランド化ガイドライン](howto-add-branding-in-azure-ad-apps.md)を参照してください。
