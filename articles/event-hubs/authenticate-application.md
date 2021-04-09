---
title: Azure Event Hubs リソースにアクセスするためのアプリケーションを認証する
description: この記事では、Azure Active Directory を使用して Azure Event Hubs リソースにアクセスするためのアプリケーションを認証する方法について説明します
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 25ec5f11ca7b5e801e18155f1a3da6474c8e66e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913315"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Event Hubs リソースにアクセスするために Azure Active Directory でアプリケーションを認証する
Microsoft Azure では、Azure Active Directory (Azure AD) を利用して、リソースとアプリケーションの統合されたアクセス制御管理が提供されています。 Azure Event Hubs で Azure AD を使用する主な利点は、資格情報をコード内に格納する必要がなくなることです。 代わりに、Microsoft ID プラットフォームから OAuth 2.0 アクセス トークンを要求することができます。 トークンを要求するリソース名は `https://eventhubs.azure.net/` であり、すべてのクラウド/テナントで同じです (Kafka クライアントの場合、トークンを要求するリソースは `https://<namespace>.servicebus.windows.net` です)。 Azure AD によって、アプリケーションを実行しているセキュリティ プリンシパル (ユーザー、グループ、またはサービス プリンシパル) が認証されます。 認証が成功すると、Azure AD からアプリケーションにアクセス トークンが返されます。アプリケーションでは、このアクセス トークンを使用して Azure Event Hubs リソースへの要求を承認できます。

ロールが Azure AD セキュリティ プリンシパルに割り当てられると、Azure によりそのセキュリティ プリンシパルのリソースへのアクセス権が付与されます。 アクセスでは、サブスクリプションのレベル、リソース グループ、Event Hubs 名前空間、またはそれ以下の任意のリソースにスコープを設定することができます。 Azure AD セキュリティは、ユーザー、グループ、アプリケーション サービス プリンシパル、または[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) にロールを割り当てることができます。 

> [!NOTE]
> ロールの定義はアクセス許可のコレクションです。 これらのアクセス許可をロールの割り当てを通じてどのように適用するかは、Azure ロールベースのアクセス制御 (Azure RBAC) によって制御されます。 ロールの割り当ては、セキュリティ プリンシパル、ロールの定義、スコープの 3 つの要素で構成されています。 詳細については、[各種ロールについて](../role-based-access-control/overview.md)の記事をご覧ください。

## <a name="built-in-roles-for-azure-event-hubs"></a>Azure Event Hubs の組み込みのロール
Azure には、Event Hubs データへの Azure AD と OAuth を使ったアクセスを承認するために、次の Azure の組み込みロールが用意されています。

- [Azure Event Hubs のデータ所有者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): Event Hubs リソースへの完全なアクセス権を付与するには、このロールを使用します。
- [Azure Event Hubs のデータ送信者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): Event Hubs リソースへの送信アクセス権を付与するには、このロールを使用します。
- [Azure Event Hubs のデータ受信者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): Event Hubs リソースへの受信アクセス権を付与するには、このロールを使用します。   

