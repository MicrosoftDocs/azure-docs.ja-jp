---
title: SQL Server Integration Services パッケージを Azure SQL Database に再デプロイする | Microsoft Docs
description: SQL Server Integration Services パッケージを Azure SQL Database に移行する方法について説明します。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/08/2019
ms.openlocfilehash: 603a9df8e3f499c832bbfdcbef966de86003d6b7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080632"
---
# <a name="redeploy-sql-server-integration-services-packages-to-azure-sql-database"></a>SQL Server Integration Services パッケージを Azure SQL Database に再デプロイする

SQL Server Integration Services (SSIS) を使用していて、その SSIS プロジェクト/パッケージを、SQL Server によってホストされている配置元の SSISDB から、Azure SQL Database によってホストされている配置先の SSISDB に移行したい場合は、Integration Services 展開ウィザードを使用して、それらを再デプロイできます。 そのウィザードは、SQL Server Management Studio (SSMS) 内から起動することができます。

2012 より前のバージョンの SSIS を使用している場合は、ご利用の SSIS プロジェクト/パッケージをプロジェクト デプロイ モデルに再デプロイする前に、まず、Integration Services プロジェクトの変換ウィザードを使用してそれらを変換する必要があります。このウィザードは、SSMS から起動することもできます。 詳細については、[プロジェクト デプロイ モデルへのプロジェクトの変換](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)に関する記事を参照してください。

> [!NOTE]
> Azure Database Migration Service (DMS) では現在、配置元の SSISDB の Azure SQL Database サーバーへの移行はサポートされていませんが、次のプロセスを使用すれば、ご利用の SSIS プロジェクト/パッケージを再デプロイできます。

この記事では、次のことについて説明します。
> [!div class="checklist"]
>
> * 配置元の SSIS プロジェクト/パッケージを評価する。
> * SSIS プロジェクト/パッケージを Azure に移行する。

## <a name="prerequisites"></a>前提条件

これらの手順を完了するには、以下が必要です。

* SSMS バージョン 17.2 以降。
* SSISDB をホストするご利用のターゲット データベース サーバーのインスタンス。 Azure SQL Database サーバー (データベースなし) がまだない場合は、Azure portal を使用し、SQL Server (論理サーバーのみ) [フォーム](https://ms.portal.azure.com/#create/Microsoft.SQLServer)に移動して作成します。
* 配置先の SSISDB が Azure SQL Database サーバーのインスタンスでホストされている場合は、Azure-SSIS Integration Runtime (IR) を含む Azure Data Factory (ADF) で SSIS をプロビジョニングする必要があります (「[Azure Data Factory に Azure-SSIS 統合ランタイムをプロビジョニングする](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)」の記事に記載されています)。

## <a name="assess-source-ssis-projectspackages"></a>配置元の SSIS プロジェクト/パッケージを評価する

配置元の SSISDB の評価が Database Migration Assistant (DMA) または Azure Database Migration Service (DMS) にまだ統合されていない場合でも、ご利用の SSIS プロジェクト/パッケージは、Azure SQL Database サーバー上でホストされている配置先の SSISDB に再デプロイされるときに評価/検証されます。

## <a name="migrate-ssis-projectspackages"></a>SSIS プロジェクト/パッケージを移行する

Azure SQL Database サーバーに SSIS プロジェクト/パッケージを移行するには、次の手順を行います。

1. SSMS を開き、 **[オプション]** を選択して、 **[サーバーに接続]** ダイアログ ボックスを表示します。

2. **[ログイン]** タブで、配置先の SSISDB をホストする Azure SQL Database サーバーに接続するために必要な情報を指定します。

    ![SSIS の [ログイン] タブ](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. **[接続プロパティ]** タブの **[データベースへの接続]** テキスト ボックスで、**SSISDB** を選択または入力してから、 **[接続]** を選択します。

    ![SSIS の [接続プロパティ] タブ](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. SSMS オブジェクト エクスプローラーで、 **[Integration Services Catalogs]** ノードを展開し、**SSISDB** を展開します。既存のフォルダーがない場合は、**SSISDB** を右クリックして新しいフォルダーを作成します。

5. **SSISDB** で任意のフォルダーを展開し、 **[プロジェクト]** を右クリックして、 **[プロジェクトの配置]** を選択します。

    ![展開された SSIS SSISDB ノード](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. Integration Services 展開ウィザードの **[概要]** ページで、情報を確認してから、 **[次へ]** を選択します。

    ![展開ウィザードの [概要] ページ](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. **[ソースの選択]** ページで、デプロイする既存の SSIS プロジェクトを指定します。

    配置元の SSISDB をホストする SQL Server に SSMS も接続されている場合は、 **[Integration Services カタログ]** を選択してから、ご利用のカタログ内のサーバー名とプロジェクト パスを入力して、そのプロジェクトを直接デプロイします。

    または、 **[プロジェクト配置ファイル]** を選択してから、既存のプロジェクト配置ファイル (.ispac) へのパスを指定して、プロジェクトをデプロイします。

    ![展開ウィザードの [ソースの選択] ページ](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. **[次へ]** を選択します。
9. **[配置先の選択]** ページで、使用するプロジェクトの配置先を指定します。

    a. [サーバー名] テキスト ボックスに、Azure SQL Database サーバーの完全修飾名 (<server_name>.database.windows.net) を入力します。

    b. 認証情報を提供して、 **[接続]** を選択します。

    ![展開ウィザードの [配置先の選択] ページ](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. **[参照]** を選択して SSISDB 内の配置先フォルダーを指定してから、 **[次へ]** を選択します。

    > [!NOTE]
    > **[次へ]** ボタンは、 **[接続]** を選択した後でのみ有効になります。

10. **[検証]** ページで、エラー/警告がないか確認し、必要があれば、ご利用のパッケージを適宜変更します。

    ![展開ウィザードの [検証] ページ](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. **[次へ]** を選択します。

12. **[レビュー]** ページで、ご利用の配置設定を確認します。

    > [!NOTE]
    > ご利用の設定を変更するには、 **[前へ]** を選択するか、または左側のウィンドウにある手順リンクのいずれかを選択します。

13. **[デプロイ]** をクリックして、デプロイ プロセスを開始します。

14. デプロイ プロセスが完了すると、[結果] ページを表示できます。このページには、各デプロイ操作の成功または失敗が表示されます。
    a. 任意の操作が失敗した場合、 **[結果]** 列で **[失敗]** を選択すると、エラーの説明が表示されます。
    b. 必要に応じて、 **[レポートの保存]** を選択して、XML ファイルに結果を保存します。

15. **[閉じる]** を選択して、Integration Services の展開ウィザードを終了します。

プロジェクトのデプロイが問題なく成功した場合は、それに含まれる任意のパッケージを選択して、ご利用の Azure-SSIS IR 上で実行することができます。

## <a name="next-steps"></a>次の手順

* 「[Microsoft Database Migration Guide](https://datamigration.microsoft.com/)」にある移行ガイドを確認する。
