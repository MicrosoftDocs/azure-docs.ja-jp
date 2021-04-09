---
title: Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのセキュリティを構成する
description: Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのセキュリティを構成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d6e27fddceb69efbb2c1697c09ee9b61d7f38ee4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687976"
---
# <a name="configure-security-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのセキュリティを構成する

このドキュメントでは、サーバー グループのセキュリティに関連するさまざまな側面について説明します。
- 保存時の暗号化
- [ユーザー管理]
   - 一般的なパースペクティブ
   - _postgres_ 管理ユーザーのパスワードを変更する
- Audit

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="encryption-at-rest"></a>保存時の暗号化
保存時の暗号化は、データベースを格納するディスクを暗号化するか、データベースの機能を使用して挿入または更新するデータを暗号化することで (あるいは両方を行うことで) 実装できます。

### <a name="hardware-linux-host-volume-encryption"></a>ハードウェア:Linux ホスト ボリュームの暗号化
Azure Arc 対応 Data Services のセットアップで使用されるディスク上に存在するデータをセキュリティ保護するには、システム データの暗号化を実装します。 このトピックの詳細については、次を参照してください。
- 一般的な Linux の「[保存データの暗号化](https://wiki.archlinux.org/index.php/Data-at-rest_encryption)」 
- LUKS `cryptsetup` 暗号化コマンド (Linux) を使用したディスクの暗号化 (https://www.cyberciti.biz/security/howto-linux-hard-disk-encryption-with-luks-cryptsetup-command/) 具体的には、Azure Arc 対応 Data Services は、お客様が提供する物理インフラストラクチャ上で実行されるため、インフラストラクチャのセキュリティを保護する責任はお客様にあります)。

### <a name="software-use-the-postgresql-pgcrypto-extension-in-your-server-group"></a>ソフトウェア:サーバー グループで PostgreSQL の `pgcrypto` 拡張機能を使用する
Azure Arc のセットアップをホストするために使用されるディスクを暗号化するだけでなく、データベース内のデータを暗号化するためにアプリケーションで使用できるメカニズムが公開されるように、Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを構成することもできます。 `pgcrypto` 拡張機能は、Postgres の `contrib` 拡張機能の一部であり、Azure Arc 対応 PostgreSQL Hyperscale サーバー グループで使用できます。 `pgcrypto` 拡張機能の詳細については、[こちら](https://www.postgresql.org/docs/current/pgcrypto.html)を参照してください。
要約すると、次のコマンドを使用して拡張機能を有効にし、それを作成して使用します。


#### <a name="create-the-pgcrypto-extension"></a>`pgcrypto` 拡張機能を作成する
任意のクライアント ツールを使用してサーバー グループに接続し、標準の PostgreSQL クエリを実行します。
```console
CREATE EXTENSION pgcrypto;
```

> 接続方法の詳細については、[こちら](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)を参照してください。

#### <a name="verify-the-list-the-extensions-ready-to-use-in-your-server-group"></a>サーバー グループですぐに使用できる拡張機能のリストを確認する
サーバー グループで使用できる拡張機能を一覧表示すると、`pgcrypto` 拡張機能をすぐに使用できることを確認できます。
任意のクライアント ツールを使用してサーバー グループに接続し、標準の PostgreSQL クエリを実行します。
```console
select * from pg_extension;
```
上記のコマンドを使用して有効にして作成した場合は、`pgcrypto` が表示されます。

#### <a name="use-the-pgcrypto-extension"></a>`pgcrypto` 拡張機能を使用する
この時点で、`pgcrypto` で提供された関数のいずれかが使用されるように、アプリケーションのコードを調整できます。
- 一般的なハッシュ関数
- パスワード ハッシュ関数
- PGP 暗号化関数
- RAW 暗号化関数
- ランダム データ関数

たとえば、ハッシュ値を生成します。 次のコマンドを実行します。

```console
Select crypt('Les sanglots longs des violons de l_automne', gen_salt('md5'));
```

次のハッシュが返されます。

```console
              crypt
------------------------------------
 $1$/9ACBYOV$z52PAGjQ5WTU9xvEECBNv/   
```

または、例:

```console
select hmac('Les sanglots longs des violons de l_automne', 'md5', 'sha256');
```

次のハッシュが返されます。

```console
                                hmac
--------------------------------------------------------------------
 \xd4e4790b69d2cc8dbce3385ee63272bc7760f1603640bb211a7b864e695570c5
```

または、たとえば、パスワードなどの暗号化されたデータを格納します。

次の表に示すシークレットがアプリケーションに格納されていると仮定します。

```console
create table mysecrets(USERid int, USERname char(255), USERpassword char(512));
```

ユーザーの作成時にパスワードを暗号化します。

```console
insert into mysecrets values (1, 'Me', crypt('MySecretPasswrod', gen_salt('md5')));
```

この時点で、パスワードは暗号化されています。

```console
select * from mysecrets;
```

出力:

- USERid:1
- USERname:自分
- USERpassword: $1$Uc7jzZOp$NTfcGo7F10zGOkXOwjHy31

アプリケーションに接続してパスワードを渡すと、`mysecrets` テーブルで検索され、アプリケーションに渡されるパスワードとテーブルに格納されているパスワードが一致すれば、ユーザーの名前が返されます。 次に例を示します。

- 間違ったパスワードを渡します。
   ```console
   select USERname from mysecrets where (USERpassword = crypt('WrongPassword', USERpassword));
   ```

   Output 

   ```returns
    USERname
   ---------
   (0 rows)
   ```
- 正しいパスワードを渡します。

   ```console
   select USERname from mysecrets where (USERpassword = crypt('MySecretPasswrod', USERpassword));
   ``` 

   出力:

   ```output
    USERname
   ---------
   Me
   (1 row)
   ```

この例は、Postgres の `pgcrypto` 拡張機能を使用して、Azure Arc 対応 PostgreSQL Hyperscale で保存データを暗号化し (暗号化されたデータを格納し)、`pgcrypto` で提供された関数をアプリケーションで使用して、その暗号化されたデータを操作できることを示しています。

## <a name="user-management"></a>[ユーザー管理]
### <a name="general-perspectives"></a>一般的なパースペクティブ
Postgres の標準的な方法を使用して、ユーザーまたはロールを作成できます。 ただし、その場合、これらの成果物はコーディネーター ロールでのみ使用できます。 これらのユーザーまたはロールは、プレビュー時に、コーディネーター ノード外およびサーバー グループのワーカー ノード上に配布されたデータにまだアクセスできません。 その理由は、プレビュー時には、ユーザー定義がワーカー ノードにレプリケートされないためです。

### <a name="change-the-password-of-the-_postgres_-administrative-user"></a>_postgres_ 管理ユーザーのパスワードを変更する
Azure Arc 対応 PostgreSQL Hyperscale には、サーバー グループの作成時にパスワードを設定する _postgres_ という標準の Postgres 管理ユーザーが用意されています。
パスワードを変更するためのコマンドの一般的な形式は、次のとおりです。
```console
azdata arc postgres server edit --name <server group name> --admin-password
```

ここで、`--admin-password` は、AZDATA_PASSWORD **セッション** 環境変数内の値の存在に関連するブール値です。
AZDATA_PASSWORD **セッション** 環境変数が存在していて値がある場合は、上記のコマンドを実行すると、postgres ユーザーのパスワードがこの環境変数の値に設定されます。

AZDATA_PASSWORD **セッション** 環境変数が存在していて値がない場合、または AZDATA_PASSWORD **セッション** の環境変数が存在しない場合は、上記のコマンドを実行すると、ユーザーは対話形式でパスワードの入力を求められます。

#### <a name="change-the-password-of-the-postgres-administrative-user-in-an-interactive-way"></a>対話形式で postgres 管理ユーザーのパスワードを変更する

1. AZDATA_PASSWORD **セッション** 環境変数を削除するか、その値を削除します
2. 次のコマンドを実行します。
   ```console
   azdata arc postgres server edit --name <server group name> --admin-password
   ```
   次に例を示します。
   ```console
   azdata arc postgres server edit -n postgres01 --admin-password
   ```
   パスワードを入力してそれを確認するように求められます。
   ```console
   Postgres Server password:
   Confirm Postgres Server password:
   ```
   パスワードが更新されると、コマンドの出力は次のようになります。
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```
   
#### <a name="change-the-password-of-the-postgres-administrative-user-using-the-azdata_password-session-environment-variable"></a>AZDATA_PASSWORD **セッション** 環境変数を使用して postgres 管理ユーザーのパスワードを変更する:
1. AZDATA_PASSWORD **セッション** 環境変数の値を、パスワードとして使用するものに設定します。
2. 次のコマンドを実行します。
   ```console
   azdata arc postgres server edit --name <server group name> --admin-password
   ```
   次に例を示します。
   ```console
   azdata arc postgres server edit -n postgres01 --admin-password
   ```
   
   パスワードが更新されると、コマンドの出力は次のようになります。
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```

> [!NOTE]
> AZDATA_PASSWORD セッションの環境変数が存在するかどうか、およびその値が何かを確認するには、次のように実行します。
> - Linux クライアントの場合:
> ```console
> printenv AZDATA_PASSWORD
> ```
>
> - PowerShell を使用した Windows クライアントの場合:
> ```console
> echo $env:AZDATA_PASSWORD
> ```

## <a name="audit"></a>Audit

監査シナリオの場合は、Postgres の `pgaudit` 拡張機能を使用するようにサーバー グループを構成してください。 `pgaudit` の詳細については、[`pgAudit` GitHub プロジェクト](https://github.com/pgaudit/pgaudit/blob/master/README.md)に関するページを参照してください。 サーバー グループで `pgaudit` 拡張機能を有効にするには、[PostgreSQL 拡張機能の使用](using-extensions-in-postgresql-hyperscale-server-group.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ
- [`pgcrypto` 拡張機能](https://www.postgresql.org/docs/current/pgcrypto.html)に関するページを参照してください
- [PostgreSQL 拡張機能の使用](using-extensions-in-postgresql-hyperscale-server-group.md)に関するページを参照してください

