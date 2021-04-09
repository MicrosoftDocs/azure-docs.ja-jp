---
title: マネージド ID を使用して接続する - Azure Database for MySQL
description: Azure Database for MySQL での認証のための、マネージド ID を使用した接続および認証の方法について説明します。
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 898f568d285c3b4b4360ea4dda8c63a1e0f13a42
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091799"
---
# <a name="connect-with-managed-identity-to-azure-database-for-mysql"></a>マネージド ID を使用して Azure Database for MySQL に接続する

この記事では、Azure 仮想マシン (VM) のユーザー割り当て ID を使用して、Azure Database for MySQL サーバーにアクセスする方法について説明します。 管理対象サービス ID は Azure によって自動的に管理され、資格情報をコードに挿入しなくても、Azure AD 認証をサポートするサービスへの認証を有効にします。 

学習内容は次のとおりです。

- VM に Azure Database for MySQL サーバーへのアクセスを許可する
- VM のユーザー割り当て ID を表すユーザーをデータベースに作成する
- VM ID を使用してアクセス トークンを取得し、それを使用して Azure Database for MySQL サーバーにクエリを実行する
- C# サンプル アプリケーションにトークン取得を実装する

> [!IMPORTANT]
> マネージド ID を使用した接続は、MySQL 5.7 以降でのみ使用できます。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID 機能に慣れていない場合は、こちらの[概要](../../articles/active-directory/managed-identities-azure-resources/overview.md)を参照してください。 Azure アカウントをお持ちでない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- 必要なリソース作成およびロール管理を実行するため、お使いのアカウントには、適切な範囲 (サブスクリプションまたはリソース グループ) を対象とする "所有者" アクセス許可が必要となります。 ロールの割り当てに関するサポートが必要な場合は、[Azure ロールの割り当てによる Azure サブスクリプション リソースへのアクセスの管理](../../articles/role-based-access-control/role-assignments-portal.md)に関するページをご覧ください。
- マネージド ID を使用したデータベースへのアクセスに使用する Azure VM (Ubuntu Linux を実行しているものなど) が必要です
- [Azure AD 認証](howto-configure-sign-in-azure-ad-authentication.md)が構成されている Azure Database for MySQL データベース サーバーが必要です
- C# のサンプルを実行するため、[C# を使用した接続](connect-csharp.md)方法に関するガイドにまず目を通している

## <a name="creating-a-user-assigned-managed-identity-for-your-vm"></a>VM のユーザー割り当てマネージド ID を作成する

