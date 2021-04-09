---
title: ファイアウォール規則の管理 - Azure CLI - Azure Database for MySQL - フレキシブル サーバー
description: Azure CLI コマンド ラインを使用して、Azure Database for MySQL - フレキシブル サーバーのファイアウォール規則を作成および管理します。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/21/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 84fdd3045d5a1d44ff611134d88fc9793ee203de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92545074"
---
# <a name="create-and-manage-azure-database-for-mysql---flexible-server-firewall-rules-using-the-azure-cli"></a>Azure CLI を使用して Azure Database for MySQL - フレキシブル サーバーのファイアウォール規則を作成および管理する

> [!IMPORTANT]
> Azure Database for MySQL フレキシブル サーバーは現在、パブリック プレビュー段階にあります

Azure Database for MySQL フレキシブル サーバーでは、フレキシブル サーバーに接続するために、2 種類の相互に排他的なネットワーク接続方法をサポートしています。 次の 2 つのオプションがあります。

- パブリック アクセス (許可された IP アドレス)
- プライベート アクセス (VNet 統合)

この記事では、Azure CLI 使用して **パブリック アクセス (許可された IP アドレス)** を使用して MySQL サーバーを作成する方法について説明します。また、サーバーの作成後にファイアウォール規則を作成、更新、削除、一覧表示、表示するために使用できる Azure CLI コマンドの概要について説明します。 *パブリック アクセス (許可された IP アドレス)* では、MySQL サーバーへの接続は、許可された IP アドレスのみに制限されます。 クライアント IP アドレスは、ファイアウォール規則で許可されている必要があります。 詳細については、「[パブリック アクセス (許可された IP アドレス)](./concepts-networking.md#public-access-allowed-ip-addresses)」をご覧ください。 ファイアウォール規則は、サーバーの作成時に定義できます (推奨) が、後で追加することもできます。

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

[Azure Cloud Shell](../../cloud-shell/overview.md) は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を開くこともできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、 **[入力]** を選択して実行します。

CLI をローカルにインストールして使用する場合、このクイックスタートでは、Azure CLI バージョン 2.0 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

[az login](/cli/azure/reference-index#az-login) コマンドを使用してアカウントにサインインする必要があります。 **ID** プロパティに注意してください。これは、お使いの Azure アカウントの **サブスクリプション ID** のことです。

```azurecli-interactive
az login
```

[az account set](/cli/azure/account#az-account-set) コマンドを使用して、アカウントの特定のサブスクリプションを選択します。 **az login** 出力の **ID** の値をメモしておいて、このコマンドの **subscription** 引数の値として使用します。 複数のサブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 すべてのサブスクリプションを取得するには、[az account list](/cli/azure/account#az-account-list) を使用します。

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-firewall-rule-during-flexible-server-create-using-azure-cli"></a>Azure CLI を使用してフレキシブル サーバーの作成時にファイアウォール規則を作成する

`az mysql flexible-server --public access` コマンドを使用して、"*パブリック アクセス (許可された IP アドレス)* " を指定してフレキシブル サーバーを作成し、フレキシブル サーバーの作成時にファイアウォール規則を構成できます。 **--public-access** スイッチを使用して、サーバーに接続できる許可された IP アドレスを指定できます。 IP アドレスの許可リストに含める 1 つの IP アドレス、または IP アドレスの範囲を指定できます。 IP アドレスの範囲はダッシュで区切る必要があり、スペースは含めません。 次の例に示すように、CLI を使用してフレキシブル サーバーを作成するためのさまざまなオプションがあります。

Azure CLI の[リファレンス ドキュメント](/cli/azure/mysql/flexible-server)で、構成可能な CLI パラメーターの完全な一覧を参照してください。 たとえば、次のコマンドでは、必要に応じてリソース グループを指定できます。

- パブリック アクセスでフレキシブル サーバーを作成し、サーバーにアクセスするためのクライアント IP アドレスを追加する
    ```azurecli-interactive
    az mysql flexible-server create --public-access <my_client_ip>
    ```
- パブリック アクセスでフレキシブル サーバーを作成し、このサーバーにアクセスするための IP アドレス範囲を追加する

    ```azurecli-interactive
    az mysql flexible-server create --public-access <start_ip_address-end_ip_address>
    ```
- パブリック アクセスでフレキシブル サーバーを作成し、アプリケーションが Azure IP アドレスからフレキシブル サーバーに接続できるようにする
    ```azurecli-interactive
    az mysql flexible-server create --public-access 0.0.0.0
    ```
    > [!IMPORTANT]
    > このオプションによって、他のお客様のサブスクリプションからの接続を含む、Azure サービスおよび Azure 内のリソースからこのサーバーへのパブリック アクセスを許可するようにファイアウォールが構成されます。 このオプションを選択する場合は、ログインおよびユーザーのアクセス許可が、承認されたユーザーのみにアクセスを制限していることを確認してください。
    >
- パブリック アクセスでフレキシブル サーバーを作成し、すべての IP アドレスを許可する
    ```azurecli-interactive
    az mysql flexible-server create --public-access all
    ```
    >[!Note]
    > 上記のコマンドにより、開始 IP アドレスが 0.0.0.0、終了 IP アドレスが 255.255.255.255 のファイアウォール規則が作成されます。ブロックされる IP アドレスはありません。 インターネット上にあるすべてのホストからこのサーバーにアクセスできます。 この規則は、機密データが含まれていないテスト サーバーに限定して、一時的にのみ使用することを強くお勧めします。

- パブリック アクセスで IP アドレスを指定しないフレキシブル サーバーを作成する
    ```azurecli-interactive
    az mysql flexible-server create --public-access none
    ```
    >[!Note]
    > ファイアウォール規則のないサーバーを作成することはお勧めしません。 ファイアウォール規則を追加しない場合、どのクライアントからもサーバーに接続できません。

## <a name="create-and-manage-firewall-rule-after-server-create"></a>サーバーを作成した後にファイアウォール規則を作成して管理する
Azure CLI の **az mysql flexible-server firewall-rule** コマンドで、ファイアウォール規則を作成、削除、一覧表示、表示、更新します。

コマンド:
- **create**:フレキシブル サーバーのファイアウォール規則を作成します。
- **list**:フレキシブル サーバーのファイアウォール規則を一覧表示します。
- **update**:フレキシブル サーバーのファイアウォール規則を更新します。
- **show**:フレキシブル サーバーのファイアウォール規則の詳細を表示します。
- **delete**:フレキシブル サーバーのファイアウォール規則を削除します。

Azure CLI の[リファレンス ドキュメント](/cli/azure/mysql/flexible-server)で、構成可能な CLI パラメーターの完全な一覧を参照してください。 たとえば、次のコマンドでは、必要に応じてリソース グループを指定できます。

### <a name="create-a-firewall-rule"></a>ファイアウォール規則を作成する
`az mysql flexible-server firewall-rule create` コマンドを使用して、サーバーに新しいファイアウォール規則を作成します。
IP アドレスの範囲に対するアクセスを許可するには、次の例のように、開始 IP アドレスと終了 IP アドレスの IP アドレスを指定します。
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

1 つの IP アドレスに対するアクセスを許可するには、次の例のように、1 つだけ IP アドレスを指定します。
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 1.1.1.1
```

アプリケーションに Azure IP アドレスからフレキシブル サーバーへの接続を許可するには、次の例のように、開始 IP として IP アドレス 0.0.0.0 を指定します。
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 0.0.0.0
```

> [!IMPORTANT]
> このオプションによって、他のお客様のサブスクリプションからの接続を含む、Azure サービスおよび Azure 内のリソースからこのサーバーへのパブリック アクセスを許可するようにファイアウォールが構成されます。 このオプションを選択する場合は、ログインおよびユーザーのアクセス許可が、承認されたユーザーのみにアクセスを制限していることを確認してください。
> 

正常に完了すると、各コマンドの出力として、作成したファイアウォール規則の詳細が JSON 形式 (既定) で一覧表示されます。 失敗した場合は、代わりにエラー メッセージ テキストが出力されます。

### <a name="list-firewall-rules"></a>ファイアウォール規則の一覧表示 
`az mysql flexible-server firewall-rule list` コマンドを使用して、サーバー上の既存のサーバー ファイアウォール規則を一覧表示します。 サーバー名属性は、 **--name** スイッチで指定されることにご注意ください。
```azurecli-interactive
az mysql flexible-server firewall-rule list --name mydemoserver
```
規則がある場合は、JSON 形式 (既定) で出力として一覧表示されます。 --output table** スイッチを使用すると、結果をよりわかりやすい表形式で出力できます。
```azurecli-interactive
az mysql flexible-server firewall-rule list --name mydemoserver --output table
```

### <a name="update-a-firewall-rule"></a>ファイアウォール規則の更新
`az mysql flexible-server firewall-rule update` コマンドを使用して、サーバーの既存のファイアウォール規則を更新します。 入力として既存のファイアウォール規則の名前に加え、更新する開始 IP アドレスと終了 IP アドレス属性を指定します。
```azurecli-interactive
az mysql flexible-server firewall-rule update --name mydemoserver --rule-name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
正常に完了すると、コマンドの出力として、更新したファイアウォール規則の詳細が JSON 形式 (既定) で一覧表示されます。 失敗した場合は、代わりにエラー メッセージ テキストが出力されます。

> [!NOTE]
> ファイアウォール規則が存在しない場合は、更新コマンドによって規則が作成されます。

### <a name="show-firewall-rule-details"></a>ファイアウォール規則の詳細の表示
`az mysql flexible-server firewall-rule show` コマンドを使用して、サーバー上にある既存のファイアウォール規則の詳細を表示します。 既存のファイアウォール規則の名前を入力します。
```azurecli-interactive
az mysql flexible-server firewall-rule show --name mydemoserver --rule-name FirewallRule1
```
正常に完了すると、コマンドの出力として、指定したファイアウォール規則の詳細が JSON 形式 (既定) で一覧表示されます。 失敗した場合は、代わりにエラー メッセージ テキストが出力されます。

### <a name="delete-a-firewall-rule"></a>ファイアウォール規則の削除
`az mysql flexible-server firewall-rule delete` コマンドを使用して、サーバーから既存のファイアウォール規則を削除します。 既存のファイアウォール規則の名前を入力します。
```azurecli-interactive
az mysql flexible-server firewall-rule delete --name mydemoserver --rule-name FirewallRule1
```
正常に完了すると、出力はありません。 失敗した場合は、エラー メッセージ テキストが表示されます。

## <a name="next-steps"></a>次のステップ
- [Azure Database for MySQL フレキシブル サーバーでのネットワーク](./concepts-networking.md)の詳細を確認する
- [Azure Database for MySQL フレキシブル サーバーのファイアウォール規則](./concepts-networking.md#public-access-allowed-ip-addresses)の詳細を確認する
- [Azure portal を使用して Azure Database for MySQL フレキシブル サーバーのファイアウォール規則を作成および管理する](./how-to-manage-firewall-portal.md)