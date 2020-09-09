---
title: Azure サブスクリプションを別の Azure AD ディレクトリに移転する (プレビュー)
description: Azure サブスクリプションと既知の関連リソースを別の Azure Active Directory (Azure AD) ディレクトリに移転する方法について説明します。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.workload: identity
ms.date: 07/01/2020
ms.author: rolyon
ms.openlocfilehash: 664687d096a3a9c6ce9a6c7de0025604e046b0a1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87029979"
---
# <a name="transfer-an-azure-subscription-to-a-different-azure-ad-directory-preview"></a>Azure サブスクリプションを別の Azure AD ディレクトリに移転する (プレビュー)

> [!IMPORTANT]
> サブスクリプションを別の Azure AD ディレクトリに移転するための以下の手順は、現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

組織には複数の Azure サブスクリプションがある場合があります。 各サブスクリプションは、特定の Azure Active Directory (Azure AD) ディレクトリと関連付けられています。 管理を容易にするために、サブスクリプションを別の Azure AD ディレクトリに移転することが必要になる場合があります。 サブスクリプションを別の Azure AD ディレクトリに移転する場合、一部のリソースはターゲット ディレクトリに移転されません。 たとえば、Azure のロールベースのアクセス制御 (Azure RBAC) でのすべてのロールの割り当てとカスタム ロールは、ソース ディレクトリからは**完全に**削除され、ターゲット ディレクトリには移転されません。

この記事では、サブスクリプションを別の Azure AD ディレクトリに移転し、移転後にいくつかのリソースを再作成するために実行できる基本的な手順について説明します。

## <a name="overview"></a>概要

別の Azure AD ディレクトリへの Azure サブスクリプションの移転は、慎重に計画して実行する必要がある複雑なプロセスです。 多くの Azure サービスでは、セキュリティ プリンシパル (ID) が通常どおりに動作するか、他の Azure リソースを管理する必要があります。 この記事では、セキュリティ プリンシパルに大きく依存する Azure サービスのほとんどについて説明しますが、これは包括的なものではありません。

> [!IMPORTANT]
> サブスクリプションを移転するには、プロセスを完了するためにダウンタイムが必要です。

次の図では、サブスクリプションを別のディレクトリに移転するときに従う必要がある基本的な手順を示します。

1. 移転を準備する

1. Azure サブスクリプションの課金所有権を別のアカウントに譲渡する

1. ロールの割り当て、カスタム ロール、マネージド ID などのリソースをターゲット ディレクトリで再作成する

    ![サブスクリプションの移転の図](./media/transfer-subscription/transfer-subscription.png)

### <a name="deciding-whether-to-transfer-a-subscription-to-a-different-directory"></a>サブスクリプションを別のディレクトリに移転するかどうかを決定する

サブスクリプションを移転する理由には、次のようなものがあります。

- 会社の合併や買収のため、取得したサブスクリプションをプライマリ Azure AD ディレクトリで管理する必要があります。
- 組織内のだれかが作成したサブスクリプションの管理を、特定の Azure AD ディレクトリに統合する必要があります。
- 特定のサブスクリプション ID または URL に依存するアプリケーションがあり、アプリケーションの構成やコードを簡単に変更することができません。
- ビジネスの一部が別の会社に分割され、一部のリソースを別の Azure AD ディレクトリに移動する必要があります。
- セキュリティを分離するため、リソースの一部を別の Azure AD ディレクトリで管理する必要があります。

サブスクリプションを移転するには、プロセスを完了するためにダウンタイムが必要です。 シナリオによっては、リソースを再作成し、ターゲット ディレクトリとサブスクリプションにデータをコピーする方がよい場合があります。

### <a name="understand-the-impact-of-transferring-a-subscription"></a>サブスクリプションの移転による影響を把握する

いくつかの Azure リソースは、サブスクリプションまたはディレクトリに依存しています。 次の表では、状況に応じたサブスクリプションの移転による既知の影響を示します。 この記事の手順を実行することで、サブスクリプションの移転前に存在していたリソースの一部を再作成できます。

