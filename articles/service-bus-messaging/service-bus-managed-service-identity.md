---
title: Azure Service Bus での Azure リソースのマネージド ID
description: この記事では、Azure Service Bus エンティティ (キュー、トピック、サブスクリプション) にアクセスするためにマネージド ID を使用する方法について説明します。
ms.topic: article
ms.date: 01/21/2021
ms.openlocfilehash: bd985acd9b775d6baef0abf488952e28c17aef2a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954312"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Azure Service Bus リソースにアクセスするために Azure Active Directory を使用してマネージド ID を認証する
[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) は、デプロイに関連付けられ、その下でアプリケーション コードが実行されるセキュリティ保護された ID を作成できる Azure 間機能です。 この ID は、アプリケーションに必要な特定の Azure リソースにアクセスするためのカスタム アクセス許可を付与するアクセス制御ロールに関連付けることができます。

マネージド ID では、Azure プラットフォームがこのランタイム ID を管理します。 ID 自体やアクセスする必要のあるリソース用に、アクセス キーをアプリケーション コードや構成に保存して保護する必要はありません。 Azure リソース サポートに対してマネージド エンティティが有効にされている Azure App Service アプリケーション内または仮想マシンで実行されている Service Bus クライアント アプリは、SAS ルールと SAS キーや、その他のアクセス トークンを処理する必要はありません。 クライアント アプリに必要なのは、Service Bus メッセージング名前空間のエンドポイント アドレスだけです。 アプリが接続すると、Service Bus はこの記事で後述する例に示す操作で、マネージド エンティティのコンテキストをクライアントにバインドします。 マネージド ID に関連付けられると、Service Bus クライアントは承認済みのすべての操作を実行できます。 マネージド エンティティを Service Bus のロールに関連付けることによって承認が付与されます。 

## <a name="overview"></a>概要
セキュリティ プリンシパル (ユーザー、グループ、またはアプリケーション) で Service Bus エンティティへのアクセスが試行された場合、要求が承認される必要があります。 Azure AD では、リソースへのアクセスは 2 段階のプロセスです。 

 1. まず、セキュリティ プリンシパルの ID が認証され、OAuth 2.0 トークンが返されます。 トークンを要求するリソース名は `https://servicebus.azure.net` です。
 1. 次に、指定されたリソースへのアクセスを承認するために、トークンが要求の一部として Service Bus サービスに渡されます。

認証の手順により、実行時にアプリケーション要求に OAuth 2.0 アクセス トークンが含まれる必要があります。 アプリケーションが Azure VM、仮想マシン スケール セット、または Azure 関数アプリなどの Azure エンティティ内から実行されている場合、マネージド ID を使用してリソースにアクセスできます。 

