---
title: アプリケーションによる Azure SignalR Service へのアクセスを認証する
description: この記事では、Azure Active Directory を使用してアプリケーションによる Azure SignalR Service へのアクセスを認証する方法について説明します
author: terencefan
ms.author: tefa
ms.service: signalr
ms.topic: conceptual
ms.date: 08/03/2020
ms.openlocfilehash: 597b69c1180ea1fb2a6812d648f8b8ad37707d66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101092598"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-azure-signalr-service"></a>Azure Active Directory を使用してアプリケーションによる Azure SignalR Service へのアクセスを認証する
Microsoft Azure では、Azure Active Directory (Azure AD) を利用して、リソースとアプリケーションの統合されたアクセス制御管理が提供されています。 Azure SignalR Service で Azure AD を使用する主な利点は、資格情報をコード内に格納する必要がなくなることです。 代わりに、Microsoft ID プラットフォームから OAuth 2.0 アクセス トークンを要求することができます。 トークンを要求するリソース名は `https://signalr.azure.com/` です。 Azure AD によって、アプリケーションを実行しているセキュリティ プリンシパル (アプリケーション、リソース グループ、またはサービス プリンシパル) が認証されます。 認証が成功すると、Azure AD からアプリケーションにアクセス トークンが返されます。その後、アプリケーションでこのアクセス トークンを使用して、Azure SignalR Service リソースへの要求を承認できます。

ロールが Azure AD セキュリティ プリンシパルに割り当てられると、Azure によりそのセキュリティ プリンシパルのリソースへのアクセス権が付与されます。 サブスクリプション、リソース グループ、または Azure SignalR リソースのレベルに、アクセスのスコープを設定できます。 Azure AD セキュリティを使用すると、ユーザー、グループ、アプリケーション サービス プリンシパル、または [Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) に、ロールを割り当てることができます。 

> [!NOTE]
> ロールの定義はアクセス許可のコレクションです。 ロールベースのアクセス制御 (RBAC) では、これらのアクセス許可をロールの割り当てによってどのように適用するかを制御します。 ロールの割り当ては、セキュリティ プリンシパル、ロールの定義、スコープの 3 つの要素で構成されています。 詳細については、[各種ロールについて](../role-based-access-control/overview.md)の記事をご覧ください。

