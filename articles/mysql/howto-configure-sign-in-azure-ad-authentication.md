---
title: Azure Active Directory の使用 - Azure Database for MySQL
description: Azure Database for MySQL での認証に Azure Active Directory (Azure AD) を設定する方法について説明します
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: how-to
ms.date: 07/23/2020
ms.openlocfilehash: f5890ddb2a4b1599dbcfd1e624c9fbe71a564de7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442759"
---
# <a name="use-azure-active-directory-for-authentication-with-mysql"></a>MySQL での認証に Azure Active Directory を使用する

この記事では、Azure Database for MySQL を使用して Azure Active Directory アクセスを構成する方法と、Azure AD トークンを使用して接続する方法について説明します。

> [!IMPORTANT]
> Azure Active Directory 認証は、MySQL 5.7 以降でのみ使用できます。

## <a name="setting-the-azure-ad-admin-user"></a>Azure AD 管理者ユーザーを設定する

Azure AD ベースの認証用にユーザーを作成/有効化できるのは、Azure AD 管理者ユーザーだけです。 Azure AD 管理者ユーザーを作成するには、次の手順に従います

1. Azure portal で、Azure AD に対して有効にする Azure Database for MySQL のインスタンスを選択します。
2. [設定] で、[Active Directory 管理者] を選択します。

![Azure AD 管理者の設定][2]

3. 顧客テナントで Azure AD 管理者にする有効な Azure AD ユーザーを選択します。

> [!IMPORTANT]
> 管理者を設定すると、管理者の完全なアクセス許可を持つ新しいユーザーが Azure Database for MySQL サーバーに追加されます。

作成できる Azure AD 管理者は、MySQL サーバーあたり 1 人だけです。別の管理者を選択すると、そのサーバーに構成されている既存の Azure AD 管理者が上書きされます。

管理者を構成した後、サインインできるようになりました。

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Azure AD を使用して Azure Database for MySQL に接続する

次の概要図は、Azure Database for MySQL で Azure AD 認証を使用するワークフローをまとめたものです。

![認証フロー][1]

Azure AD 統合は、mysql CLI などの一般的な MySQL ツールと連携するように設計されています。これらのツールは Azure AD 対応ではなく、MySQL に接続するときにユーザー名とパスワードを指定することのみをサポートしています。 上の図に示されているように、Azure AD トークンをパスワードとして渡します。

現在、次のクライアントがテストされています。

- MySQLWorkbench 
- Mysql CLI

また、よく使われるアプリケーション ドライバーもテストされています。詳細については、このページの最後を参照してください。

ユーザー/アプリケーションで Azure AD を使用して認証を行う必要がある手順を次に示します。

### <a name="prerequisites"></a>前提条件

Azure Cloud Shell、Azure VM、またはお使いのローカル コンピューター上で、次の手順を実行できます。 [Azure CLI がインストールされている](/cli/azure/install-azure-cli)ことを確認します。

### <a name="step-1-authenticate-with-azure-ad"></a>手順 1:Azure AD による認証

最初に、Azure CLI ツールを使用して Azure AD による認証を行います。 この手順は、Azure Cloud Shell では必要ありません。

```
az login
```

コマンドを実行すると、ブラウザー ウィンドウが起動されて、Azure AD 認証ページが表示されます。 ご自分の Azure AD ユーザー ID とパスワードを指定する必要があります。

### <a name="step-2-retrieve-azure-ad-access-token"></a>手順 2:Azure AD アクセス トークンを取得する

Azure CLI ツールを起動して、手順 1 で認証された Azure AD ユーザーのアクセス トークンを取得し、Azure Database for MySQL にアクセスします。

例 (パブリック クラウドの場合):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

上記のリソース値は、示されているとおりに正確に指定する必要があります。 他のクラウドの場合、リソース値は次を使用して検索できます。

```azurecli-interactive
az cloud show
```

Azure CLI バージョン 2.0.71 以降では、すべてのクラウドに対して、次のより便利なバージョンでコマンドを指定できます。

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

認証が成功すると、Azure AD によってアクセス トークンが返されます。

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

トークンは、認証されたユーザーに関するすべての情報をエンコードする Base 64 文字列であり、Azure Database for MySQL サービスをターゲットとしています。

> [!NOTE]
> アクセス トークンの有効性は、5 分から 60 分の範囲内です。 アクセス トークンは、Azure Database for MySQL へのログインを開始する直前に取得することをお勧めします。

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>手順 3:MySQL でログインするためのパスワードとしてトークンを使用する

