---
title: Azure Active Directory を使用したマネージド ID の認証
description: この記事では、Azure Active Directory を使用して Azure SignalR Service にアクセスするためのマネージド ID を認証する方法について説明します。
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: c561bb507a5178f4a838b370a3da8af9447829f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101092555"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-signalr-resources"></a>Azure SignalR リソースにアクセスするために Azure Active Directory を使用してマネージド ID を認証する
Azure SignalR Service では、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用した Azure Active Directory (Azure AD) 認証がサポートされています。 Azure リソースのマネージド ID では、Azure Virtual Machines (VMs)、Function Apps、Virtual Machine Scale Sets などのサービスで実行されているアプリケーションから Azure SignalR Service リソースへのアクセスを、Azure AD 資格情報を使用して承認することができます。 Azure リソースのマネージド ID を Azure AD 認証と一緒に使用することで、クラウドで動作するアプリケーションに資格情報を保存することを避けることができます。

この記事では、Azure VM から Azure SignalR Service へのアクセスを、マネージド ID を使用して承認する方法について示します。

## <a name="enable-managed-identities-on-a-vm"></a>VM 上のマネージド ID を有効にする
Azure リソースのマネージド ID を使用して、ご利用の VM から Azure SignalR Service リソースを承認するには、最初に VM 上で Azure リソースのマネージド ID を有効にする必要があります。 Azure リソースのマネージド ID を有効にする方法については、次の記事のいずれかを参照してください。

- [Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager テンプレート](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager クライアント ライブラリ](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Azure AD のマネージド ID にアクセス許可を付与する
ご利用のアプリケーション内のマネージド ID から Azure SignalR Service への要求を承認するには、最初にそのマネージド ID に対してロールベースのアクセス制御 (RBAC) の設定を構成します。 `AccessKey` または `ClientToken` へのアクセス許可を含む RBAC ロールは、Azure SignalR Service によって定義されます。 RBAC ロールがマネージド ID に割り当てられると、適切なスコープで Azure SignalR Service へのアクセス権がそのマネージド ID に付与されます。

RBAC ロールの割り当ての詳細については、[Azure SignalR Service リソースにアクセスするための Azure Active Directory を使用した認証](authorize-access-azure-active-directory.md)に関するページを参照してください。

## <a name="connect-to-azure-signalr-service-with-managed-identities"></a>マネージド ID を使用した Azure SignalR Service への接続
マネージド ID で Azure SignalR Service に接続するには、ロールと適切なスコープを ID に割り当てる必要があります。 このセクションの手順では、マネージド ID で実行され、Azure SignalR Service リソースにアクセスするシンプルなアプリケーションを使用します。

ここでは、Azure 仮想マシンのサンプル リソースを使用します。

1. **[設定]** に移動し、 **[ID]** を選択します。 
1. **[状態]** を選択して **[オン]** にします。 
1. **[保存]** を選択して設定を保存します。 

    ![仮想マシンのマネージド ID](./media/authenticate/identity-virtual-machine.png)

この設定を有効にすると、ご利用の Azure Active Directory (Azure AD) に新しいサービス ID が作成され、App Service ホストに構成されます。

ここで、このサービス ID をご利用の Azure SignalR Service リソースの必要なスコープ内のロールに割り当てます。

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

## <a name="samples-code-while-configuring-your-app-server"></a>アプリケーション サーバーを構成するときのサンプル コード

`AddAzureSignalR` の場合は次のオプションを追加します。

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;";
});
```

## <a name="next-steps"></a>次のステップ
- RBAC の詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../role-based-access-control/overview.md)」を参照してください。
- Azure PowerShell、Azure CLI、または REST API で Azure ロールを割り当てて管理する方法については、次の記事を参照してください。
    - [Azure PowerShell を使用してロールベースのアクセス制御 (RBAC) を管理する](../role-based-access-control/role-assignments-powershell.md)  
    - [Azure CLI を使用してロールベースのアクセス制御 (RBAC) を管理する](../role-based-access-control/role-assignments-cli.md)
    - [REST API を使用してロールベースのアクセス制御 (RBAC) を管理する](../role-based-access-control/role-assignments-rest.md)
    - [Azure Resource Manager テンプレートを使用してロールベースのアクセス制御 (RBAC) を管理する](../role-based-access-control/role-assignments-template.md)

次の関連記事を参照してください。
- [Azure SignalR Service にアクセスするために Azure Active Directory でアプリケーションを認証する](authenticate-application.md)
- [Azure Active Directory を使用して Azure SignalR Service へのアクセスを承認する](authorize-access-azure-active-directory.md)