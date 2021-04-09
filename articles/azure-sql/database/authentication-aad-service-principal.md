---
title: Azure SQL での Azure Active Directory のサービス プリンシパル
description: Azure AD アプリケーション (サービス プリンシパル) では、Azure SQL Database、Azure SQL Managed Instance、Azure Synapse Analytics での Azure AD ユーザーの作成がサポートされます
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/11/2021
ms.openlocfilehash: 68267cdedd2f0b64549791866e8750cf42928ab4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201242"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Azure SQL での Azure Active Directory のサービス プリンシパル

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure AD アプリケーション (サービスプリンシパル) の代わりに Azure SQL Database (SQL DB) および [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) に Azure Active Directory (Azure AD) ユーザーを作成する機能のサポートは、現在、**パブリック プレビュー** の段階です。

> [!NOTE]
> この機能は、SQL Managed Instance では既にサポートされています。

## <a name="service-principal-azure-ad-applications-support"></a>サービス プリンシパル (Azure AD アプリケーション) のサポート

この記事は、Azure AD に統合されて、Azure AD 登録の一部となったアプリケーションに適用されます。 これらのアプリケーションでは、多くの場合、さまざまなタスクを実行するために、Azure SQL への認証と承認のアクセスが必要になります。 **パブリック プレビュー** のこの機能により、サービス プリンシパルは SQL Database と Azure Synapse に Azure AD ユーザーを作成できるようになりました。 削除された Azure AD アプリケーションの代わりに Azure AD オブジェクトを作成することが許可されない制限がありました。

Azure portal または PowerShell コマンドを使用して Azure AD アプリケーションを登録すると、Azure AD のテナントに 2 つのオブジェクトが作成されます。

- アプリケーション オブジェクト
- サービス プリンシパル オブジェクト

Azure AD アプリケーションの詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../../active-directory/develop/app-objects-and-service-principals.md)」および「[Azure PowerShell を使用して Azure サービス プリンシパルを作成する](/powershell/azure/create-azure-service-principal-azureps)」を参照してください。

SQL Database、Azure Synapse、および SQL Managed Instance では、次の Azure AD オブジェクトがサポートされています。

- Azure AD ユーザー (管理対象、フェデレーション、およびゲスト)
- Azure AD グループ (管理対象とフェデレーション)
- Azure AD アプリケーション 

Azure AD アプリケーションの代わりに T-SQL コマンド `CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER` が、SQL Database と Azure Synapse でサポートされるようになりました。

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>サービス プリンシパルを使用した Azure AD ユーザーの作成機能