## <a name="register-your-application-with-an-azure-ad-tenant"></a>アプリケーションを Azure AD テナントに登録する
Azure AD を使用して Azure SignalR Service リソースを承認する最初のステップは、[Azure portal](https://portal.azure.com/) からアプリケーションを Azure AD テナントに登録することです。 アプリケーションを登録するときは、アプリケーションに関する情報を AD に提供します。 これで Azure AD から、アプリケーションを Azure AD ランタイムと関連付ける際に使用できるクライアント ID (アプリケーション ID とも呼ばれます) が提供されます。 クライアント ID の詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md)」を参照してください。 

次の画像は、Web アプリケーションを登録するための手順を示します。

![アプリケーションを登録する](./media/authenticate/app-registrations-register.png)

> [!Note]
> アプリケーションをネイティブ アプリケーションとして登録する場合は、リダイレクト URI 用に任意の有効な URI を指定できます。 ネイティブ アプリケーションの場合、この値が実際の URL である必要はありません。 Web アプリケーションの場合、リダイレクト URI はトークンが提供される URL を指定するため、有効な URI である必要があります。

アプリケーションを登録すると、 **[設定]** に **[アプリケーション (クライアント) ID]** が表示されます。

![登録されたアプリケーションのアプリケーション ID](./media/authenticate/application-id.png)

Azure AD へのアプリケーションの登録について詳しくは、「[Azure Active Directory とアプリケーションの統合](../active-directory/develop/quickstart-register-app.md)」を参照してください。


### <a name="create-a-client-secret"></a>クライアント シークレットの作成   
アプリケーションでは、トークンを要求するときに ID を証明するためにクライアント シークレットが必要です。 クライアント シークレットを追加するには、次の手順を行います。

1. Azure portal でアプリの登録に移動します。
1. **[証明書とシークレット]** の設定を選択します。
1. **[クライアント シークレット]** で、 **[新しいクライアント シークレット]** を選択して新しいシークレットを作成します。
1. シークレットの説明を入力し、適切な有効期限の間隔を選択します。
1. すぐに新しいシークレットの値を安全な場所にコピーします。 完全な値は 1 回だけ表示されます。

![クライアント シークレットを作成する](./media/authenticate/client-secret.png)

### <a name="upload-a-certificate"></a>証明書のアップロード

クライアント シークレットを作成する代わりに、証明書をアップロードすることもできます。

![証明書をアップロードする](./media/authenticate/certification.png)

## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure portal を使用して Azure ロールを割り当てる  
Azure RBAC と Azure portal を使用して Azure リソースへのアクセスを管理する方法の詳細については、[こちらの記事](..//role-based-access-control/role-assignments-portal.md)を参照してください。 

ロールの割り当ての適切なスコープを決定したら、Azure portal でそのリソースに移動します。 リソースの [アクセス制御 (IAM)] 設定を表示し、次の手順に従ってロールの割り当てを管理します。

1. [Azure portal](https://portal.azure.com/) で、お使いの SignalR リソースに移動します。
1. **[アクセス制御 (IAM)]** を選択して、Azure SignalR のアクセス制御設定を表示します。 
1. **[ロールの割り当て]** タブを選択して、ロールの割り当ての一覧を表示します。 ツールバーの **[追加]** ボタンを選択し、 **[ロールの割り当ての追加]** を選択します。 

    ![ツール バーの [追加] ボタン](./media/authenticate/role-assignments-add-button.png)

1. **[ロールの割り当ての追加]** ページで、次の手順を実行します。
    1. 割り当てる **Azure SignalR ロール** を選択します。 
    1. ロールの割り当て先となる **セキュリティ プリンシパル** (ユーザー、グループ、サービス プリンシパル) を検索して見つけます。
    1. **[保存]** を選択して、ロールの割り当てを保存します。 

        ![アプリケーションにロールを割り当てる](./media/authenticate/assign-role-to-application.png)

    1. ロールの割り当て先となった ID が、そのロールに一覧表示されます。 たとえば、次の図は、アプリケーションの `signalr-dev` と `signalr-service` が SignalR App Server ロールにあることを示しています。 
        
        ![ロールの割り当てリスト](./media/authenticate/role-assignment-list.png)

同様の手順に従って、リソース グループ、またはサブスクリプションにスコープを設定したロールを割り当てることができます。 ロールとそのスコープを定義したら、[GitHub のこちらの場所](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)にあるサンプルを使用してこの動作をテストできます。

## <a name="sample-codes-while-configuring-your-app-server"></a>アプリ サーバーを構成するときのサンプル コード

`AddAzureSignalR` の場合は次のオプションを追加します。

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>";
});
```

または、単に `appsettings.json` ファイルで `ConnectionString` を構成します。

```json
{
"Azure": {
    "SignalR": {
      "Enabled": true,
      "ConnectionString": "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>"
    }
  },
}
```

`certificate` を使用するときは、次のように `clientSecret` を `clientCert` に変更します。

```C#
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientCert=<clientCertFilepath>;tenantId=<tenantId>";
```

## <a name="next-steps"></a>次のステップ
- RBAC の詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../role-based-access-control/overview.md)」を参照してください。
- Azure PowerShell、Azure CLI、または REST API で Azure ロールを割り当てて管理する方法については、次の記事を参照してください。
    - [Azure PowerShell を使用してロールベースのアクセス制御 (RBAC) を管理する](../role-based-access-control/role-assignments-powershell.md)  
    - [Azure CLI を使用してロールベースのアクセス制御 (RBAC) を管理する](../role-based-access-control/role-assignments-cli.md)
    - [REST API を使用してロールベースのアクセス制御 (RBAC) を管理する](../role-based-access-control/role-assignments-rest.md)
    - [Azure Resource Manager テンプレートを使用してロールベースのアクセス制御 (RBAC) を管理する](../role-based-access-control/role-assignments-template.md)

次の関連記事を参照してください。
- [Azure Active Directory を使用してマネージド ID による Azure SignalR Service へのアクセスを認証する](authenticate-managed-identity.md)
- [Azure Active Directory を使用して Azure SignalR Service へのアクセスを承認する](authorize-access-azure-active-directory.md)