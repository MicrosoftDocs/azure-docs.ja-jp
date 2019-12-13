---
title: Azure-SSIS Integration Runtime の Enterprise Edition をプロビジョニングする
description: この記事では、Azure SSIS Integration Runtime の Enterprise Edition の機能およびそのプロビジョニング方法について説明します
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: 8096da955da0266f3727197f21d67c33d099aa4f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74922640"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime の Enterprise Edition をプロビジョニングする

Azure SSIS 統合ランタイムの Enterprise Edition では、次の高度なプレミアム機能を使用できます。
-   変更データ キャプチャ (CDC) コンポーネント
-   Oracle、Teradata、SAP BW のコネクタ
-   SQL Server Analysis Services (SSAS) および Azure Analysis Services (AAS) のコネクタと変換
-   あいまいグループ化変換とあいまい参照変換
-   用語抽出変換と用語参照変換

これらの機能の一部では、Azure-SSIS IR をカスタマイズするために追加コンポーネントをインストールする必要があります。 追加コンポーネントのインストール方法については、「[Azure SSIS 統合ランタイムのカスタム セットアップ](how-to-configure-azure-ssis-ir-custom-setup.md)」をご覧ください。

## <a name="enterprise-features"></a>Enterprise の機能

| **Enterprise の機能** | **説明** |
|---|---|
| CDC コンポーネント | Azure-SSIS IR Enterprise Edition には、CDC ソース、管理タスク、およびスプリッター変換がプレインストールされています。 Oracle に接続するには、別のコンピューターに CDC Designer と CDC Service をインストールする必要もあります。 |
| Oracle コネクタ | Azure-SSIS IR Enterprise Edition には、Oracle 接続マネージャー、接続元、および接続先がプレインストールされています。 また、Azure-SSIS IR に Oracle Call Interface (OCI) ドライバーをインストールし、必要な場合は Oracle Transport Network Substrate (TNS) を構成する必要があります。 詳細については、「[Azure SSIS 統合ランタイムのカスタム セットアップ](how-to-configure-azure-ssis-ir-custom-setup.md)」をご覧ください。 |
| Teradata コネクタ | Teradata 接続マネージャー、接続元、接続先、Teradata Parallel Transporter (TPT) API、および Teradata ODBC ドライバーを、Azure-SSIS IR Enterprise Edition にインストールする必要があります。 詳細については、「[Azure SSIS 統合ランタイムのカスタム セットアップ](how-to-configure-azure-ssis-ir-custom-setup.md)」をご覧ください。 |
| SAP BW コネクタ | Azure-SSIS IR Enterprise Edition には、SAP BW 接続マネージャー、接続元、および接続先がプレインストールされています。 また、SAP BW ドライバーを Azure-SSIS IR にインストールする必要があります。 これらのコネクタは、SAP BW 7.0 以前のバージョンをサポートします。 これより後のバージョンの SAP BW または他の SAP 製品に接続するには、サード パーティの ISV から SAP コネクタを購入して、Azure-SSIS IR にインストールすることができます。 追加コンポーネントのインストール方法については、「[Azure SSIS 統合ランタイムのカスタム セットアップ](how-to-configure-azure-ssis-ir-custom-setup.md)」をご覧ください。 |
| Analysis Services のコンポーネント               | データ マイニング モデル トレーニング変換先、ディメンション処理変換先、パーティション処理変換先、データ マイニング クエリ変換が、Azure-SSIS IR Enterprise Edition にプレインストールされています。 これらのコンポーネントはすべて SQL Server Analysis Services (SSAS) をサポートしますが、パーティション処理変換先だけは Azure Analysis Services (AAS) をサポートします。 SSAS に接続するには、[SSISDB で Windows 認証資格情報を構成する](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)必要もあります。 これらのコンポーネントに加えて、Azure-SSIS IR Standard/Enterprise Edition には Analysis Services DDL 実行タスク、Analysis Services 処理タスク、およびデータ マイニング クエリ タスクもプレインストールされています。 |
| あいまいグループ化変換とあいまい参照変換  | Azure-SSIS IR Enterprise Edition には、あいまいグループ化変換とあいまい参照変換がプレインストールされています。 これらのコンポーネントは、参照データの格納用に SQL Server と Azure SQL Database の両方をサポートします。 |
| 用語抽出変換と用語参照変換 | Azure-SSIS IR Enterprise Edition には、用語抽出変換と用語参照変換がプレインストールされています。 これらのコンポーネントは、参照データの格納用に SQL Server と Azure SQL Database の両方をサポートします。 |

## <a name="instructions"></a>Instructions

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  [Azure PowerShell](/powershell/azure/install-az-ps)をダウンロードしてインストールします。

2.  PowerShell を使用して Azure-SSIS IR をプロビジョニングまたは再構成するときは、Azure-SSIS IR を開始する前に、**Edition** パラメーターの値として **Enterprise** を指定して `Set-AzDataFactoryV2IntegrationRuntime` を実行します。 スクリプトのサンプルを次に示します。

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>次の手順

-   [Azure SSIS 統合ランタイムのカスタム セットアップ](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Azure SSIS 統合ランタイムの有料 (ライセンスあり) カスタム コンポーネントを開発する方法](how-to-develop-azure-ssis-ir-licensed-components.md)
