---
title: Azure SSIS Integration Runtime に対する AAD の有効化
description: この記事では、Azure-SSIS 統合ランタイムを作成するために、Azure Data Factory のマネージド ID を使用して Azure Active Directory 認証を有効にする方法について説明します。
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 07/09/2020
ms.openlocfilehash: cd3f590e1869b28f0ac08ce98da32a98160e4e86
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392733"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムに対して Azure Active Directory 認証を有効にする

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、Azure Active Directory (Azure AD) 認証と Azure Data Factory (ADF) のマネージド ID を有効にし、従来の認証方法 (SQL 認証など) の代わりにそれを使用して以下を行う方法について説明します。

- お客様に代わって SQL Database または SQL Managed Instance で SSIS カタログ データベース (SSISDB) をプロビジョニングする Azure SSIS Integration Runtime (IR) を作成します。

- Azure SSIS IR で SSIS パッケージを実行するときに、さまざまな Azure リソースに接続します。

ADF のマネージド ID について詳しくは、「[Data Factory のマネージド ID](./data-factory-service-identity.md)」をご覧ください。

> [!NOTE]
>
> - このシナリオで、ADF のマネージド ID を使用した　Azure AD 認証は、SSIS IR の作成とそのあとの開始操作でのみ使用されます。それにより SSISDB のプロビジョニングと接続が行われます。 SSIS パッケージの実行では、SSIS IR はまだ、SSIDB のプロビジョニング中に作成されるフル マネージド アカウントで SQL 認証を使用して SSISDB に接続します。
> - SQL 認証を使用して SSIS IR を既に作成している場合、現時点では、PowerShell を介して Azure AD 認証を使用するように再構成することはできませんが、Azure portal/ADF アプリを介して行うことは可能です。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Azure SQL Database で Azure AD を有効にする

SQL Database は、Azure AD ユーザーを使用したデータベースの作成をサポートしています。 最初に、ADF のマネージド ID をメンバーとして使用して Azure AD グループを作成する必要があります。 次に、SQL Database の Active Directory 管理者として Azure AD ユーザーを設定してから、そのユーザーを使用して SQL Server Management Studio (SSMS) でそれに接続する必要があります。 最後に、Azure-SSIS IR が ADF のマネージド ID を使用してユーザーに代わって SSISDB を作成できるように、Azure AD グループを表す包含ユーザーを作成する必要があります。

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>ADF のマネージド ID をメンバーとして使用して Azure AD グループを作成する

既存の Azure AD グループを使用するか、または Azure AD PowerShell を使用して新しいグループを作成できます。

1.  [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) モジュールをインストールします。

2.  `Connect-AzureAD` を使用してサインインし、次のコマンドレットを実行してグループを作成し、それを変数に保存します。

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    結果は次の例のようになります。ここには、変数値も表示されます。

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  ADF のマネージド ID をグループに追加します。 「[Data Factory のマネージド ID](./data-factory-service-identity.md)」の記事に従って、プリンシパル マネージド ID オブジェクト ID (たとえば 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc ですが、この目的にマネージド ID アプリケーション ID は使用しないでください) を取得できます。

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    後でグループ メンバーシップを確認することもできます。

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-sql-database"></a>SQL Database に対する Azure AD 認証を構成する

次の手順を使用して、[SQL による Azure AD 認証の構成と管理](../azure-sql/database/authentication-aad-configure.md)を行うことができます。

1.  Azure Portal で、左側のナビゲーションから **[すべてのサービス]**  ->  **[SQL Server]** を選択します。

2.  Azure AD 認証を構成する SQL Database 内のサーバーを選択します。

3.  ブレードの **[設定]** セクションで **[Active Directory 管理者]** を選択します。

4.  コマンド バーで、 **[管理者の設定]** を選択します。

5.  サーバーの管理者にする Azure AD ユーザー アカウントを選択してから、 **[選択]** を選択します。

6.  コマンド バーで、 **[保存]** を選択します。

### <a name="create-a-contained-user-in-sql-database-representing-the-azure-ad-group"></a>Azure AD グループを表す包含ユーザーを SQL Database に作成する

この次のステップでは、[Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) が必要になります。

1. SSMS を起動します。

2. **[サーバーに接続]** ダイアログで、 **[サーバー名]** フィールドにサーバー名を入力します。

3. **[認証]** フィールドで、 **[Active Directory - MFA サポートで汎用]** を選択します (他の 2 つの Active Directory 認証の種類を使用することもできます。「[SQL による Azure AD 認証の構成と管理](../azure-sql/database/authentication-aad-configure.md)」を参照)。

4. **[ユーザー名]** フィールドに、サーバー管理者として設定した Azure AD アカウントの名前 (testuser@xxxonline.com など) を入力します。

5. **[接続]** を選択し、サインイン プロセスを完了します。

6. **オブジェクト エクスプローラー** で、 **[データベース]**  ->  **[システム データベース]** フォルダーを展開します。

7. **マスター** データベースを右クリックし、 **[新しいクエリ]** を選択します。

8. クエリ ウィンドウで、次の T-SQL コマンドを入力し、ツールバーの **[実行]** を選択します。

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   このコマンドは正常に完了し、グループを表す包含ユーザーが作成されます。

