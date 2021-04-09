---
title: Azure-SSIS 統合ランタイムの再構成
description: プロビジョニングが済んだ Azure-SSIS 統合ランタイムを Azure Data Factory で再構成する方法を説明します。
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 724411dc12654aec1614230c943923062b334cd2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100370684"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムの再構成

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、既存の Azure-SSIS 統合ランタイムを再構成する方法について説明します。 Azure Data Factory で Azure-SSIS 統合ランタイム (IR) を作成する場合は、[Azure-SSIS 統合ランタイムの作成](create-azure-ssis-integration-runtime.md)に関する記事を参照してください。  

## <a name="data-factory-ui"></a>Data Factory UI 
Data Factory UI を使用して、Azure-SSIS IR を停止、編集/再構成、または削除できます。 

1. データ ファクトリのホーム ページの **[作成と監視]** タイルを選択して、Data Factory UI を開きます。
2. **[ホーム]** 、 **[編集]** 、 **[監視]** の各ハブの下にある **[管理]** ハブを選択して、 **[接続]** ペインを表示します。

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Azure-SSIS IR を再構成するには
**[管理]** ハブの **[接続]** ペインで、 **[Integration runtimes]\(統合ランタイム\)** ページに切り替え、 **[Refresh]\(更新\)** を選択します。 

   ![[接続] ペイン](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Azure-SSIS IR は、その名前を選択することで編集したり再構成したりすることができます。 該当するボタンを選択することで、Azure-SSIS IR を監視、開始、停止、削除したり、Azure-SSIS IR 上で動作する SSIS パッケージの実行アクティビティを含む ADF パイプラインを自動生成したりできます。また、Azure-SSIS IR の JSON コード (またはペイロード) を表示することもできます。  Azure-SSIS IR の編集と削除は、それが停止しているときにしか実行できません。

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

## <a name="next-steps"></a>次のステップ
Azure-SSIS 統合ランタイムについて詳しくは、以下のトピックをご覧ください。 

- [Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR など、統合ランタイムの一般的な概念について説明されています。 
- [チュートリアル: SSIS パッケージを Azure にデプロイする](./tutorial-deploy-ssis-packages-azure.md): この記事は、Azure-SSIS IR を作成し、Azure SQL Database を使用して SSIS カタログをホストするための詳細な手順を示しています。 
- [方法: Azure-SSIS 統合ランタイムを作成する](create-azure-ssis-integration-runtime.md)。 この記事では、チュートリアルを基に、Azure SQL Managed Instance の使い方と、IR を仮想ネットワークに参加させる方法が説明されています。 
- [仮想ネットワークへの Azure-SSIS IR の参加](join-azure-ssis-integration-runtime-virtual-network.md): この記事では、Azure 仮想ネットワークへの Azure-SSIS IR の参加に関する概念情報が説明されています。 Azure-SSIS IR が仮想ネットワークに参加できるように Azure Portal を使用して仮想ネットワークを構成する手順も説明されています。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR に関する情報を取得する方法と、返された情報での状態が説明されています。