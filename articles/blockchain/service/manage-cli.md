---
title: Azure CLI を使用して Azure Blockchain Service を管理する
description: Azure CLI を使用して Azure Blockchain Service を作成および管理する方法
services: azure-blockchain
keywords: ''
author: pataltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: d078ca181b2eed4b80d4f12f1c03b42f4e242194
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65154451"
---
# <a name="manage-azure-blockchain-service-with-azure-cli"></a>Azure CLI で Azure Blockchain Service を管理する

Azure portal だけでなく、Azure CLI を使用しても、Azure Blockchain Service 用のブロックチェーン メンバーとトランザクション ノードをすばやく作成して管理することができます。

最新の [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) がインストールされていること、および `az login` で Azure アカウントにログインしていることを確認します。

次の例では、`<parameter names>` を独自の値に置き換えます。

## <a name="create-blockchain-member"></a>ブロックチェーン メンバーを作成する

例では、新しいコンソーシアムで Quorum 台帳プロトコルを実行するブロックチェーン メンバーを Azure Blockchain Service で作成します。

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"<myBlockchainLocation>\", \"properties\": {\"password\": \"<myStrongPassword>\", \"protocol\": \"Quorum\", \"consortium\": \"<myConsortiumName>\", \"consortiumManagementAccountPassword\": \"<myConsortiumManagementAccountPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] }, \"sku\": { \"name\": \"<skuName>\" } }"
```

| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが作成されるリソース グループ名。 |
| **name** | Azure Blockchain Service のブロックチェーン メンバーを識別する一意の名前。 名前はパブリック エンドポイント アドレスに使用されます。 たとえば、「 `myblockchainmember.blockchain.azure.com` 」のように入力します。 |
| **location** | ブロックチェーン メンバーが作成される Azure リージョン。 たとえば、「 `eastus` 」のように入力します。 ユーザーや他の Azure アプリケーションに最も近い場所を選択します。 |
| **password** | メンバー アカウントのパスワード。 メンバー アカウントのパスワードは、基本認証を使用してブロックチェーン メンバーのパブリック エンドポイントへの認証を行うために使用されます。 このパスワードは、次の 4 つの要件のうちの 3 つを満たし、12 文字から 72 文字までの長さで指定する必要があります。1 つの小文字、1 つの大文字、1 つの数字、番号記号 (#)、パーセント (%)、コンマ (,)、アスタリスク (*)、逆引用符 (\`)、二重引用符 (")、単一引用符 (')、ダッシュ (-)、セミコロン (;) 以外の 1 つの特殊文字。|
| **protocol** | パブリック プレビューでは Quorum がサポートされます。 |
| **consortium** | 参加または作成するコンソーシアムの名前。 |
| **consortiumManagementAccountPassword** | コンソーシアム管理パスワード。 パスワードは、コンソーシアムに参加するために使用されます。 |
| **ruleName** | IP アドレス範囲をホワイトリストに登録するためのルールの名前。 ファイアウォール規則に関する省略可能なパラメーター。|
| **startIpAddress** | ホワイトリストの IP アドレス範囲の開始。 ファイアウォール規則に関する省略可能なパラメーター。 |
| **endIpAddress** | ホワイトリストの IP アドレス範囲の終了。 ファイアウォール規則に関する省略可能なパラメーター。 |
| **skuName** | レベルの種類。 Standard には S0 を使用し、Basic には B0 を使用します。 |

## <a name="change-blockchain-member-password"></a>ブロックチェーン メンバーのパスワードを変更する

例ではブロックチェーン メンバーのパスワードを変更します。

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.password="<myStrongPassword>" --remove properties.consortiumManagementAccountAddress
```
| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが作成されるリソース グループ名。 |
| **name** | Azure Blockchain Service のメンバーを示す名前。 |
| **password** | メンバー アカウントのパスワード。 このパスワードは、次の 4 つの要件のうちの 3 つを満たし、12 文字から 72 文字までの長さで指定する必要があります。1 つの小文字、1 つの大文字、1 つの数字、番号記号 (#)、パーセント (%)、コンマ (,)、アスタリスク (*)、逆引用符 (\`)、二重引用符 (")、単一引用符 (')、ダッシュ (-)、セミコロン (;) 以外の 1 つの特殊文字。 |


## <a name="create-transaction-node"></a>トランザクション ノードを作成する

既存のブロックチェーン メンバーの内部にトランザクション ノードを作成します。 トランザクション ノードを追加することにより、セキュリティの分離と負荷の分散を向上させることができます。 たとえば、異なるクライアント アプリケーションごとにトランザクション ノード エンドポイントを作成できます。

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --is-full-object --properties "{ \"location\": \"<myRegion>\", \"properties\": { \"password\": \"<myStrongPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] } }"
```

| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが作成されるリソース グループ名。 |
| **name** | 新しいトランザクション ノード名も含む Azure Blockchain Service のブロックチェーン メンバーの名前。 |
| **location** | ブロックチェーン メンバーが作成される Azure リージョン。 たとえば、「 `eastus` 」のように入力します。 ユーザーや他の Azure アプリケーションに最も近い場所を選択します。 |
| **password** | トランザクション ノードのパスワード。 このパスワードは、次の 4 つの要件のうちの 3 つを満たし、12 文字から 72 文字までの長さで指定する必要があります。1 つの小文字、1 つの大文字、1 つの数字、番号記号 (#)、パーセント (%)、コンマ (,)、アスタリスク (*)、逆引用符 (\`)、二重引用符 (")、単一引用符 (')、ダッシュ (-)、セミコロン (;) 以外の 1 つの特殊文字。 |
| **ruleName** | IP アドレス範囲をホワイトリストに登録するためのルールの名前。 ファイアウォール規則に関する省略可能なパラメーター。 |
| **startIpAddress** | ホワイトリストの IP アドレス範囲の開始。 ファイアウォール規則に関する省略可能なパラメーター。 |
| **endIpAddress** | ホワイトリストの IP アドレス範囲の終了。 ファイアウォール規則に関する省略可能なパラメーター。|

## <a name="change-transaction-node-password"></a>トランザクション ノードのパスワードを変更する

例ではトランザクション ノードのパスワードを変更します。

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --set properties.password="<myStrongPassword>"
```

| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが存在するリソース グループ名。 |
| **name** | 新しいトランザクション ノード名も含む Azure Blockchain Service のブロックチェーン メンバーの名前。 |
| **password** | トランザクション ノードのパスワード。 このパスワードは、次の 4 つの要件のうちの 3 つを満たし、12 文字から 72 文字までの長さで指定する必要があります。1 つの小文字、1 つの大文字、1 つの数字、番号記号 (#)、パーセント (%)、コンマ (,)、アスタリスク (*)、逆引用符 (\`)、二重引用符 (")、単一引用符 (')、ダッシュ (-)、セミコロン (;) 以外の 1 つの特殊文字。 |

## <a name="change-consortium-management-account-password"></a>コンソーシアム管理アカウントのパスワードを変更する

コンソーシアム管理アカウントは、コンソーシアムのメンバーシップの管理に使用されます。 各メンバーはコンソーシアム管理アカウントによって一意に識別され、このアカウントのパスワードを次のコマンドで変更することができます。

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.consortiumManagementAccountPassword="<myConsortiumManagementAccountPassword>" --remove properties.consortiumManagementAccountAddress
```

| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが作成されるリソース グループ名。 |
| **name** | Azure Blockchain Service のメンバーを示す名前。 |
| **consortiumManagementAccountPassword** | コンソーシアム管理アカウントのパスワード。 このパスワードは、次の 4 つの要件のうちの 3 つを満たし、12 文字から 72 文字までの長さで指定する必要があります。1 つの小文字、1 つの大文字、1 つの数字、番号記号 (#)、パーセント (%)、コンマ (,)、アスタリスク (*)、逆引用符 (\`)、二重引用符 (")、単一引用符 (')、ダッシュ (-)、セミコロン (;) 以外の 1 つの特殊文字。 |
  
## <a name="update-firewall-rules"></a>ファイアウォール規則を更新する

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.firewallRules="[ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ]" --remove properties.consortiumManagementAccountAddress
```

| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが存在するリソース グループ名。 |
| **name** | Azure Blockchain Service のブロックチェーン メンバーの名前。 |
| **ruleName** | IP アドレス範囲をホワイトリストに登録するためのルールの名前。 ファイアウォール規則に関する省略可能なパラメーター。|
| **startIpAddress** | ホワイトリストの IP アドレス範囲の開始。 ファイアウォール規則に関する省略可能なパラメーター。|
| **endIpAddress** | ホワイトリストの IP アドレス範囲の終了。 ファイアウォール規則に関する省略可能なパラメーター。|

## <a name="list-api-keys"></a>API キーのリスト

API キーは、ユーザー名とパスワードのようにノード アクセスに使用できます。 キー ローテーションをサポートするために 2 つの API キーがあります。 API キーの一覧を表示するには、次のコマンドを使用します。

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "listApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers
```

| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが存在するリソース グループ名。 |
| **name** | 新しいトランザクション ノード名も含む Azure Blockchain Service のブロックチェーン メンバーの名前。 |

## <a name="regenerate-api-keys"></a>API キーを再生成する

API キーを再生成するには、次のコマンドを使用します。

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "regenerateApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers --request-body '{"keyName":"<keyValue>"}'
```


| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが存在するリソース グループ名。 |
| **name** | 新しいトランザクション ノード名も含む Azure Blockchain Service のブロックチェーン メンバーの名前。 |
| **keyName** | \<keyValue\> は、key1 または key2 のいずれかで置き換えます。 |

## <a name="delete-a-transaction-node"></a>トランザクション ノードを削除する

例では、ブロックチェーン メンバーのトランザクション ノードを削除します。

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers
```

| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが存在するリソース グループ名。 |
| **name** | 削除する新しいトランザクション ノード名も含む Azure Blockchain Service のブロックチェーン メンバーの名前。 |

## <a name="delete-a-blockchain-member"></a>ブロックチェーン メンバーを削除する

例ではブロックチェーン メンバーを削除します。

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers
```

| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが存在するリソース グループ名。 |
| **name** | 削除する Azure Blockchain Service のブロックチェーン メンバーの名前。 |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Azure AD ユーザーのアクセスを許可する

```azurecli
az role assignment create --role <role> --assignee <assignee> --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| パラメーター | 説明 |
|---------|-------------|
| **role** | Azure AD ロールの名前。 |
| **assignee** | Azure AD ユーザーの ID。 たとえば、`user@contoso.com` のように指定します。 |
| **scope** | ロール割り当てのスコープ。 トランザクション ノードのいずれかのブロックチェーン メンバーにすることができます。 |

**例:**

Azure AD ユーザーにブロックチェーンの**メンバー**へのノード アクセスを許可します。

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**例:**

Azure AD ユーザーにブロックチェーンの**トランザクション ノード**へのノード アクセスを許可します。

```azurecli
az role assignment create \
  --role "MyRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Azure AD グループまたはアプリケーション ロールにノード アクセスを許可する

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id>
```
| パラメーター | 説明 |
|---------|-------------|
| **role** | Azure AD ロールの名前。 |
| **assignee-object-id** | Azure AD グループ ID またはアプリケーション ID。 |
| **scope** | ロール割り当てのスコープ。 トランザクション ノードのいずれかのブロックチェーン メンバーにすることができます。 |

**例:**

**アプリケーション ロール**にノード アクセスを許可する

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee-object-id 22222222-2222-2222-2222-222222222222 \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Azure AD のノード アクセスを削除する

```azurecli
az role assignment delete --role <myRole> --assignee <assignee> --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| パラメーター | 説明 |
|---------|-------------|
| **role** | Azure AD ロールの名前。 |
| **assignee** | Azure AD ユーザーの ID。 たとえば、`user@contoso.com` のように指定します。 |
| **scope** | ロール割り当てのスコープ。 トランザクション ノードのいずれかのブロックチェーン メンバーにすることができます。 |

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure portal で Azure Blockchain Service のトランザクション ノードを構成する](configure-transaction-nodes.md)