スキーマ レジストリの組み込みロールについては、[スキーマ レジストリのロール](schema-registry-overview.md#azure-role-based-access-control)に関する記事を参照してください。

> [!IMPORTANT]
> Microsoft のプレビュー リリースで、所有者または共同作成者ロールへの Event Hubs データ アクセス特権の追加がサポートされました。 しかし、所有者ロールと共同作成者ロールのデータ アクセス特権は受け入れられなくなりました。 所有者ロールまたは共同作成者ロールを使用している場合は、Azure Event Hubs データ所有者ロールの使用に切り替えてください。


## <a name="authenticate-from-an-application"></a>アプリケーションからの認証
Event Hubs で Azure AD を使用する主な利点は、資格情報をコード内に格納する必要がなくなることです。 代わりに、Microsoft ID プラットフォームから OAuth 2.0 アクセス トークンを要求することができます。 Azure AD によって、アプリケーションを実行しているセキュリティ プリンシパル (ユーザー、グループ、またはサービス プリンシパル) が認証されます。 認証が成功すると、Azure AD からアプリケーションにアクセス トークンが返されます。アプリケーションでは、このアクセス トークンを使用して Azure Event Hubs への要求を承認できます。

次のセクションでは、Microsoft ID プラットフォーム 2.0 による認証を行うためにネイティブ アプリケーションまたは Web アプリケーションを構成する方法を説明します。 Microsoft ID プラットフォーム 2.0 の詳細については、「[Microsoft ID プラットフォーム (v2.0) の概要](../active-directory/develop/v2-overview.md)」を参照してください。

OAuth 2.0 コード付与フローの概要については、「[OAuth 2.0 コード付与フローを使用して Azure Active Directory Web アプリケーションへアクセスを承認する](../active-directory/develop/v2-oauth2-auth-code-flow.md)」を参照してください。

### <a name="register-your-application-with-an-azure-ad-tenant"></a>アプリケーションを Azure AD テナントに登録する
Azure AD を使用して Event Hubs リソースを承認する最初の手順は、[Azure portal](https://portal.azure.com/) からクライアント アプリケーションを Azure AD テナントに登録することです。 クライアント アプリケーションの登録では、アプリケーションに関する情報を AD に提供します。 これで Azure AD から、アプリケーションを Azure AD ランタイムと関連付ける際に使用できるクライアント ID (アプリケーション ID とも呼ばれます) が提供されます。 クライアント ID の詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md)」を参照してください。 

次の画像は、Web アプリケーションを登録するための手順を示します。

![アプリケーションを登録する](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> アプリケーションをネイティブ アプリケーションとして登録する場合は、リダイレクト URI 用に任意の有効な URI を指定できます。 ネイティブ アプリケーションの場合、この値が実際の URL である必要はありません。 Web アプリケーションの場合、リダイレクト URI はトークンが提供される URL を指定するため、有効な URI である必要があります。

アプリケーションを登録すると、 **[設定]** に **[アプリケーション (クライアント) ID]** が表示されます。

![登録されたアプリケーションのアプリケーション ID](./media/authenticate-application/application-id.png)

Azure AD へのアプリケーションの登録について詳しくは、「[Azure Active Directory とアプリケーションの統合](../active-directory/develop/quickstart-register-app.md)」を参照してください。


### <a name="create-a-client-secret"></a>クライアント シークレットの作成   
アプリケーションでは、トークンを要求するときに ID を証明するためにクライアント シークレットが必要です。 クライアント シークレットを追加するには、次の手順を行います。

1. Azure portal でアプリの登録に移動します。
1. **[証明書とシークレット]** の設定を選択します。
1. **[クライアント シークレット]** で、 **[新しいクライアント シークレット]** を選択して新しいシークレットを作成します。
1. シークレットの説明を入力し、適切な有効期限の間隔を選択します。
1. すぐに新しいシークレットの値を安全な場所にコピーします。 完全な値は 1 回だけ表示されます。

    ![クライアント シークレット](./media/authenticate-application/client-secret.png)


## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure portal を使用して Azure ロールを割り当てる  
アプリケーションを登録した後、アプリケーションのサービス プリンシパルを Event Hubs の Azure AD ロールに割り当てます。これについては、「[Azure Event Hubs の組み込みのロール](#built-in-roles-for-azure-event-hubs)」セクションで説明しています。 

1. [Azure portal](https://portal.azure.com/) で Event Hubs 名前空間に移動します。
2. **[概要]** ページで、ロールを割り当てるイベント ハブを選択します。

    ![自分のイベント ハブを選択する](./media/authenticate-application/select-event-hub.png)
1. **[アクセス制御 (IAM)]** を選択して、イベント ハブのアクセス制御設定を表示します。 
1. **[ロールの割り当て]** タブを選択して、ロールの割り当ての一覧を表示します。 ツールバーの **[追加]** ボタンを選択し、 **[ロールの割り当ての追加]** を選択します。 

    ![ツール バーの [追加] ボタン](./media/authenticate-application/role-assignments-add-button.png)
1. **[ロールの割り当ての追加]** ページで、次の手順を実行します。
    1. 割り当てる **Event Hubs ロール** を選択します。 
    1. ロールの割り当て先となる **セキュリティ プリンシパル** (ユーザー、グループ、サービス プリンシパル) を検索して見つけます。 一覧から **登録されたアプリケーション** を選択します。 
    1. **[保存]** を選択して、ロールの割り当てを保存します。 

        ![ユーザーにロールを割り当てる](./media/authenticate-application/assign-role-to-user.png)
    4. **[ロールの割り当て]** タブに切り替えて、ロールの割り当てを確認します。 たとえば、次の画像は、**mywebapp** が **[Azure Event Hubs のデータ送信者]** ロールに含まれていることを示しています。 
        
        ![リスト内のユーザー](./media/authenticate-application/user-in-list.png)

同様の手順を行って、Event Hubs 名前空間、リソース グループ、またはサブスクリプションに対してスコープが指定されたロールを割り当てることができます。 ロールとそのスコープを定義したら、[GitHub のこちらの場所](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)にあるサンプルを使用してこの動作をテストできます。 Azure RBAC と Azure portal を使用して Azure リソースへのアクセスを管理する方法の詳細については、[こちらの記事](..//role-based-access-control/role-assignments-portal.md)を参照してください。 


### <a name="client-libraries-for-token-acquisition"></a>トークン取得のためのクライアント ライブラリ  
アプリケーションを登録し、Azure Event Hubs でデータを送受信するためのアクセス許可をこれに付与したら、セキュリティ プリンシパルを認証して OAuth 2.0 トークンを取得するためのコードをアプリケーションに追加できます。 認証してトークンを取得するには、[Microsoft ID プラットフォームの認証ライブラリ](../active-directory/develop/reference-v2-libraries.md)か、OpenID または Connect 1.0 をサポートする別のオープンソース ライブラリのいずれかを使用することができます。 その後、アプリケーションでアクセス トークンを使用して、Azure Event Hubs に対する要求を承認することができます。

トークンの取得がサポートされるシナリオの一覧は、[Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub リポジトリの[シナリオ](https://aka.ms/msal-net-scenarios)のセクションを参照してください。

## <a name="samples"></a>サンプル
- [Microsoft.Azure.EventHubs サンプル](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
    
    これらのサンプルでは、古い **Microsoft.Azure.EventHubs** ライブラリが使用されていますが、最新の **Azure.Messaging.EventHubs** ライブラリを使用するように簡単に更新できます。 古いライブラリから新しいライブラリを使用するようにサンプルを移行する方法については、[Microsoft.Azure.EventHubs から Azure.Messaging.EventHubs への移行のガイド](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)に関する記事を参照してください。
- [ Azure.Messaging.EventHubs サンプル](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    このサンプルは、最新の **Azure.Messaging.EventHubs** ライブラリを使用するように更新されています。

## <a name="next-steps"></a>次のステップ
- Azure RBAC の詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../role-based-access-control/overview.md)」を参照してください。
- Azure PowerShell、Azure CLI、または REST API で Azure ロールを割り当てて管理する方法については、次の記事を参照してください。
    - [Azure PowerShell を使用して Azure でのロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-powershell.md)  
    - [Azure CLI を使用して Azure ロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-cli.md)
    - [REST API を使用して Azure ロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-rest.md)
    - [Azure Resource Manager テンプレートを使用して Azure でのロールの割り当てを追加する](../role-based-access-control/role-assignments-template.md)

次の関連記事を参照してください。
- [Azure Active Directory を使用して Event Hubs リソースにアクセスするためのマネージド ID を認証する](authenticate-managed-identity.md)
- [Shared Access Signature を使用して Azure Event Hubs に対する要求を認証する](authenticate-shared-access-signature.md)
- [Azure Active Directory を使用して Event Hubs リソースへのアクセスを承認する](authorize-access-azure-active-directory.md)
- [Shared Access Signature を使用して Event Hubs リソースへのアクセスを承認する](authorize-access-shared-access-signature.md)
