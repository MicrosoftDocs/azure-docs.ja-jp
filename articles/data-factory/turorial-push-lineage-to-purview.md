---
title: Data Factory 系列データを Azure Purview にプッシュする
description: Data Factory 系列データを Azure Purview にプッシュする方法について説明します
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 3f2297ae619145ec19b53ba79d70b7c085cbcaab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361453"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>Data Factory 系列データを Azure Purview にプッシュする (プレビュー)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

このチュートリアルでは、Data Factory ユーザー インターフェイス (UI) を使用して、アクティビティを実行して系列データを Azure Purview アカウントにレポートするパイプラインを作成します。 その後、Azure Purview アカウントのすべての系列情報を表示できます。

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* **Azure Data Factory**。 Azure Data Factory がない場合は、[Azure Data Factory の作成](./quickstart-create-data-factory-portal.md)に関するページを参照してください。
* **Azure Purview アカウント**。 Purview アカウントにより、データ ファクトリによって生成されるすべての系列データがキャプチャされます。 Azure Purview アカウントがない場合は、[Azure Purview の作成](../purview/create-catalog-portal.md)に関するページを参照してください。


## <a name="run-data-factory-activities-and-push-lineage-data-to-azure-purview"></a>Data Factory のアクティビティを実行して、系列データを Azure Purview にプッシュする
### <a name="step-1--connect-data-factory-to-your-purview-account"></a>手順 1:Data Factory を Purview アカウントに接続する
Purview ポータルで Purview アカウントにログインし、**Management Center** にアクセスします。 **[外部接続]** で **[Data Factory]** を選択し、 **[新規]** ボタンをクリックして、新しい Data Factory への接続を作成します。 
[![Data Factory と Purview アカウントの間の接続を作成する場合のスクリーンショット](./media/data-factory-purview/connect-adf-to-purview.png) ](./media/data-factory-purview/connect-adf-to-purview.png#lightbox)

ポップアップ ページで、この Purview アカウントに接続する Data Factory を選択できます。 
![新しい接続のスクリーンショット](./media/data-factory-purview/new-adf-purview-connection.png)

接続の作成後に状態を確認できます。 **[接続済み]** は、Data Factory とこの Purview の間の接続が正常に行われていることを意味します。 
> [!NOTE]
> Data Factory と Azure Purview の間の接続を作成するには、Purview アカウントの以下のいずれかのロールおよび Data Factory の **共同作成者** ロールが割り当てられている必要があります。
> - 所有者
> - User Access Administrator

### <a name="step-2-run-copy-and-dataflow-activities-in-data-factory"></a>手順 2:Data Factory でコピー アクティビティとデータフロー アクティビティを実行する
Data Factory でパイプライン、コピー アクティビティ、およびデータフロー アクティビティを作成できます。 系列データ キャプチャのために追加の構成は必要ありません。 系列データは、アクティビティの実行中に自動的にキャプチャされます。
![コピーとデータフローのアクティビティのスクリーンショット](./media/data-factory-purview/adf-activities-for-lineage.png) コピーとデータフローのアクティビティを作成する方法がわからない場合は、「[Azure Data Factory を使用して Azure Blob Storage から Azure SQL Database のデータベースにデータをコピーする](./tutorial-copy-data-portal.md)」および「[マッピング データ フローを使用してデータを変換する](./tutorial-data-flow.md)」を参照してください。

### <a name="step-3-run-execute-ssis-package-activities-in-data-factory"></a>手順 3:Data Factory で SSIS パッケージの実行アクティビティを実行する
Data Factory でパイプラインを作成し、SSIS パッケージの実行アクティビティを実行することができます。 系列データ キャプチャのために追加の構成は必要ありません。 系列データは、アクティビティの実行中に自動的にキャプチャされます。
![SSIS パッケージの実行アクティビティのスクリーンショット](./media/data-factory-purview/ssis-activities-for-lineage.png)

SSIS パッケージの実行アクティビティを作成する方法がわからない場合は、「[Azure で SSIS パッケージを実行する](./tutorial-deploy-ssis-packages-azure.md)」を参照してください。

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>手順 4:Purview アカウントで系列情報を表示する
Purview アカウントに戻ります。 ホーム ページで、 **[アセットを参照する]** を選択します。 目的の資産を選択し、[系列] タブをクリックします。すべての系列情報が表示されます。
[![Purview アカウントのスクリーンショット](./media/data-factory-purview/view-dataset.png) ](./media/data-factory-purview/view-dataset.png#lightbox)

コピー アクティビティの系列データを表示できます。
[![コピー系列のスクリーンショット](./media/data-factory-purview/copy-lineage.png) ](./media/data-factory-purview/copy-lineage.png#lightbox)

データフロー アクティビティの系列データも表示できます。
[![データフロー系列のスクリーンショット](./media/data-factory-purview/dataflow-lineage.png) ](./media/data-factory-purview/dataflow-lineage.png#lightbox)

> [!NOTE] 
> データフロー アクティビティの系列では、ソースとシンクのみがサポートされています。 データフロー変換の系列はまだサポートされていません。

また、SSIS パッケージの実行アクティビティの系列データも表示できます。
[![SSIS 系列のスクリーンショット](./media/data-factory-purview/ssis-lineage.png) ](./media/data-factory-purview/ssis-lineage.png#lightbox)

> [!NOTE] 
> SSIS パッケージの実行アクティビティの系列では、ソースと宛先のみがサポートされています。 変換の系列はまだサポートされていません。

## <a name="next-steps"></a>次のステップ
[カタログ系列ユーザー ガイド](../purview/catalog-lineage-user-guide.md)

[Data Factory を Azure Purview に接続する](connect-data-factory-to-azure-purview.md)