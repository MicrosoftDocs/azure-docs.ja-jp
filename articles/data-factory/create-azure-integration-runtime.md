---
title: Azure Data Factory で Azure Integration Runtime を作成する
description: データのコピーや変換操作のディスパッチに使用される Azure 統合ランタイムを Azure Data Factory で作成する方法を説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 1a9a88f975ca10a63741818dcb88874a8f1bf436
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926383"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Azure 統合ランタイムを作成して構成する方法
統合ランタイム (IR) は、異なるネットワーク環境間でデータ統合機能を提供するために Azure Data Factory によって使用されるコンピューティング インフラストラクチャです。 IR の詳細については、「[Integration runtime (統合ランタイム)](concepts-integration-runtime.md)」を参照してください。

Azure IR は、ネイティブにデータ移動を実行したり、HDInsight などのコンピューティング サービスにデータ変換操作をディスパッチしたりするための、フル マネージドのコンピューティングを提供します。 Azure IR は Azure 環境でホストされます。また、パブリック ネットワーク環境内にあってパブリック アクセス可能なエンドポイントを持つリソースへの接続をサポートします。

このドキュメントでは、Azure 統合ランタイムを作成して構成する方法について説明します。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>既定の Azure IR
既定では、個々のデータ ファクトリは、パブリック ネットワーク内のクラウド データ ストアおよびコンピューティング サービスに対する操作をサポートする Azure IR をバックエンドに備えます。 その Azure IR の場所は自動解決です。 リンクされたサービス定義で **connectVia** プロパティが指定されていない場合、既定の Azure IR が使用されます。 IR の場所を明示的に定義したい場合、または、管理上の目的から、異なる IR でのアクティビティ実行を仮想的にグループ化したい場合に限り、Azure IR を明示的に作成する必要があります。 

## <a name="create-azure-ir"></a>Azure IR を作成する
統合ランタイムは、**Set-AzDataFactoryV2IntegrationRuntime** PowerShell コマンドレットを使用して作成できます。 Azure IR を作成するには、名前、場所、およびタイプをコマンドに指定します。 次に示すのは、場所を "West Europe" (西ヨーロッパ) に設定して Azure IR を作成するコマンドの例です。

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Azure IR の場合、タイプを **Managed** に設定する必要があります。 コンピューティングの詳細はクラウドで柔軟に完全管理されるため、指定する必要はありません。 Azure-SSIS IR を作成したい場合、ノード サイズやノード数など、コンピューティングの詳細を指定します。 詳細については、「[Create and Configure Azure-SSIS IR (Azure-SSIS IR の作成と構成)](create-azure-ssis-integration-runtime.md)」を参照してください。

Set-AzDataFactoryV2IntegrationRuntime PowerShell コマンドレットを使用して、既存の Azure IR を構成し、その場所を変更することができます。 Azure IR の場所の詳細については、「[Introduction to integration runtime (統合ランタイムの概要)](concepts-integration-runtime.md)」を参照してください。

## <a name="use-azure-ir"></a>Azure IR を使用する

Azure IR が作成されたら、リンクされたサービスの定義でその Azure IR を参照することができます。 次に示すのは、上記の手順で作成した Azure 統合ランタイムを、リンクされたサービスの Azure Storage から参照する方法の例です。  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=...",
          "type": "SecureString"
        }
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>次の手順
その他のタイプの統合ランタイムの作成方法については、以下の記事を参照してください。

- [Create self-hosted integration runtime (自己ホスト型統合ランタイムの作成)](create-self-hosted-integration-runtime.md)
- [Create Azure-SSIS integration runtime (Azure-SSIS 統合ランタイムの作成)](create-azure-ssis-integration-runtime.md)
 
