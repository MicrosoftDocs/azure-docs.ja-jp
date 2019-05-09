---
title: Windows VM のシステム割り当てマネージド ID を使用して Azure SQL にアクセスする
description: Windows VM のシステム割り当てマネージド ID を使用して Azure SQL にアクセスするプロセスについて説明するチュートリアルです。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: bryanla
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6d5452f23e830ca7a9ffe5ca5ed3d4aa12fb717
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444665"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>チュートリアル:Windows VM のシステム割り当てマネージド ID を使用して Azure SQL にアクセスする

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Windows 仮想マシン (VM) のシステム割り当て ID を使用して Azure SQL サーバーにアクセスする方法について説明します。 管理対象サービス ID は Azure によって自動的に管理され、資格情報をコードに挿入しなくても、Azure AD 認証をサポートするサービスへの認証を有効にします。 学習内容は次のとおりです。

> [!div class="checklist"]
> * VM に Azure SQL サーバーへのアクセスを許可します。
> * SQL サーバーに対する Azure AD 認証を有効にする
> * VM のシステム割り当て ID を表す包含ユーザーをデータベースに作成する
> * VM ID を使用してアクセス トークンを取得し、それを使用して Azure SQL サーバーにクエリを実行する

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>VM に Azure SQL サーバー内のデータベースへのアクセス権を付与する

