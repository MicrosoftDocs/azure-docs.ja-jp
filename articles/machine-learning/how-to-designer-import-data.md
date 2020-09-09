---
title: デザイナー (プレビュー) にデータをインポートする
titleSuffix: Azure Machine Learning
description: さまざまなデータ ソースから Azure Machine Learning デザイナー (プレビュー) にデータをインポートする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: d977c8e13ce75eb276c8fdb11e9dd40e40a923ad
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495373"
---
# <a name="import-data-into-azure-machine-learning-designer-preview"></a>Azure Machine Learning デザイナー (プレビュー) にデータをインポートする

この記事では、デザイナーに独自のデータをインポートして、カスタム ソリューションを作成する方法について説明します。 デザイナーにデータをインポートするには、次の 2 つの方法があります。 

* **Azure Machine Learning データセット** - Azure Machine Learning に[データセット](concept-data.md#datasets)を登録して、データを管理するのに役立つ高度な機能を有効にします。
* **データのインポート モジュール** - [データのインポート](algorithm-module-reference/import-data.md) モジュールを使用して、オンライン データ ソースから直接データにアクセスします。

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-azure-machine-learning-datasets"></a>Azure Machine Learning データセットを使用する

Microsoft では、[データセット](concept-data.md#datasets) 使用して、デザイナーにデータをインポートすることをお勧めします。 データセットを登録すると、[バージョン管理と追跡](how-to-version-track-datasets.md)や[データ監視](how-to-monitor-datasets.md)などの高度なデータ機能を最大限に活用できます。

### <a name="register-a-dataset"></a>データセットを登録する

既存のデータセットは、[SDK を使用してプログラム](how-to-create-register-datasets.md#datasets-sdk)で登録することも、[Azure Machine Learning studio で視覚的](how-to-create-register-datasets.md#datasets-ui)に登録することもできます。

また、任意のデザイナー モジュールの出力をデータセットとして登録することもできます。

1. 登録するデータを出力するモジュールを選択します。

1. [プロパティ] ウィンドウで、 **[出力]**  >  **[データセットの登録]** の順に選択します。

    ![データセットの登録オプションに移動する方法を示すスクリーンショット](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>データセットを使用する

登録したデータセットは、 **[データセット]**  >  **[マイ データセット]** にあるモジュール パレットからアクセスできます。 データセットを使用するには、パイプライン キャンバスにドラッグ アンド ドロップします。 次に、データセットの出力ポートをパレット内の他のモジュールに接続します。

![デザイナー パレットに保存されているデータセットの場所を示すスクリーンショット](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> 現時点では、デザイナーでは[表形式データ](how-to-create-register-datasets.md#dataset-types)の処理のみがサポートされています。 [ファイル データセット](how-to-create-register-datasets.md#dataset-types)を使用する場合は、Python および R で使用可能な Azure Machine Learning SDK を使用します。

## <a name="import-data-using-the-import-data-module"></a>データのインポート モジュールを使用してデータをインポートする

データセットを使用してデータをインポートすることをお勧めしていますが、[[データのインポート]](algorithm-module-reference/import-data.md) モジュールを使用することもできます。 [データのインポート] モジュールは、Azure Machine Learning でのデータセットの登録をスキップし、[データストア](concept-data.md#datastores)または HTTP URL から直接データをインポートします。

[データのインポート] モジュールの詳しい使用方法については、[データのインポートの参照ページ](algorithm-module-reference/import-data.md)を参照してください。

> [!NOTE]
> データセットに含まれる列が多すぎる場合、次のエラーが発生することがあります。"サイズ制限のため、検証に失敗しました。" これを回避するには、[データセットを Datasets インターフェイスに登録します](how-to-create-register-datasets.md#datasets-ui)。

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

ワークスペースが仮想ネットワーク内にある場合は、追加の構成手順を実行して、デザイナーでデータを視覚化する必要があります。 仮想ネットワークでデータストアとデータセットを使用する方法の詳細については、「[プライベート仮想ネットワークを使用して機械学習のライフサイクルをセキュリティで保護する](how-to-enable-virtual-network.md#machine-learning-studio)」を参照してください。

## <a name="next-steps"></a>次のステップ

デザイナーの基本については、「[チュートリアル:デザイナーを使用して自動車の価格を予測する](tutorial-designer-automobile-price-train-score.md)」を参照してください。
