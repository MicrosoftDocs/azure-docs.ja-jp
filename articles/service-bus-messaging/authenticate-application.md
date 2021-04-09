---
title: Azure Service Bus エンティティにアクセスするためにアプリケーションを認証する
description: この記事では、Azure Service Bus エンティティ (キュー、トピックなど) にアクセスするための Azure Active Directory を使用したアプリケーションの認証に関する情報を提供します。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: c4e19c0ab26d491ba0b95159e274383431aefaee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518230"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Azure Service Bus エンティティにアクセスするために Azure Active Directory を使用してアプリケーションを認証および承認する
Azure Service Bus では、Azure Active Directory (Azure AD) を使用して Service Bus エンティティ (キュー、トピック、サブスクリプション、またはフィルター) への要求を承認することがサポートされています。 Azure AD では、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、サービス プリンシパル (ユーザー、グループ、またはアプリケーションのサービス プリンシパルである可能性があります) にアクセス許可を付与します。 ロールとロールの割り当ての詳細については、[各種ロールの理解](../role-based-access-control/overview.md)に関するページを参照してください。

## <a name="overview"></a>概要
セキュリティ プリンシパル (ユーザー、グループ、またはアプリケーション) で Service Bus エンティティへのアクセスが試行された場合、要求が承認される必要があります。 Azure AD では、リソースへのアクセスは 2 段階のプロセスです。 

 1. まず、セキュリティ プリンシパルの ID が認証され、OAuth 2.0 トークンが返されます。 トークンを要求するリソース名は `https://servicebus.azure.net` です。
 1. 次に、指定されたリソースへのアクセスを承認するために、トークンが要求の一部として Service Bus サービスに渡されます。

認証の手順により、実行時にアプリケーション要求に OAuth 2.0 アクセス トークンが含まれる必要があります。 アプリケーションが Azure VM、仮想マシン スケール セット、または Azure 関数アプリなどの Azure エンティティ内から実行されている場合、マネージド ID を使用してリソースにアクセスできます。 マネージド ID によって Service Bus サービスに対して行われる要求を認証する方法については、「[Azure Service Bus での Azure リソースのマネージド ID](service-bus-managed-service-identity.md)」を参照してください。 

