---
title: Linux VM のユーザー割り当てマネージド ID を使用して Azure Resource Manager にアクセスする
description: Linux VM 上でユーザー割り当てマネージド ID を使用して Azure Resource Manager にアクセスするプロセスについて説明するチュートリアル。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2020
ms.author: barclayn
ROBOTS: NOINDEX,NOFOLLOW
ms.collection: M365-identity-device-management
ms.openlocfilehash: 247b3c1b4341eff11069a6af324fff5cf1fba62c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546592"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>チュートリアル:Linux VM 上でユーザー割り当てマネージド ID を使用して Azure Resource Manager にアクセスする

このチュートリアルでは、ユーザー割り当てマネージド ID を作成して Linux 仮想マシン (VM) に割り当て、その ID を使用して Azure Resource Manager API にアクセスする方法について説明します。 Azure リソースのマネージド ID は、Azure によって自動的に管理されます。 管理対象サービス ID を使用すると、コード内に資格情報を埋め込む必要なく、Azure AD の認証をサポートするサービスに認証することができます。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ユーザー割り当てマネージド ID を作成する
> * ユーザー割り当てマネージド ID を Linux VM に割り当てる 
> * Azure Resource Manager でユーザー割り当てマネージド ID にリソース グループへのアクセスを許可する 
> * ユーザー割り当てマネージド ID を使用してアクセス トークンを取得し、そのアクセス トークンを使用して Azure Resource Manager を呼び出す 

## <a name="prerequisites"></a>前提条件

