---
title: チュートリアル:マネージド ID を使用して Azure SQL Database にアクセスする - Windows - Azure AD
description: Windows VM のシステム割り当てマネージド ID を使用して Azure SQL Database にアクセスするプロセスについて説明するチュートリアルです。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4f56ce9fa86dc27b77ad6b463479d13c8e4e7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91856514"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>チュートリアル:Windows VM のシステム割り当てマネージド ID を使用して Azure SQL にアクセスする

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Windows 仮想マシン (VM) のシステム割り当て ID を使用して Azure SQL Database にアクセスする方法について説明します。 管理対象サービス ID は Azure によって自動的に管理され、資格情報をコードに挿入しなくても、Azure AD 認証をサポートするサービスへの認証を有効にします。 学習内容は次のとおりです。

> [!div class="checklist"]
>
> * VM に Azure SQL Database へのアクセスを許可する
> * Azure AD 認証を有効にする
> * VM のシステム割り当て ID を表す包含ユーザーをデータベースに作成する
> * VM ID を使用してアクセス トークンを取得し、それを使用して Azure SQL Database にクエリを実行する

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="enable"></a>有効化

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]

## <a name="grant-access"></a>アクセス権の付与

Azure SQL Database 内のデータベースに対するアクセス権を VM に付与する際は、既存の[論理 SQL サーバー](../../azure-sql/database/logical-servers.md)を使用する方法と、論理 SQL サーバーを新しく作成する方法とがあります。 Azure ポータルを使用して新しいサーバーとデータベースを作成するには、[Azure SQL のクイック スタート](../../azure-sql/database/single-database-create-quickstart.md)に従います。 [Azure SQL のドキュメント](/azure/sql-database/)に、Azure CLI と Azure PowerShell を使用するクイックスタートも用意されています。

VM にデータベースへのアクセス権を付与するには次の 2 つの手順があります。

1. サーバーに対する Azure AD 認証を有効にします。
2. VM のシステム割り当て ID を表す **包含ユーザー** をデータベースに作成します。

### <a name="enable-azure-ad-authentication"></a>Azure AD 認証を有効にする

**[Azure AD 認証を構成するには](../../azure-sql/database/authentication-aad-configure.md):**

1. Azure ポータルで、左側のナビゲーションから **[SQL サーバー]** を選択します。
2. Azure AD 認証で有効にする SQL サーバーをクリックします。
3. ブレードの **[設定]** セクションで **[Active Directory 管理者]** をクリックします。
4. コマンド バーで、 **[管理者の設定]** をクリックします。
5. サーバーの管理者になる Azure AD ユーザー アカウントを選択し、 **[選択]** をクリックします。
6. コマンド バーで、 **[保存]** をクリックします。

### <a name="create-contained-user"></a>包含ユーザーを作成する

このセクションでは、VM のシステム割り当て ID を表す包含ユーザーをデータベースに作成する方法を説明します。 このステップでは、[Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) が必要になります。 始める前に、Azure AD 統合の背景について次の記事で確認しておくことも有益です。

- [SQL Database と Azure Synapse Analytics を使用したユニバーサル認証 (SSMS での MFA のサポート)](../../azure-sql/database/authentication-mfa-ssms-overview.md)
- [SQL Database または Azure Synapse Analytics を使用した Azure Active Directory 認証の構成と管理](../../azure-sql/database/authentication-aad-configure.md)

SQL DB では、一意の AAD 表示名が必要です。 このため、ユーザー、グループ、サービス プリンシパル (アプリケーション)、およびマネージド ID 用に有効化された VM 名などの AAD アカウントの表示名は、AAD 内で一意に定義する必要があります。 SQL DB では、このようなユーザーの T-SQL の作成時に AAD 表示名がチェックされ、一意でない場合には、指定したアカウントに一意の AAD 表示名を指定するように要求するコマンドが失敗します。

**包含ユーザーを作成するには:**

