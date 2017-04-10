---
title: "Azure Active Directory とアプリケーションの統合 | Microsoft Docs"
description: "Azure Active Directory (Azure AD) でアプリケーションを追加、更新、削除する方法について詳しく説明します。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: mbaldwin
ms.assetid: ae637be5-0b71-4b1e-b1fe-b83df3eb4845
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: mbaldwin;bryanla
translationtype: Human Translation
ms.sourcegitcommit: 57383c11682342cb0a6446c79e603843a698fc8c
ms.openlocfilehash: 835e1c494de59576fd8ac529240729cb33eaa50b
ms.lasthandoff: 03/01/2017


---
# <a name="integrating-applications-with-azure-active-directory"></a>Azure Active Directory とアプリケーションの統合
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

エンタープライズ開発者と SaaS (サービスとしてのソフトウェア) プロバイダーは、自社のサービスのセキュリティで保護されたサインインと承認を実現するために、Azure Active Directory (Azure AD) と統合できる商用クラウド サービスまたは基幹業務アプリケーションを開発できます。 アプリケーションまたはサービスを Azure AD と統合するには、Azure クラシック ポータルを使用して、アプリケーションに関する詳細情報を Azure AD に登録しておく必要があります。

この記事では、Azure AD でアプリケーションを追加、更新、削除する方法を説明します。 また、Azure AD と統合できるさまざまな種類のアプリケーション、Web API などの他のリソースにアクセスするようにアプリケーションを構成する方法などについても説明します。

登録されたアプリケーションおよびそれらの間の関係を表す&2; つの Azure AD オブジェクトの詳細については、[アプリケーション オブジェクトおよびサービス プリンシパル オブジェクト](active-directory-application-objects.md)に関するページを参照してください。Azure Active Directory でアプリケーションを開発するときに使用するブランド化ガイドラインの詳細については、[統合アプリケーションのブランド化に関するガイドライン](active-directory-branding-guidelines.md)を参照してください。

## <a name="adding-an-application"></a>アプリケーションの追加
Azure AD の機能を使用するアプリケーションは、まず Azure AD テナントに登録する必要があります。 この登録プロセスでは、アプリケーションが配置されている URL、ユーザーの認証後の応答の送信先となる URL、アプリケーションを識別する URI など、アプリケーションの詳細を Azure AD に提供します。

