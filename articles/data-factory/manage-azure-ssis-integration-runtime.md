---
title: "Azure-SSIS 統合ランタイムの管理 | Microsoft Docs"
description: "プロビジョニングが済んだ Azure-SSIS 統合ランタイムを Azure Data Factory で再構成する方法を説明します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: spelluru
ms.openlocfilehash: 780e5673b5ed159a7f170373d54fea7c1713a910
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-an-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムを管理する
[Azure-SSIS 統合ランタイムの作成](create-azure-ssis-integration-runtime.md)に関する記事では、Azure Data Factory を使って Azure-SSIS 統合ランタイムを作成する方法が説明されています。 この記事では、Azure-SSIS 統合ランタイムを停止、開始、再構成、または削除する方法について説明します。  


## <a name="stop"></a>停止 
Azure-SSIS 統合ランタイムを停止します。 このコマンドは、そのすべてのノードを解放し、課金を停止します。

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
```

## <a name="start"></a>開始 
Azure SSIS 統合ランタイムを開始します。 このコマンドは、そのすべてのノードを割り当てて、課金を開始します。   

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
```

## <a name="reconfigure"></a>再構成
Azure-SSIS 統合ランタイムのインスタンスをプロビジョニングして開始した後、`Stop` - `Set` - `Start` PowerShell コマンドレットを順番に実行して、Azure-SSIS 統合ランタイムを再構成できます。 たとえば、次の PowerShell スクリプトは、Azure-SSIS 統合ランタイムのインスタンスに割り当てられているノードの数を 5 に変更します。

1. 最初に、次のコマンドを実行して、Azure-SSIS 統合ランタイムを停止します。

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. そして、Azure-SSIS 統合ランタイムを 5 ノードにスケールアウトします。

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. それから、Azure SSIS 統合ランタイムを開始します。 これにより、すべてのノードが SSIS パッケージの実行用に割り当てられます。   

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

## <a name="remove"></a>Remove
既存の Azure-SSIS 統合ランタイムを削除するには、次のコマンドを実行します。 

```powershell
Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
```

## <a name="next-steps"></a>次のステップ
Azure-SSIS 統合ランタイムについて詳しくは、以下のトピックをご覧ください。 

- [Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime):  この記事では、Azure-SSIS IR など、統合ランタイムの一般的な概念について説明されています。 
- [チュートリアル: SSIS パッケージを Azure にデプロイする](tutorial-deploy-ssis-packages-azure.md):  この記事では、Azure-SSIS IR を作成し、Azure SQL Database を使って SSIS カタログをホストする手順が説明されています。 
- [方法: Azure-SSIS 統合ランタイムを作成する](create-azure-ssis-integration-runtime.md):  この記事では、チュートリアルを基に、Azure SQL マネージ インスタンス (プライベート プレビュー) の使い方と、IR を VNet に参加させる方法が説明されています。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime):  この記事では、Azure-SSIS IR に関する情報を取得する方法と、返された情報での状態が説明されています。 
- [Azure-SSIS IR を VNet に参加させる](join-azure-ssis-integration-runtime-virtual-network.md):  この記事では、Azure 仮想ネットワーク (VNet) への Azure-SSIS IR の参加に関する概念情報が説明されています。 Azure-SSIS IR が VNet に参加できるように Azure Portal を使って VNet を構成する手順も説明されています。 
