---
title: Azure Active Directory を使用したマネージド ID の認証
description: この記事では、Azure Active Directory を使用して Azure Event Hubs リソースにアクセスするためのマネージド ID を認証する方法について説明します
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: cbd7de7d526e1954aaad60f7d71e5cdf202f6a29
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992459"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Azure Active Directory を使用して Event Hubs リソースにアクセスするためのマネージド ID を認証する
Azure Event Hubs では、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用した Azure Active Directory (Azure AD) 認証がサポートされています。 Azure リソースのマネージド ID では、Azure Virtual Machines (VMs)、Function Apps、Virtual Machine Scale Sets などのサービスで実行されているアプリケーションから Event Hubs リソースへのアクセスを、Azure AD 資格情報を使用して承認することができます。 Azure リソースのマネージド ID を Azure AD 認証と一緒に使用することで、クラウドで動作するアプリケーションに資格情報を保存することを避けることができます。

この記事では、Azure VM からイベント ハブへのアクセスを、マネージド ID を使用して承認する方法について示します。

## <a name="enable-managed-identities-on-a-vm"></a>VM 上のマネージド ID を有効にする
Azure リソースのマネージド ID を使用してご利用の VM から Event Hubs リソースへのアクセスを承認するには、最初に VM 上で Azure リソースのマネージド ID を有効にする必要があります。 Azure リソースのマネージド ID を有効にする方法については、次の記事のいずれかを参照してください。

- [Azure Portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager テンプレート](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager クライアント ライブラリ](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Azure AD のマネージド ID にアクセス許可を付与する
ご利用のアプリケーション内のマネージド ID から Event Hubs サービスへの要求を承認するには、最初にそのマネージド ID に対してロールベースのアクセス制御 (RBAC) の設定を構成します。 Azure Event Hubs で、Event Hubs との間で送受信を行うためのアクセス許可を含む RBAC ロールを定義します。 RBAC ロールがマネージド ID に割り当てられると、適切なスコープで Event Hubs データへのアクセス権がそのマネージド ID に付与されます。

RBAC ロールの割り当ての詳細については、「[Authenticate with Azure Active Directory for access to Event Hubs resources](authorize-access-azure-active-directory.md)」 (Event Hubs リソースへのアクセスに Azure Active Directory を使用して認証する) を参照してください。

## <a name="use-event-hubs-with-managed-identities"></a>マネージド ID で Event Hubs を使用する
マネージド ID で Event Hubs を使用するには、ロールと適切なスコープを ID に割り当てる必要があります。 このセクションの手順では、マネージド ID で実行され Event Hubs リソースにアクセスするシンプルなアプリケーションを使用します。

ここでは、[Azure App Service](https://azure.microsoft.com/services/app-service/) でホストされているサンプル Web アプリケーションを使用します。 Web アプリケーションを作成するための詳細な手順については、[Azure に ASP.NET Core Web アプリを作成する](../app-service/app-service-web-get-started-dotnet.md)に関する記事を参照してください

アプリケーションが作成されたら、次の手順を行います。 

1. **[設定]** に移動し、 **[ID]** を選択します。 
1. **[状態]** を選択して **[オン]** にします。 
1. **[保存]** を選択して設定を保存します。 

    ![Web アプリのマネージド ID](./media/authenticate-managed-identity/identity-web-app.png)

この設定を有効にすると、ご利用の Azure Active Directory (Azure AD) に新しいサービス ID が作成され、App Service ホストに構成されます。

次に、このサービス ID をご利用の Event Hubs リソースの必要なスコープ内のロールに割り当てます。

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Azure portal を使用して RBAC ロールを割り当てるには
Event Hubs リソースにロールを割り当てるには、Azure portal でそのリソースに移動します。 リソースの [アクセス制御 (IAM)] 設定を表示し、次の手順に従ってロールの割り当てを管理します。

> [!NOTE]
> 次の手順では、Event Hubs 名前空間にサービス ID のロールを割り当てます。 同じ手順に従って、任意の Event Hubs リソースにスコープ指定されたロールを割り当てることができます。 

1. Azure portal でご利用の Event Hubs 名前空間に移動し、その名前空間の **[概要]** を表示します。 
1. 左側のメニューの **[アクセス制御 (IAM)]** を選択して、イベント ハブのアクセス制御設定を表示します。
1.  **[ロールの割り当て]** タブを選択して、ロールの割り当ての一覧を表示します。
3.  **[追加]** を選択して、新しいロールを追加します。
4.  **[ロールの割り当ての追加]** ページで、割り当てる Event Hubs ロールを選択します。 次に、ロールを割り当てる登録済みのサービス ID を検索して特定します。
    
    ![[ロールの割り当ての追加] ページ](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  **[保存]** を選択します。 ロールの割り当て先となった ID が、そのロールに一覧表示されます。 たとえば、次の画像は、サービス ID に Event Hubs データ所有者が含まれていることを示しています。
    
    ![ロールに割り当てられた ID](./media/authenticate-managed-identity/role-assigned.png)

ロールを割り当てると、Web アプリケーションには、定義されたスコープの下で Event Hubs リソースへのアクセス権が付与されます。 

### <a name="test-the-web-application"></a>Web アプリケーションをテストする
これで Web アプリケーションを起動し、ブラウザーで aspx のサンプル ページを参照できるようになりました。 [GitHub リポジトリ](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp)には、Event Hubs リソースとの間でデータの送受信を行うサンプル Web アプリケーションがあります。

[Nuget](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) から最新のパッケージをインストールし、さらに次のコードに示すように EventHubClient を使用して Event Hub との間でデータの送受信を開始します。 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```

## <a name="next-steps"></a>次の手順
- GitHub から[サンプル](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp)をダウンロードします。
- Azure リソースのマネージド ID の詳細については、次の記事を参照してください。[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)
- 次の関連記事を参照してください。
    - [Azure Active Directory を使用してアプリケーションから Azure Event Hubs への要求を認証する](authenticate-application.md)
    - [Shared Access Signature を使用して Azure Event Hubs に対する要求を認証する](authenticate-shared-access-signature.md)
    - [Azure Active Directory を使用して Event Hubs リソースへのアクセスを承認する](authorize-access-azure-active-directory.md)
    - [Shared Access Signature を使用して Event Hubs リソースへのアクセスを承認する](authorize-access-shared-access-signature.md)