Azure AD でのユーザーのサインインのサポートだけを必要とする Web アプリケーションを構築する場合は、次の手順に従うだけでかまいません。 アプリケーションが Web API にアクセスするための資格情報またはアクセス許可を必要とする場合、または他の Azure AD テナントのユーザーによるアクセスを許可する必要がある場合は、「[アプリケーションの更新](#updating-an-application)」を参照して、引き続きアプリケーションを構成してください。

### <a name="to-register-a-new-application-in-the-azure-portal"></a>Azure Portal で新しいアプリケーションを登録するには
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ページの右上隅のアカウント名を選択して、Azure AD テナントを選択します。
3. 左側のナビゲーション ウィンドウで **[More Services (その他のサービス)]** を選択し、**[アプリの登録]**、**[追加]** の順にクリックします。
4. 画面の指示に従い、新しいアプリケーションを作成します。 Web アプリケーションまたはネイティブ アプリケーションの具体的な例については、[クイック スタート](active-directory-developers-guide.md)をご覧ください。
  * Web アプリケーションの場合は、**サインオン URL** (ユーザーが `http://localhost:12345` などにサインインできる、アプリのベース URL) を入力します。
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * ネイティブ アプリケーションの場合は、**リダイレクト URI** (Azure AD がトークン応答を返すために使用する) を入力します。 アプリケーション固有の値 (たとえば、 `http://MyFirstAADApp`
5. 登録が完了すると、Azure AD により、アプリケーションに一意のクライアント ID (アプリケーション ID) が割り当てられます。 アプリケーションが追加されると、アプリケーションのクイック スタート ページが表示されます。 アプリケーションが Web アプリケーションとネイティブ アプリケーションのどちらであるかに応じて、アプリケーションに機能を追加する方法のさまざまなオプションが表示されます。 アプリケーションが追加されたら、ユーザーによるサインインの有効化、他のアプリケーションの Web API へのアクセスの提供、(他の組織がアプリケーションにアクセスできるようにするための) マルチテナント アプリケーションの構成などを目的に、アプリケーションを更新できるようになります。

> [!NOTE]
> 既定では、新しく作成されたアプリケーションの登録は、ディレクトリのユーザーがアプリケーションにサインインできるように構成されます。
> 
> 

## <a name="updating-an-application"></a>アプリケーションの更新
アプリケーションを Azure AD に登録したら、Web API にアクセスできるようにしたり、他の組織で使用できるようにしたりするために、アプリケーションを更新することが必要な場合があります。 このセクションでは、アプリケーションの各種構成方法について説明します。 まず、同意フレームワークの概要について説明します。これは、自分の組織または別の組織の開発者が作成したクライアント アプリケーションで使用されるリソース/API アプリケーションを構築する場合に理解しておくことが重要です。

Azure AD での認証のしくみの詳細については、「 [Azure AD の認証シナリオ](active-directory-authentication-scenarios.md)」をご覧ください。

### <a name="overview-of-the-consent-framework"></a>同意フレームワークの概要
Azure AD の同意フレームワークを使用すると、クライアント アプリケーションが登録されているものとは異なる、Azure AD テナントによって保護された Web API にアクセスする必要があるマルチテナントの Web クライアント アプリケーションやネイティブ クライアント アプリケーションの開発が容易になります。 この Web API には、独自の Web API だけでなく、Microsoft Graph API (Azure Active Directory、Intune、および Office 365 のサービスへのアクセスに使用) およびその他の Microsoft サービス API も含まれます。 このフレームワークは、ディレクトリ データへのアクセスを必要とする場合がある、ディレクトリへの登録を要求するアプリケーションに同意するユーザーまたは管理者に基づいています。

たとえば、Web クライアント アプリケーションで Office 365 からユーザーに関するカレンダー情報を読み取る必要がある場合、そのユーザーはそのクライアント アプリケーションに同意する必要があります。 ユーザーが同意すると、クライアント アプリケーションはユーザーに代わって Microsoft Graph API を呼び出し、必要に応じてカレンダー情報を使用できるようになります。 [Microsoft Graph API](https://graph.microsoft.io) により、Office 365 のデータ (Exchange の予定表とメッセージ、SharePoint のサイトとリスト、OneDrive のドキュメント、OneNote のノートブック、Planner のタスク、Excel のブックなど) のほか、Azure AD のユーザーとグループ、Microsoft Cloud Services のその他のデータ オブジェクトにアクセスできます。 

同意フレームワークは、OAuth 2.0 と、パブリック クライアントまたは秘密のクライアントを使用した各種フロー (認証コードの付与やクライアント資格情報の付与など) を基盤としています。 Azure AD は、OAuth 2.0 を使用することで、スマートフォン、タブレット、サーバーなどで実行されるさまざまな種類のクライアント アプリケーションや Web アプリケーションを構築し、必要なリソースにアクセスすることを可能にします。

同意フレームワークの詳細については、[Azure AD での OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) に関するページと「[Azure AD の認証シナリオ](active-directory-authentication-scenarios.md)」を参照してください。また、Microsoft Graph で Office 365 への承認アクセスの取得については、「[Microsoft Graph でのアプリ認証](https://graph.microsoft.io/docs/authorization/auth_overview)」を参照してください。

#### <a name="example-of-the-consent-experience"></a>同意エクスペリエンスの例
次の手順は、アプリケーション開発者とユーザーに対して、同意エクスペリエンスがどのように機能するかを示しています。

1. Azure Portal の Web クライアント アプリケーションの構成ページで、[必要なアクセス許可] セクションのメニューを使用して、アプリケーションに必要なアクセス許可を設定します。
   
    ![他のアプリケーションに対するアクセス許可](./media/active-directory-integrating-applications/requiredpermissions.png)
2. たとえば、アプリケーションのアクセス許可の更新後、アプリケーションが実行され、ユーザーがアプリケーションを初めて使用しようとしているとします。 アプリケーションがアクセス トークンも更新トークンもまだ取得していない場合、アプリケーションは Azure AD の承認エンドポイントにアクセスして、新しいアクセス トークンと更新トークンを取得する際に使用できる認証コードを取得する必要があります。
3. ユーザーがまだ認証されていない場合、Azure AD にサインインするよう求められます。
   
    ![ユーザーまたは管理者による Azure AD へのサインイン](./media/active-directory-integrating-applications/usersignin.png)
4. ユーザーがサインインすると、ユーザーに同意ページを表示する必要があるかどうかが Azure AD により判別されます。 この判断は、ユーザー (または組織の管理者) がアプリケーションに既に同意しているかどうかに基づいています。 まだ同意していない場合、Azure AD によりユーザーに同意を求めるメッセージが表示され、アプリケーションが機能するために必要なアクセス許可が表示されます。 同意ダイアログに表示される一連のアクセス許可は、Azure Portal の [委任されたアクセス許可] で選択したアクセス許可と同じです。
   
    ![ユーザーの同意エクスペリエンス](./media/active-directory-integrating-applications/consent.png)
5. ユーザーが同意すると、アプリケーションに認証コードが返されます。この認証コードを使用して、アクセス トークンと更新トークンを取得できます。 このフローの詳細については、「[Azure AD の認証シナリオ](active-directory-authentication-scenarios.md)」の「[Web アプリケーション対 Web API](active-directory-authentication-scenarios.md#web-application-to-web-api)」を参照してください。

6. 管理者は、テナントのすべてのユーザーに代わって、アプリケーションの委任されたアクセス許可に同意できます。 この操作を行うと、テナントの各ユーザーに同意ダイアログが表示されなくなります。 この操作は、[Azure Portal](https://portal.azure.com) のアプリケーション ページで行うことができます。 アプリケーションの **[設定]** ブレードで、**[必要なアクセス許可]**、**[アクセス許可の付与]** ボタンの順にクリックします。 

    ![明示的な管理者の同意としてアクセス許可を付与する](./media/active-directory-integrating-applications/grantpermissions.png)
    
> [!NOTE]
> 現時点では、adal.js を使用するシングルページ アプリ (SPA) では、**[アクセス許可の付与]** ボタンを使用して明示的に同意を付与する必要があります。これは、アクセス トークンが同意プロンプトなしで要求され、事前に同意が付与されていないと要求に失敗するためです。   

### <a name="configuring-a-client-application-to-access-web-apis"></a>Web API にアクセスするためのクライアント アプリケーションの構成
Web/confidential クライアント アプリケーションが認証を必要とする承認付与フローに参加 (およびアクセス トークンを取得) できるようにするには、セキュリティで保護された資格情報を確立する必要があります。 Azure Portal でサポートされている既定の認証方法は、クライアント ID と対称キーの組み合わせです。 このセクションでは、秘密キーにクライアントの資格情報を提供するのに必要な構成手順について説明します。

さらに、クライアント アプリケーションがリソース アプリケーションによって公開されている Web API (つまり Microsoft Graph API) にアクセスする前に、同意フレームワークにより、要求されたアクセス許可に基づいてクライアントが必要なアクセス許可を取得することが保証されます。 既定では、すべてのアプリケーションが Azure Active Directory (Graph API) と Azure サービス管理 API のアクセス許可を選択できます。Azure AD の "サインオンを有効にし、ユーザーのプロファイルを読み取る" アクセス許可は、既定で既に選択されています。 クライアント アプリケーションが Office 365 Azure AD テナントに登録している場合は、SharePoint Online および Exchange Online に対する Web API とアクセス許可も選択できます。 目的の Web API の横にあるドロップダウン メニューでは、次の [2 種類のアクセス許可](active-directory-dev-glossary.md#permissions)から選択できます。

* アプリケーションのアクセス許可: クライアント アプリケーションは、(ユーザー コンテキストなしで) アプリケーションとして Web API に直接アクセスする必要があります。 この種類のアクセス許可には管理者の同意が必要であり、ネイティブ クライアント アプリケーションでは使用できません。
* 委任されたアクセス許可: クライアント アプリケーションは、サインインしているユーザーとして Web API にアクセスする必要がありますが、選択したアクセス許可によってアクセスが制限されます。 この種類のアクセス許可は、管理者の同意を要求するように構成されていない限り、ユーザーが付与できます。 

> [!NOTE]
> 委任されたアクセス許可をアプリケーションに追加しても、テナント内のユーザーに同意が自動的に付与されるわけではありません。この点が Azure クラシック ポータルと異なります。 委任されたアクセス許可が追加されたら、ユーザーは引き続き実行時に手動で同意する必要があります。ただし、管理者が Azure Portal のアプリケーション ページで **[必要なアクセス許可]** セクションの **[アクセス許可の付与]** ボタンをクリックした場合は、同意ダイアログが表示されなくなります。 

#### <a name="to-add-credentials-or-permissions-to-access-web-apis"></a>Web API にアクセスするための資格情報またはアクセス許可を追加するには
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ページの右上隅のアカウント名を選択して、Azure AD テナントを選択します。
3. 上部のメニューで **[Azure Active Directory]** を選択し、**[アプリの登録]**、構成するアプリケーションの順にクリックします。 これにより、アプリケーションの [クイック スタート] ページが表示され、アプリケーションの [設定] ブレードが開きます。
4. Web アプリケーションの資格情報の秘密キーを追加するために、[設定] ブレードの [キー] セクションをクリックします。  
   
   * キーの説明を追加し、期間として 1 年または 2 年を選択します。 
   * 構成の変更を保存すると、右端の列にキーの値が格納されます。 [保存] をクリックした後にこのセクションに戻り、値をコピーしてください。この値は、クライアント アプリケーションの実行時に認証用に使用します。
5. クライアントからリソース API にアクセスするためのアクセス許可を追加するために、[設定] ブレードの [必要なアクセス許可] セクションをクリックします。 
   
   * まず、[追加] ボタンをクリックします。
   * [API を選択します] をクリックし、選択するリソースの種類を選択します。
   * 利用可能な API の一覧を確認するか、検索ボックスを使用して、ディレクトリ内の、Web API を公開している利用可能なリソース アプリケーションの中から選択できます。 目的のリソースをクリックし、**[選択]** をクリックします。
   * 選択した後、**[アクセス許可の選択]** メニューに移動し、そこでアプリケーションの "アプリケーションのアクセス許可" と "委任されたアクセス許可" を選択できます。
   
6. 選択が完了したら、**[完了]** ボタンをクリックします。

> [!NOTE]
> **[完了]** ボタンをクリックすると、構成済みの他のアプリケーションに対するアクセス許可に基づいて、ディレクトリ内のアプリケーションのアクセス許可も自動的に設定されます。  これらのアプリケーションのアクセス許可は、アプリケーションの **[設定]** ブレードで確認できます。
> 
> 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Web API を公開するためのリソース アプリケーションの構成
Web API を開発し、アクセス [スコープ](active-directory-dev-glossary.md#scopes)および[ロール](active-directory-dev-glossary.md#roles)を公開することによってクライアント アプリケーションで使用できるようにすることができます。 適切に構成された Web API は、Graph API や Office 365 API など、他の Microsoft Web API と同様に使用できます。 アクセス スコープおよびロールを公開するには、[アプリケーションのマニフェスト](active-directory-dev-glossary.md#application-manifest)を使用します。アプリケーション マニフェストは、アプリケーションの ID 構成を示す JSON ファイルです。  

次のセクションでは、リソース アプリケーションのマニフェストを変更してアクセス スコープを公開する方法について説明します。

#### <a name="adding-access-scopes-to-your-resource-application"></a>リソース アプリケーションへのアクセス スコープの追加
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ページの右上隅のアカウント名を選択して、Azure AD テナントを選択します。
3. 上部のメニューで **[Azure Active Directory]** を選択し、**[アプリの登録]**、構成するアプリケーションの順にクリックします。 これにより、アプリケーションの [クイック スタート] ページが表示され、アプリケーションの [設定] ブレードが開きます。
4. アプリケーション ページで **[マニフェスト]** をクリックしてインライン マニフェスト エディターを開きます。 
5. "oauth2Permissions" ノードを次の JSON スニペットに置き換えます。 このスニペットは、リソースの所有者が一種のリソースへの委任アクセス権をクライアント アプリケーションに付与できるように、"user impersonation" というスコープを公開する方法の一例を示したものです。 テキストと値は実際のアプリケーションのものに変更してください。
   
        "oauth2Permissions": [
        {
            "adminConsentDescription": "Allow the application full access to the Todo List service on behalf of the signed-in     user",
            "adminConsentDisplayName": "Have full access to the Todo List service",
            "id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
            "isEnabled": true,
            "type": "User",
            "userConsentDescription": "Allow the application full access to the todo service on your behalf",
            "userConsentDisplayName": "Have full access to the todo service",
            "value": "user_impersonation"
            }
        ],
   
    id 値は、 [GUID 生成ツール](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) またはプログラムを使用して作成した新しい GUID である必要があります。 GUID は、Web API によって公開されているアクセス許可の一意の識別子です。 Web API へのアクセスを要求するように適切に構成されているクライアントは、Web API を呼び出すと、スコープ (scp) 要求が上記の値 (この例では user_impersonation) に設定された OAuth 2.0 JWT トークンを提示します。
   
   > [!NOTE]
   > 必要に応じて、他のスコープを後で公開することもできます。 たとえば、Web API で、さまざまな機能に関連付けられた複数のスコープを公開しているとします。 この場合、受け取った OAuth 2.0 JWT トークンのスコープ (scp) 要求を使用して、Web API へのアクセスを制御できるようになります。
   > 
   > 
6. **[保存]** をクリックして、マニフェストを保存します。 これで、ディレクトリ内の他のアプリケーションが使用できるように Web API が構成されました。

#### <a name="to-verify-the-web-api-is-exposed-to-other-applications-in-your-directory"></a>ディレクトリ内の他のアプリケーションに Web API が公開されていることを確認するには
1. 上部のメニューで **[アプリの登録]** をクリックし、Web API へのアクセスを構成するクライアント アプリケーションを選択して、[設定] ブレードに移動します。
2. **[必要なアクセス許可]** セクションで、先ほどアクセス許可を公開した Web API を選択します。 [委任されたアクセス許可] ドロップダウン メニューから、新しいアクセス許可を選択します。

![Todo リストのアクセス許可の表示](./media/active-directory-integrating-applications/todolistpermissions.png)

#### <a name="more-on-the-application-manifest"></a>アプリケーション マニフェストの詳細
アプリケーション マニフェストは、実際にはアプリケーション エンティティを更新するためのメカニズムとして機能します。このエンティティでは、上記の API アクセス スコープなど、Azure AD アプリケーションの ID 構成のすべての属性が定義されています。 アプリケーション エンティティの詳細については、[Graph API の Application エンティティ](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)に関するセクションを参照してください。 API のアクセス許可を指定するために使用するアプリケーション エンティティ メンバーについての詳細な参照情報がわかります。  

* appRoles メンバー: Web API の**アプリケーション アクセス許可**を定義するために使用できる [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) エンティティのコレクションです  
* oauth2Permissions メンバー: Web API の**委任されたアクセス許可**を定義するために使用できる [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) エンティティのコレクションです

アプリケーション マニフェストの一般的概念の詳細については、「 [Azure Active Directory のアプリケーション マニフェストについて](active-directory-application-manifest.md)」をご覧ください。

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Microsoft Graph API による Azure AD Graph と Office 365 へのアクセス  
前に説明したように、独自のリソース アプリケーションで API を公開してアクセスできるようにするだけでなく、Microsoft のリソースによって公開される API にアクセスするようにクライアント アプリケーションを更新することもできます。  Microsoft Graph API (他のアプリケーションに対するアクセス許可のリストでは "Microsoft Graph" と呼ばれます) は、Azure AD に登録されたすべてのアプリケーションが使用できます。 Office 365 によってプロビジョニングされた Azure AD テナントでクライアント アプリケーションを登録する場合は、Microsoft Graph API によって公開される、さまざまな Office 365 リソースへのすべてのアクセス許可にアクセスできます。

Microsoft Graph API によって公開されるアクセス スコープの詳細については、[アクセス許可スコープ | Microsoft Graph API の概念](https://graph.microsoft.io/docs/authorization/permission_scopes)に関する記事をご覧ください。

> [!NOTE]
> 現在の制限により、ネイティブ クライアント アプリケーションは、"組織のディレクトリにアクセスする" アクセス許可を使用する場合に、Azure AD Graph API しか呼び出すことはできません。  この制限は、Web アプリケーションには適用されません。
> 
> 

### <a name="configuring-multi-tenant-applications"></a>マルチテナント アプリケーションの構成
Azure AD にアプリケーションを追加するときに、組織のユーザーだけがアプリケーションにアクセスできるようにする場合があります。 また、外部組織のユーザーがアプリケーションにアクセスできるようにする場合もあります。 前者をシングル テナント アプリケーション、後者をマルチテナント アプリケーションと呼びます。 このセクションで後述するように、シングル テナント アプリケーションの構成を変更して、マルチテナント アプリケーションにすることができます。

シングル テナント アプリケーションとマルチテナント アプリケーションの違いに注意することが重要です。  

* シングル テナント アプリケーションは、1 つの組織で使用することを目的としています。 通常、これらのアプリケーションは、エンタープライズ開発者によって作成された基幹業務 (LOB) アプリケーションです。 シングル テナント アプリケーションには、1 つのディレクトリ内のユーザーだけがアクセスできればよいため、1 つのディレクトリにプロビジョニングするだけで済みます。
* マルチテナント アプリケーションは、多数の組織で使用することを目的としています。 通常、これらのアプリケーションは、独立系ソフトウェア ベンダー (ISV) によって作成された SaaS (サービスとしてのソフトウェア) Web アプリケーションです。 マルチテナント アプリケーションは、アプリケーションが使用される各ディレクトリにプロビジョニングする必要があります。アプリケーションを登録するには、ユーザーまたは管理者の同意が必要です。これは、Azure AD 同意フレームワークによってサポートされます。 すべてのネイティブ クライアント アプリケーションは、リソース所有者のデバイスにインストールされるので、既定でマルチテナントになることに注意してください。 同意フレームワークの詳細については、前の「同意フレームワークの概要」セクションを参照してください。

#### <a name="enabling-external-users-to-grant-your-application-access-to-their-resources"></a>外部ユーザーがアプリケーションにリソースへのアクセスを付与できるようにする
組織の外部の顧客やパートナーが使用できるアプリケーションを作成する場合は、Azure Portal でアプリケーションの定義を更新する必要があります。

> [!NOTE]
> マルチテナントを有効にするときは、アプリケーションのアプリケーション ID URI が検証済みドメインに属していることを確認する必要があります。 また、戻り先 URL が https:// で始まる必要があります。 詳細については、「 [アプリケーション オブジェクトおよびサービス プリンシパル オブジェクト](active-directory-application-objects.md)」をご覧ください。
> 
> 

アプリケーションへの外部ユーザーのアクセスを有効にするには、次の手順に従います。 

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ページの右上隅のアカウント名を選択して、Azure AD テナントを選択します。
3. 上部のメニューで **[Azure Active Directory]** を選択し、**[アプリの登録]**、構成するアプリケーションの順にクリックします。 これにより、アプリケーションの [クイック スタート] ページが表示され、アプリケーションの [設定] ブレードが開きます。
4. [設定] ブレードで、**[プロパティ]** をクリックし、**[マルチテナント]** スイッチを **[はい]** に設定します。

上記の変更を完了すると、他の組織のユーザーと管理者は、アプリケーションにディレクトリや他のデータへのアクセスを許可することができるようになります。

#### <a name="triggering-the-azure-ad-consent-framework-at-runtime"></a>実行時の Azure AD 同意フレームワークのトリガー
同意フレームワークを使用するには、マルチテナント クライアント アプリケーションが OAuth 2.0 を使用して承認を要求する必要があります。 Web アプリケーション、ネイティブ アプリケーション、またはサーバー/デーモン アプリケーションが認証コードとアクセス トークンを要求して Web API を呼び出す方法を示す[コード サンプル](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant)が用意されています。

Web アプリケーションでは、ユーザーにサインアップ エクスペリエンスを提供することもできます。 サインアップ エクスペリエンスを提供する場合、ユーザーがサインアップ ボタンをクリックしたときに、Azure AD OAuth2.0 承認エンドポイントまたは OpenID Connect ユーザー情報エンドポイントにブラウザーをリダイレクトすることが求められます。 これらのエンドポイントでは、アプリケーションが id_token を調べて、新しいユーザーに関する情報を取得できます。 サインアップ フェーズの後、前の「同意フレームワークの概要」セクションで示したものと似た同意プロンプトがユーザーに表示されます。

また、Web アプリケーションでは、管理者が "自社をサインアップ" できるエクスペリエンスを提供することもできます。 このエクスペリエンスでも、ユーザーを Azure AD OAuth 2.0 承認エンドポイントにリダイレクトします。 この場合、prompt=admin_consent パラメーターを承認エンドポイントに渡して、管理者が組織に代わって同意する、管理者の同意エクスペリエンスを強制的に表示します。 グローバル管理者ロールに属するアカウントで認証したユーザーだけが同意できます。他のユーザーが同意するとエラーが発生します。 同意に成功すると、応答に admin_consent=true が含まれます。 アクセス トークンを使用したときに、アプリケーションにサインアップした組織や管理者に関する情報を提供する id_token も受け取ります。

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>シングル ページ アプリケーションでの OAuth 2.0 Implicit Grant の有効化
通常、シングル ページ アプリケーション (SPA) は、ブラウザで実行される JavaScript ヘビーなフロント エンドで構成されており、このフロント エンドがアプリケーションの Web API バックエンドを呼び出してビジネス ロジックを実行します。 Azure AD でホストされている SPA では、Azure AD でのユーザーの認証と、アプリケーションの JavaScript クライアントからバックエンド Web API への呼び出しを保護するために使用できるトークンの取得に OAuth 2.0 Implicit Grant を使用します。 ユーザーの同意後、この同じ認証プロトコルを使用して、クライアントと、アプリケーション用に構成された他の Web API リソースとの間での呼び出しを保護するトークンを取得できます。 暗黙的な認証付与の詳細と、この機能がご使用のアプリケーション シナリオに適しているかどうかについては、 [Azure Active Directory での OAuth2 の暗黙的な許可フロー ](active-directory-dev-understanding-oauth2-implicit-grant.md)に関するページを参照してください。

既定では、OAuth 2.0 Implicit Grant はアプリケーションに対して無効化されています。 [アプリケーション マニフェスト](active-directory-application-manifest.md)で `oauth2AllowImplicitFlow` の値を設定することにより、アプリケーションに対して OAuth 2.0 Implicit Grant を有効化できます。アプリケーション マニフェストは、アプリケーションの ID 構成を示す JSON ファイルです。

#### <a name="to-enable-oauth-20-implicit-grant"></a>OAuth 2.0 Implicit Grant を有効化するには
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ページの右上隅のアカウント名を選択して、Azure AD テナントを選択します。
3. 上部のメニューで **[Azure Active Directory]** を選択し、**[アプリの登録]**、構成するアプリケーションの順にクリックします。 これにより、アプリケーションの [クイック スタート] ページが表示され、アプリケーションの [設定] ブレードが開きます。
4. アプリケーションのページで **[マニフェスト]** をクリックして、インライン マニフェスト エディターを開きます。
   "oauth2AllowImplicitFlow" を見つけて、値を "true" に設定します。 既定では、"false" になっています。
   
    `"oauth2AllowImplicitFlow": true,`
5. 更新したマニフェストを保存します。 保存が完了すると、ユーザー認証に OAuth 2.0 Implicit Grant を使用するように Web API が構成されています。


## <a name="removing-an-application"></a>アプリケーションの削除
このセクションでは、Azure AD テナントからアプリケーションを削除する方法について説明します。

### <a name="removing-an-application-authored-by-your-organization"></a>組織によって作成されたアプリケーションの削除
これらは、Azure AD テナントのメイン [アプリケーション] ページで [自分の会社が所有するアプリケーション] フィルターを使用すると表示されるアプリケーションです。 技術的には、これらは、Azure クラシック ポータルを使用して手動で登録した、あるいは PowerShell または Graph API を使用してプログラムで登録したアプリケーションです。 具体的には、テナント内ではアプリケーション オブジェクトとサービス プリンシパル オブジェクトの両方によって表されます。 詳細については、「 [アプリケーション オブジェクトおよびサービス プリンシパル オブジェクト](active-directory-application-objects.md) 」を参照してください。

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>ディレクトリからシングル テナント アプリケーションを削除するには
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ページの右上隅のアカウント名を選択して、Azure AD テナントを選択します。
3. 上部のメニューで **[Azure Active Directory]** を選択し、**[アプリの登録]**、構成するアプリケーションの順にクリックします。 これにより、アプリケーションの [クイック スタート] ページが表示され、アプリケーションの [設定] ブレードが開きます。
4. アプリケーション ページで **[削除]** をクリックします。
5. 確認メッセージが表示されたら、 **[はい]** をクリックします。

#### <a name="to-remove-a-multi-tenant-application-from-your-directory"></a>ディレクトリからマルチテナント アプリケーションを削除するには
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ページの右上隅のアカウント名を選択して、Azure AD テナントを選択します。
3. 上部のメニューで **[Azure Active Directory]** を選択し、**[アプリの登録]**、構成するアプリケーションの順にクリックします。 これにより、アプリケーションの [クイック スタート] ページが表示され、アプリケーションの [設定] ブレードが開きます。
4. [設定] ブレードで、**[プロパティ]** を選択し、**[マルチテナント]** スイッチを **[いいえ]** に設定します。 これでアプリケーションはシングル テナントに変換されますが、アプリケーションは既に同意している組織に引き続き残されます。
5. アプリケーション ページで **[削除]** ボタンをクリックします。
6. 確認メッセージが表示されたら、 **[はい]** をクリックします。

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>別の組織によって承認されたマルチテナント アプリケーションの削除
これらは、Azure AD テナントのメイン [アプリケーション] ページで [自分の会社が使用するアプリケーション] フィルターを使用すると表示されるアプリケーションのサブセットであり、[自分の会社が所有するアプリケーション] リストに表示されないアプリケーションです。 技術的には、これらは同意プロセス中に登録されたマルチテナント アプリケーションです。 具体的には、テナント内ではサービス プリンシパル オブジェクトのみによって表されます。 詳細については、「 [アプリケーション オブジェクトおよびサービス プリンシパル オブジェクト](active-directory-application-objects.md) 」を参照してください。

(同意後に) ディレクトリへのマルチテナント アプリケーションのアクセス権を削除するには、Azure Portal からアクセス権を削除するための Azure サブスクリプションを会社の管理者が持っている必要があります。 または、会社の管理者が、 [Azure AD PowerShell コマンドレット](http://go.microsoft.com/fwlink/?LinkId=294151) を使用してアクセス権を削除することもできます。

## <a name="next-steps"></a>次のステップ
* アプリの視覚的なガイダンスに関するヒントについては、[統合アプリケーションのブランド化に関するガイドライン](active-directory-branding-guidelines.md)を参照してください。
* アプリケーションのアプリケーション オブジェクトとサービス プリンシパル オブジェクトの関係の詳細については、[アプリケーション オブジェクトとサービス プリンシパル オブジェクト](active-directory-application-objects.md)に関するページを参照してください。
* アプリ マニフェストの役割の詳細については、「[Azure Active Directory のアプリケーション マニフェストについて](active-directory-application-manifest.md)」を参照してください。
* Azure Active Directory (AD) の開発者向けの重要な概念の定義については、「[Azure Active Directory 開発者向け用語集](active-directory-dev-glossary.md)」を参照してください。
* 開発者向けのすべての関連コンテンツの概要については、[Active Directory 開発者ガイド](active-directory-developers-guide.md)を参照してください。


