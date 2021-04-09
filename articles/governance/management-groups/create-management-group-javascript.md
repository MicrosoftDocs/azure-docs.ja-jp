---
title: 'クイック スタート: JavaScript 使用した管理グループの作成'
description: このクイックスタートでは、JavaScript を使用して、リソースをリソース階層で整理する管理グループを作成します。
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 99a2ed406d4837f6fc346e68b3b400003feed38c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100099151"
---
# <a name="quickstart-create-a-management-group-with-javascript"></a>クイック スタート: JavaScript 使用した管理グループの作成

管理グループは、複数のサブスクリプションのアクセス、ポリシー、コンプライアンスを管理するのに役立つコンテナーです。 これらのコンテナーを作成して、[Azure Policy](../policy/overview.md) と [Azure ロール ベースのアクセス制御](../../role-based-access-control/overview.md)で使用できる効果的で効率的な階層を構築します。 管理グループについて詳しくは、「[Azure 管理グループでリソースを整理する](overview.md)」をご覧ください。

ディレクトリに作成される最初の管理グループは、完了までに最大 15 分かかる場合があります。 Azure 内でディレクトリの管理グループ サービスを初めて設定する際に実行するプロセスがあります。 プロセスが完了すると、通知を受け取ります。 詳細については、「[管理グループの初期セットアップ](./overview.md#initial-setup-of-management-groups)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

- 開始する前に、バージョン 12 以降の [Node.js](https://nodejs.org/) がインストールされていることを確認してください。

- [階層の保護](./how-to/protect-resource-hierarchy.md#setting---require-authorization)が有効になっていない場合、テナント内のすべての Azure AD ユーザーは、そのユーザーに割り当てられた管理グループの書き込みアクセス許可なしで管理グループを作成できます。 この新しい管理グループは、ルート管理グループ ([既定の管理グループ](./how-to/protect-resource-hierarchy.md#setting---default-management-group)) の子になり、作成者には "所有者" ロールの割り当てが付与されます。 管理グループ サービスでは、この機能が許可されるため、ルート レベルでのロールの割り当ては必要ありません。 ルート管理グループは、作成されると、どのユーザーもアクセスできません。 管理グループの使用を開始する場合に Azure AD の全体管理者を見つけるという困難を回避するために、ルート レベルで最初の管理グループを作成できるようになっています。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>アプリケーションのセットアップ

JavaScript で管理グループを管理できるようにするには、環境を設定する必要があります。 [Windows 10 上での Bash](/windows/wsl/install-win10) など、JavaScript を利用できる場所であればどこでもこのセットアップは機能します。

1. 次のコマンドを実行し、新しい Node.js プロジェクトをセットアップします。

   ```bash
   npm init -y
   ```

1. yargs モジュールへの参照を追加します。

   ```bash
   npm install yargs
   ```

1. Azure Resource Graph モジュールへの参照を追加します。

   ```bash
   npm install @azure/arm-managementgroups
   ```

1. Azure 認証ライブラリへの参照を追加します。

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > _package.json_ で `@azure/arm-managementgroups` がバージョン **1.1.0** 以降であることと、`@azure/ms-rest-nodeauth` がバージョン **3.0.5** 以降であることを確認します。

## <a name="create-the-management-group"></a>管理グループの作成

1. _index.js_ という名前のファイルを新規作成し、次のコードを入力します。

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const managementGroups = require("@azure/arm-managementgroups");

   if (argv.groupID && argv.displayName) {
       const createMG = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new managementGroups.ManagementGroupsAPI(credentials);
          const result = await client.managementGroups.createOrUpdate(
             groupId: argv.groupID,
             {
                 displayName: argv.displayName
             }
          );
          console.log(result);
       };

       createMG();
   }
   ```

1. ターミナルに次のコマンドを入力します。

   ```bash
   node index.js --groupID "<NEW_MG_GROUP_ID>" --displayName "<NEW_MG_FRIENDLY_NAME>"
   ```

   各トークンの `<>` プレースホルダーはそれぞれ、"_管理グループ ID_" と "_管理グループのフレンドリ名_" に置き換えてください。

   スクリプトによって認証が試行されると、次のようなメッセージがターミナルに表示されます。

   > サインインするには、Web ブラウザーを使用して https://microsoft.com/devicelogin ページを開き、認証用のコード FGB56WJUGK を入力します。

   ブラウザーでの認証が完了すると、スクリプトが引き続き実行されます。

管理グループを作成した結果はコンソールに出力されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

インストール済みのライブラリをアプリケーションから削除する場合、次のコマンドを実行します。

```bash
npm uninstall @azure/arm-managementgroups @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、リソース階層を整理するための管理グループを作成しました。 管理グループには、サブスクリプションや他の管理グループを含めることができます。

管理グループについて、またリソース階層の管理方法について詳しくは、次の記事に進んでください。

> [!div class="nextstepaction"]
> [管理グループを使用してリソースを管理する](./manage.md)