Azure SQL Server 内のデータベースに対するアクセス権を VM に付与する際は、既存の SQL サーバーを使用する方法と、SQL サーバーを新しく作成する方法とがあります。 Azure ポータルを使用して新しいサーバーとデータベースを作成するには、[Azure SQL のクイック スタート](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)に従います。 [Azure SQL のドキュメント](https://docs.microsoft.com/azure/sql-database/)に、Azure CLI と Azure PowerShell を使用するクイックスタートも用意されています。

VM にデータベースへのアクセス権を付与するには次の 2 つの手順があります。

1. SQL サーバーに対する Azure AD 認証を有効にします。
2. VM のシステム割り当て ID を表す**包含ユーザー**をデータベースに作成します。

## <a name="enable-azure-ad-authentication-for-the-sql-server"></a>SQL サーバーに対する Azure AD 認証を有効にする

次の手順を使用して、[SQL サーバーに対する Azure AD 認証を構成](/azure/sql-database/sql-database-aad-authentication-configure)します。

1.  Azure ポータルで、左側のナビゲーションから **[SQL サーバー]** を選択します。
2.  Azure AD 認証で有効にする SQL サーバーをクリックします。
3.  ブレードの **[設定]** セクションで **[Active Directory 管理者]** をクリックします。
4.  コマンド バーで、**[管理者の設定]** をクリックします。
5.  サーバーの管理者になる Azure AD ユーザー アカウントを選択し、**[選択]** をクリックします。
6.  コマンド バーで、**[保存]** をクリックします。

## <a name="create-a-contained-user-in-the-database-that-represents-the-vms-system-assigned-identity"></a>VM のシステム割り当て ID を表す包含ユーザーをデータベースに作成する

この次の手順では、[Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) が必要になります。 始める前に、Azure AD 統合の背景について次の記事で確認しておくことも有益です。

- [SQL Database と SQL Data Warehouse でのユニバーサル認証 (MFA 対応の SSMS サポート)](/azure/sql-database/sql-database-ssms-mfa-authentication)
- [SQL Database または SQL Data Warehouse で Azure Active Directory 認証を構成して管理する](/azure/sql-database/sql-database-aad-authentication-configure)

SQL DB では、一意の AAD 表示名が必要です。 これにより、ユーザー、グループ、サービス プリンシパル (アプリケーション)、およびマネージド ID 用に有効化された VM 名などの AAD アカウントの表示名を、AAD で一意に定義する必要があります。 SQL DB では、このようなユーザーの T-SQL の作成時に AAD 表示名がチェックされ、一意でない場合には、指定したアカウントに一意の AAD 表示名を指定するように要求するコマンドが失敗します。

1. SQL Server Management Studio を起動します。
2. **[サーバーに接続]** ダイアログで、**[サーバー名]** フィールドに SQL サーバーの名前を入力します。
3. **[認証]** フィールドで、**[Active Directory - MFA サポートで汎用]** を選択します。
4. **[ユーザー名]** フィールドに、サーバー管理者として設定した Azure AD アカウントの名前を入力します (例: helen@woodgroveonline.com)。
5. **[オプション]** をクリックします。
6. **[データベースに接続]** フィールドに、構成する非システム データベースの名前を入力します。
7. **[接続]** をクリックします。 サインイン プロセスを完了します。
8. **オブジェクト エクスプローラー**で、**[データベース]** フォルダーを展開します。
9. ユーザー データベースを右クリックし、**[新しいクエリ]** をクリックします。
10. クエリ ウィンドウで、次の行を入力し、ツールバーの **[実行]** をリックします。

    > [!NOTE]
    > 次のコマンドの `VMName` は、前提条件のセクションでシステム割り当て ID を有効にした VM の名前です。
    
    ```
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```
    
    コマンドは正常に完了し、VM のシステム割り当て ID の包含ユーザーが作成されます。
11. クエリ ウィンドウをクリアし、次の行を入力し、ツールバーの **[実行]** をリックします。

    > [!NOTE]
    > 次のコマンドの `VMName` は、前提条件のセクションでシステム割り当て ID を有効にした VM の名前です。
    
    ```
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    コマンドは正常に完了し、包含ユーザーにデータベース全体を読み取る権限が与えられます。

これで、VM 上で実行されるコードは、システム割り当てマネージド ID を使用してトークンを取得し、そのトークンを使用して SQL サーバーへの認証を行うようになりました。

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-sql"></a>VM のシステム割り当てマネージド ID を使用してアクセス トークンを取得し、それを使用して Azure SQL を呼び出す

Azure SQL は Azure AD 認証をネイティブにサポートするため、Azure リソースのマネージド ID を使用して取得されたアクセス トークンを直接受け入れることができます。 SQL への接続を作成する**アクセス トークン** メソッドを使用します。 これは Azure SQL の Azure AD との統合の一部であり、接続文字列に資格情報を提供することとは異なります。

アクセス トークンを使用して SQL への接続を開く .NET のコード例を次に示します。 このコードは、VM のシステム割り当てマネージド ID のエンドポイントにアクセスできる VM 上で実行する必要があります。 アクセス トークン メソッドを使用するには、**.NET Framework 4.6** 以降が必要です。 AZURE-SQL-SERVERNAME と DATABASE の値を適切な値に置き換えます。 Azure SQL のリソース ID が `https://database.windows.net/` であることにご注意ください。

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
// Open a connection to the SQL server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

または、アプリの記述と VM へのデプロイなしでエンド ツー エンドの設定をテストする簡単な方法は、PowerShell を使用することです。

1.  ポータルで **[Virtual Machines]** にナビゲートして Windows 仮想マシンに移動し、**[概要]** の **[接続]** をクリックします。
2.  Windows VM を作成したときに追加した**ユーザー名**と**パスワード**を入力します。
3.  これで、仮想マシンを使用する**リモート デスクトップ接続**が作成されました。リモート セッションで **PowerShell** を開きます。
4.  Powershell の `Invoke-WebRequest` を使用して、ローカルのマネージド ID のエンドポイントに Azure SQL のアクセス トークンを取得するよう要求します。

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

5. SQL サーバーへの接続を開きます。 AZURE-SQL-SERVERNAME と DATABASE の値を置き換えることを忘れないでください。
    
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

## <a name="next-steps"></a>次の手順

このチュートリアルでは、システム割り当てマネージド ID を使用して Azure SQL サーバーにアクセスする方法について説明しました。 Azure SQL Server の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [Azure SQL Database サービス](/azure/sql-database/sql-database-technical-overview)
