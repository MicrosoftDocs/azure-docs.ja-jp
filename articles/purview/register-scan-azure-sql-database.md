---
title: Azure SQL Database の登録とスキャン
description: このチュートリアルでは、Azure SQL Database をスキャンする方法について説明します
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: a708f0c1884073a2f2ea73092d25006415c59d5f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655225"
---
# <a name="register-and-scan-an-azure-sql-database"></a>Azure SQL Database の登録とスキャン

この記事では、Purview に Azure SQL Database データ ソースを登録し、そのスキャンを設定する方法について説明します。

## <a name="supported-capabilities"></a>サポートされる機能

Azure SQL Database データ ソースでは、以下の機能がサポートされています。

- Azure SQL Database のメタデータと分類をキャプチャする **完全および増分スキャン**。

- ADF コピーとデータフロー アクティビティのためのデータ アセット間の **系列**。

### <a name="known-limitations"></a>既知の制限事項

Azure Purview では、Azure SQL Database の[ビュー](/sql/relational-databases/views/views?view=azuresqldb-current&preserve-view=true)のスキャンはサポートされていません。

## <a name="prerequisites"></a>必須コンポーネント

1. Purview アカウントをまだお持ちでない場合は、新たに作成します。

1. Purview アカウントと Azure SQL Database の間のネットワーク アクセス。


### <a name="set-up-authentication-for-a-scan"></a>スキャンの認証の設定

Azure SQL Database をスキャンするための認証。 新しい認証を作成する必要がある場合は、[SQL Database へのデータベース アクセスを承認する](../azure-sql/database/logins-create-manage.md)必要があります。 現在、Purview では、3 つの認証方法がサポートされています。

- SQL 認証
- サービス プリンシパル
- マネージド ID

#### <a name="sql-authentication"></a>SQL 認証

> [!Note]
> サーバーレベル プリンシパルのログイン (準備プロセスで作成) または master データベースの `loginmanager` データベース ロールのメンバーだけが新しいログインを作成できます。 アクセス許可を付与した後、**15 分** ほどかかります。Purview アカウントには、リソースをスキャンできるようにするための適切なアクセス許可が必要です。

Azure SQL Database のログインが使用可能になっていない場合は、「[CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1)」の手順に従って、ログインを作成できます。 次の手順で、**ユーザー名** と **パスワード** が必要になります。