接続時には、MySQL ユーザー パスワードとしてアクセス トークンを使用する必要があります。 MySQLWorkbench などの GUI クライアントを使用する場合は、上記のメソッドを使用してトークンを取得できます。 

CLI を使用する場合は、この短縮形を使用して接続できます。 

**例 (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

接続時の重要な考慮事項:

* `user@tenant.onmicrosoft.com` は、接続に使用しようとしている Azure AD ユーザーまたはグループの名前です
* Azure AD ユーザー/グループ名の後には常にサーバー名を付加してください (`@mydb` など)
* Azure AD ユーザーまたはグループ名の正確なスペルを使用するようにしてください
* Azure AD ユーザーおよびグループの名前では、大文字と小文字が区別されます
* グループとして接続する場合は、グループ名のみ (`GroupName@mydb` など) を使用します
* 名前にスペースが含まれている場合は、各スペースの前に `\` を使用してエスケープします

“enable-cleartext-plugin” 設定に注意してください。トークンがハッシュされずにサーバーに送信されるようにするには、他のクライアントと同様の構成を使用する必要があります。

これで、Azure AD 認証を使用して MySQL サーバーに対して認証されました。

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Azure Database for MySQL で Azure AD ユーザーを作成する

Azure Database for MySQL データベースに Azure AD ユーザーを追加するには、接続後に次の手順を実行します (接続方法については、後述のセクションを参照してください)。

1. まず、Azure AD ユーザー `<user>@yourtenant.onmicrosoft.com` が Azure AD テナントの有効なユーザーであることを確認します。
2. Azure AD 管理者ユーザーとして Azure Database for MySQL インスタンスにサインインします。
3. Azure Database for MySQL でユーザー `<user>@yourtenant.onmicrosoft.com` を作成します。

**例:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

ユーザー名が 32 文字を超える場合は、接続時に使用するため、代わりに別名を使用することをお勧めします。 

例:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> Azure AD を通じてユーザーを認証しても、Azure Database for MySQL データベース内のオブジェクトにアクセスするためのアクセス許可はユーザーに付与されません。 必要なアクセス許可をユーザーに手動で付与する必要があります。

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Azure Database for MySQL で Azure AD グループを作成する

Azure AD グループがデータベースにアクセスできるようにするには、ユーザーの場合と同じメカニズムを使用しますが、代わりにグループ名を指定します。

**例:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

ログインするときに、グループのメンバーは自分の個人用アクセス トークンを使用しますが、ユーザー名として指定されたグループ名でサインインします。

## <a name="token-validation"></a>トークンの検証

Azure Database for MySQL での Azure AD 認証により、ユーザーが MySQL サーバーに存在することが保証され、トークンの内容を検証することによってトークンの有効性がチェックされます。 次のトークンの検証手順が実行されます。

-   トークンが Azure AD によって署名されていて、改ざんされていないこと
-   トークンがサーバーに関連付けられているテナントの Azure AD によって発行されたこと
-   トークンの有効期限が切れていないこと
-   トークンが (別の Azure リソースではなく) Azure Database for MySQL リソース用であること

## <a name="compatibility-with-application-drivers"></a>アプリケーション ドライバーとの互換性

ほとんどのドライバーはサポートされていますが、パスワードをクリア テキストで送信する設定を使用して、トークンが変更されずに送信されるようにする必要があります。

* C/C++
  * libmysqlclient:サポートされています
  * mysql-connector-c++:サポートされています
* Java
  * Connector/J (mysql-connector-java):サポートされています。`useSSL` 設定を使用する必要があります
* Python
  * Connector/Python:サポートされています
* Ruby
  * mysql2:サポートされています
* .NET
  * mysql-connector-net:サポートされています。mysql_clear_password 用のプラグインを追加する必要があります
  * mysql-net/MySqlConnector:サポートされています
* Node.js
  * mysqljs:サポートされていません (修正プログラムなしでクリア テキストでトークンを送信しません)
  * node-mysql2:サポートされています
* Perl
  * DBD::mysql:サポートされています
  * Net::MySQL:サポートされていません
* Go
  * go-sql-driver:サポートされています。接続文字列に `?tls=true&allowCleartextPasswords=true` を追加してください

## <a name="next-steps"></a>次のステップ

* [Azure Database for MySQL を使用した Azure Active Directory 認証](concepts-azure-ad-authentication.md)の全体的な概念を確認する

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
