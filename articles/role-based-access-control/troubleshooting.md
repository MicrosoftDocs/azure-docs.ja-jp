---
title: Azure リソースの RBAC のトラブルシューティング | Microsoft Docs
description: Azure リソースのロールベースのアクセス制御 (RBAC) に関する問題を解決します。
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 59ece9c37a563efba6329a30c06c1b596b1a5d57
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058153"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Azure リソースの RBAC のトラブルシューティング

この記事では、Azure portal でロールを使用するときに予想されることをユーザーが理解し、アクセスの問題を解決できるように、Azure リソースのロールベースのアクセス制御 (RBAC) に関する一般的な質問に回答します。

## <a name="problems-with-rbac-role-assignments"></a>RBAC ロールの割り当てに関する問題

- **[追加]**  >  **[ロール割り当ての追加]** オプションが無効になっているため、または "オブジェクト ID のクライアントは、アクションの実行を承認されていません" というアクセス許可エラーが発生するために、Azure portal の **[アクセス制御 (IAM)]** でロールの割り当てを追加できない場合は、ロールを割り当てようとしているスコープで `Microsoft.Authorization/roleAssignments/write` のアクセス許可を持っている[所有者](built-in-roles.md#owner)や[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)などのロールを割り当てられているユーザーで、現在サインインしていることを確認してください。
- エラー メッセージ "ロールの割り当てはこれ以上作成できません (コード: RoleAssignmentLimitExceeded)" が、ロールを割り当てようとすると発生する場合は、代わりにロールをグループに割り当てて、ロールの割り当て数を減らしてみます。 Azure では、サブスクリプションあたり最大 **2,000** 個のロールの割り当てをサポートしています。

## <a name="problems-with-custom-roles"></a>カスタム ロールに関する問題

- カスタム ロールを作成する方法の手順が必要な場合は、[Azure PowerShell](tutorial-custom-role-powershell.md) または [Azure CLI](tutorial-custom-role-cli.md) を使用するカスタム ロールのチュートリアルをご覧ください。
- 既存のカスタム ロールを更新できない場合は、現在サインインしているユーザーに `Microsoft.Authorization/roleDefinition/write` アクセス許可を持つロール ([所有者](built-in-roles.md#owner)や[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)など) が割り当てられていることを確認します。
- カスタム ロールを削除することができず、エラー メッセージ "ロールを参照している既存のロールの割り当てがあります (コード: RoleDefinitionHasAssignments)" が表示される場合は、カスタム ロールを使用しているロールの割り当てがまだ存在します。 それらのロール割り当てを削除してから、もう一度カスタム ロールを削除してみてください。
- エラー メッセージ "ロールの定義の制限を超えました。 ロールの定義をこれ以上作成することはできません (コード: RoleDefinitionLimitExceeded)" が、新しいカスタム ロールを作成しようとすると表示される場合は、使用されていないすべてのカスタム ロールを削除します。 Azure では、テナントあたり最大 **5,000** 個のカスタム ロールをサポートします (Azure Government、Azure Germany、Azure China 21Vianet などの特殊なクラウドの場合、カスタム ロールの上限は 2,000 個です)。
- カスタム ロールを更新しようとすると、"このクライアントには、範囲 '/subscriptions/{subscriptionid}' に対してアクション 'Microsoft.Authorization/roleDefinitions/write' を実行する権限がありますが、リンクされているサブスクリプションが見つかりません" のようなエラーが発生する場合は、テナントで 1 つまたは[複数の割り当て可能な範囲](role-definitions.md#assignablescopes)が削除さているかどうかを確認してください。 スコープが削除された場合は、現時点で使用可能なセルフ サービス ソリューションがないため、サポート チケットを作成します。

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>サブスクリプションがテナントをまたいで移動される際に RBAC を復旧します

- サブスクリプションを別の Azure AD テナントに譲渡する方法の手順が必要な場合は、「[Azure サブスクリプションの所有権を別のアカウントに譲渡する](../billing/billing-subscription-transfer.md)」を参照してください。
- 別の Azure AD テナントにサブスクリプションを譲渡する場合、すべてのロールの割り当てがソース Azure AD テナントから完全に削除され、ターゲット Azure AD テナントに移行されることはありません。 ターゲット テナントでロールの割り当てを再作成する必要があります。 また、Azure リソースのマネージド ID を手動で再作成する必要もあります。 詳細については、[マネージド ID に関する FAQ と既知の問題](../active-directory/managed-identities-azure-resources/known-issues.md)に関するページを参照してください。
- Azure AD グローバル管理者であり、テナント間で移動された後のサブスクリプションにアクセスできない場合は、 **[Azure リソースのアクセス管理]** トグルを使用して、一時的に[アクセス権を昇格](elevate-access-global-admin.md)させて、サブスクリプションにアクセスします。

## <a name="issues-with-service-admins-or-co-admins"></a>サービス管理者または共同管理者に関する問題

- サービス管理者または共同管理者に問題が発生した場合は、「[Add or change Azure subscription administrators](../billing/billing-add-change-azure-subscription-administrator.md)」(Azure サブスクリプション管理者を追加または変更する) および「[Classic subscription administrator roles, Azure RBAC roles, and Azure AD administrator roles](rbac-and-directory-admin-roles.md)」(クラシック サブスクリプション管理者ロール、Azure RBAC ロール、および Azure AD 管理者ロール) を参照してください。

## <a name="access-denied-or-permission-errors"></a>アクセス拒否またはアクセス許可エラー

- アクセス許可エラー "オブジェクト ID のクライアントは、スコープに対するアクションの実行を承認されていません (コード: AuthorizationFailed)" が、リソースを作成しようとすると発生する場合は、選択したスコープでリソースへの書き込みアクセス許可を持つロールを割り当てられたユーザーで、現在サインインしていることを確認します。 たとえば、リソース グループ内の仮想マシンを管理するには、そのリソース グループ (または親スコープ) に対する[仮想マシン共同作成者](built-in-roles.md#virtual-machine-contributor)ロールを持っている必要があります。 各組み込みロールに対するアクセス許可の一覧については、「[Azure リソースの組み込みロール](built-in-roles.md)」を参照してください。
- サポート チケットを作成または更新しようとすると "サポート要求を作成するためのアクセス許可がありません" というアクセス許可エラーが発生する場合は、現在サインインしているユーザーに、`Microsoft.Support/supportTickets/write` アクセス許可を持つロール ([サポート リクエスト共同作成者](built-in-roles.md#support-request-contributor)など) が割り当てられていることを確認します。

## <a name="role-assignments-without-a-security-principal"></a>セキュリティ プリンシパルのないロールの割り当て

Azure PowerShell を使ってロールの割り当てを一覧表示すると、空の `DisplayName` と、[Unknown]\(不明\) に設定された `ObjectType` を持つ割り当てが表示される場合があります。 たとえば、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) では、次のようなロールの割り当てが返されます。

```azurepowershell
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

同様に、Azure CLI を使ってロールの割り当てを一覧表示すると、空の `principalName` を持つ割り当てが表示される場合があります。 たとえば、[az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) では、次のようなロールの割り当てが返されます。

```azurecli
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

これらのロールの割り当ては、セキュリティ プリンシパル (ユーザー、グループ、サービス プリンシパル、または管理対象 ID) にロールを割り当てて、後でそのセキュリティ プリンシパルを削除したときに発生します。 これらのロールの割り当ては、Azure portal には表示されず、そのままにしておいても問題ありません。 ただし、必要であれば、これらのロールの割り当てを削除できます。

これらのロールの割り当てを削除するには、[Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) コマンドまたは [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete) コマンドを使用します。

PowerShell では、オブジェクト ID とロール定義名を使ってロールの割り当てを削除しようとし、複数のロールの割り当てがパラメーターに一致する場合、次のエラー メッセージを受け取ります。"The provided information does not map to a role assignment" (指定された情報は、ロールの割り当てにマップされていません)。 エラー メッセージの例を次に示します。

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

このエラー メッセージを受け取る場合は、`-Scope` パラメーターまたは `-ResourceGroupName` パラメーターも指定するようにしてください。

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="rbac-changes-are-not-being-detected"></a>RBAC の変更が検出されない

Azure Resource Manager は、パフォーマンスを高めるために構成やデータをキャッシュすることがあります。 ロールの割り当てを作成または削除した場合、その変更が有効となるまでに最大 30 分かかる場合があります。 Azure portal、Azure PowerShell、Azure CLI のいずれかを使用している場合は、一度サインアウトしてからサインインすることで、ロールの割り当てを強制的に最新の情報に更新し、その変更を有効にすることができます。 REST API 呼び出しでロールの割り当てを変更する場合は、アクセス トークンを更新することによって、最新の情報への更新を強制することができます。

## <a name="web-app-features-that-require-write-access"></a>書き込みアクセス権限を必要とする Web アプリ機能

1 つの Web アプリに対する読み取り専用アクセスをユーザーに付与する場合、予期しない機能が無効になることがあります。 以下の管理機能には、Web アプリに対する**書き込み**アクセス権 (共同作成者または所有者) が必要なので、読み取り専用のシナリオでは利用できません。

* コマンド (開始や停止など)
* 一般的な構成、スケール設定、バックアップ設定、監視設定などの設定の変更。
* 発行資格情報およびその他の機密情報 (アプリケーション設定や接続文字列など) へのアクセス。
* ストリーミング ログ
* 診断ログの構成
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

以下の項目には、Web サイトに対応する **App Service プランへ**の**書き込み**アクセス権が必要です。  

* Web アプリの価格レベル (Free または Standard) の表示  
* スケールの構成 (インスタンスの数、仮想マシンのサイズ、自動スケールの設定)  
* クォータ (ストレージ、帯域幅、CPU)  

以下の項目には、Web サイトが含まれる**リソース グループ**全体に対する**書き込み**アクセス権が必要です。  

* SSL 証明書とバインド (SSL 証明書は同じリソース グループや地理的な場所にあるサイト間で共有できるため)  
* アラート ルール  
* 自動スケールの設定  
* Application Insights コンポーネント  
* Web テスト  

## <a name="virtual-machine-features-that-require-write-access"></a>書き込みアクセス権限を必要とする仮想マシン機能

Web アプリと同様、仮想マシン ブレード上の機能にも、仮想マシンかリソース グループ内の他のリソースに対する書き込みアクセス権が必要なものがあります。

仮想マシンは、ドメイン名、仮想ネットワーク、ストレージ アカウント、アラート ルールなどのリソースと関連しています。

以下の項目には、**仮想マシン**に対する**書き込み**アクセス権が必要です。

* エンドポイント  
* IP アドレス  
* ディスク  
* Extensions  

以下には、**仮想マシン**と、その仮想マシンが含まれる**リソース グループ** (とドメイン名) の両方に対する**書き込み**アクセス権が必要です。  

* 可用性セット  
* 負荷分散セット  
* アラート ルール  

これらのタイルのいずれにもアクセスできない場合、管理者に問い合わせてリソース グループに対する共同作成者アクセス権を入手してください。

## <a name="azure-functions-and-write-access"></a>Azure Functions と書き込みアクセス権限

[Azure Functions](../azure-functions/functions-overview.md) の一部の機能では、書き込みアクセスが必要です。 たとえば、ユーザーに閲覧者ロールが割り当てられている場合、そのユーザーは関数アプリ内の関数を表示することができません。 ポータルには **(アクセスなし)** が表示されます。

![Function App のアクセスなし](./media/troubleshooting/functionapps-noaccess.png)

閲覧者は、 **[プラットフォーム機能]** タブをクリックし、 **[すべての設定]** をクリックすることで、関数アプリ (Web アプリに類似) に関連する一部の設定を表示できます。ただし、これらの設定を変更することはできません。

## <a name="next-steps"></a>次の手順
* [RBAC と Azure portal を使用して Azure リソースへのアクセスを管理する](role-assignments-portal.md)
* [Azure リソースに対する RBAC の変更のアクティビティ ログを表示する](change-history-report.md)

