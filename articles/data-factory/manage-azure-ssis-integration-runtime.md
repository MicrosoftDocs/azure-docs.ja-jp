---
title: Azure-SSIS 統合ランタイムの再構成
description: プロビジョニングが済んだ Azure-SSIS 統合ランタイムを Azure Data Factory で再構成する方法を説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: fbac52d65433f2137d565ca60fcf754e49199640
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931387"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムの再構成
この記事では、既存の Azure-SSIS 統合ランタイムを再構成する方法について説明します。 Azure Data Factory で Azure-SSIS 統合ランタイム (IR) を作成する場合は、[Azure-SSIS 統合ランタイムの作成](create-azure-ssis-integration-runtime.md)に関する記事を参照してください。  

## <a name="data-factory-ui"></a>Data Factory UI 
Data Factory UI を使用して、Azure-SSIS IR を停止、編集/再構成、または削除できます。 

1. **Data Factory の UI** で、 **[編集]** タブに切り替えます。データ ファクトリの UI を起動するには、データ ファクトリのホーム ページの **[作成と監視]** をクリックします。
2. 左側のウィンドウで、 **[接続]** をクリックします。
3. 右側のウィンドウで **[統合ランタイム]** に切り替えます。 
4. [アクション] 列のボタンを使用して、統合ランタイムを**停止**、**編集**、または**削除**します。 **[アクション]** 列の **[コード]** ボタンを使用すると、統合ランタイムに関連付けられている JSON 定義を表示できます。  
    
    ![Azure SSIS IR のアクション](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Azure-SSIS IR を再構成するには
1. **[アクション]** 列の **[停止]** をクリックして、統合ランタイムを停止します。 リスト ビューを更新するには、ツール バーの **[更新]** をクリックします。 IR が停止したら、最初のアクションによって IR が開始されます。 

    ![Azure SSIS IR のアクション - 停止後](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. **[アクション]** 列の **[編集]** ボタンをクリックして、IR を編集/再構成します。 **[Integration Runtime Setup]\(統合ランタイムのセットアップ\)** ウィンドウで、設定 (ノードのサイズ、ノード数、ノードあたりの並列実行の最大数など) を変更します。 
3. IR を再起動するには、 **[アクション]** 列の **[開始]** ボタンをクリックします。     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure-SSIS 統合ランタイムのインスタンスをプロビジョニングして開始した後、`Stop` - `Set` - `Start` PowerShell コマンドレットを順番に実行して、Azure-SSIS 統合ランタイムを再構成できます。 たとえば、次の PowerShell スクリプトは、Azure-SSIS 統合ランタイムのインスタンスに割り当てられているノードの数を 5 に変更します。

### <a name="reconfigure-an-azure-ssis-ir"></a>Azure-SSIS IR の再構成

1. 最初に、[Stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) コマンドレットを使用して Azure-SSIS 統合ランタイムを停止します。 このコマンドは、そのすべてのノードを解放し、課金を停止します。

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. 次に、[Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) コマンドレットを使用して Azure-SSIS IR を再構成します。 次のサンプル コマンドは、Azure-SSIS 統合ランタイムを 5 つのノードにスケールアウトします。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. 次に、[Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) コマンドレットを使用して Azure-SSIS 統合ランタイムを開始します。 このコマンドにより、すべてのノードが SSIS パッケージの実行用に割り当てられます。   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Azure-SSIS IR の削除
1. 最初に、データ ファクトリにあるすべての既存 Azure SSIS IR をすべて一覧表示します。

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. 次に、データ ファクトリにあるすべての既存 Azure SSIS IR を停止します。

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. 次に、データ ファクトリにある既存の Azure SSIS IR を 1 つずつすべて削除します。

    ```powershell
    Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. 最後に、データ ファクトリを削除します。

    ```powershell
    Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. 新しいリソース グループを作成していた場合は、そのリソース グループを削除します。

    ```powershell
    Remove-AzResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>次の手順
Azure-SSIS 統合ランタイムについて詳しくは、以下のトピックをご覧ください。 

- [Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR など、統合ランタイムの一般的な概念について説明されています。 
- [チュートリアル: SSIS パッケージを Azure にデプロイする](tutorial-create-azure-ssis-runtime-portal.md): この記事では、Azure-SSIS IR を作成し、Azure SQL データベースを使って SSIS カタログをホストする手順が説明されています。 
- [方法:Azure-SSIS 統合ランタイムを作成する](create-azure-ssis-integration-runtime.md)。 この記事では、チュートリアルを基に、Azure SQL Database Managed Instance の使い方と、IR を仮想ネットワークに参加させる方法が説明されています。 
- [仮想ネットワークへの Azure-SSIS IR の参加](join-azure-ssis-integration-runtime-virtual-network.md): この記事では、Azure 仮想ネットワークへの Azure-SSIS IR の参加に関する概念情報が説明されています。 Azure-SSIS IR が仮想ネットワークに参加できるように Azure Portal を使用して仮想ネットワークを構成する手順も説明されています。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR に関する情報を取得する方法と、返された情報での状態が説明されています。 
 
