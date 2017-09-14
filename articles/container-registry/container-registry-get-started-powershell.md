---
title: "Azure Container Registry リポジトリ |Microsoft Docs"
description: "Docker イメージ用に Azure Container Registry リポジトリを使用する方法"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: cristyg
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 1e5d5ea5b1ec121fe008abc48178b1d58f540ce1
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---

# <a name="create-a-private-docker-container-registry-using-the-azure-powershell"></a>Azure PowerShell を使用してプライベート Docker コンテナー レジストリを作成する
Windows コンピューターから [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview) のコマンドを使用してコンテナー レジストリを作成し、その設定を管理します。 コンテナー レジストリの作成と管理は、[Azure Portal](container-registry-get-started-portal.md) を使用するか、[Azure CLI](container-registry-get-started-azure-cli.md) を使用するか、Container Registry [REST API](https://go.microsoft.com/fwlink/p/?linkid=834376) を使用してプログラムで行うこともできます。


* 背景と概念については、[概要](container-registry-intro.md)に関するページを参照してください。
* サポートされているコマンドレットの一覧については、「[Azure Container Registry Management Cmdlets](https://docs.microsoft.com/en-us/powershell/module/azurerm.containerregistry/) (Azure コンテナー レジストリ管理コマンドレット)」を参照してください。


## <a name="prerequisites"></a>前提条件
* **Azure PowerShell**: Azure PowerShell をインストールし、使用を開始するには、[インストール手順](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps)を参照してください。 `Login-AzureRMAccount` を実行して Azure サブスクリプションにログインします。 詳細については、[Azure PowerShell の概要](https://docs.microsoft.com/en-us/powershell/azure/get-started-azurep)に関するページを参照してください。
* **リソース グループ**: コンテナー レジストリを作成する前に[リソース グループ](../azure-resource-manager/resource-group-overview.md#resource-groups)を作成するか、既存のリソース グループを使用します。 Container Registry サービスが[利用可能](https://azure.microsoft.com/regions/services/)な場所にリソース グループがあることを確認します。 Azure PowerShell を使用してリソース グループを作成するには、[PowerShell リファレンス](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps#create-a-resource-group)を参照してください。
* **ストレージ アカウント** (省略可能): 同じ場所にコンテナー レジストリをバックアップするには、Azure Standard [ストレージ アカウント](../storage/common/storage-introduction.md)を作成します。 `New-AzureRMContainerRegistry` を使用してレジストリを作成するときにストレージ アカウントを指定しないと、ストレージ アカウントが自動的に作成されます。 PowerShell を使用してストレージ アカウントを作成するには、[PowerShell リファレンス](https://docs.microsoft.com/en-us/powershell/module/azure/new-azurestorageaccount)を参照してください。 現在、Premium Storage はサポートされていません。
* **サービス プリンシパル** (省略可能): PowerShell を使用してレジストリを作成する場合、既定ではアクセス権が設定されません。 必要に応じて、既存の Azure Active Directory サービス プリンシパルをレジストリに割り当てるか、またはサービス プリンシパルを新しく作成して割り当てます。 代わりに、レジストリの管理者ユーザー アカウントを有効にすることもできます。 この記事の後半のセクションを参照してください。 レジストリ アクセスの詳細については、「[Authenticate with a container registry (コンテナー レジストリによる認証)](container-registry-authentication.md)」を参照してください。

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成
コンテナー レジストリを作成するには、`New-AzureRMContainerRegistry` コマンドを実行します。

> [!TIP]
> レジストリを作成する場合は、アルファベットと数字のみを含む、グローバルに一意のトップレベル ドメイン名を指定します。 この例のレジストリの名前は `MyRegistry` ですが、独自の一意の名前に置き換えてください。
>
>

次のコマンドでは、最小限のパラメーターを使用して、米国中南部の場所のリソース グループ `MyResourceGroup` にコンテナー レジストリ `MyRegistry` を作成します。

```PowerShell
$Registry = New-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry"
```

* `-StorageAccountName` はオプションです。 指定しない場合、レジストリ名とタイムスタンプから成る名前のストレージ アカウントが指定したリソース グループに作成されます。

## <a name="assign-a-service-principal"></a>サービス プリンシパルの割り当て
PowerShell コマンドを使用して、Azure Active Directory [サービス プリンシパル](../azure-resource-manager/resource-group-authenticate-service-principal.md)をレジストリに割り当てます。 これらの例のサービス プリンシパルでは所有者ロールが割り当てられますが、必要に応じて[他のロール](../active-directory/role-based-access-control-configure.md)を割り当てることができます。

### <a name="create-a-service-principal"></a>サービス プリンシパルの作成
次のコマンドでは、新しいサービス プリンシパルが作成されます。 `-Password` パラメーターで強力なパスワードを指定します。

```PowerShell
$ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName ApplicationDisplayName -Password "MyPassword"
```

### <a name="assign-a-new-or-existing-service-principal"></a>新規または既存のサービス プリンシパルを割り当てる
レジストリには、新規または既存のサービス プリンシパルを割り当てることができます。 レジストリに所有者ロールのアクセス権を割り当てる場合は、次の例のようなコマンドを実行します。

```PowerShell
New-AzureRMRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Registry.Id
```

##<a name="sign-in-to-the-registry-with-the-service-principal"></a>サービス プリンシパルを使用してレジストリにサインインする
レジストリにサービス プリンシパルを割り当てた後は、次のコマンドを使用してサインインすることができます。

```PowerShell
docker login -u $ServicePrincipal.ApplicationId -p myPassword
```

## <a name="manage-admin-credentials"></a>管理者の資格情報の管理
管理者アカウントは、コンテナー レジストリごとに自動的に作成され、既定で無効になります。 次の例では、コンテナー レジストリの管理者の資格情報を管理する PowerShell コマンドを示しています。

### <a name="obtain-admin-user-credentials"></a>管理者ユーザーの資格情報を取得する
```PowerShell
Get-AzureRMContainerRegistryCredential -ResourceGroupName "MyResourceGroup" -Name "MyRegistry"
```

### <a name="enable-admin-user-for-an-existing-registry"></a>既存のレジストリの管理者ユーザーを有効にする
```PowerShell
Update-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry" -EnableAdminUser
```

### <a name="disable-admin-user-for-an-existing-registry"></a>既存のレジストリの管理者ユーザーを無効にする
```PowerShell
Update-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry" -DisableAdminUser
```

## <a name="next-steps"></a>次のステップ
* [Docker CLI を使用した最初のイメージのプッシュ](container-registry-get-started-docker-cli.md)