この機能のサポートは、ユーザーの介入なしに SQL Database と Azure Synapse で Azure AD オブジェクトを作成および管理する Azure AD アプリケーションの自動化処理で役立ちます。 サービス プリンシパルは、グループのメンバーまたは個々のユーザーとして、SQL 論理サーバーの Azure AD 管理者になることができます。 このアプリケーションは、システム管理者として実行した場合、SQL Database と Azure Synapse での Azure AD オブジェクトの作成を自動化できます。また、追加の SQL 権限は必要ありません。 これにより、データベース ユーザーの作成を完全に自動化できます。 この機能は、システム割り当てマネージド ID とユーザー割り当てマネージド ID でもサポートされています。 詳細については、「[Azure リソースのマネージド ID とは](../../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

## <a name="enable-service-principals-to-create-azure-ad-users"></a>サービス プリンシパルが Azure AD ユーザーを作成できるようにする

Azure AD アプリケーションの代わりに SQL Database と Azure Synapse で Azure AD オブジェクトを作成できるようにするには、次の設定が必要となります。

1. サーバー ID を割り当てます。 割り当てられたサーバー ID は Managed Service Identity (MSI) を表します。 現時点では、Azure SQL のサーバー ID ではユーザー マネージド ID (UMI) がサポートされていません。
    - 新しい Azure SQL 論理サーバーの場合は、次の PowerShell コマンドを実行します。
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    詳細については、[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) コマンドを参照してください。

    - 既存の Azure SQL 論理サーバーの場合は、次のコマンドを実行します。
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    詳細については、[Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver) コマンドを参照してください。

    - サーバー ID がサーバーに割り当てられているかどうかを確認するには、Get AzSqlServer コマンドを実行します。

    > [!NOTE]
    > サーバー ID は CLI コマンドを使用して割り当てることもできます。 詳細については、「[az sql server create](/cli/azure/sql/server#az-sql-server-create)」および「[az sql server update](/cli/azure/sql/server#az-sql-server-update)」を参照してください。

2. サーバーに対して作成したか、割り当てたサーバー ID に Azure AD の [**ディレクトリ閲覧者**](../../active-directory/roles/permissions-reference.md#directory-readers)アクセス許可を付与します。
    - このアクセス許可を付与するには、次の記事に記載されている SQL Managed Instance で使用される説明に従ってください。[Azure AD 管理者 (SQL Managed Instance) をプロビジョニングする](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance)
    - このアクセス許可を付与する Azure AD ユーザーは、Azure AD の **全体管理者** または **特権ロール管理者** ロールに属している必要があります。

> [!IMPORTANT]
> 手順 1 および 2 は、上記の順序で実行する必要があります。 最初にサーバー ID を作成するか、割り当てて、次に [**ディレクトリ閲覧者**](../../active-directory/roles/permissions-reference.md#directory-readers)アクセス許可を付与します。 これらの手順のいずれかまたは両方を省略すると、Azure AD アプリケーションの代わりに Azure SQL で Azure AD オブジェクトを作成するときに実行エラーが発生します。
>
> サービス プリンシパルを使用して Azure AD 管理者を設定または設定解除する場合、アプリケーションには、Azure AD で [Directory.Read.All](/graph/permissions-reference#application-permissions-18) アプリケーション API アクセス許可も必要です。 [Azure AD 管理者を設定するために必要なアクセス許可](authentication-aad-service-principal-tutorial.md#permissions-required-to-set-or-unset-the-azure-ad-admin)の詳細、および Azure AD アプリケーションの代わりに Azure AD ユーザーを作成するための詳細な手順については、「[チュートリアル:Azure AD アプリケーションを使用して Azure AD ユーザーを作成する](authentication-aad-service-principal-tutorial.md)」から)。
>
> **パブリック プレビュー** では、Azure AD 内のグループに **ディレクトリ閲覧者** ロールを割り当てることができます。 グループの所有者は、このグループのメンバーとしてマネージド ID を追加できます。これにより、**グローバル管理者**、または **特権ロール管理者** のニーズがバイパスされ、**ディレクトリ閲覧者** ロールが許可されます。 この機能の詳細については、「[Azure SQL の Azure Active Directory のディレクトリ閲覧者ロール](authentication-aad-directory-readers-role.md)」を参照してください。

## <a name="troubleshooting-and-limitations-for-public-preview"></a>パブリック プレビューのトラブルシューティングと制限

- Azure AD アプリケーションの代わりに Azure SQL で Azure AD オブジェクトを作成するときに、サーバー ID を有効にして **ディレクトリ閲覧者** 権限を付与しなかった場合、次のようなエラーが表示されて操作が失敗します。 次のエラーの例は、SQL Database ユーザー `myapp` を作成するために PowerShell コマンドを実行したときのエラーです (記事「[チュートリアル:Azure AD アプリケーションを使用して Azure AD ユーザーを作成する](authentication-aad-service-principal-tutorial.md)」から)。
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - 上記のエラーが発生した場合は、「[Azure SQL 論理サーバーに ID を割り当てる](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server)」および「[SQL 論理サーバーの ID にディレクトリ閲覧者のアクセス許可を割り当てる](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity)」の手順に従ってください。
    > [!NOTE]
    > 上記のエラー メッセージは、Azure AD アプリケーションのサポートに不足しているセットアップ要件を明確に特定するように、機能 GA の前に変更されます。
- SQL Managed Instance の Azure AD 管理者として Azure AD アプリケーションを設定することは、CLI コマンドで [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) 以上の PowerShell コマンドを使用した場合のみサポートされます。 詳細については、「[az sql mi ad-admin create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create)」および「[Set-AzSqlInstanceActiveDirectoryAdministrato](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator)」コマンドを参照してください。 
    - Azure portal を使用して SQL Managed Instance の Azure AD 管理者を設定する場合、考えられる回避策は Azure AD グループを作成することです。 その後、サービス プリンシパル (Azure AD アプリケーション) をこのグループに追加し、このグループを SQL Managed Instance の Azure AD 管理者として設定します。
    - SQL Database および Azure Synapse の Azure AD 管理者としてサービス プリンシパル (Azure AD アプリケーション) を設定する操作は、Azure portal、[PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)、および [CLI](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse) コマンドを使用した場合にサポートされます。
- 別のテナントで作成された SQL Database または SQL Managed Instance にアクセスする場合、別の Azure AD テナントのサービス プリンシパルの Azure AD アプリケーションを使用すると、失敗します。 このアプリケーションに割り当てるサービス プリンシパルは、SQL 論理サーバーまたは Managed Instance と同じテナントのサービス プリンシパルである必要があります。
- PowerShell を使用して個々の Azure AD アプリケーションを Azure SQL の Azure AD 管理者として設定する場合、[Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) 以降のモジュールが必要です。 最新のモジュールにアップグレードしてください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:Azure AD アプリケーションを使用して Azure AD ユーザーを作成する](authentication-aad-service-principal-tutorial.md)