1. Azure portal で、キー コンテナーに移動します
1. **[設定]、[シークレット]** の順に選択します
1. **[+ Generate/Import]\(+ 生成/インポート\)** を選択し、**名前** と **値** を Azure SQL Database の "*パスワード*" として入力します
1. **[作成]** を選択して完了します
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、**ユーザー名** と **パスワード** を使用して [新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します

#### <a name="service-principal-and-managed-identity"></a>サービス プリンシパルとマネージド ID

Purview でサービス プリンシパルまたは Purview の **マネージド ID** を使用して Azure SQL データベースをスキャンできるようにするには、いくつかの手順があります

   > [!Note]
   > Purview でスキャンを行うには、**アプリケーション (クライアント) ID** および **クライアント シークレット** が必要になります。

##### <a name="create-or-use-an-existing-service-principal"></a>サービス プリンシパルを作成するか、既存のものを使用する

> [!Note]
> Purview の **マネージド ID** を使用している場合は、この手順をスキップします

サービス プリンシパルを使用するには、既存のものを使用するか、新しく作成することができます。 

> [!Note]
> 新しいサービス プリンシパルを作成する必要がある場合は、次の手順に従います。
> 1. [Azure Portal](https://portal.azure.com) に移動します。
> 1. 左側のメニューで、 **[Azure Active Directory]** を選択します。
> 1. **[アプリの登録]** を選択します。
> 1. **[+ 新しいアプリケーションの登録]** を選択します。
> 1. **アプリケーション** の名前 (サービス プリンシパル名) を入力します。
> 1. **[この組織のディレクトリ内のアカウントのみ]** を選択します。
> 1. [リダイレクト URI] には **[Web]** を選択し、必要な URL を入力します。これは、実際の URL でなくてもよく、機能しなくてもかまいません。
> 1. 次に、 **[登録]** を選択します。

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>データベース アカウントで Azure AD 認証を構成する

サービス プリンシパルまたはマネージド ID には、データベース、スキーマ、およびテーブルのメタデータを取得するためのアクセス許可が必要です。 また、分類用のサンプリングを行うために、テーブルに対してクエリを実行できる必要もあります。

- [Azure SQL での Azure AD 認証を構成して管理する](../azure-sql/database/authentication-aad-configure.md)
- マネージド ID を使用している場合、Purview アカウントには独自のマネージド ID があります。これは基本的に、作成時の Purview の名前です。 「[Azure SQL Database にサービス プリンシパル ユーザーを作成する](../azure-sql/database/authentication-aad-service-principal-tutorial.md#create-the-service-principal-user-in-azure-sql-database)」のチュートリアルに従い、Purview の正確なマネージド ID または独自のサービス プリンシパルを使用して、Azure SQL Database に Azure AD ユーザーを作成する必要があります。 ID に適切なアクセス許可 ( `db_owner` または `db_datareader`) を割り当てる必要があります。 ユーザーを作成してアクセス許可を付与する SQL 構文の例:

    ```sql
    CREATE USER [Username] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [Username]
    GO
    ```

    > [!Note]
    > `Username` は、独自のサービス プリンシパルか、Purview のマネージド ID です。 [固定データベース ロールとその機能](/sql/relational-databases/security/authentication-access/database-level-roles?view=sql-server-ver15&preserve-view=true#fixed-database-roles)を確認してください。
    
##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>キー コンテナーおよび Purview の資格情報にサービス プリンシパルを追加する

> [!Note]
> Purview の **マネージド ID** の使用を計画している場合は、Purview の既定のマネージド ID が既に **Purview-MSI** 資格情報に含まれているため、この手順は省略できます。

サービス プリンシパルのアプリケーション ID とシークレットを取得する必要があります。

1. [Azure portal](https://portal.azure.com) で自分のサービス プリンシパルに移動します
1. **[概要]** から **[アプリケーション (クライアント) ID]** 、 **[証明書とシークレット]** から **[クライアント シークレット]** の値をコピーします。
1. お使いのキー コンテナーに移動する
1. **[設定] > [シークレット]** の順に選択します。
1. **[生成/インポート]** を選択し、サービス プリンシパルの **クライアント シークレット** として任意の **名前** と **値** を入力します
1. **[作成]** を選択して完了します。
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、サービス プリンシパルを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します

### <a name="firewall-settings"></a>ファイアウォールの設定

データベース サーバーで Azure 接続の有効化を許可する必要があります。 これにより、Azure Purview はサーバーにアクセスして接続できるようになります。 [Azure 内からの接続](../azure-sql/database/firewall-configure.md#connections-from-inside-azure)については、ハウツー ガイドに従ってください。

1. データベース アカウントに移動します
1. **[概要]** ページでサーバー名を選択します
1. **[セキュリティ]、[ファイアウォールと仮想ネットワーク]** の順に選択します
1. **[Azure サービスおよびリソースにこのサーバーへのアクセスを許可する]** で **[はい]** を選択します

    :::image type="content" source="media/register-scan-azure-sql-database/sql-firewall.png" alt-text="Azure サービスおよびリソースにこのサーバーへのアクセスを許可します。" border="true":::
    
> [!Note]
> 現在、Azure Purview では VNET 構成はサポートされていません。 そのため、IP ベースのファイアウォール設定を行うことはできません。

## <a name="register-an-azure-sql-database-data-source"></a>Azure SQL Database データ ソースの登録

新しい Azure SQL Database をデータ カタログに登録するには、次の手順を実行します。

1. 自分の Purview アカウントに移動します

1. 左側のナビゲーションで **[ソース]** を選択します。

1. **[登録]** を選択します

1. **[Register sources]\(ソースの登録\)** で、 **[Azure SQL Database]** を選択します。 **[続行]** をクリックします。

:::image type="content" source="media/register-scan-azure-sql-database/register-new-data-source.png" alt-text="新しいデータ ソースを登録する" border="true":::

**[Register sources (Azure SQL Database)]\(ソースの登録 (Azure SQL Database)\)** 画面で、以下を実行します。

1. データ ソースがカタログに表示される際の **名前** を入力します。
1. **[From Azure subscription]\(Azure サブスクリプションから\)** を選択し、 **[Azure subscription]\(Azure サブスクリプション\)** ドロップダウン ボックスから適切なサブスクリプション、 **[Server name]\(サーバー名\)** ドロップダウン ボックスから適切なサーバーを選択します。
1. **[完了]** を選択して、データ ソースを登録します。

:::image type="content" source="media/register-scan-azure-sql-database/add-azure-sql-database.png" alt-text="ソースの登録のオプション" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> * スキャンを削除しても、以前の Azure SQL Database スキャンからアセットは削除されません。
> * Purview の [スキーマ] タブの説明を編集した後に、ソース テーブルが変更され、ソース テーブルを再スキャンした場合、アセットはスキーマの変更によって更新されなくなります。

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)
