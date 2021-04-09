---
title: マネージド ID に関する FAQ と既知の問題 - Azure AD
description: Azure リソースのマネージド ID に関する既知の問題。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/04/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 3f1be2e64435cb0bcdb369a398a9a65fc3714fb2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100008538"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID に関する FAQ と既知の問題

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>よく寄せられる質問 (FAQ)

> [!NOTE]
> Azure リソースのマネージド ID は、以前のマネージドサービス ID (MSI) の新しい名前です。

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>マネージド ID を持つリソースを見つけるにはどうすればいいですか?

次の Azure CLI コマンドを使用して、システム割り当てマネージド ID を持つリソースの一覧を検索できます。 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```

### <a name="do-managed-identities-have-a-backing-app-object"></a>マネージド ID にバッキング アプリ オブジェクトはありますか?

いいえ。 マネージド ID と Azure AD アプリ登録は、ディレクトリ内で同じものではありません。 

アプリの登録には、次の 2 つのコンポーネントがあります。アプリケーション オブジェクトとサービス プリンシパル オブジェクト。 Azure リソースのマネージド ID にあるのは、これらのコンポーネントのうち、次の 1 つだけです:サービス プリンシパル オブジェクト。 

マネージド ID では、アプリケーション オブジェクトがディレクトリにありません。これは、MS グラフに対するアプリのアクセス許可を付与するために一般的に使用されるものです。 代わりに、マネージド ID に対する MS グラフのアクセス許可をサービス プリンシパルに直接付与する必要があります。  

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>同じマネージド ID を複数のリージョンで使用できますか?

簡単に言えば、はい、ユーザー割り当て済みマネージド ID は複数の Azure リージョンで使用できます。 詳しく回答すると、ユーザー割り当て済みマネージド ID はリージョン リソースとして作成されますが、Azure AD で作成される関連[サービス プリンシパル](../develop/app-objects-and-service-principals.md#service-principal-object) (SPN) はグローバルに使用できます。 サービス プリンシパルはどの Azure リージョンからでも使用でき、その可用性は Azure AD の可用性に依存します。 たとえば、中南部リージョンでユーザー割り当て済みマネージド ID を作成し、そのリージョンが使用できなくなった場合、この問題はマネージド ID 自体の[コントロール プレーン アクティビティ](../../azure-resource-manager/management/control-plane-and-data-plane.md)のみに影響します。  マネージド ID を使用するように既に構成されているリソースによって実行されるアクティビティは影響を受けません。

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Azure リソースのマネージド ID は Azure Cloud Services で動作しますか?

いいえ、Azure Cloud Services で Azure リソースのマネージド ID をサポートする予定はありません。

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>マネージド ID に関連付けられる資格情報は何ですか? その有効期間とローテーションの頻度は?

> [!NOTE]
> マネージド ID の認証方法は、内部的な実装の詳細であり、予告なく変更されます。

マネージド ID には、証明書ベースの認証が使用されます。 各マネージド ID の資格情報は有効期限が 90 日で、45 日後にローテーションされます。

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID のセキュリティ境界は何ですか?

ID のセキュリティ境界は、ID のアタッチ先リソースです。 たとえば、Azure リソースのマネージド ID が有効な仮想マシンのセキュリティ境界は、仮想マシンです。 その VM 上で実行されているすべてのコードは、Azure リソース エンドポイントと要求トークンのマネージド ID を呼び出すことができます。 これは Azure リソースのマネージド ID をサポートする他のリソースと同様のエクスペリエンスです。

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>要求内で ID を指定しない場合、IMDS はどの ID を規定値としますか?

- システム割り当てマネージド ID が有効で、要求内で ID が指定されない場合、IMDS はシステム割り当てマネージド ID を規定値とします。
- システム割り当てマネージド ID が有効でなく、ユーザー割り当てマネージド ID が 1 つのみの場合、IMDS はその単一のユーザー割り当てマネージド ID を規定値とします。 
- システム割り当てマネージド ID が有効でなく、複数のユーザー割り当てマネージド ID が存在する場合、要求内でのマネージド ID の指定が必要です。

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>サブスクリプションを別のディレクトリに移動する場合、マネージド ID は自動的に再作成されますか?

いいえ。 サブスクリプションを別のディレクトリに移動する場合、お客様が手動でそれらを再作成し、再度 Azure ロールの割り当てを許可する必要があります。
- システム割り当てマネージドID の場合、無効にしてから最有効化します。 
- ユーザー割り当てマネージド ID の場合、削除、再作成の後、必要なリソース (例： 仮想マシン) へ再度アタッチします

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>マネージド ID を使って違うディレクトリやテナント内のリソースへアクセスできますか?

いいえ。 マネージド ID は現在、クロスディレクトリ シナリオをサポートしていません。 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>リソースのマネージド ID に必要な Azure RBAC アクセス許可は何ですか? 

- システム割り当てマネージド ID:リソースに対する書き込みアクセス許可が必要です。 たとえば、仮想マシンには Microsoft.Compute/virtualMachines/write が必要です。 このアクションは、[Virtual Machine Contributor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) などのリソース固有の組み込みロールに含まれています。
- ユーザー割り当てマネージド ID:リソースに対する書き込みアクセス許可が必要です。 たとえば、仮想マシンには Microsoft.Compute/virtualMachines/write が必要です。 さらにマネージド ID に対する [Managed Identity Operator](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ロールの割り当て。

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>ユーザー割り当てマネージド ID を作成できないようにするにはどうすればよいですか。

[Azure Policy](../../governance/policy/overview.md) を使用して、ユーザー割り当てマネージド ID をユーザーが作成できないようにすることができます

- [Azure portal](https://portal.azure.com) に移動し、 **[ポリシー]** に移動します。
- **[定義]** を選択します。
- **[+ ポリシー定義]** を選択し、必要な情報を入力します。
- ポリシー規則セクションに、次を貼り付けます。

```json
{
  "mode": "All",
  "policyRule": {
    "if": {
      "field": "type",
      "equals": "Microsoft.ManagedIdentity/userAssignedIdentities"
    },
    "then": {
      "effect": "deny"
    }
  },
  "parameters": {}
}

