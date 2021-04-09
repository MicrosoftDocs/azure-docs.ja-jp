---
title: Azure CLI から Azure Active Directory マネージド ID アプリケーションを作成する
titleSuffix: An Azure Communication Services quickstart
description: マネージド ID を使用すると、Azure VM、関数アプリ、その他のリソースで実行されているアプリケーションからの Azure Communication Services へのアクセスを承認できます。 このクイックスタートは、Azure CLI を使用した ID の管理に重点を置いています。
services: azure-communication-services
author: jbeauregardb
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: jbeauregardb
ms.reviewer: mikben
ms.openlocfilehash: e708536395807fc74dc5bfd73836e050832cca39
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493341"
---
# <a name="authorize-access-with-managed-identity-to-your-communication-resource-in-your-development-environment"></a>開発環境で、マネージド ID を使用した通信リソースへのアクセスを承認する

Azure ID クライアント ライブラリでは、Azure SDK に対する Azure Active Directory (Azure AD) トークン認証のサポートが提供されています。 .NET、Java、Python、および JavaScript 用の最新バージョンの Azure Communication Services クライアント ライブラリは、Azure ID ライブラリと統合され、Azure Communication Services 要求を承認するための OAuth 2.0 トークンを取得するための簡単で安全な手段を提供します。

Azure ID クライアント ライブラリの利点は、アプリケーションが開発環境または Azure のどちらで実行されているかにかかわらず、複数のサービスで同じコードを使用して認証できることです。 Azure ID クライアント ライブラリによって、セキュリティ プリンシパルが認証されます。 コードが Azure 上で実行されている場合、セキュリティ プリンシパルは Azure リソースに対するマネージド ID です。 開発環境では、マネージド ID が存在しないため、クライアント ライブラリはテスト目的でユーザーまたは登録したアプリケーションのいずれかを認証します。

## <a name="prerequisites"></a>前提条件

 - Azure CLI。 [インストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)
 - アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成する](https://azure.microsoft.com/free)

## <a name="setting-up"></a>設定

承認対象の Azure リソースでマネージド ID を有効にする必要があります。 Azure リソースのマネージド ID を有効にする方法については、次の記事のいずれかを参照してください。

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager テンプレート](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager クライアント ライブラリ](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [App Services](../../app-service/overview-managed-identity.md)

## <a name="authenticate-a-registered-application-in-the-development-environment"></a>開発環境で登録したアプリケーションを認証する

開発環境で Web ブラウザーによるシングル サインオンまたはログインがサポートされていない場合は、登録したアプリケーションを使用して開発環境から認証を受けることができます。

### <a name="creating-an-azure-active-directory-registered-application"></a>Azure Active Directory で登録したアプリケーションの作成

Azure CLI から登録したアプリケーションを作成するには、操作を実行する Azure アカウントにログインする必要があります。 これを行うには、`az login` コマンドを使用して、ブラウザーで資格情報を入力します。 CLI から Azure アカウントにログインしたら、`az ad sp create-for-rbac` コマンドを呼び出して、登録したアプリケーションを作成できます。

次の例は、Azure CLI を使用して、登録した新しいアプリケーションを作成しています

```azurecli
az ad sp create-for-rbac --name <application-name> 
```

`az ad sp create-for-rbac` コマンドによって、サービス プリンシパルのプロパティの一覧が JSON 形式で返されます。 これらの値をコピーして、次の手順で必要な環境変数を作成するために使用できるようにします。

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```
> [!IMPORTANT]
> Azure ロールの割り当ての反映には数分かかることがあります。

#### <a name="set-environment-variables"></a>環境変数の設定

Azure ID クライアント ライブラリでは、実行時に 3 つの環境変数から値を読み取って、アプリケーションが認証されます。 次の表で、各環境変数に設定する値について説明します。

|環境変数|値
|-|-
|`AZURE_CLIENT_ID`|生成された JSON の `appId` の値 
|`AZURE_TENANT_ID`|生成された JSON の `tenant` の値
|`AZURE_CLIENT_SECRET`|生成された JSON の `password` の値

> [!IMPORTANT]
> 環境変数を設定したら、コンソール ウィンドウを閉じて再度開きます。 Visual Studio または他の開発環境を使用している場合は、新しい環境変数を登録するために再起動が必要となる可能性があります。


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [認証について学習する](../concepts/authentication.md)

次のことも実行できます。

- [Azure ID ライブラリの詳細について学習する](/dotnet/api/overview/azure/identity-readme)
