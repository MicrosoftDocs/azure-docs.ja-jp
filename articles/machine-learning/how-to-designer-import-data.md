---
title: デザイナーにデータをインポートする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning データセットとデータのインポート モジュールを使用して Azure Machine Learning デザイナーにデータをインポートする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: a2cc0840b7ba4b26cf9f5b1219fc189230870774
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "97739860"
---
# <a name="import-data-into-azure-machine-learning-designer"></a>Azure Machine Learning デザイナーにデータをインポートする

この記事では、デザイナーに独自のデータをインポートして、カスタム ソリューションを作成する方法について説明します。 デザイナーにデータをインポートするには、次の 2 つの方法があります。 

* **Azure Machine Learning データセット** - Azure Machine Learning に [データセット](concept-data.md#datasets)を登録して、データを管理するのに役立つ高度な機能を有効にします。
* **データのインポート モジュール** - [データのインポート](algorithm-module-reference/import-data.md) モジュールを使用して、オンライン データ ソースから直接データにアクセスします。

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-azure-machine-learning-datasets"></a>Azure Machine Learning データセットを使用する

Microsoft では、[データセット](concept-data.md#datasets) 使用して、デザイナーにデータをインポートすることをお勧めします。 データセットを登録すると、[バージョン管理と追跡](how-to-version-track-datasets.md)や[データ監視](how-to-monitor-datasets.md)などの高度なデータ機能を最大限に活用できます。

### <a name="register-a-dataset"></a>データセットを登録する

既存のデータセットは、[SDK を使用してプログラム](how-to-create-register-datasets.md#datasets-sdk)で登録することも、[Azure Machine Learning studio で視覚的](how-to-connect-data-ui.md#create-datasets)に登録することもできます。

また、任意のデザイナー モジュールの出力をデータセットとして登録することもできます。

1. 登録するデータを出力するモジュールを選択します。

1. [プロパティ] ウィンドウで、 **[出力とログ]**  >  **[データセットの登録]** の順に選択します。

    ![データセットの登録オプションに移動する方法を示すスクリーンショット](media/how-to-designer-import-data/register-dataset-designer.png)

モジュールの出力データが表形式である場合は、出力を **ファイル データセット** として登録するか、または **表形式データセット** として登録するかを選択する必要があります。

 - **ファイル データセット** は、モジュールの出力フォルダーをファイル データセットとして登録します。 出力フォルダーには、デザイナーが内部で使用するデータ ファイルとメタファイルが含まれています。 登録されたデータセットをデザイナーで引き続き使用する場合は、このオプションを選択します。 

 - **表形式データセット** は、モジュールの出力データ ファイルのみを表形式のデータセットとして登録します。 この形式は、Automated Machine Learning や Python SDK など、他のツールで簡単に使用できます。 登録されたデータセットをデザイナーの外部で使用する場合は、このオプションを選択します。  
 

### <a name="use-a-dataset"></a>データセットを使用する

登録したデータセットは、 **[データセット]** にあるモジュール パレットからアクセスできます。 データセットを使用するには、パイプライン キャンバスにドラッグ アンド ドロップします。 次に、データセットの出力ポートをキャンバス内の他のモジュールに接続します。 

ファイル データセットを登録すると、データセットの出力ポートの種類は **AnyDirectory** になります。 表形式のデータセットを登録すると、データセットの出力ポートの種類は **DataFrameDirectory** になります。 データセットの出力ポートをデザイナーの他のモジュールに接続する場合は、データセットとモジュールのポートの種類を調整する必要があることにご注意ください。

![デザイナー パレットに保存されているデータセットの場所を示すスクリーンショット](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> デザイナーでは、[データセットのバージョン管理](how-to-version-track-datasets.md)がサポートされています。 データセット モジュールの [プロパティ] パネルで、データセットのバージョンを指定します。

### <a name="limitations"></a>制限事項 

- 現時点では、デザイナーでは表形式のデータセットのみを視覚化できます。 デザイナーの外部でファイル データセットを登録した場合、デザイナー キャンバスでそのデータセットを視覚化することはできません。
- データセットは仮想ネットワーク (VNet) に格納されます。 視覚化する場合は、データストアのワークスペース マネージド ID を有効にする必要があります。
    1. 関連するデータストアにアクセスし、 **[資格情報の更新]** をクリックします。
    :::image type="content" source="./media/resource-known-issues/datastore-update-credential.png" alt-text="資格情報の更新":::
    1. **[はい]** を選択して、ワークスペース マネージド ID を有効にします。
    :::image type="content" source="./media/resource-known-issues/enable-workspace-managed-identity.png" alt-text="ワークスペース マネージド ID を有効にする":::

## <a name="import-data-using-the-import-data-module"></a>データのインポート モジュールを使用してデータをインポートする

データセットを使用してデータをインポートすることをお勧めしていますが、[[データのインポート]](algorithm-module-reference/import-data.md) モジュールを使用することもできます。 [データのインポート] モジュールは、Azure Machine Learning でのデータセットの登録をスキップし、[データストア](concept-data.md#datastores)または HTTP URL から直接データをインポートします。

[データのインポート] モジュールの詳しい使用方法については、[データのインポートの参照ページ](algorithm-module-reference/import-data.md)を参照してください。

> [!NOTE]
> データセットに含まれる列が多すぎる場合、次のエラーが発生することがあります。"サイズ制限のため、検証に失敗しました。" これを回避するには、[データセットを Datasets インターフェイスに登録します](how-to-connect-data-ui.md#create-datasets)。

## <a name="supported-sources"></a>サポートされているソース

このセクションでは、デザイナーでサポートされているデータ ソースの一覧を示します。 データは、データストアまたは[表形式データセット](how-to-create-register-datasets.md#dataset-types)からデザイナーにインポートされます。

### <a name="datastore-sources"></a>データストアのソース
サポートされているデータストア ソースの一覧については、「[Azure ストレージ サービスのデータにアクセスする](how-to-access-data.md#supported-data-storage-service-types)」を参照してください。

### <a name="tabular-dataset-sources"></a>表形式データセットのソース

デザイナーは、次のソースから作成された表形式データセットをサポートしています。
 * 区切りファイル
 * JSON ファイル
 * Parquet ファイル
 * SQL クエリ

## <a name="data-types"></a>データ型

デザイナーは、内部的に次のデータ型を認識します。

* String
* Integer
* Decimal
* Boolean
* Date

デザイナーは、内部データ型を使用してモジュール間でデータを渡します。 [データセットへの変換](algorithm-module-reference/convert-to-dataset.md)モジュールを使用して、データ テーブル形式にデータを明示的に変換できます。 内部形式以外の形式を受け取るどのモジュールでも、次のモジュールに渡す前に、サイレント モードでデータを変換します。

## <a name="data-constraints"></a>データの制約

デザイナーのモジュールは、コンピューティング先のサイズによって制限されます。 大規模なデータセットの場合は、より大きな Azure Machine Learning コンピューティング リソースを使用する必要があります。 Azure Machine Learning コンピューティングの詳細については、「[Azure Machine Learning でのコンピューティング先とは](concept-compute-target.md#azure-machine-learning-compute-managed)」を参照してください。

## <a name="access-data-in-a-virtual-network"></a>仮想ネットワーク内のデータへのアクセス

ワークスペースが仮想ネットワーク内にある場合は、追加の構成手順を実行して、デザイナーでデータを視覚化する必要があります。 仮想ネットワークでデータストアとデータセットを使用する方法の詳細については、「[Azure 仮想ネットワークで Azure Machine Learning Studio を使用する](how-to-enable-studio-virtual-network.md)」を参照してください。

## <a name="next-steps"></a>次の手順

デザイナーの基礎について、[チュートリアル:デザイナーを使用して自動車の価格を予測する](tutorial-designer-automobile-price-train-score.md)」を参照してください。
