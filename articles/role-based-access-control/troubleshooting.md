---
title: Azure RBAC のトラブルシューティング
description: Azure ロールベースのアクセス制御 (Azure RBAC) に関する問題を解決します。
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1, devx-track-azurecli
ms.openlocfilehash: d77468619fcd67887273b2fbd452b37add1e19b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555883"
---
# <a name="troubleshoot-azure-rbac"></a>Azure RBAC のトラブルシューティング

この記事では、ロールを使用するときに予想されることをユーザーが理解し、アクセスの問題を解決できるように、Azure ロールベースのアクセス制御 (Azure RBAC) に関する一般的な質問にいくつか回答します。

## <a name="azure-role-assignments-limit"></a>Azure でのロールの割り当て制限

Azure では、サブスクリプションあたり最大 **2,000** 個のロールの割り当てをサポートしています。 この制限には、サブスクリプション、リソース グループ、およびリソースのスコープでのロールの割り当てが含まれます。 エラー メッセージ "ロールの割り当てはこれ以上作成できません (コード: RoleAssignmentLimitExceeded)" が、ロールを割り当てようとすると発生する場合は、サブスクリプションのロールの割り当て数を減らしてみます。

> [!NOTE]
> **2,000** のサブスクリプション当たりのロール割り当ての制限は固定されており、増やすことはできません。

この制限に近づいている場合は、次の方法でロールの割り当ての数を減らすことができます。

- ユーザーをグループに追加し、ロールをユーザーではなくグループに割り当てます。 
- 複数の組み込みロールをカスタム ロールと結合します。 
- サブスクリプションまたは管理グループなど、上位のスコープで共通のロールの割り当てを行います。
- Azure AD Premium P2 を使用している場合は、ロールを永続的に割り当てるのではなく、[Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) で割り当てます。 
- 追加のサブスクリプションを追加します。 

