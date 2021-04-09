---
title: Azure CLI を使用して Azure Blockchain Service を管理する
description: Azure CLI を使用して Azure Blockchain Service を管理する方法
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 36b012c486c0c7d3303a81998e88f1605999c899
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87170859"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Azure CLI を使用して Azure Blockchain Service を管理する

Azure portal だけでなく、Azure CLI を使用しても、Azure Blockchain Service 用のブロックチェーン メンバーとトランザクション ノードを管理することができます。

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

CLI をインストールしてローカルで使用する場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="prepare-your-environment"></a>環境を準備する

1. サインインします。

    CLI のローカル インストールを使用する場合は、[az login](/cli/azure/reference-index#az-login) コマンドを使用してサインインします。

    ```azurecli
    az login
    ```

    ターミナルに表示される手順に従って、認証プロセスを完了します。

1. Azure CLI 拡張機能をインストールします。

    Azure CLI の拡張機能の参照を操作する場合は、最初に拡張機能をインストールする必要があります。  Azure CLI 拡張機能を使用すると、コア CLI の一部としてまだ出荷されていない実験用コマンドおよびプレリリース コマンドにアクセスできます。  更新とアンインストールを含む拡張機能の詳細については、「[Azure CLI で拡張機能を使用する](/cli/azure/azure-cli-extensions-overview)」を参照してください。

    次のコマンドを実行して、[Azure Blockchain Service の拡張機能](/cli/azure/ext/blockchain/blockchain)をインストールします。

    ```azurecli-interactive
    az extension add --name blockchain
    ```

## <a name="create-blockchain-member"></a>ブロックチェーン メンバーを作成する

例では、新しいコンソーシアムで Quorum 台帳プロトコルを実行する[ブロックチェーン メンバーを Azure Blockchain Service で作成](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-create)します。

```azurecli
az blockchain member create \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            --location <myBlockchainLocation> \
                            --password <strongMemberAccountPassword> \
                            --protocol "Quorum" \
                            --consortium <myConsortiumName> \
                            --consortium-management-account-password <strongConsortiumManagementPassword> \
                            --sku <skuName>
```

| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが作成されるリソース グループ名。 |
| **name** | Azure Blockchain Service のブロックチェーン メンバーを識別する一意の名前。 名前はパブリック エンドポイント アドレスに使用されます。 たとえば、「 `myblockchainmember.blockchain.azure.com` 」のように入力します。 |
| **location** | ブロックチェーン メンバーが作成される Azure リージョン。 たとえば、「 `eastus` 」のように入力します。 ユーザーや他の Azure アプリケーションに最も近い場所を選択します。 一部のリージョンでは、機能が利用できない場合があります。 |
| **password** | メンバーの既定のトランザクション ノードのパスワード。 このパスワードは、ブロックチェーン メンバーの既定のトランザクション ノード パブリック エンドポイントに接続する際の基本認証に使用します。 このパスワードは、次の 4 つの要件のうちの 3 つを満たし、12 文字から 72 文字までの長さで指定する必要があります。1 つの小文字、1 つの大文字、1 つの数字、番号記号 (#)、パーセント (%)、コンマ (,)、アスタリスク (*)、逆引用符 (\`)、二重引用符 (")、単一引用符 (')、ダッシュ (-)、セミコロン (;) 以外の 1 つの特殊文字。|
| **protocol** | ブロックチェーンのプロトコル。 現時点では、*Quorum* プロトコルがサポートされています。 |
| **consortium** | 参加または作成するコンソーシアムの名前。 コンソーシアムの詳細については、「[Azure Blockchain Service のコンソーシアム](consortium.md)」を参照してください。 |
| **consortium-management-account-password** | コンソーシアム アカウントのパスワードは、メンバー アカウントのパスワードとも呼ばれます。 メンバー アカウントのパスワードは、メンバー用に作成される Ethereum アカウントの秘密キーの暗号化に使用されます。 メンバー アカウントとメンバー アカウントのパスワードをコンソーシアムの管理に使用します。 |
| **sku** | レベルの種類。 *Standard* または *Basic*。 開発、テスト、概念実証には、*Basic* レベルを使用します。 運用グレードのデプロイには、*Standard* レベルを使用します。 また、Blockchain Data Manager を使用している場合や大量のプライベート トランザクションを送信する場合にも、*Standard* レベルを使用する必要があります。 メンバーの作成後に価格レベルを Basic と Standard の間で変更することはできません。 |

## <a name="change-blockchain-member-passwords-or-firewall-rules"></a>ブロックチェーン メンバーのパスワードまたはファイアウォール規則を変更する

例では、[ブロックチェーン メンバー](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-update)のパスワード、コンソーシアム管理パスワード、およびファイアウォール規則を更新します。

```azurecli
az blockchain member update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --password <strongMemberAccountPassword> \
                     --consortium-management-account-password <strongConsortiumManagementPassword> \
                     --firewall-rules <firewallRules>
```

| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが作成されるリソース グループ名。 |
| **name** | Azure Blockchain Service のメンバーを示す名前。 |
| **password** | メンバーの既定のトランザクション ノードのパスワード。 このパスワードは、ブロックチェーン メンバーの既定のトランザクション ノード パブリック エンドポイントに接続する際の基本認証に使用します。 このパスワードは、次の 4 つの要件のうちの 3 つを満たし、12 文字から 72 文字までの長さで指定する必要があります。1 つの小文字、1 つの大文字、1 つの数字、番号記号 (#)、パーセント (%)、コンマ (,)、アスタリスク (*)、逆引用符 (\`)、二重引用符 (")、単一引用符 (')、ダッシュ (-)、セミコロン (;) 以外の 1 つの特殊文字。|
| **consortium-management-account-password** | コンソーシアム アカウントのパスワードは、メンバー アカウントのパスワードとも呼ばれます。 メンバー アカウントのパスワードは、メンバー用に作成される Ethereum アカウントの秘密キーの暗号化に使用されます。 メンバー アカウントとメンバー アカウントのパスワードをコンソーシアムの管理に使用します。 |
| **firewall-rules** | IP 許可リストの開始 IP アドレスと終了 IP アドレス。 |

## <a name="create-transaction-node"></a>トランザクション ノードを作成する

既存のブロックチェーン メンバーの内部に[トランザクション ノードを作成](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-create)します。 トランザクション ノードを追加することにより、セキュリティの分離と負荷の分散を向上させることができます。 たとえば、異なるクライアント アプリケーションごとにトランザクション ノード エンドポイントを作成できます。

```azurecli
az blockchain transaction-node create \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが作成されるリソース グループ名。 |
| **location** | ブロックチェーン メンバーの Azure リージョン。 |
| **member-name** | Azure Blockchain Service のメンバーを示す名前。 |
| **password** | トランザクション ノードのパスワード。 このパスワードは、トランザクション ノード パブリック エンドポイントに接続する際の基本認証に使用します。 このパスワードは、次の 4 つの要件のうちの 3 つを満たし、12 文字から 72 文字までの長さで指定する必要があります。1 つの小文字、1 つの大文字、1 つの数字、番号記号 (#)、パーセント (%)、コンマ (,)、アスタリスク (*)、逆引用符 (\`)、二重引用符 (")、単一引用符 (')、ダッシュ (-)、セミコロン (;) 以外の 1 つの特殊文字。|
| **name** | トランザクション ノードの名前。 |

## <a name="change-transaction-node-password"></a>トランザクション ノードのパスワードを変更する

例では[トランザクション ノードのパスワードを更新](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-update)します。

```azurecli
az blockchain transaction-node update \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが存在するリソース グループ名。 |
| **member-name** | Azure Blockchain Service のメンバーを示す名前。 |
| **password** | トランザクション ノードのパスワード。 このパスワードは、トランザクション ノード パブリック エンドポイントに接続する際の基本認証に使用します。 このパスワードは、次の 4 つの要件のうちの 3 つを満たし、12 文字から 72 文字までの長さで指定する必要があります。1 つの小文字、1 つの大文字、1 つの数字、番号記号 (#)、パーセント (%)、コンマ (,)、アスタリスク (*)、逆引用符 (\`)、二重引用符 (")、単一引用符 (')、ダッシュ (-)、セミコロン (;) 以外の 1 つの特殊文字。|
| **name** | トランザクション ノードの名前。 |

## <a name="list-api-keys"></a>API キーのリスト

API キーは、ユーザー名とパスワードのようにノード アクセスに使用できます。 キー ローテーションをサポートするために 2 つの API キーがあります。 [API キーの一覧を表示](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-list-api-key)するには、次のコマンドを使用します。

```azurecli
az blockchain member list-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>
```

| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが存在するリソース グループ名。 |
| **name** | Azure Blockchain Service のブロックチェーン メンバーの名前。 |

## <a name="regenerate-api-keys"></a>API キーを再生成する

[API キーを再生成する](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-regenerate-api-key)には、次のコマンドを使用します。

```azurecli
az blockchain member regenerate-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            [--key-name {<keyValue1>, <keyValue2>}]
```

| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが存在するリソース グループ名。 |
| **name** | Azure Blockchain Service のブロックチェーン メンバーの名前。 |
| **keyName** | \<keyValue\> を key1 または key2、あるいはその両方に置き換えます。 |

## <a name="delete-a-transaction-node"></a>トランザクション ノードを削除する

例では、[ブロックチェーン メンバーのトランザクション ノードを削除](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-delete)します。

```azurecli
az blockchain transaction-node delete \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --name <myTransactionNode>
```

| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが存在するリソース グループ名。 |
| **member-name** | 削除するトランザクション ノード名も含む Azure Blockchain Service のブロックチェーン メンバーの名前。 |
| **name** | 削除するトランザクション ノードの名前。 |

## <a name="delete-a-blockchain-member"></a>ブロックチェーン メンバーを削除する

例では[ブロックチェーン メンバーを削除](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-delete)します。

```azurecli
az blockchain member delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>

```

| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが存在するリソース グループ名。 |
| **name** | 削除する Azure Blockchain Service のブロックチェーン メンバーの名前。 |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Azure AD ユーザーのアクセスを許可する

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| パラメーター | 説明 |
|---------|-------------|
| **role** | Azure AD ロールの名前。 |
| **assignee** | Azure AD ユーザーの ID。 たとえば、`user@contoso.com` のように指定します。 |
| **スコープ (scope)** | ロール割り当てのスコープ。 ブロックチェーン メンバーまたはトランザクション ノードのいずれかにすることができます。 |

**例:**

Azure AD ユーザーにブロックチェーンの **メンバー** へのノード アクセスを許可します。

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**例:**

Azure AD ユーザーにブロックチェーンの **トランザクション ノード** へのノード アクセスを許可します。

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Azure AD グループまたはアプリケーション ロールにノード アクセスを許可する

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| パラメーター | 説明 |
|---------|-------------|
| **role** | Azure AD ロールの名前。 |
| **assignee-object-id** | Azure AD グループ ID またはアプリケーション ID。 |
| **スコープ (scope)** | ロール割り当てのスコープ。 ブロックチェーン メンバーまたはトランザクション ノードのいずれかにすることができます。 |

**例:**

**アプリケーション ロール** にノード アクセスを許可する

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Azure AD のノード アクセスを削除する

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| パラメーター | 説明 |
|---------|-------------|
| **role** | Azure AD ロールの名前。 |
| **assignee** | Azure AD ユーザーの ID。 たとえば、`user@contoso.com` のように指定します。 |
| **スコープ (scope)** | ロール割り当てのスコープ。 ブロックチェーン メンバーまたはトランザクション ノードのいずれかにすることができます。 |

## <a name="next-steps"></a>次のステップ

[Azure portal で Azure Blockchain Service のトランザクション ノードを構成する](configure-transaction-nodes.md)方法を参照してください。