9. クエリ ウィンドウをクリアして、次の T-SQL コマンドを入力し、ツールバーの **[実行]** を選択します。

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   このコマンドは正常に完了し、包含ユーザーにデータベース (SSISDB) を作成する権限が与えられます。

10. SQL 認証を使用して作成された SSISDB を、Azure-SSIS IR でアクセスするために Azure AD 認証を使用するように切り替えたい場合は、まず、**マスター** データベースにアクセス許可を付与する手順が正常に完了したことを確認してください。 次に、**SSISDB** データベースを右クリックし、 **[新しいクエリ]** を選択します。

11. クエリ ウィンドウで、次の T-SQL コマンドを入力し、ツールバーの **[実行]** を選択します。

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    このコマンドは正常に完了し、グループを表す包含ユーザーが作成されます。

12. クエリ ウィンドウをクリアして、次の T-SQL コマンドを入力し、ツールバーの **[実行]** を選択します。

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    コマンドは正常に完了し、包含ユーザーに SSISDB にアクセスする権限が与えられます。

## <a name="enable-azure-ad-on-sql-managed-instance"></a>SQL Managed Instance で Azure AD を有効にする

SQL Managed Instance では、ADF のマネージド ID を使用してデータベースを直接作成することがサポートされています。 ADF のマネージド ID を Azure AD グループに参加させたり、そのグループを表す包含ユーザーを SQL Managed Instance 内に作成したりする必要はありません。

### <a name="configure-azure-ad-authentication-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance に対する Azure AD 認証を構成する

[SQL Managed Instance の Azure Active Directory 管理者のプロビジョニング](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)の手順に従います。

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-sql-managed-instance"></a>ADF のマネージド ID を SQL Managed Instance 内のユーザーとして追加する

この次のステップでは、[Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) が必要になります。

1.  SSMS を起動します。

2.  **sysadmin** である SQL Server アカウントを使用して、SQL Managed Instance に接続します。 これは、Azure SQL Managed Instance 用 Azure AD サーバー プリンシパル (ログイン) の一般提供が開始されたら削除される一時的な制限事項です。 次のエラーは、Azure AD 管理者アカウントを使用してログインを作成しようとした場合に表示されます。"Msg 15247、Level 16、State 1、Line 1 このアクションを実行するアクセス許可がユーザーにありません"

3.  **オブジェクト エクスプローラー** で、 **[データベース]**  ->  **[システム データベース]** フォルダーを展開します。

4.  **マスター** データベースを右クリックし、 **[新しいクエリ]** を選択します。

5.  クエリ ウィンドウで、次の T-SQL スクリプトを実行して、ADF のマネージド ID をユーザーとして追加します

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    このコマンドは正常に完了し、ADF のマネージド ID にデータベース (SSISDB) を作成する権限が与えられます。

6.  SQL 認証を使用して作成された SSISDB を、Azure-SSIS IR でアクセスするために Azure AD 認証を使用するように切り替えたい場合は、まず、**マスター** データベースにアクセス許可を付与する手順が正常に完了したことを確認してください。 次に、**SSISDB** データベースを右クリックし、 **[新しいクエリ]** を選択します。

7.  クエリ ウィンドウで、次の T-SQL コマンドを入力し、ツールバーの **[実行]** を選択します。

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    このコマンドは正常に完了し、ADF のマネージド ID に SSISDB にアクセスする権限が与えられます。

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Azure Portal/ADF アプリで Azure-SSIS IR をプロビジョニングする

Azure Portal/ADF アプリで Azure-SSIS IR をプロビジョニングする場合は、 **[SQL の設定]** ページで **[Use AAD authentication with the managed identity for your ADF] (ADF のマネージド ID で AAD 認証を使用する)** オプションを選択します。 次のスクリーンショットは、SSISDB をホストしている SQL Database での IR の設定を示しています。 SSISDB をホストしている SQL Managed Instance での IR には、 **[Catalog Database Service Tier]\(カタログ データベースのサービス レベル)** および **[Allow Azure services to access]\(Azure サービスにアクセスを許可する\)** の設定は適用されませんが、その他の設定は同じです。

Azure-SSIS IR を作成する方法の詳細については、「[Azure Data Factory で Azure-SSIS 統合ランタイムを作成する](./create-azure-ssis-integration-runtime.md)」を参照してください。

![Azure-SSIS 統合ランタイムの設定](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>PowerShell で Azure-SSIS IR をプロビジョニングする

PowerShell を使用して Azure-SSIS IR をプロビジョニングするには、次のようにします。

1.  [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) モジュールをインストールします。

2.  スクリプトで `CatalogAdminCredential` パラメーターを設定しないでください。 次に例を示します。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
    ```

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>マネージド ID 認証を使用して SSIS パッケージを実行する

Azure SSIS IR で SSIS パッケージを実行するときに、マネージド ID 認証を使用してさまざまな Azure リソースに接続できます。 現在、次の接続マネージャーでマネージド ID 認証が既にサポートされています。

- [OLE DB 接続マネージャー](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET 接続マネージャー](/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure Storage 接続マネージャー](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