ロールの割り当ての数を取得するには、Azure portal の [アクセス制御 (IAM) ページのグラフを](role-assignments-list-portal.md#list-number-of-role-assignments)を表示します。 また、次の Azure PowerShell コマンドを使用することもできます。

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Azure のロールの割り当てに関する問題

- **[追加]**  >  **[ロール割り当ての追加]** オプションが無効になっているため、または "オブジェクト ID のクライアントは、アクションの実行を承認されていません" というアクセス許可エラーが発生するために、Azure portal の **[アクセス制御 (IAM)]** でロールを割り当てることができない場合は、ロールを割り当てようとしているスコープで `Microsoft.Authorization/roleAssignments/write` のアクセス許可を持っている [所有者](built-in-roles.md#owner)や [ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)などのロールを割り当てられているユーザーで、現在サインインしていることを確認してください。
- サービス プリンシパルを使用してロールを割り当てると、"この操作を完了するのに十分な特権がありません" というエラーが表示されることがあります。 たとえば、所有者ロールが割り当てられたサービス プリンシパルがあり、Azure CLI を使用して、次のロールの割り当てをサービスプリンシパルとして作成しようとするとします。

    ```azurecli
    az login --service-principal --username "SPNid" --password "password" --tenant "tenantid"
    az role assignment create --assignee "userupn" --role "Contributor"  --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

    "この操作を完了するのに十分な特権がありません" というエラーが表示される場合は、Azure CLI が Azure AD で担当者 ID を参照しようとしていて、サービス プリンシパルが既定で Azure AD を読み取ることができないためである可能性があります。

    このエラーを解決する可能性がある 2 つの方法があります。 最初の方法は、ディレクトリ内のデータを読み取ることができるように、[Directory Readers](../active-directory/roles/permissions-reference.md#directory-readers) ロールをサービス プリンシパルに割り当てることです。

    このエラーを解決する 2 番目の方法は、`--assignee` ではなく `--assignee-object-id` パラメーターを使用して、ロールの割り当てを作成することです。 `--assignee-object-id` を使用すると、Azure CLI で Azure AD 検索がスキップされます。 ロールを割り当てるユーザー、グループ、またはアプリケーションのオブジェクト ID を取得する必要があります。 詳細については、「[Azure CLI を使用して Azure ロールを割り当てる](role-assignments-cli.md#assign-a-role-for-a-new-service-principal-at-a-resource-group-scope)」を参照してください。

    ```azurecli
    az role assignment create --assignee-object-id 11111111-1111-1111-1111-111111111111  --role "Contributor" --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```
- サブスクリプションの最後の所有者ロール割り当てを削除しようとすると、"最後の RBAC 管理者割り当てを削除できません" エラーが表示されることがあります。 サブスクリプションの最後の所有者ロールの割り当ては、サブスクリプションの孤立化を回避するため、削除できません。 サブスクリプションを取り消す場合、「[Azure サブスクリプションの取り消し](../cost-management-billing/manage/cancel-azure-subscription.md)」を参照してください。

## <a name="problems-with-custom-roles"></a>カスタム ロールに関する問題

- カスタム ロールを作成する手順が必要な場合は、[Azure portal](custom-roles-portal.md) (現在はプレビュー段階)、[Azure PowerShell](tutorial-custom-role-powershell.md)、[Azure CLI](tutorial-custom-role-cli.md) を使用して、カスタム ロールのチュートリアルを参照してください。
- 既存のカスタム ロールを更新できない場合は、現在サインインしているユーザーに `Microsoft.Authorization/roleDefinition/write` アクセス許可を持つロール ([所有者](built-in-roles.md#owner)や[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)など) が割り当てられていることを確認します。
- カスタム ロールを削除することができず、エラー メッセージ "ロールを参照している既存のロールの割り当てがあります (コード: RoleDefinitionHasAssignments)" が表示される場合は、カスタム ロールを使用しているロールの割り当てがまだ存在します。 それらのロール割り当てを削除してから、もう一度カスタム ロールを削除してみてください。
- エラー メッセージ "ロールの定義の制限を超えました。 ロールの定義をこれ以上作成することはできません (コード: RoleDefinitionLimitExceeded)" が、新しいカスタム ロールを作成しようとすると表示される場合は、使用されていないすべてのカスタム ロールを削除します。 Azure では、ディレクトリあたり最大 **5,000** 個のカスタム ロールがサポートされます。 (Azure Germany と Azure China 21Vianet の場合、制限は 2,000 カスタム ロールです)。
- カスタム ロールを更新しようとすると、"このクライアントには、範囲 '/subscriptions/{subscriptionid}' に対してアクション 'Microsoft.Authorization/roleDefinitions/write' を実行する権限がありますが、リンクされているサブスクリプションが見つかりません" のようなエラーが発生する場合は、ディレクトリで 1 つまたは複数の[割り当て可能なスコープ](role-definitions.md#assignablescopes)が削除されているかどうかを確認してください。 スコープが削除された場合は、現時点で使用可能なセルフ サービス ソリューションがないため、サポート チケットを作成します。

## <a name="custom-roles-and-management-groups"></a>カスタム ロールと管理グループ

- カスタム ロールの `AssignableScopes` に定義できる管理グループは 1 つだけです。 `AssignableScopes` への管理グループの追加は、現在プレビューの段階です。
- `DataActions` が含まれるカスタム ロールを管理グループのスコープで割り当てることはできません。
- ロールの定義の割り当て可能なスコープに管理グループが存在するかどうかは、Azure Resource Manager では確認されません。
- カスタム ロールと管理グループについて詳しくは、「[Azure 管理グループでリソースを整理する](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)」をご覧ください。

## <a name="transferring-a-subscription-to-a-different-directory"></a>サブスクリプションを別のディレクトリに譲渡する

- サブスクリプションを別の Azure AD ディレクトリに譲渡する手順については、「[Azure サブスクリプションを別の Azure AD ディレクトリに移転する](transfer-subscription.md)」をご覧ください。
- 別の Azure AD ディレクトリにサブスクリプションを譲渡する場合、すべてのロールの割り当てがソース Azure AD ディレクトリから **完全** に削除され、ターゲット Azure AD ディレクトリに移行されることはありません。 ターゲット ディレクトリでロールの割り当てを再作成する必要があります。 また、Azure リソースのマネージド ID を手動で再作成する必要もあります。 詳細については、[マネージド ID に関する FAQ と既知の問題](../active-directory/managed-identities-azure-resources/known-issues.md)に関するページを参照してください。
- Azure AD グローバル管理者であり、ディレクトリ間で譲渡された後のサブスクリプションにアクセスできない場合は、 **[Azure リソースのアクセス管理]** トグルを使用して、一時的に [アクセス権を昇格](elevate-access-global-admin.md)させて、サブスクリプションにアクセスします。

## <a name="issues-with-service-admins-or-co-admins"></a>サービス管理者または共同管理者に関する問題

- サービス管理者または共同管理者に問題が発生した場合は、「[Azure サブスクリプション管理者を追加または変更する](../cost-management-billing/manage/add-change-subscription-administrator.md)」および「[従来のサブスクリプション管理者ロール、Azure RBAC ロール、および Azure AD ロール](rbac-and-directory-admin-roles.md)」を参照してください。

## <a name="access-denied-or-permission-errors"></a>アクセス拒否またはアクセス許可エラー

- アクセス許可エラー "オブジェクト ID のクライアントは、スコープに対するアクションの実行を承認されていません (コード: AuthorizationFailed)" が、リソースを作成しようとすると発生する場合は、選択したスコープでリソースへの書き込みアクセス許可を持つロールを割り当てられたユーザーで、現在サインインしていることを確認します。 たとえば、リソース グループ内の仮想マシンを管理するには、そのリソース グループ (または親スコープ) に対する[仮想マシン共同作成者](built-in-roles.md#virtual-machine-contributor)ロールを持っている必要があります。 各組み込みロールに対するアクセス許可の一覧については、「[Azure 組み込みロール](built-in-roles.md)」を参照してください。
- サポート チケットを作成または更新しようとすると "サポート要求を作成するためのアクセス許可がありません" というアクセス許可エラーが発生する場合は、現在サインインしているユーザーに、`Microsoft.Support/supportTickets/write` アクセス許可を持つロール ([サポート リクエスト共同作成者](built-in-roles.md#support-request-contributor)など) が割り当てられていることを確認します。

## <a name="move-resources-with-role-assignments"></a>ロールが割り当てられているリソースを移動する

Azure ロールがリソース (または子リソース) に直接割り当てられており、そのリソースを移動する場合、ロールの割り当ては移動されず、孤立します。 移動した後は、ロールの割り当てを再作成する必要があります。 最終的に、孤立したロールの割り当ては自動的に削除されますが、ベスト プラクティスとして、リソースを移動する前にロールの割り当てを削除しておくことをお勧めします。

リソースの移動方法について詳しくは、「[リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)」をご覧ください。

## <a name="role-assignments-with-identity-not-found"></a>ID が見つからないロールの割り当て

Azure portal のロールの割り当ての一覧で、セキュリティ プリンシパル (ユーザー、グループ、サービス プリンシパル、またはマネージド ID) が、**不明** な種類の **見つからない ID** として表示されている場合があります。

![Azure のロールの割り当ての一覧に ID が見つからない](./media/troubleshooting/unknown-security-principal.png)

ID は、次の 2 つの理由で見つからない可能性があります。

- ロールの割り当てを作成するときに、ユーザーを最近招待した
- ロールの割り当てを持つセキュリティ プリンシパルを削除した

ロールの割り当てを作成するときに最近ユーザーを招待した場合、このセキュリティ プリンシパルはまだリージョン間のレプリケーション プロセス中である可能性があります。 その場合は、しばらく待ってから、ロールの割り当ての一覧を更新してください。

ただし、このセキュリティ プリンシパルが最近招待されたユーザーでない場合は、削除されたセキュリティ プリンシパルである可能性があります。 ロールをセキュリティ プリンシパに割り当てた後、最初にロールの割り当てを削除せずにそのセキュリティ プリンシパルを削除した場合、そのセキュリティ プリンシパルは **ID が見つからない** **[不明]** な種類として表示されます。

Azure PowerShell を使用してこのロールの割り当てを一覧表示すると、空の `DisplayName` と **Unknown** に設定された `ObjectType` が表示される場合があります。 たとえば、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) では、次の出力のようなロールの割り当てが返されます。

```
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

同様に、Azure CLI を使用してこのロールの割り当てを一覧表示すると、空の `principalName` が表示される場合があります。 たとえば、[az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) では、次の出力のようなロールの割り当てが返されます。

```
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

セキュリティ プリンシパルが削除されているこれらのロールの割り当てを残しておくのは問題ではありません。 必要であれば、他のロールの割り当てと同様の手順を使用して、これらのロールの割り当てを削除できます。 ロールの割り当てを削除する方法の詳細については、「[Azure ロールの割り当ての削除](role-assignments-remove.md)」を参照してください。

PowerShell では、オブジェクト ID とロール定義名を使ってロールの割り当てを削除しようとし、複数のロールの割り当てがパラメーターに一致する場合、次のエラー メッセージを受け取ります。"The provided information does not map to a role assignment" (指定された情報は、ロールの割り当てにマップされていません)。 次の出力は、エラー メッセージの例を示しています。

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

このエラー メッセージを受け取る場合は、`-Scope` パラメーターまたは `-ResourceGroupName` パラメーターも指定するようにしてください。

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>ロールの割り当ての変更が検出されない

Azure Resource Manager は、パフォーマンスを高めるために構成やデータをキャッシュすることがあります。 ロールを割り当てたり、ロールの割り当てを削除したりする場合、変更が有効になるまでに最大 30 分かかることがあります。 Azure portal、Azure PowerShell、Azure CLI のいずれかを使用している場合は、一度サインアウトしてからサインインすることで、ロールの割り当てを強制的に最新の情報に更新し、その変更を有効にすることができます。 REST API 呼び出しでロールの割り当てを変更する場合は、アクセス トークンを更新することによって、最新の情報への更新を強制することができます。

管理グループのスコープでロールの割り当てを追加または削除し、ロールに `DataActions` がある場合、データ プレーンへのアクセスが数時間更新されない可能性があります。 これは、管理グループのスコープとデータ プレーンにのみ適用されます。

## <a name="web-app-features-that-require-write-access"></a>書き込みアクセス権限を必要とする Web アプリ機能

1 つの Web アプリに対する読み取り専用アクセスをユーザーに付与する場合、予期しない機能が無効になることがあります。 以下の管理機能には、Web アプリに対する **書き込み** アクセス権 (共同作成者または所有者) が必要なので、読み取り専用のシナリオでは利用できません。

* コマンド (開始や停止など)
* 一般的な構成、スケール設定、バックアップ設定、監視設定などの設定の変更。
* 発行資格情報およびその他の機密情報 (アプリケーション設定や接続文字列など) へのアクセス。
* ストリーミング ログ
* リソース ログの構成
* コンソール (コマンド プロンプト)
* アクティブな最新のデプロイ (ローカル Git の継続的デプロイの場合)
* 所要時間の見積もり
* Web テスト
* 仮想ネットワーク (書き込みアクセス権を持つユーザーが仮想ネットワークを事前に構成している場合のみ閲覧者が参照できる)。

これらのタイルのいずれにもアクセスできない場合、管理者に問い合わせて Web アプリに対する共同作成者アクセス権を得る必要があります。

## <a name="web-app-resources-that-require-write-access"></a>書き込みアクセス権限を必要とする Web アプリ リソース

相互作用する数種類のリソースがあると、Web アプリは複雑になります。 複数の Web サイトから成る代表的なリソース グループを以下に示します。

![Web アプリ リソース グループ](./media/troubleshooting/website-resource-model.png)

結果として、Web アプリのみに対するアクセス権を付与すると、Azure Portal の Web サイト ブレード上の多数の機能が使用できなくなります。

以下の項目には、Web サイトに対応する **App Service プランへ** の **書き込み** アクセス権が必要です。  

* Web アプリの価格レベル (Free または Standard) の表示  
* スケールの構成 (インスタンスの数、仮想マシンのサイズ、自動スケールの設定)  
* クォータ (ストレージ、帯域幅、CPU)  

以下の項目には、Web サイトが含まれる **リソース グループ** 全体に対する **書き込み** アクセス権が必要です。  

* TLS/SSL 証明書とバインド (TLS/SSL 証明書は同じリソース グループや地理的な場所にあるサイト間で共有できるため)  
* アラート ルール  
* 自動スケールの設定  
* Application Insights コンポーネント  
* Web テスト  

## <a name="virtual-machine-features-that-require-write-access"></a>書き込みアクセス権限を必要とする仮想マシン機能

Web アプリと同様、仮想マシン ブレード上の機能にも、仮想マシンかリソース グループ内の他のリソースに対する書き込みアクセス権が必要なものがあります。

仮想マシンは、ドメイン名、仮想ネットワーク、ストレージ アカウント、アラート ルールなどのリソースと関連しています。

以下の項目には、**仮想マシン** に対する **書き込み** アクセス権が必要です。

* エンドポイント  
* IP アドレス  
* ディスク  
* 拡張機能  

以下には、**仮想マシン** と、その仮想マシンが含まれる **リソース グループ** (とドメイン名) の両方に対する **書き込み** アクセス権が必要です。  

* 可用性セット  
* 負荷分散セット  
* アラート ルール  

これらのタイルのいずれにもアクセスできない場合、管理者に問い合わせてリソース グループに対する共同作成者アクセス権を入手してください。

## <a name="azure-functions-and-write-access"></a>Azure Functions と書き込みアクセス権限

[Azure Functions](../azure-functions/functions-overview.md) の一部の機能では、書き込みアクセスが必要です。 たとえば、ユーザーに[閲覧者](built-in-roles.md#reader)ロールが割り当てられている場合、そのユーザーは関数アプリ内の関数を表示することができません。 ポータルには **(アクセスなし)** が表示されます。

![Function App のアクセスなし](./media/troubleshooting/functionapps-noaccess.png)

閲覧者は、 **[プラットフォーム機能]** タブをクリックし、 **[すべての設定]** をクリックすることで、関数アプリ (Web アプリに類似) に関連する一部の設定を表示できます。ただし、これらの設定を変更することはできません。 これらの機能にアクセスするには、[共同作成者](built-in-roles.md#contributor)ロールが必要です。

## <a name="next-steps"></a>次のステップ

- [ゲスト ユーザーのトラブルシューティング](role-assignments-external-users.md#troubleshoot)
- [Azure portal を使用して Azure ロールを割り当てる](role-assignments-portal.md)
- [Azure RBAC の変更のアクティビティ ログを表示する](change-history-report.md)