1. SQL Server Management Studio を起動します。
2. **[サーバーに接続]** ダイアログで、 **[サーバー名]** フィールドにサーバーの名前を入力します。
3. **[認証]** フィールドで、 **[Active Directory - MFA サポートで汎用]** を選択します。
4. **[ユーザー名]** フィールドに、サーバー管理者として設定した Azure AD アカウントの名前を入力します (例: helen@woodgroveonline.com)。
5. **[オプション]** をクリックします。
6. **[データベースに接続]** フィールドに、構成する非システム データベースの名前を入力します。
7. **[Connect]** をクリックします。 サインイン プロセスを完了します。
8. **オブジェクト エクスプローラー** で、 **[データベース]** フォルダーを展開します。
9. ユーザー データベースを右クリックし、 **[新しいクエリ]** をクリックします。
10. クエリ ウィンドウで、次の行を入力し、ツールバーの **[実行]** をリックします。

    > [!NOTE]
    > 次のコマンドの `VMName` は、前提条件のセクションでシステム割り当て ID を有効にした VM の名前です。

    ```sql
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```

    コマンドは正常に完了し、VM のシステム割り当て ID の包含ユーザーが作成されます。
11. クエリ ウィンドウをクリアし、次の行を入力し、ツールバーの **[実行]** をリックします。

    > [!NOTE]
    > 次のコマンドの `VMName` は、前提条件のセクションでシステム割り当て ID を有効にした VM の名前です。

    ```sql
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    コマンドは正常に完了し、包含ユーザーにデータベース全体を読み取る権限が与えられます。

これで、VM 上で実行されるコードは、システム割り当てマネージド ID を使用してトークンを取得し、そのトークンを使用してサーバーへの認証を行うようになりました。

## <a name="access-data"></a>データにアクセスする

このセクションでは、VM のシステム割り当てマネージド ID を使用してアクセス トークンを取得し、それを使用して Azure SQL を呼び出す方法を説明します。 Azure SQL は Azure AD 認証をネイティブにサポートするため、Azure リソースのマネージド ID を使用して取得されたアクセス トークンを直接受け入れることができます。 SQL への接続を作成する **アクセス トークン** メソッドを使用します。 これは Azure SQL の Azure AD との統合の一部であり、接続文字列に資格情報を提供することとは異なります。

アクセス トークンを使用して SQL への接続を開く .NET のコード例を次に示します。 このコードは、VM のシステム割り当てマネージド ID のエンドポイントにアクセスできる VM 上で実行する必要があります。 アクセス トークン メソッドを使用するには、 **.NET Framework 4.6** 以降または **.NET Core 2.2** 以降が必要です。 AZURE-SQL-SERVERNAME と DATABASE の値を適切な値に置き換えます。 Azure SQL のリソース ID が `https://database.windows.net/` であることにご注意ください。

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://database.windows.net/");
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
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

または、アプリの記述と VM へのデプロイなしでエンド ツー エンドの設定をテストする簡単な方法は、PowerShell を使用することです。

1. ポータルで **[Virtual Machines]** にナビゲートして Windows 仮想マシンに移動し、 **[概要]** の **[接続]** をクリックします。
2. Windows VM を作成したときに追加した **ユーザー名** と **パスワード** を入力します。
3. これで、仮想マシンを使用する **リモート デスクトップ接続** が作成されました。リモート セッションで **PowerShell** を開きます。
4. Powershell の `Invoke-WebRequest` を使用して、ローカルのマネージド ID のエンドポイントに Azure SQL のアクセス トークンを取得するよう要求します。

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```

    応答を JSON オブジェクトから PowerShell オブジェクトに変換します。

    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    応答からアクセス トークンを抽出します。

    ```powershell
    $AccessToken = $content.access_token
    ```

5. サーバーへの接続を開きます。 AZURE-SQL-SERVERNAME と DATABASE の値を置き換えることを忘れないでください。

    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    次に、クエリを作成してサーバーに送信します。 TABLE の値を必ず置き換えてください。

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

`$DataSet.Tables[0]` の値を調べて、クエリの結果を確認します。

## <a name="disable"></a>Disable

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、システム割り当てマネージド ID を使用して Azure SQL Database にアクセスする方法について説明しました。 Azure SQL Database の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md)