承認の手順では、セキュリティ プリンシパルに 1 つまたは複数の Azure ロールを割り当てる必要があります。 Azure Service Bus には、Service Bus リソースの一連のアクセス許可を含む Azure ロールが用意されています。 セキュリティ プリンシパルに割り当てられたロールによって、そのプリンシパルが持つアクセス許可が決定されます。 Azure Service Bus に Azure ロールを割り当てる方法の詳細については、「[Azure Service Bus 用の Azure 組み込みロール](#azure-built-in-roles-for-azure-service-bus)」を参照してください。 

Service Bus に対して要求を行うネイティブ アプリケーションと Web アプリケーションは、Azure AD を使用して承認することもできます。 この記事では、アクセス トークンを要求し、それを使用して Service Bus リソースへの要求を承認する方法を示します。 


## <a name="assigning-azure-roles-for-access-rights"></a>アクセス権に対して Azure ロールを割り当てる
Azure Active Directory (Azure AD) では、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 Azure Service Bus では Azure 組み込みロールのセットが定義されており、それには Service Bus エンティティへのアクセスに使用されるアクセス許可の一般的なセットが含まれています。また、データにアクセスするためのカスタム ロールを定義することもできます。

Azure ロールが Azure AD セキュリティ プリンシパルに割り当てられると、Azure によりそのセキュリティ プリンシパルのリソースへのアクセス権が付与されます。 アクセスは、サブスクリプションのレベル、リソース グループ、または Service Bus 名前空間にスコープを設定できます。 Azure AD セキュリティ プリンシパルは、ユーザー、グループ、アプリケーション サービス プリンシパル、または Azure リソースのマネージド ID の場合があります。

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Azure Service Bus 用の Azure 組み込みロール
Azure Service Bus の場合、名前空間およびそれに関連するすべてのリソースの Azure portal および Azure リソース管理 API による管理は、Azure RBAC モデルを使って既に保護されています。 Azure では、Service Bus 名前空間へのアクセスを承認するための次の Azure 組み込みロールが提供されています。

- [Azure Service Bus データ所有者](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner):Service Bus 名前空間とそのエンティティ (キュー、トピック、サブスクリプション、およびフィルター) へのデータ アクセスが可能です
- [Azure Service Bus データ送信者](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender):このロールを使用して、Service Bus 名前空間とそのエンティティへの送信アクセスを許可します。
- [Azure Service Bus データ受信者](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver):このロールを使用して、Service Bus 名前空間とそのエンティティへの受信アクセスを許可します。 

## <a name="resource-scope"></a>リソースのスコープ 
セキュリティ プリンシパルに Azure ロールを割り当てる前に、セキュリティ プリンシパルに必要なアクセスのスコープを決定します。 ベスト プラクティスとしては、常にできるだけ狭いスコープのみを付与するのが最善の方法です。

次の一覧で、Service Bus リソースへのアクセスのスコープとして指定できるレベルを、最も狭いスコープから順に示します。

- **キュー**、**トピック**、または **サブスクリプション**:ロールの割り当ては、特定の Service Bus エンティティに適用されます。 現在、Azure portal では、サブスクリプション レベルでの Service Bus Azure ロールへのユーザー、グループ、マネージド ID の割り当てはサポートされていません。 以下に示したのは、Azure CLI コマンドの使用例です。[az-role-assignment-create](/cli/azure/role/assignment?#az-role-assignment-create) によって、Service Bus の Azure ロールに ID を割り当てています。 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **[Service Bus 名前空間]** :ロールの割り当ては、名前空間以下とそれに関連付けられているコンシューマー グループに対する Service Bus のトポロジ全体にわたります。
- **[リソース グループ]** :ロールの割り当ては、リソース グループのすべての Service Bus リソースに適用されます。
- **サブスクリプション**:ロールの割り当ては、サブスクリプションのすべてのリソース グループ内のすべての Service Bus リソースに適用されます。

> [!NOTE]
> Azure ロールの割り当ての反映には最大で 5 分かかる場合があることに留意してください。 

組み込みのロールの定義方法の詳細については、[ロール定義](../role-based-access-control/role-definitions.md#management-and-data-operations)に関するページを参照してください。 Azure カスタム ロールの作成については、「[Azure カスタム ロール](../role-based-access-control/custom-roles.md)」を参照してください。

## <a name="enable-managed-identities-on-a-vm"></a>VM 上のマネージド ID を有効にする
Azure リソースのマネージド ID を使用して、ご利用の VM から Service Bus リソースを承認するには、最初に VM 上で Azure リソースのマネージド ID を有効にする必要があります。 Azure リソースのマネージド ID を有効にする方法については、次の記事のいずれかを参照してください。

- [Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager テンプレート](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager クライアント ライブラリ](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Azure AD のマネージド ID にアクセス許可を付与する
ご利用のアプリケーション内のマネージド ID から Service Bus サービスへの要求を承認するには、最初にそのマネージド ID に対して Azure ロールベースのアクセス制御 (Azure RBAC) の設定を構成します。 Azure Service Bus によって、Service Bus からの送信と読み取りを行うためのアクセス許可を含む Azure ロールが定義されます。 Azure ロールがマネージド ID に割り当てられると、適切なスコープで Service Bus エンティティへのアクセス権がそのマネージド ID に付与されます。

Azure ロールの割り当ての詳細については、[Azure Active Directory を使用した Service Bus リソースへのアクセスの認証と承認](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)に関する記事を参照してください。

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID による Service Bus の使用
マネージド ID で Service Bus を使用するには、ロールと適切なスコープを ID に割り当てる必要があります。 このセクションの手順では、マネージド ID で実行され Service Bus リソースにアクセスするシンプルなアプリケーションを使用します。

ここでは、[Azure App Service](https://azure.microsoft.com/services/app-service/) でホストされているサンプル Web アプリケーションを使用します。 Web アプリケーションを作成するための詳細な手順については、[Azure に ASP.NET Core Web アプリを作成する](../app-service/quickstart-dotnetcore.md)に関する記事を参照してください

アプリケーションが作成されたら、次の手順を行います。 

1. **[設定]** に移動し、 **[ID]** を選択します。 
1. **[状態]** を選択して **[オン]** にします。 
1. **[保存]** を選択して設定を保存します。 

    ![Web アプリのマネージド ID](./media/service-bus-managed-service-identity/identity-web-app.png)

この設定を有効にすると、ご利用の Azure Active Directory (Azure AD) に新しいサービス ID が作成され、App Service ホストに構成されます。

> [!NOTE]
> マネージド ID を使用する場合、接続文字列は `Endpoint=sb://<NAMESPACE NAME>.servicebus.windows.net/;Authentication=Managed Identity` の形式にする必要があります。

ここで、このサービス ID をご利用の Service Bus リソースの必要なスコープ内のロールに割り当てます。

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Azure portal を使用して Azure ロールを割り当てるには
Service Bus 名前空間にロールを割り当てるには、Azure portal で名前空間に移動します。 リソースの [アクセス制御 (IAM)] 設定を表示し、次の手順に従ってロールの割り当てを管理します。

> [!NOTE]
> 次の手順では、Service Bus 名前空間にサービス ID のロールを割り当てます。 同じ手順に従って、他のサポートされているスコープ (リソース グループとサブスクリプション) でロールを割り当てることができます。 
> 
> 存在しない場合は、[Service Bus Messaging 名前空間を作成](service-bus-create-namespace-portal.md)します。 

1. Azure portal で、ご利用の Service Bus 名前空間に移動し、その名前空間の **[概要]** を表示します。 
1. 左側のメニューの **[アクセス制御 (IAM)]** を選択して、Service Bus 名前空間のアクセス制御設定を表示します。
1.  **[ロールの割り当て]** タブを選択して、ロールの割り当ての一覧を表示します。
3.  **[追加]** を選択し、 **[ロール割り当ての追加]** を選択します。
4.  **[ロールの割り当ての追加]** ページで、これらの手順に従います。
    1. **[ロール]** で、割り当てる Service Bus ロールを選択します。 この例では、**Azure Service Bus データ所有者** です。
    1. **[アクセスの割り当て先]** フィールドで、 **[システム割り当てマネージド ID]** の **[App Service]** を選択します。 
    1. Web アプリのマネージド ID が作成された **サブスクリプション** を選択します。
    1. 作成した Web アプリの **マネージド ID** を選択します。 ID の既定の名前は、Web アプリの名前と同じです。 
    1. 次に、 **[保存]** を選択します。
        
        ![[ロールの割り当ての追加] ページ](./media/service-bus-managed-service-identity/add-role-assignment-page.png)

    ロールを割り当てると、Web アプリケーションには、定義されたスコープの下で Service Bus リソースへのアクセス権が付与されます。 

    > [!NOTE]
    > マネージド ID をサポートするサービスの一覧については、「[Azure リソースのマネージド ID をサポートするサービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)」を参照してください。

### <a name="run-the-app"></a>アプリを実行する
次に、作成した ASP.NET アプリケーションの既定のページを変更します。 [こちらの GitHub リポジトリ](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)の Web アプリケーション コードを使用します。  

Default.aspx ページはランディング ページです。 コードは Default.aspx.cs ファイルにあります。 いくつかの入力フィールドと、Service Bus に接続してメッセージを送受信するための **send** ボタンおよび **receive** ボタンを備えた最小限の Web アプリケーションが作成されます。

[MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) オブジェクトを初期化する方法に注意してください。 共有アクセス トークン (SAS) トークン プロバイダーを使用するのではなく、コードで `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` を呼び出してマネージド ID のトークン プロバイダーを作成します。 そのため、保持および使用するシークレットはありません。 マネージド ID コンテキストから Service Bus へのフローと承認ハンドシェイクは、トークン プロバイダーによって自動的に処理されます。 これは SAS を使用するよりも単純なモデルです。

これらの変更を行ったら、アプリケーションを発行して実行します。 適切な発行データを簡単に取得するには、Visual Studio で発行プロファイルをダウンロードしてインポートします。

![発行プロファイルを取得する](./media/service-bus-managed-service-identity/msi3.png)
 
メッセージを送受信するには、名前空間の名前と作成したエンティティの名前を入力します。 次に、 **[send]** または **[receive]** をクリックします。


> [!NOTE]
> - マネージド ID は、Azure 環境内の App Services、Azure VM、およびスケール セットでのみ機能します。 .NET アプリケーションの場合は、Service Bus NuGet パッケージで使用される Microsoft.Azure.Services.AppAuthentication ライブラリがこのプロトコルの抽象化を提供し、ローカル開発エクスペリエンスをサポートします。 このライブラリを使うと、Visual Studio、Azure CLI 2.0、または Active Directory 統合認証のユーザー アカウントを使って、開発用マシン上でローカルにコードをテストすることもできます。 このライブラリでのローカル開発オプションの詳細については、「[Service-to-service authentication to Azure Key Vault using .NET](/dotnet/api/overview/azure/service-to-service-authentication)」 (.NET を使用した Azure Key Vault に対するサービス間認証) を参照してください。  

## <a name="next-steps"></a>次のステップ

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)
