---
title: Azure SQL Database Managed Instance の登録とスキャン
description: このチュートリアルでは、Azure SQL Database Managed Instance をスキャンする方法について説明します
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: a30980ba61a1dfec918dce1a55e78f1be2a36dd7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677849"
---
# <a name="register-and-scan-an-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance の登録とスキャン

この記事では、Purview に Azure SQL Database Managed Instance データ ソースを登録し、そのスキャンを設定する方法について説明します。

## <a name="supported-capabilities"></a>サポートされる機能

Azure SQL Database Managed Instance データ ソースでは、以下の機能がサポートされています。

- Azure SQL Database Managed Instance のメタデータと分類をキャプチャする **完全および増分スキャン**。

- ADF コピーとデータフロー アクティビティのためのデータ アセット間の **系列**。

### <a name="known-limitations"></a>既知の制限事項

Azure Purview では、Azure SQL Managed Instance の[ビュー](/sql/relational-databases/views/views?view=azuresqldb-mi-current&preserve-view=true)のスキャンはサポートされていません。

## <a name="prerequisites"></a>必須コンポーネント

- Purview アカウントをまだお持ちでない場合は、新たに作成します。

- [Azure SQL マネージド インスタンスのパブリック エンドポイントを構成する](../azure-sql/managed-instance/public-endpoint-configure.md)
    > [!Note]
    > Purview では **プライベート エンドポイントはまだサポートされていない** ため、組織はパブリック エンドポイントを許可できる必要があります。 プライベート エンドポイントを使用する場合、スキャンは正常に実行されません。

### <a name="setting-up-authentication-for-a-scan"></a>スキャンの認証の設定

Azure SQL Database Managed Instance をスキャンするための認証。 新しい認証を作成する必要がある場合は、[SQL Database Managed Instance へのデータベース アクセスを承認する](../azure-sql/database/logins-create-manage.md)必要があります。 現在、Purview では、3 つの認証方法がサポートされています。

- SQL 認証
- サービス プリンシパル
- マネージド ID

#### <a name="sql-authentication"></a>SQL 認証

> [!Note]
> サーバーレベル プリンシパルのログイン (準備プロセスで作成) または master データベースの `loginmanager` データベース ロールのメンバーだけが新しいログインを作成できます。 アクセス許可を付与した後、**15 分** ほどかかります。Purview アカウントには、リソースをスキャンできるようにするための適切なアクセス許可が必要です。

Azure SQL Database Managed Instance のログインが使用可能になっていない場合は、「[CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1)」の手順に従って、ログインを作成できます。 次の手順で、**ユーザー名** と **パスワード** が必要になります。

1. Azure portal で、キー コンテナーに移動します
1. **[設定]、[シークレット]** の順に選択します
1. **[+ Generate/Import]\(+ 生成/インポート\)** を選択し、**名前** と **値** を Azure SQL Database Managed Instance の "*パスワード*" として入力します
1. **[作成]** を選択して完了します
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、**ユーザー名** と **パスワード** を使用して [新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します

#### <a name="service-principal-and-managed-identity"></a>サービス プリンシパルとマネージド ID

Purview でサービス プリンシパルを使用して Azure SQL Database Managed Instance をスキャンできるようにするには、いくつかの手順があります

##### <a name="create-or-use-an-existing-service-principal"></a>サービス プリンシパルを作成するか、既存のものを使用する

> [!Note]
> **マネージド ID** を使用している場合は、この手順をスキップします

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
- 「[Azure AD ID にマップされる包含ユーザーを作成する](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities)」の前提条件とチュートリアルに従って、Azure SQL Database Managed Instance に Azure AD ユーザーを作成する
- `db_owner` (**推奨**) アクセス許可を ID に割り当てる

##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>キー コンテナーおよび Purview の資格情報にサービス プリンシパルを追加する

> [!Note]
> **マネージド ID** の使用を計画している場合は、既定の Purview ID が既に **Purview-MSI** に含まれているため、この手順は省略できます

サービス プリンシパルのアプリケーション ID とシークレットを取得する必要があります。

1. [Azure portal](https://portal.azure.com) で自分のサービス プリンシパルに移動します
1. **[アプリケーション (クライアント) ID]** の値を **[概要]** から、および **[クライアント シークレット]** の値を **[証明書とシークレット]** からコピーします。
1. お使いのキー コンテナーに移動する
1. **[設定] > [シークレット]** の順に選択します。
1. **[生成/インポート]** を選択し、サービス プリンシパルの **クライアント シークレット** として任意の **名前** と **値** を入力します
1. **[作成]** を選択して完了します。
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、サービス プリンシパルを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します

## <a name="register-an-azure-sql-database-managed-instance-data-source"></a>Azure SQL Database Managed Instance データ ソースの登録

1. 自分の Purview アカウントに移動します

1. 左側のナビゲーションで **[ソース]** を選択します。

1. **[登録]** を選択します

1. **[Azure SQL Database Managed Instance]** 、 **[続行]** の順に選択します

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-the-sql-data-source.png" alt-text="SQL データ ソースを設定する":::

1. **[From Azure subscription]\(Azure サブスクリプションから\)** を選択し、 **[Azure subscription]\(Azure サブスクリプション\)** ドロップダウン ボックスから適切なサブスクリプション、 **[Server name]\(サーバー名\)** ドロップダウン ボックスから適切なサーバーを選択します。

1. **パブリック エンドポイントの完全修飾ドメイン名** および **ポート番号** を指定します。 **[完了]** を選択して、データ ソースを登録します。

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/add-azure-sql-database-managed-instance.png" alt-text="Azure SQL Database Managed Instance を追加する":::

    例: `foobar.public.123.database.windows.net,3342`

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> スキャンを削除しても、以前の Azure SQL Database Managed Instance スキャンからアセットは削除されません。

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)