```

ポリシーを作成したら、使用するリソース グループに割り当てます。

- リソース グループに移動します。
- テスト用に使用しているリソース グループを見つけます。
- 左側のメニューから **[ポリシー]** を選択します。
- **[ポリシーの割り当て]** を選択します。
- **[基本]** セクションで、次を指定します。
    - **[Scope]\(スコープ\)** : テスト用に使用しているリソース グループ
    - **[ポリシー定義]** :前に作成したポリシー。
- 他のすべての設定は既定値のままにして、 **[確認と作成]** を選択します

この時点で、リソース グループ内でユーザー割り当てマネージド ID を作成しようとすると失敗します。

  ![ポリシー違反](./media/known-issues/policy-violation.png)

## <a name="known-issues"></a>既知の問題

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Azure リソース拡張機能のマネージド ID のスキーマ エクスポートを試行すると、"自動スクリプト" が失敗する

VM で Azure リソースのマネージド ID が有効になっている場合、その VM またはリソース グループに対して "自動スクリプト" 機能を使用しようとすると、次のエラーが表示されます:

![Azure リソースのマネージド ID の自動スクリプトのエクスポート エラー](./media/msi-known-issues/automation-script-export-error.png)

現時点では、Azure リソース VM 拡張機能のマネージド ID (2019 年 1 月に非推奨になる予定) は、そのスキーマをリソース グループ テンプレートにエクスポートする機能はサポートされていません。 その結果、生成されたテンプレートには、リソース上で Azure リソースのマネージド ID を有効にする構成パラメーターは表示されません。 これらのセクションは、[テンプレート を使用して Azure VM 上で Azure リソースのマネージド ID を構成する](qs-configure-template-windows-vm.md)の例に従って手動で追加できます。

Azure リソース VM 拡張機能のマネージド ID (2019 年 1 月に非推奨になる予定) でスキーマのエクスポート機能が利用可能になると、[VM 拡張機能を含むリソース グループのエクスポート](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)の一覧に表示されます。

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>リソース グループまたはサブスクリプションから移動した後に VM を開始できない

実行中の状態で VM を移動する場合、VM は移動中も実行し続けます。 ただし、移動後に、VM を停止および再起動すると、VM を開始できなくなります。 この問題は、VM が Azure リソースのマネージド ID への参照を更新できず、以前のリソース グループでポイントし続けるために発生します。

**回避策** 
 
Azure リソースのマネージド ID の正しい値を取得できるように、VM 上で更新をトリガーします。 VM プロパティの変更を行って、Azure リソース ID のマネージド ID への参照を更新できます。 たとえば、次のコマンドを使用して、VM で新しいタグの値を設定できます。

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
このコマンドは、新しいタグ "fixVM" を値 1 で VM に設定します。 
 
このプロパティの設定によって、VM は Azure リソース URI の正しいマネージド ID で更新され、VM を開始できるようになります。 
 
VM が開始されると、次のコマンドを使用してタグを削除できます。

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Azure AD ディレクトリ間のサブスクリプションの転送

マネージド ID は、サブスクリプションが別のディレクトリに移動/転送されたときに更新されません。 その結果、既存のシステム割り当てマネージド ID やユーザー割り当てマネージド ID は破損します。 

別のディレクトリに移動されたサブスクリプションのマネージド ID の回避策:

 - システム割り当てマネージドID の場合、無効にしてから最有効化します。 
 - ユーザー割り当てマネージド ID の場合、削除、再作成の後、必要なリソース (例： 仮想マシン) へ再度アタッチします

詳細については、「[Azure サブスクリプションを別の Azure AD ディレクトリに移転する](../../role-based-access-control/transfer-subscription.md)」を参照してください。

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>ユーザー割り当てマネージド ID の異なるリソース グループ/サブスクリプションへの移動

ユーザー割り当てマネージド ID の異なるリソース グループへの移動はサポートされていません。
