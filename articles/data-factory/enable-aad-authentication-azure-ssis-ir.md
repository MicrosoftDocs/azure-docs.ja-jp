---
title: Azure-SSIS 統合ランタイムに対して Azure Active Directory 認証を有効にする | Microsoft Docs
description: この記事では、Azure-SSIS 統合ランタイムを作成するために、Azure Data Factory のマネージド ID を使用して Azure Active Directory 認証を有効にする方法について説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 5/14/2019
author: swinarko
ms.author: sawinark
manager: craigg
ms.openlocfilehash: f3d0aaee624bdba169f13313bb57a3ebe8075592
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490069"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムに対して Azure Active Directory 認証を有効にする

この記事では、Azure Data Factory (ADF) のマネージド ID を使用して Azure Active Directory (Azure AD) 認証を有効にし、それを SQL 認証の代わりに使用して、Azure SQL Database サーバー/Managed Instance 内に SSIS カタログ データベース (SSISDB) をユーザーに代わってプロビジョニングする Azure-SSIS Integration Runtime (IR) を作成する方法について説明します。

ADF のマネージド ID について詳しくは、「[Data Factory のマネージド ID](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)」をご覧ください。

> [!NOTE]
>-  このシナリオで、ADF のマネージド ID を使用した　Azure AD 認証は、SSIS IR の作成とそのあとの開始操作でのみ使用されます。それにより SSISDB のプロビジョニングと接続が行われます。 SSIS パッケージの実行では、SSIS IR はまだ、SSIDB のプロビジョニング中に作成されるフル マネージド アカウントで SQL 認証を使用して SSISDB に接続します。
>-  SQL 認証を使用して SSIS IR を既に作成している場合、現時点では、PowerShell を介して Azure AD 認証を使用するように再構成することはできませんが、Azure portal/ADF アプリを介して行うことは可能です。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Azure SQL Database で Azure AD を有効にする

Azure SQL Database サーバーは、Azure AD ユーザーによるデータベースの作成をサポートしています。 最初に、ADF のマネージド ID をメンバーとして使用して Azure AD グループを作成する必要があります。 次に、Azure SQL Database サーバーの Active Directory 管理者として Azure AD ユーザーを設定してから、そのユーザーを使用して SQL Server Management Studio (SSMS) でそこに接続する必要があります。 最後に、Azure-SSIS IR が ADF のマネージド ID を使用してユーザーに代わって SSISDB を作成できるように、Azure AD グループを表す包含ユーザーを作成する必要があります。

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>ADF のマネージド ID をメンバーとして使用して Azure AD グループを作成する

既存の Azure AD グループを使用するか、または Azure AD PowerShell を使用して新しいグループを作成できます。

1.  [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) モジュールをインストールします。

2.   `Connect-AzureAD` を使用してサインインし、次のコマンドレットを実行してグループを作成し、それを変数に保存します。

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

3.  ADF のマネージド ID をグループに追加します。 「[Data Factory のマネージド ID](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)」の記事に従って、プリンシパル マネージド ID オブジェクト ID (たとえば 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc ですが、この目的にマネージド ID アプリケーション ID は使用しないでください) を取得できます。

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    後でグループ メンバーシップを確認することもできます。

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Azure SQL Database サーバーに対する Azure AD 認証を構成する

次の手順を使用して、「 [SQL による Azure AD 認証の構成と管理](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)」を行うことができます。

1.  Azure Portal で、左側のナビゲーションから **[すべてのサービス]**  ->  **[SQL Server]** を選択します。

2.  Azure AD 認証を構成する Azure SQL Database サーバーを選択します。

3.  ブレードの **[設定]** セクションで **[Active Directory 管理者]** を選択します。

4.  コマンド バーで、 **[管理者の設定]** を選択します。

5.  サーバーの管理者にする Azure AD ユーザー アカウントを選択してから、 **[選択]** を選択します。

6.  コマンド バーで、 **[保存]** を選択します。

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Azure AD グループを表す Azure SQL Database サーバー内の包含ユーザーを作成する

この次のステップでは、 [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)  (SSMS) が必要です。

1. SSMS を起動します。

2. **[サーバーに接続]** ダイアログで、 **[サーバー名]** フィールドに Azure SQL Database サーバーの名前を入力します。

3. **[認証]** フィールドで、 **[Active Directory - MFA サポートで汎用]** を選択します (他の 2 つの Active Directory 認証の種類を使用することもできます。「[SQL による Azure AD 認証の構成と管理](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)」を参照)。

4. **[ユーザー名]** フィールドに、サーバー管理者として設定した Azure AD アカウントの名前 (testuser@xxxonline.com など) を入力します。

5. **[接続]** を選択し、サインイン プロセスを完了します。