- マネージド ID の知識。 Azure リソースのマネージド ID 機能に慣れていない場合は、こちらの[概要](overview.md)を参照してください。 
- Azure アカウント。[無料アカウント](https://azure.microsoft.com/free/)にサインアップしてください。
- Linux 仮想マシンも必要です。 このチュートリアル用に仮想マシンを作成する必要がある場合は、[Azure portal での Linux 仮想マシンの作成](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)に関する記事に従ってください。
- サンプル スクリプトを実行するには、次の 2 つのオプションがあります。
    - [Azure Cloud Shell](../../cloud-shell/overview.md) を使用する。これは、コード ブロックの右上隅にある **[Try It]\(試してみる\)** ボタンを使用して開くことができます。
    - 最新バージョンの [Azure CLI](/cli/azure/install-azure-cli) をインストールしてスクリプトをローカルで実行した後、[az login](/cli/azure/reference-index#az-login) を使用して Azure にサインインします。

## <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する

[az identity create](/cli/azure/identity#az-identity-create) を使用して、ユーザー割り当てマネージド ID を作成します。 `-g` パラメーターにはユーザー割り当てマネージド ID を作成するリソース グループを指定し、`-n` パラメーターにはその名前を指定します。 `<RESOURCE GROUP>` と `<UAMI NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。
    
[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <UAMI NAME>
```

応答には、次の例のように、作成されたユーザー割り当てマネージド ID の詳細が含まれています。 次の手順でユーザー割り当てマネージド ID に `id` の値を使用するため、この値をメモしておきます。

```json
{
"clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
"clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
"id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>",
"location": "westcentralus",
"name": "<UAMI NAME>",
"principalId": "9012",
"resourceGroup": "<RESOURCE GROUP>",
"tags": {},
"tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
"type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

## <a name="assign-an-identity-to-your-linux-vm"></a>Linux VM に ID を割り当てる

ユーザー割り当てマネージド ID は、複数の Azure リソース上のクライアントで使用できます。 単一の VM にユーザー割り当てマネージド ID を割り当てるには、次のコマンドを使用します。 `-IdentityID` パラメーターには、前の手順で返された `Id` プロパティを使用します。

[az vm assign-identity](/cli/azure/vm) を使用して、ユーザー割り当てマネージド ID を Linux VM に割り当てます。 `<RESOURCE GROUP>` と `<VM NAME>` のパラメーターの値は、必ず実際の値に置き換えてください。 `--identities` パラメーターの値には、前の手順で返された `id` プロパティを使用します。

```azurecli-interactive
az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>"
```

## <a name="grant-access-to-a-resource-group-in-azure-resource-manager"></a>Azure Resource Manager でリソース グループへのアクセスを許可する

Azure AD 認証をサポートするリソース API に認証するアクセス トークンを要求するためにコードで使用する ID が、Azure リソースのマネージド ID により提供されます。 このチュートリアルでは、コードは Azure Resource Manager API にアクセスします。  

コードで API にアクセスできるようにするには、事前に ID に Azure Resource Manager のリソースへのアクセスを許可する必要があります。 このケースでは、VM が含まれているリソース グループが対象になります。 使用する環境に合わせて、`<SUBSCRIPTION ID>` および `<RESOURCE GROUP>` の値を更新します。 さらに、`<UAMI PRINCIPALID>` を、「[ユーザー割り当てマネージド ID を作成する](#create-a-user-assigned-managed-identity)」の `az identity create` コマンドによって返された `principalId` プロパティで置き換えます。

```azurecli-interactive
az role assignment create --assignee <UAMI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

応答には、次の例のように、作成されたロールの割り当ての詳細が含まれています。

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>VM の ID を使用してアクセス トークンを取得し、このトークンを使用して Resource Manager を呼び出す 

チュートリアルの残りの部分では、以前に作成した VM から作業を行います。

これらの手順を完了するには、SSH クライアントが必要です。 Windows を使用している場合は、[Windows Subsystem for Linux](/windows/wsl/about) で SSH クライアントを使用することができます。 

1. Azure [Portal](https://portal.azure.com) にサインインします。
2. Portal で **[仮想マシン]** に移動し、Linux 仮想マシンに移動して、**[概要]** の **[接続]** をクリックします。 VM に接続する文字列をコピーします。
3. 任意の SSH クライアントを使用して、VM に接続します。 Windows を使用している場合は、[Windows Subsystem for Linux](/windows/wsl/about) で SSH クライアントを使用することができます。 SSH クライアント キーの構成について支援が必要な場合は、「[Azure 上の Windows で SSH キーを使用する方法](~/articles/virtual-machines/linux/ssh-from-windows.md)」または「[Azure に Linux VM 用の SSH 公開キーと秘密キーのペアを作成して使用する方法](~/articles/virtual-machines/linux/mac-create-ssh-keys.md)」をご覧ください。
4. ターミナル ウィンドウで、CURL を使用して、Azure Instance Metadata Service (IMDS) の ID エンドポイントに対して Azure Resource Manager のアクセス トークンを取得するよう要求します。  

   アクセス トークンを取得するための CURL 要求を次の例に示します。 `<CLIENT ID>` を、「[ユーザー割り当てマネージド ID を作成する](#create-a-user-assigned-managed-identity)」の `az identity create` コマンドによって返された `clientId` プロパティで置き換えてください。 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<UAMI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > `resource` パラメーターの値は、Azure AD で予期される値と完全に一致している必要があります。 Resource Manager のリソース ID を使用する場合は、URI の末尾にスラッシュを含める必要があります。 
    
    応答には、Azure Resource Manager へのアクセスに必要なアクセス トークンが含まれています。 
    
    応答の例:  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

5. このアクセス トークンを使用して Azure Resource Manager にアクセスし、以前にユーザー割り当てマネージド ID にアクセスを許可したリソース グループのプロパティを読み取ります。 `<SUBSCRIPTION ID>` と `<RESOURCE GROUP>` は、以前に指定した値で必ず置き換えてください。`<ACCESS TOKEN>` は、前の手順で返されたトークンで置き換えてください。

    > [!NOTE]
    > URL では大文字小文字が区別されるため、リソース グループの命名時に使用したものと同じ大文字と小文字の使い分けが使用されていること、および `resourceGroups` の "G" が大文字であることを確認してください。  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    応答には、次の例のように、特定のリソース グループの情報が含まれています。 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Resource Manager API にアクセスするために、ユーザー割り当てマネージド ID を作成して、それを Linux 仮想マシンに添付する方法について学習しました。  Azure Resource Manager の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md)
