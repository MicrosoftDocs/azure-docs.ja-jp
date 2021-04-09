---
title: クイックスタート:JavaScript を使用した新しいポリシーの割り当て
description: このクイックスタートでは、JavaScript を使用して、準拠していないリソースを特定するための Azure Policy 割り当てを作成します。
ms.date: 09/24/2020
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 7548053e4bd5be214bf7de3eef3dc4c6c95442d4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91348173"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-javascript"></a>クイックスタート:JavaScript を使用して準拠していないリソースを特定するためのポリシー割り当てを作成する

Azure のコンプライアンスを理解する第一歩は、リソースの状態を特定することです。 このクイック スタートでは、マネージド ディスクを使用していない仮想マシンを識別するためのポリシー割り当てを作成します。 完了すると、"_準拠していない_" 仮想マシンが特定されます。

JavaScript ライブラリを使用して、コマンド ラインまたはスクリプトで Azure リソースを管理します。 このガイドでは、JavaScript ライブラリを使用してポリシー割り当てを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料](https://azure.microsoft.com/free/)アカウントを作成してください。

- **Node.js**:[Node.js](https://nodejs.org/) バージョン 12 以降が必須です。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-libraries"></a>Policy ライブラリを追加する

JavaScript が Azure Policy で動作するようにするには、ライブラリを追加する必要があります。 これらのライブラリは、[Windows 10 上の bash](/windows/wsl/install-win10) など、JavaScript を使用できる場所であればどこでも動作します。

1. 次のコマンドを実行し、新しい Node.js プロジェクトをセットアップします。

   ```bash
   npm init -y
   ```

1. yargs ライブラリへの参照を追加します。

   ```bash
   npm install yargs
   ```

1. Azure Policy ライブラリへの参照を追加します。

   ```bash
   # arm-policy is for working with Azure Policy objects such as definitions and assignments
   npm install @azure/arm-policy

   # arm-policyinsights is for working with Azure Policy compliance data such as events and states
   npm install @azure/arm-policyinsights
   ```

1. Azure 認証ライブラリへの参照を追加します。

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > _package.json_ で `@azure/arm-policy` がバージョン **3.1.0** 以降であることと、`@azure/arm-policyinsights` がバージョン **3.2.0** 以降であること、`@azure/ms-rest-nodeauth` がバージョン **3.0.5** 以降であることを確認します。

## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

このクイックスタートでは、ポリシー割り当てを作成し、**マネージド ディスクを使用しない監査 VM** (`06a78e20-9358-41c9-923c-fb736d382a4d`) 定義を割り当てます。 このポリシー定義では、ポリシー定義で設定されている条件に準拠していないリソースが識別されます。

1. _policyAssignment.js_ という名前の新しいファイルを作成し、次のコードを入力します。

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyObjects = require("@azure/arm-policy");
   
   if (argv.subID && argv.name && argv.displayName && argv.policyDefID && argv.scope && argv.description) {
   
       const createAssignment = async () => {
           const credentials = await authenticator.interactiveLogin();
           const client = new policyObjects.PolicyClient(credentials, argv.subID);
           const assignments = new policyObjects.PolicyAssignments(client);
   
           const result = await assignments.create(
               argv.scope,
               argv.name,
               {
                   displayName: argv.displayName,
                   policyDefinitionId: argv.policyDefID,
                   description: argv.description
               }
           );
           console.log(result);
       };
   
       createAssignment();
   }
   ```

1. ターミナルに次のコマンドを入力します。

   ```bash
   node policyAssignment.js `
      --subID "{subscriptionId}" `
      --name "audit-vm-manageddisks" `
      --displayName "Audit VMs without managed disks Assignment" `
      --policyDefID "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      --description "Shows all virtual machines not using managed disks" `
      --scope "{scope}"
   ```

上記のコマンドでは次の情報が使用されています。

- **subID** - 認証コンテキストのサブスクリプション ID。 必ず `{subscriptionId}` は実際のサブスクリプションに置き換えてください。
- **name** - ポリシー割り当てオブジェクトの一意の名前。 上記の例では、_audit-vm-manageddisks_ を使用しています。
- **displayName** - ポリシーの割り当てに使用する表示名。 このケースでは、"_Audit VMs without managed disks Assignment_" を使用します。
- **policyDefID** - 割り当てを作成する際に基礎として使用するポリシー定義パス。 ここでは、"_Managed Disks を使用していない VM の監査_" というポリシー定義の ID です。
- **description** - ポリシーの機能またはこのスコープに割り当てる理由の詳細な説明。
- **scope** - スコープによって、ポリシー割り当てを適用するリソースまたはリソース グループが決まります。 スコープには、管理グループから個々のリソースまで指定できます。 `{scope}` は、次のパターンのいずれかに必ず置き換えてください。
  - 管理グループ: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - サブスクリプション: `/subscriptions/{subscriptionId}`
  - リソース グループ: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - リソース: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

以上の手順で、準拠していないリソースを特定し、環境のコンプライアンスの状態を理解できるようになりました。

## <a name="identify-non-compliant-resources"></a>準拠していないリソースを特定する

ポリシー割り当てが作成されたので、準拠していないリソースを特定できます。

1. _policyState.js_ という名前のファイルを新規作成し、次のコードを入力します。

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyInsights = require("@azure/arm-policyinsights");
   
   if (argv.subID && argv.name) {
   
       const getStates = async () => {
   
           const credentials = await authenticator.interactiveLogin();
           const client = new policyInsights.PolicyInsightsClient(credentials);
           const policyStates = new policyInsights.PolicyStates(client);
           const result = await policyStates.listQueryResultsForSubscription(
               "latest",
               argv.subID,
               {
                   queryOptions: {
                       filter: "IsCompliant eq false and PolicyAssignmentId eq '" + argv.name + "'",
                       apply: "groupby((ResourceId))"
                   }
               }
           );
           console.log(result);
       };
   
       getStates();
   }
   ```

1. ターミナルに次のコマンドを入力します。

   ```bash
   node policyState.js --subID "{subscriptionId}" --name "audit-vm-manageddisks"
   ```

`{subscriptionId}` を、前の手順で作成した "audit-vm-manageddisks" という名前のポリシー割り当てのコンプライアンス結果を確認するサブスクリプションに置き換えます。 他のスコープの一覧とデータを集計する方法については、[PolicyStates*](/javascript/api/@azure/arm-policyinsights/) メソッドに関する記事を参照してください。

次のような結果が返されます。

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

この結果は、Azure portal ビュー内のポリシー割り当ての **[リソース コンプライアンス]** タブに表示される内容と同じです。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

- ポータルを使用して、ポリシー割り当て _Audit VMs without managed disks Assignment_ を削除します。 ポリシー定義は組み込まれているので、削除できる定義はありません。

- インストール済みのライブラリをアプリケーションから削除する場合、次のコマンドを実行します。

  ```bash
  npm uninstall @azure/arm-policy @azure/arm-policyinsights @azure/ms-rest-nodeauth yargs
  ```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ポリシー定義を割り当てて、Azure 環境内で準拠していないリソースを特定しました。

新しいリソースが準拠していることを検証するためのポリシー定義の割り当てについて詳しく学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [ポリシーの作成と管理](./tutorials/create-and-manage.md)