> [!IMPORTANT]
> このセクションでは、サブスクリプションに依存する既知の Azure サービスまたはリソースの一覧を示します。 Azure のリソースの種類は絶えず進化しているため、ここに記載されていない追加の依存関係で、環境の破壊的変更の原因になるものがある可能性があります。 

| サービスまたはリソース | 影響を受ける | 復旧可能 | 影響を受けるかどうか? | 実行可能な操作 |
| --------- | --------- | --------- | --------- | --------- |
| ロールの割り当て | はい | はい | [ロールの割り当てを一覧表示する](#save-all-role-assignments) | すべてのロールの割り当ては完全に削除されます。 ユーザー、グループ、サービス プリンシパルを、ターゲット ディレクトリ内の対応するオブジェクトにマップする必要があります。 ロールの割り当てを再作成する必要があります。 |
| カスタム ロール | はい | はい | [カスタム ロールを一覧表示する](#save-custom-roles) | すべてのカスタム ロールは完全に削除されます。 カスタム ロールとロールの割り当てを再作成する必要があります。 |
| システム割り当てのマネージド ID | はい | はい | [マネージド ID を一覧表示する](#list-role-assignments-for-managed-identities) | マネージド ID を無効にして、再度有効にする必要があります。 ロールの割り当てを再作成する必要があります。 |
| ユーザー割り当て済みマネージド ID | はい | はい | [マネージド ID を一覧表示する](#list-role-assignments-for-managed-identities) | マネージド ID を削除して再作成し、適切なリソースにアタッチする必要があります。 ロールの割り当てを再作成する必要があります。 |
| Azure Key Vault | はい | はい | [Key Vault アクセス ポリシーを一覧表示する](#list-other-known-resources) | キー コンテナーに関連付けられているテナント ID を更新する必要があります。 アクセス ポリシーを削除して、新しく追加する必要があります。 |
| Azure SQL データベースと Azure AD 認証 | はい | いいえ | [Azure SQL データベースと Azure AD 認証を確認する](#list-other-known-resources) |  |  |
| Azure Storage と Azure Data Lake Storage Gen2 | はい | はい |  | すべての ACL を再作成する必要があります。 |
| Azure Data Lake Storage Gen1 | はい |  |  | すべての ACL を再作成する必要があります。 |
| Azure Files | はい | はい |  | すべての ACL を再作成する必要があります。 |
| Azure File Sync | はい | はい |  |  |
| Azure Managed Disks | はい | 該当なし |  |  |
| Kubernetes 向け Azure Container Service | はい | はい |  |  |
| Azure Active Directory Domain Services | はい | いいえ |  |  |
| アプリの登録 | はい | はい |  |  |

ストレージ アカウントや SQL データベースなどのリソースに対して保存時の暗号化を使用していて、移転されるのと同じサブスクリプションに含まれていないキー コンテナーへの依存関係がある場合、復旧不可能なシナリオが発生する可能性があります。 このような状況が発生した場合は、別のキー コンテナーを使用するか、ユーザーが管理するキーを一時的に無効にして、この復旧不可能なシナリオを回避する必要があります。

## <a name="prerequisites"></a>前提条件

これらの手順を完了するには、以下が必要です。

- [Azure Cloud Shell の Bash](/azure/cloud-shell/overview) または [Azure CLI](https://docs.microsoft.com/cli/azure)
- ソース ディレクトリ内の移転するサブスクリプションのアカウント管理者
- ターゲット ディレクトリの[所有者](built-in-roles.md#owner) ロール

## <a name="step-1-prepare-for-the-transfer"></a>手順 1:移転を準備する

### <a name="sign-in-to-source-directory"></a>ソース ディレクトリにサインインする

1. 管理者として Azure にサインインします。

1. [az account list](/cli/azure/account#az-account-list) コマンドを使用して、サブスクリプションの一覧を取得します。

    ```azurecli
    az account list --output table
    ```

1. [az account set](https://docs.microsoft.com/cli/azure/account#az-account-set) を使用して、移転するアクティブなサブスクリプションを設定します。

    ```azurecli
    az account set --subscription "Marketing"
    ```

### <a name="install-the-resource-graph-extension"></a>resource-graph 拡張機能をインストールする

 resource-graph 拡張機能を使用すると、[az graph](https://docs.microsoft.com/cli/azure/ext/resource-graph/graph) コマンドを使用して、Azure Resource Manager によって管理されているリソースを照会できます。 このコマンドは、後の手順で使用します。

1. [az extension list](https://docs.microsoft.com/cli/azure/extension#az-extension-list) を使用して、*resource-graph* 拡張機能がインストールされているかどうかを確認します。

    ```azurecli
    az extension list
    ```

1. ない場合、*resource-graph* 拡張機能をインストールします。

    ```azurecli
    az extension add --name resource-graph
    ```

### <a name="save-all-role-assignments"></a>すべてのロールの割り当てを保存する

1. [az role assignment list](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-list) を使用して、すべてのロールの割り当ての一覧を表示します (継承されたロールの割り当てを含む)。

    一覧を簡単に確認できるように、出力を JSON、TSV、またはテーブルとしてエクスポートできます。 詳細については、「[Azure RBAC と Azure CLI を使用してロールの割り当てを一覧表示する](role-assignments-list-cli.md)」を参照してください。

    ```azurecli
    az role assignment list --all --include-inherited --output json > roleassignments.json
    az role assignment list --all --include-inherited --output tsv > roleassignments.tsv
    az role assignment list --all --include-inherited --output table > roleassignments.txt
    ```

1. ロールの割り当ての一覧を保存します。

    サブスクリプションを移転すると、すべてのロールの割り当てが**完全に**削除されます。そのため、コピーを保存しておくことが重要です。

1. ロールの割り当ての一覧を確認します。 ターゲット ディレクトリに必要のないロールの割り当てが存在する可能性があります。

### <a name="save-custom-roles"></a>カスタムロールを保存する

1. カスタム ロールの一覧を表示するには、[az role definition list](https://docs.microsoft.com/cli/azure/role/definition#az-role-definition-list) を使用します。 詳細については、「[Azure CLIを使用して Azure カスタム ロールを作成または更新する](custom-roles-cli.md)」を参照してください。

    ```azurecli
    az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
    ```

1. ターゲット ディレクトリに必要な各カスタム ロールを、別の JSON ファイルとして保存します。

    ```azurecli
    az role definition list --name <custom_role_name> > customrolename.json
    ```

1. カスタム ロール ファイルのコピーを作成します。

1. 次の形式を使用するように各コピーを変更します。

    後でこれらのファイルを使用して、ターゲット ディレクトリにカスタム ロールを再作成します。

    ```json
    {
      "Name": "",
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": []
    }
    ```

### <a name="determine-user-group-and-service-principal-mappings"></a>ユーザー、グループ、サービス プリンシパルのマッピングを決定する

1. ロールの割り当ての一覧に基づいて、ターゲット ディレクトリでマップするユーザー、グループ、サービス プリンシパルを決定します。

    各ロールの割り当ての `principalType` プロパティを参照することで、プリンシパルの種類を特定できます。

1. 必要に応じて、ターゲット ディレクトリに、必要なユーザー、グループ、またはサービス プリンシパルを作成します。

### <a name="list-role-assignments-for-managed-identities"></a>マネージド ID のロールの割り当ての一覧を表示する

サブスクリプションを別のディレクトリに移転したとき、マネージド ID は更新されません。 その結果、既存のシステム割り当てマネージド ID やユーザー割り当てマネージド ID は破損します。 移転後、システム割り当てマネージド ID を再び有効にすることができます。 ユーザー割り当てマネージド ID の場合は、ターゲット ディレクトリで再作成してアタッチする必要があります。

1. [マネージド ID がサポートされている Azure サービスの一覧](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)を確認して、マネージド ID を使用している可能性がある場所を記録します。

1. システム割り当てとユーザー割り当てのマネージド ID の一覧を表示するには、[az ad sp list](/cli/azure/identity?view=azure-cli-latest#az-identity-list) を使用します。

    ```azurecli
    az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
    ```

1. マネージド ID の一覧で、システム割り当てのものとユーザー割り当てのものを特定します。 次の条件を使用して、種類を特定できます。

    | 条件 | マネージド ID の種類 |
    | --- | --- |
    | `alternativeNames` プロパティに `isExplicit=False` が含まれる | システム割り当て |
    | `alternativeNames` プロパティに `isExplicit` が含まれない | システム割り当て |
    | `alternativeNames` プロパティに `isExplicit=True` が含まれる | ユーザー割り当て |

    また、[az identity list](https://docs.microsoft.com/cli/azure/identity#az-identity-list) コマンドを使用して、ユーザー割り当てマネージド ID だけの一覧を表示することもできます。 詳細については、「[Azure CLI を使用してユーザー割り当てマネージド ID を作成、一覧表示、または削除する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)」を参照してください。

    ```azurecli
    az identity list
    ```

1. マネージド ID の `objectId` 値の一覧を取得します。

1. ロールの割り当ての一覧を検索して、マネージド ID に対するロールの割り当てがあるかどうかを確認します。

### <a name="list-key-vaults"></a>Key Vault のリスト

キー コンテナーを作成すると、そのキー コンテナーは、それが作成されたサブスクリプションの既定の Azure Active Directory テナント ID に自動的に関連付けられます。 また、すべてのアクセス ポリシー エントリがこのテナント ID に関連付けられます。 詳細については、「[Azure Key Vault を別のサブスクリプションに移動する](../key-vault/general/move-subscription.md)」を参照してください。

> [!WARNING]
> ストレージ アカウントや SQL データベースなどのリソースに対して保存時の暗号化を使用していて、移転されるのと同じサブスクリプションに含まれていないキー コンテナーへの依存関係がある場合、復旧不可能なシナリオが発生する可能性があります。 このような状況が発生した場合は、別のキー コンテナーを使用するか、ユーザーが管理するキーを一時的に無効にして、この復旧不可能なシナリオを回避する必要があります。

- キー コンテナーがある場合は、[az keyvault show](https://docs.microsoft.com/cli/azure/keyvault#az-keyvault-show) を使用してアクセス ポリシーの一覧を表示します。 詳細については、「[アクセス制御ポリシーを使用して Key Vault の認証を提供する](../key-vault/key-vault-group-permissions-for-apps.md)」を参照してください。

    ```azurecli
    az keyvault show --name MyKeyVault
    ```

### <a name="list-azure-sql-databases-with-azure-ad-authentication"></a>Azure SQL データベースと Azure AD 認証の一覧を表示する

- Azure SQL データベースと Azure AD 認証を使用しているかどうかを確認するには、[az sql server ad-admin list](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) と [az graph](https://docs.microsoft.com/cli/azure/ext/resource-graph/graph) 拡張機能を使用します。 詳細については、「[SQL による Azure Active Directory 認証の構成と管理](../sql-database/sql-database-aad-authentication-configure.md)」を参照してください。

    ```azurecli
    az sql server ad-admin list --ids $(az graph query -q 'resources | where type == "microsoft.sql/servers" | project id' -o tsv | cut -f1)
    ```

### <a name="list-acls"></a>ACL の一覧を表示する

1. Azure Data Lake Storage Gen1 を使用している場合は、Azure portal または PowerShell を使用して、ファイルに適用されている ACL の一覧を表示します。

1. Azure Data Lake Storage Gen2 を使用している場合は、Azure portal または PowerShell を使用して、ファイルに適用されている ACL の一覧を表示します。

1. Azure Files を使用している場合は、ファイルに適用されている ACL の一覧を表示します。

### <a name="list-other-known-resources"></a>他の既知のリソースの一覧を表示する

1. [az account show](https://docs.microsoft.com/cli/azure/account#az-account-show) を使用して、サブスクリプション ID を取得します。

    ```azurecli
    subscriptionId=$(az account show --query id | sed -e 's/^"//' -e 's/"$//')
    ```

1. [az graph](https://docs.microsoft.com/cli/azure/ext/resource-graph/graph) 拡張機能を使用して、Azure AD ディレクトリの既知の依存関係がある他の Azure リソースの一覧を表示します。

    ```azurecli
    az graph query -q \
    'resources | where type != "microsoft.azureactivedirectory/b2cdirectories" | where  identity <> "" or properties.tenantId <> "" or properties.encryptionSettingsCollection.enabled == true | project name, type, kind, identity, tenantId, properties.tenantId' \
    --subscriptions $subscriptionId --output table
    ```

## <a name="step-2-transfer-billing-ownership"></a>手順 2:課金所有権を移転する

このステップでは、サブスクリプションの課金所有権を、ソース ディレクトリからターゲット ディレクトリに移転します。

> [!WARNING]
> サブスクリプションの課金所有権を移転すると、ソース ディレクトリ内のすべてのロールの割り当てが**完全に**削除されて復元できなくなります。 サブスクリプションの課金所有権を移転すると、元に戻すことはできません。 このステップを実行する前に、これまでの手順を完了していることを確認してください。

1. 「[Azure サブスクリプションの課金所有権を別のアカウントに譲渡する](../cost-management-billing/manage/billing-subscription-transfer.md)」の手順に従ってください。 サブスクリプションを別の Azure AD ディレクトリに移転するには、 **[サブスクリプション Azure AD テナント]** チェック ボックスをオンにする必要があります。

1. 所有権の移転が完了したら、この記事に戻り、ターゲット ディレクトリにリソースを再作成します。

## <a name="step-3-re-create-resources"></a>手順 3:リソースを再作成する

### <a name="sign-in-to-target-directory"></a>ターゲット ディレクトリにサインインする

1. ターゲット ディレクトリで、移転要求を受け入れたユーザーとしてサインインします。

    移転要求を受け入れた新しいアカウントのユーザーだけが、リソースにアクセスして管理できます。

1. [az account list](https://docs.microsoft.com/cli/azure/account#az-account-list) コマンドを使用して、サブスクリプションの一覧を取得します。

    ```azurecli
    az account list --output table
    ```

1. [az account set](https://docs.microsoft.com/cli/azure/account#az-account-set) を使用して、使用するアクティブなサブスクリプションを設定します。

    ```azurecli
    az account set --subscription "Contoso"
    ```

### <a name="create-custom-roles"></a>カスタム ロールを作成する
        
- [az role definition create](https://docs.microsoft.com/cli/azure/role/definition#az-role-definition-create) を使用して、前に作成したファイルから各カスタム ロールを作成します。 詳細については、「[Azure CLIを使用して Azure カスタム ロールを作成または更新する](custom-roles-cli.md)」を参照してください。

    ```azurecli
    az role definition create --role-definition <role_definition>
    ```

### <a name="create-role-assignments"></a>ロールの割り当ての作成

- [az role assignment create](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-create) を使用して、ユーザー、グループ、サービス プリンシパルに対するロールの割り当てを作成します。 詳細については、「[AAzure RBAC と Azure CLI を使用してロールの割り当てを追加または削除する](role-assignments-cli.md)」を参照してください。

    ```azurecli
    az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
    ```

### <a name="update-system-assigned-managed-identities"></a>システム割り当てのマネージド ID を更新する

1. システム割り当てのマネージドID を無効にした後、再び有効にします。

    | Azure サービス | 詳細情報 | 
    | --- | --- |
    | 仮想マシン | [Azure CLI を使用して Azure VM 上に Azure リソースのマネージド ID を構成する](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#system-assigned-managed-identity) |
    | 仮想マシン スケール セット | [Azure CLI を使用して仮想マシン スケール セットで Azure リソースのマネージド ID を構成する](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#system-assigned-managed-identity) |
    | その他のサービス | [Azure リソースのマネージド ID をサポートするサービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) |

1. [az role assignment create](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-create) を使用して、システム割り当てのマネージド ID に対するロールの割り当てを作成します。 詳細については、「[Azure CLI を使用して、リソースにマネージド ID アクセスを割り当てる](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)」を参照してください。

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-user-assigned-managed-identities"></a>ユーザー割り当てのマネージド ID を更新する

1. ユーザー割り当てのマネージド ID を削除し、再作成して、アタッチします。

    | Azure サービス | 詳細情報 | 
    | --- | --- |
    | 仮想マシン | [Azure CLI を使用して Azure VM 上に Azure リソースのマネージド ID を構成する](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) |
    | 仮想マシン スケール セット | [Azure CLI を使用して仮想マシン スケール セットで Azure リソースのマネージド ID を構成する](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#user-assigned-managed-identity) |
    | その他のサービス | [Azure リソースのマネージド ID をサポートするサービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)<br/>[Azure CLI を使用してユーザー割り当てマネージド ID を作成、一覧表示、または削除する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |

1. [az role assignment create](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-create) を使用して、ユーザー割り当てのマネージド ID に対するロールの割り当てを作成します。 詳細については、「[Azure CLI を使用して、リソースにマネージド ID アクセスを割り当てる](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)」を参照してください。

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-key-vaults"></a>キー コンテナーを更新する

ここでは、キー コンテナーを更新する基本的な手順について説明します。 詳細については、「[Azure Key Vault を別のサブスクリプションに移動する](../key-vault/general/move-subscription.md)」を参照してください。

1. サブスクリプションで既存のすべてのキー コンテナーに関連付けられているテナント ID を、ターゲット ディレクトリに更新します。

1. すべての既存のアクセス ポリシー エントリを削除する。

1. ターゲット ディレクトリに関連付けられた新しいアクセス ポリシー エントリを追加します。

### <a name="update-acls"></a>ACL を更新する

1. Azure Data Lake Storage Gen1 を使用している場合は、適切な ACL を割り当てます。 詳細については、「[Securing data stored in Azure Data Lake Storage Gen1 (Azure Data Lake Storage Gen1 に格納されているデータのセキュリティ保護)](../data-lake-store/data-lake-store-secure-data.md)」をご覧ください。

1. Azure Data Lake Storage Gen2 を使用している場合は、適切な ACL を割り当てます。 詳細については、[Azure Data Lake Storage Gen2 でのアクセス制御](../storage/blobs/data-lake-storage-access-control.md)に関するページを参照してください。

1. Azure Files を使用している場合は、適切な ACL を割り当てます。

### <a name="review-other-security-methods"></a>他のセキュリティ メソッドを確認する

ロールの割り当ては移転の間に削除されますが、元の所有者アカウントのユーザーが、次のような他のセキュリティ メソッドを使用して、引き続きサブスクリプションにアクセスできる場合があります。

- Storage などのサービス用のアクセス キー。
- サブスクリプションのリソースに対する管理者アクセス権をユーザーに付与する[管理証明書](../cloud-services/cloud-services-certs-create.md)。
- Azure Virtual Machines などのサービス用のリモート アクセス資格情報。

ソース ディレクトリのユーザーがターゲット ディレクトリにアクセスできないように、アクセス権を削除する場合は、すべての資格情報のローテーションを検討する必要があります。 資格情報が更新されるまで、移転後もユーザーは引き続きアクセスできます。

1. ストレージ アカウントのアクセス キーをローテーションします。 詳細については、「[ストレージ アカウントのアクセス キーの管理](../storage/common/storage-account-keys-manage.md)」を参照してください。

1. Azure SQL Database や Azure Service Bus メッセージングなどの他のサービスにアクセス キーを使用している場合は、アクセス キーをローテーションします。

1. シークレットを使用しているリソースについては、リソースの設定を開き、シークレットを更新します。

1. 証明書を使用しているリソースについては、証明書を更新します。

## <a name="next-steps"></a>次のステップ

- [Azure サブスクリプションの課金所有権を別のアカウントに譲渡する](../cost-management-billing/manage/billing-subscription-transfer.md)
- [サブスクライバーと CSP の間で Azure サブスクリプションを譲渡する](../cost-management-billing/manage/transfer-subscriptions-subscribers-csp.md)
- [Azure サブスクリプションを Azure Active Directory テナントに関連付けるまたは追加する](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