承認の手順では、セキュリティ プリンシパルに 1 つ以上の Azure ロールを割り当てる必要があります。 Azure Service Bus には、Service Bus リソースの一連のアクセス許可を含む Azure ロールが用意されています。 セキュリティ プリンシパルに割り当てられたロールによって、そのプリンシパルが持つアクセス許可が決定されます。 Azure Service Bus に Azure ロールを割り当てる方法の詳細については、「[Azure Service Bus 用の Azure 組み込みロール](#azure-built-in-roles-for-azure-service-bus)」を参照してください。 

Service Bus に対して要求を行うネイティブ アプリケーションと Web アプリケーションは、Azure AD を使用して承認することもできます。 この記事では、アクセス トークンを要求し、それを使用して Service Bus リソースへの要求を承認する方法を示します。 


## <a name="assigning-azure-roles-for-access-rights"></a>アクセス権に対して Azure ロールを割り当てる
Azure Active Directory (Azure AD) では、[Azure RBAC](../role-based-access-control/overview.md) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 Azure Service Bus では Azure 組み込みロールのセットが定義されており、それには Service Bus エンティティへのアクセスに使用されるアクセス許可の一般的なセットが含まれています。また、データにアクセスするためのカスタム ロールを定義することもできます。

Azure ロールが Azure AD セキュリティ プリンシパルに割り当てられると、Azure によりそのセキュリティ プリンシパルのリソースへのアクセス権が付与されます。 アクセスは、サブスクリプションのレベル、リソース グループ、または Service Bus 名前空間にスコープを設定できます。 Azure AD セキュリティ プリンシパルは、Azure リソースのユーザー、グループ、アプリケーション サービス プリンシパル、または[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) の場合があります。

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Azure Service Bus 用の Azure 組み込みロール
Azure Service Bus の場合、名前空間およびそれに関連するすべてのリソースの Azure portal および Azure リソース管理 API による管理は、Azure RBAC モデルを使って既に保護されています。 Azure では、Service Bus 名前空間へのアクセスを承認するための次の Azure 組み込みロールが提供されています。

- [Azure Service Bus データ所有者](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner):Service Bus 名前空間とそのエンティティ (キュー、トピック、サブスクリプション、およびフィルター) へのデータ アクセスが可能です
- [Azure Service Bus データ送信者](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender):このロールを使用して、Service Bus 名前空間とそのエンティティへの送信アクセスを許可します。
- [Azure Service Bus データ受信者](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver):このロールを使用して、Service Bus 名前空間とそのエンティティへの受信アクセスを許可します。 

## <a name="resource-scope"></a>リソースのスコープ 
セキュリティ プリンシパルに Azure ロールを割り当てる前に、セキュリティ プリンシパルに必要なアクセスのスコープを決定します。 ベスト プラクティスとしては、常にできるだけ狭いスコープのみを付与するのが最善の方法です。

次の一覧で、Service Bus リソースへのアクセスのスコープとして指定できるレベルを、最も狭いスコープから順に示します。

- **キュー**、**トピック**、または **サブスクリプション**:ロールの割り当ては、特定の Service Bus エンティティに適用されます。 現在、Azure portal では、サブスクリプション レベルでの Service Bus Azure ロールへのユーザー/グループ/マネージド ID の割り当てはサポートされていません。 
- **[Service Bus 名前空間]** :ロールの割り当ては、名前空間以下とそれに関連付けられているコンシューマー グループに対する Service Bus のトポロジ全体にわたります。
- **[リソース グループ]** :ロールの割り当ては、リソース グループのすべての Service Bus リソースに適用されます。
- **サブスクリプション**:ロールの割り当ては、サブスクリプションのすべてのリソース グループ内のすべての Service Bus リソースに適用されます。

> [!NOTE]
> Azure ロールの割り当ての反映には最大で 5 分かかる場合があることに留意してください。 

組み込みのロールの定義方法の詳細については、[ロール定義](../role-based-access-control/role-definitions.md#management-and-data-operations)に関するページを参照してください。 Azure カスタム ロールの作成については、「[Azure カスタム ロール](../role-based-access-control/custom-roles.md)」を参照してください。


## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure portal を使用して Azure ロールを割り当てる  
Azure RBAC と Azure portal を使用して Azure リソースへのアクセスを管理する方法の詳細については、[こちらの記事](..//role-based-access-control/role-assignments-portal.md)を参照してください。 

ロールの割り当ての適切なスコープを決定したら、Azure portal でそのリソースに移動します。 リソースの [アクセス制御 (IAM)] 設定を表示し、次の手順に従ってロールの割り当てを管理します。

> [!NOTE]
> 以下に示す手順では、Service Bus 名前空間にロールを割り当てます。 同じ手順に従って、他のサポートされているスコープ (リソース グループ、サブスクリプションなど) にロールを割り当てることができます。

1. [Azure portal](https://portal.azure.com/) で、使用する Service Bus 名前空間に移動します。 左側のメニューの **[アクセス制御 (IAM)]** を選択し、名前空間のアクセス制御設定を表示します。 Service Bus 名前空間を作成する必要がある場合は、次の記事の手順に従ってください: [Azure Portal を使用して Service Bus 名前空間を作成する](service-bus-create-namespace-portal.md)。

    ![左側のメニューのアクセス制御を選択する](./media/authenticate-application/select-access-control-menu.png)
1. **[ロールの割り当て]** タブを選択して、ロールの割り当ての一覧を表示します。 ツールバーの **[追加]** ボタンを選択し、 **[ロールの割り当ての追加]** を選択します。 

    ![ツール バーの [追加] ボタン](./media/authenticate-application/role-assignments-add-button.png)
1. **[ロールの割り当ての追加]** ページで、次の手順を実行します。
    1. 割り当てる **Service Bus ロール** を選択します。 
    1. ロールの割り当て先となる **セキュリティ プリンシパル** (ユーザー、グループ、サービス プリンシパル) を検索して見つけます。
    1. **[保存]** を選択して、ロールの割り当てを保存します。 

        ![ユーザーにロールを割り当てる](./media/authenticate-application/assign-role-to-user.png)
    4. ロールの割り当て先となった ID が、そのロールに一覧表示されます。 たとえば、次の画像は、Azure-users のロールが [Azure Service Bus のデータ所有者] であることを示しています。 
        
        ![リスト内のユーザー](./media/authenticate-application/user-in-list.png)

同様の手順に従って、リソース グループ、またはサブスクリプションにスコープを設定したロールを割り当てることができます。 ロールとそのスコープを定義したら、[GitHub のサンプル](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)でこの動作をテストできます。


## <a name="authenticate-from-an-application"></a>アプリケーションからの認証
Service Bus で Azure AD を使用する主な利点は、資格情報をコードに格納する必要がなくなることです。 代わりに、Microsoft ID プラットフォームから OAuth 2.0 アクセス トークンを要求することができます。 Azure AD によって、アプリケーションを実行しているセキュリティ プリンシパル (ユーザー、グループ、またはサービス プリンシパル) が認証されます。 認証が成功すると、Azure AD からアプリケーションにアクセス トークンが返されます。アプリケーションでは、このアクセス トークンを使用して Azure Service Bus への要求を承認できます。

次のセクションでは、Microsoft ID プラットフォーム 2.0 による認証を行うためにネイティブ アプリケーションまたは Web アプリケーションを構成する方法を説明します。 Microsoft ID プラットフォーム 2.0 の詳細については、「[Microsoft ID プラットフォーム (v2.0) の概要](../active-directory/develop/v2-overview.md)」を参照してください。

OAuth 2.0 コード付与フローの概要については、「[OAuth 2.0 コード付与フローを使用して Azure Active Directory Web アプリケーションへアクセスを承認する](../active-directory/develop/v2-oauth2-auth-code-flow.md)」を参照してください。

### <a name="register-your-application-with-an-azure-ad-tenant"></a>アプリケーションを Azure AD テナントに登録する
Azure AD を使用して Service Bus エンティティを承認する最初の手順は、[Azure portal](https://portal.azure.com/) からクライアント アプリケーションを Azure AD テナントに登録することです。 クライアント アプリケーションの登録では、アプリケーションに関する情報を AD に提供します。 これで Azure AD から、アプリケーションを Azure AD ランタイムと関連付ける際に使用できるクライアント ID (アプリケーション ID とも呼ばれます) が提供されます。 クライアント ID の詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md)」を参照してください。 

次の画像は、Web アプリケーションを登録するための手順を示します。

![アプリケーションを登録する](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> アプリケーションをネイティブ アプリケーションとして登録する場合は、リダイレクト URI 用に任意の有効な URI を指定できます。 ネイティブ アプリケーションの場合、この値が実際の URL である必要はありません。 Web アプリケーションの場合、リダイレクト URI はトークンが提供される URL を指定するため、有効な URI である必要があります。

アプリケーションを登録すると、 **[設定]** に **[アプリケーション (クライアント) ID]** が表示されます。

![登録されたアプリケーションのアプリケーション ID](./media/authenticate-application/application-id.png)

Azure AD へのアプリケーションの登録について詳しくは、「[Azure Active Directory とアプリケーションの統合](../active-directory/develop/quickstart-register-app.md)」を参照してください。

> [!IMPORTANT]
> **TenantId** と **ApplicationId** をメモしておきます。 アプリケーションを実行するためにこれらの値が必要になります。

### <a name="create-a-client-secret"></a>クライアント シークレットの作成   
アプリケーションでは、トークンを要求するときに ID を証明するためにクライアント シークレットが必要です。 クライアント シークレットを追加するには、次の手順を行います。

1. Azure portal でアプリの登録に移動します (そのページにまだアクセスしていない場合)。
1. 左側のメニューの **[証明書とシークレット]** を選択します。
1. **[クライアント シークレット]** で、 **[新しいクライアント シークレット]** を選択して新しいシークレットを作成します。

    ![[新しいクライアント シークレット] - ボタン](./media/authenticate-application/new-client-secret-button.png)
1. シークレットの説明を入力し、必要な有効期限の間隔を選んで、 **[追加]** を選択します。

    ![クライアント シークレットの追加ページ](./media/authenticate-application/add-client-secret-page.png)
1. すぐに新しいシークレットの値を安全な場所にコピーします。 完全な値は 1 回だけ表示されます。

    ![クライアント シークレット](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Service Bus API のアクセス許可
アプリケーションがコンソール アプリケーションである場合は、ネイティブ アプリケーションを登録し、**Microsoft.ServiceBus** に対する API アクセス許可を **必要なアクセス許可** セットに追加する必要があります。 また、ネイティブ アプリケーションには、識別子として機能する、Azure AD の **リダイレクト URI** も必要です。この URI はネットワーク宛先である必要はありません。 この例では、サンプル コードが `https://servicebus.microsoft.com` を既に使っているため、この URI を使います。

### <a name="client-libraries-for-token-acquisition"></a>トークン取得のためのクライアント ライブラリ  
アプリケーションを登録し、Azure Service Bus でデータを送受信するためのアクセス許可をこれに付与したら、セキュリティ プリンシパルを認証して OAuth 2.0 トークンを取得するためのコードをアプリケーションに追加できます。 認証してトークンを取得するには、[Microsoft ID プラットフォームの認証ライブラリ](../active-directory/develop/reference-v2-libraries.md)か、OpenID または Connect 1.0 をサポートする別のオープンソース ライブラリのいずれかを使用することができます。 その後、アプリケーションでアクセス トークンを使用して、Azure Service Bus に対する要求を承認することができます。

トークンの取得がサポートされるシナリオの一覧は、[Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub リポジトリの[シナリオ](https://aka.ms/msal-net-scenarios)のセクションを参照してください。

## <a name="sample-on-github"></a>GitHub 上のサンプル
GitHub の次のサンプルを参照してください: [Service Bus の Azure ロールベースのアクセス制御](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl) 

**[Interactive User Login]\(対話型のユーザー ログイン\)** オプションではなく、 **[Client Secret Login]\(クライアント シークレット ログイン\)** オプションを使用します。 クライアント シークレット オプションを使用すると、ポップアップ ウィンドウが表示されません。 アプリケーションでは、認証にテナント ID とアプリ ID が利用されます。 

### <a name="run-the-sample"></a>サンプルを実行する

サンプルを実行する前に、**app.config** ファイルを編集し、シナリオに応じて、次の値を設定します。

- `tenantId`:**TenantId** の値に設定します。
- `clientId`:**ApplicationId** の値に設定します。
- `clientSecret`:クライアント シークレットを使ってサインオンする場合は、Azure AD で作成します。 また、ネイティブ アプリの代わりに Web アプリまたは API を使います。 また、前に作成した名前空間の **[アクセス制御 (IAM)]** にアプリを追加します。
- `serviceBusNamespaceFQDN`:新しく作成した Service Bus 名前空間の完全な DNS 名に設定します (例: `example.servicebus.windows.net`)。
- `queueName`:作成したキューの名前に設定します。
- 前の手順においてアプリで指定したリダイレクト URI です。

コンソール アプリケーションを実行すると、シナリオを選択するように求められます。 数値を入力し、Enter キーを押して、 **[Interactive User Login]\(対話型のユーザー ログイン\)** を選択します。 アプリケーションはログイン ウィンドウを表示し、Service Bus へのアクセスの同意を求めた後、サービスを使ってログイン ID を用いた送信/受信シナリオを実行します。


## <a name="next-steps"></a>次のステップ
- Azure RBAC の詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../role-based-access-control/overview.md)」を参照してください。
- Azure PowerShell、Azure CLI、または REST API で Azure ロールを割り当てて管理する方法については、次の記事を参照してください。
    - [Azure PowerShell を使用して Azure でのロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-powershell.md)  
    - [Azure CLI を使用して Azure ロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-cli.md)
    - [REST API を使用して Azure ロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-rest.md)
    - [Azure Resource Manager テンプレートを使用して Azure でのロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-template.md)

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

- [Service Bus Azure RBAC のサンプル](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
- [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
- [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)