6. **オブジェクト エクスプローラー**で、 **[データベース]**  ->  **[システム データベース]** フォルダーを展開します。

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

10. SQL 認証を使用して作成された SSISDB を、Azure-SSIS IR でアクセスするために Azure AD 認証を使用するように切り替えたい場合は、**SSISDB** データベースを右クリックして、 **[新しいクエリ]** を選択します。

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

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance で Azure AD を有効にする

Azure SQL Database Managed Instance は、ADF のマネージド ID によるデータベースの直接の作成をサポートしています。 ADF のマネージド ID を Azure AD グループに参加させたり、そのグループを表す包含ユーザーを Managed Instance 内に作成したりする必要はありません。

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance に対する Azure AD 認証を構成する

「[マネージド インスタンスの Azure Active Directory 管理者をプロビジョニングする](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance)」の手順に従います。

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>ADF のマネージド ID を Azure SQL Database Managed Instance 内のユーザーとして追加する

この次のステップでは、 [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)  (SSMS) が必要です。

1.  SSMS を起動します。

2.  **sysadmin** である SQL Server アカウントを使用して、Managed Instance に接続します。 これは、Azure SQL Database Managed Instance 用 Azure AD サーバー プリンシパル (ログイン) の一般提供が開始されたら削除される一時的な制限事項です。 次のエラーは、Azure AD 管理者アカウントを使用してログインを作成しようとした場合に表示されます。"Msg 15247、Level 16、State 1、Line 1 このアクションを実行するアクセス許可がユーザーにありません"

3.  **オブジェクト エクスプローラー**で、 **[データベース]**  ->  **[システム データベース]** フォルダーを展開します。

4.  **マスター** データベースを右クリックし、 **[新しいクエリ]** を選択します。

5.  ADF のマネージド ID を取得します。 「[Data Factory のマネージド ID](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)」の記事に従って、プリンシパル マネージド ID アプリケーション ID を取得できます (ただし、この目的にマネージド ID オブジェクト ID を使用しないでください)。

6.  クエリ ウィンドウで、次の T-SQL スクリプトを実行して、ADF のマネージド ID をバイナリ型に変換します。

    ```sql
    DECLARE @applicationId uniqueidentifier = '{your Managed Identity Application ID}'
    select CAST(@applicationId AS varbinary)
    ```
    
    このコマンドは正常に完了し、ADF のマネージド ID がバイナリとして表示されます。

7.  クエリ ウィンドウをクリアし、次の T-SQL スクリプトを実行して、ADF のマネージド ID をユーザーとして追加します。

    ```sql
    CREATE LOGIN [{a name for the managed identity}] FROM EXTERNAL PROVIDER with SID = {your Managed Identity Application ID as binary}, TYPE = E
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{the managed identity name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{the managed identity name}]
    ```
    
    このコマンドは正常に完了し、ADF のマネージド ID にデータベース (SSISDB) を作成する権限が与えられます。

8.  SQL 認証を使用して作成された SSISDB を、Azure-SSIS IR でアクセスするために Azure AD 認証を使用するように切り替えたい場合は、**SSISDB** データベースを右クリックして、 **[新しいクエリ]** を選択します。

9.  クエリ ウィンドウで、次の T-SQL コマンドを入力し、ツールバーの **[実行]** を選択します。

    ```sql
    CREATE USER [{the managed identity name}] FOR LOGIN [{the managed identity name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{the managed identity name}]
    ```

    このコマンドは正常に完了し、ADF のマネージド ID に SSISDB にアクセスする権限が与えられます。

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Azure Portal/ADF アプリで Azure-SSIS IR をプロビジョニングする

Azure Portal/ADF アプリで Azure-SSIS IR をプロビジョニングする場合は、 **[SQL の設定]** ページで **[Use AAD authentication with the managed identity for your ADF] (ADF のマネージド ID で AAD 認証を使用する)** オプションを選択します。 次のスクリーンショットは、SSISDB をホストしている Azure SQL Database サーバーでの IR の設定を示しています。 SSISDB をホストしている Managed Instance での IR には、**カタログ データベースのサービス レベル** および **Azure サービスにアクセスを許可する** 設定は適用されませんが、その他の設定は同じです。

Azure-SSIS IR を作成する方法の詳細については、「[Azure Data Factory で Azure-SSIS 統合ランタイムを作成する](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)」を参照してください。

![Azure-SSIS 統合ランタイムの設定](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>PowerShell で Azure-SSIS IR をプロビジョニングする

PowerShell を使用して Azure-SSIS IR をプロビジョニングするには、次のようにします。

1.  [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018)  モジュールをインストールします。

2.  スクリプトで `CatalogAdminCredential` パラメーターを設定しないでください。 例:

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