[az identity create](/cli/azure/identity#az-identity-create) コマンドを使用して、サブスクリプション内に ID を作成します。 使用している仮想マシンが実行されているのと同じリソース グループを使用することも、別のものを使用することもできます。

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myManagedIdentity
```

後の手順で ID を構成するために、[az identity show](/cli/azure/identity#az-identity-show) コマンドを使用して、ID のリソース ID とクライアント ID を変数に格納します。

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query id --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query clientId --output tsv)
```

これで、[az vm identity assign](/cli/azure/vm/identity#az-vm-identity-assign) コマンドを使用して、ユーザー割り当て ID を VM に割り当てることができるようになりました。

```azurecli
az vm identity assign --resource-group myResourceGroup --name myVM --identities $resourceID
```

セットアップを完了するため、クライアント ID の値を表示します。この値は、この後のいくつかの手順で必要になります。

```bash
echo $clientID
```

## <a name="creating-a-mysql-user-for-your-managed-identity"></a>マネージド ID の MySQL ユーザーを作成する

ここで、Azure AD 管理者ユーザーとして MySQL データベースに接続し、次の SQL ステートメントを実行します。

```sql
SET aad_auth_validate_oids_in_tenant = OFF;
CREATE AADUSER 'myuser' IDENTIFIED BY 'CLIENT_ID';
```

これで、マネージド ID に、ユーザー名 `myuser` (任意の名前に置き換えてください) を使用して認証するときのアクセス権が付与されました。

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>Azure Instance Metadata Service からアクセス トークンを取得する

この段階で、アプリケーションは Azure Instance Metadata Service からアクセス トークンを取得して、データベースでの認証に使用できるようになっています。

このトークンの取得は、`http://169.254.169.254/metadata/identity/oauth2/token` に対して HTTP 要求を実行し、次のパラメーターを渡すことによって行われます。

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID` (前の手順で取得したもの)

`access_token` フィールドを含む JSON の結果が返されます。この長いテキスト値がマネージド ID アクセス トークンで、データベースに接続する際にこれをパスワードとして使用する必要があります。

テストのために、シェルで次のコマンドを実行できます。 `curl`、`jq`、`mysql` クライアントがインストールされている必要があることにご注意ください。

```bash
# Retrieve the access token
accessToken=$(curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token)

# Connect to the database
mysql -h SERVER --user USER@SERVER --enable-cleartext-plugin --password=$accessToken
```

これで、先ほど構成したデータベースに接続できました。

## <a name="connecting-using-managed-identity-in-c"></a>C# でマネージド ID を使用して接続する

このセクションでは、VM のユーザー割り当てマネージド ID を使用してアクセス トークンを取得し、それを使用して Azure Database for MySQL を呼び出す方法を説明します。 Azure Database for MySQL は Azure AD 認証をネイティブにサポートするため、Azure リソース用マネージド ID を使用して取得されたアクセス トークンを直接受け入れることができます。 MySQL への接続を作成する場合は、"パスワード" フィールドにアクセス トークンを渡します。

アクセス トークンを使用して MySQL への接続を開く .NET のコード例を次に示します。 このコードは、VM のユーザー割り当てマネージド ID のエンドポイントにアクセスするため、VM 上で実行する必要があります。 アクセス トークン メソッドを使用するには、.NET Framework 4.6 以降または .NET Core 2.2 以降が必要です。 HOST、USER、DATABASE、CLIENT_ID の値は置き換えてご使用ください。

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using System.Threading.Tasks;
using MySql.Data.MySqlClient;

namespace Driver
{
    class Script
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "HOST";
        private static string User = "USER";
        private static string Database = "DATABASE";
        private static string ClientId = "CLIENT_ID";

        static async Task Main(string[] args)
        {
            //
            // Get an access token for MySQL.
            //
            Console.Out.WriteLine("Getting access token from Azure Instance Metadata service...");

            // Azure AD resource ID for Azure Database for MySQL is https://ossrdbms-aad.database.windows.net/
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=" + ClientId);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";
            string accessToken = null;

            try
            {
                // Call managed identities for Azure resources endpoint.
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader and extract access token.
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                var list = JsonSerializer.Deserialize<Dictionary<string, string>>(stringResponse);
                accessToken = list["access_token"];
            }
            catch (Exception e)
            {
                Console.Out.WriteLine("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                System.Environment.Exit(1);
            }

            //
            // Open a connection to the MySQL server using the access token.
            //
            var builder = new MySqlConnectionStringBuilder
            {
                Server = Host,
                Database = Database,
                UserID = User,
                Password = accessToken,
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "SELECT VERSION()";

                    using (var reader = await command.ExecuteReaderAsync())
                    {
                        while (await reader.ReadAsync())
                        {
                            Console.WriteLine("\nConnected!\n\nMySQL version: {0}", reader.GetString(0));
                        }
                    }
                }
            }
        }
    }
}
```

このコマンドを実行すると、次のような出力が表示されます。

```
Getting access token from Azure Instance Metadata service...
Opening connection using access token...

Connected!

MySQL version: 5.7.27
```

## <a name="next-steps"></a>次のステップ

* [Azure Database for MySQL を使用した Azure Active Directory 認証](concepts-azure-ad-authentication.md)の全体的な概念を確認する
