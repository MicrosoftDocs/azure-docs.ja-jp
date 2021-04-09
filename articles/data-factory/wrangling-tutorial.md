---
title: Azure Data Factory でラングリング データ フローの使用を開始する
description: ラングリング データ フローを使用して Azure Data Factory データを準備する方法に関するチュートリアル
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: 3d43a532f57feab361c6a3de79269991f46fc55d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684024"
---
# <a name="prepare-data-with-data-wrangling"></a>データ ラングリングを使用してデータを準備する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

データ ファクトリのデータ ラングリングを使用すると、対話型の Power Query マッシュアップを ADF でネイティブに構築し、ADF パイプライン内で大規模に実行することができます。

> [!NOTE]
> ADF での Power Query アクティビティは、現在パブリック プレビューで使用可能

## <a name="create-a-power-query-activity"></a>Power Query アクティビティを作成する

Azure Data Factory に Power Query を作成するには、2 つの方法があります。 1 つ目の方法は、プラス記号のアイコンをクリックし、Factory リソースのウィンドウで **[データ フロー]** を選択することです。

> [!NOTE]
> 以前は、データ ラングリング機能はデータ フロー ワークフローに配置されていました。 現在は、データ ラングリング マッシュアップを ```New > Power query``` から作成します

![ファクトリ リソース ペインでの Power Query を示すスクリーンショット。](media/data-flow/power-query-wrangling.png)

もう 1 つの方法は、パイプライン キャンバスのアクティビティ ウィンドウで行います。 **Power Query** アコーディオンを開き、**Power Query** アクティビティをキャンバスにドラッグします。

![データ ラングリング オプションを強調表示したスクリーンショット。](media/data-flow/power-query-activity.png)

## <a name="author-a-power-query-data-wrangling-activity"></a>Power Query データ ラングリング アクティビティを作成する

Power Query マッシュアップの **ソース データセット** を追加します。 既存のデータセットを選択するか、新しいデータセットを作成することができます。 シンク データセットを選択することもできます。 1 つまたは複数のソース データセットを選択できますが、現時点では 1 つのシンクのみが許可されます。 シンク データセットの選択は省略可能ですが、少なくとも 1 つのソース データセットが必要です。

![ラングリング](media/wrangling-data-flow/tutorial4.png)

**[作成]** をクリックして、Power Query Online マッシュアップ エディターを開きます。

![Power Query Online マッシュアップ エディターを開く [作成] ボタンを示すスクリーンショット。](media/wrangling-data-flow/tutorial5.png)

コーディング不要のデータ準備を使用して、ラングリング Power Query を作成します。 使用できる関数の一覧については、[変換関数](wrangling-functions.md)に関するページを参照してください。 ADF は、M スクリプトをデータ フロー スクリプトに変換し、Azure Data Factory データ フロー Spark 環境を使用して大規模に Power Query を実行できるようにします。

![データ ラングリング Power Query を作成するためのプロセスを示すスクリーンショット。](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-power-query-data-wrangling-activity"></a>Power Query データ ラングリング アクティビティの実行と監視

Power Query アクティビティのパイプライン デバッグ実行を実行するには、パイプラインキャンバスの **[デバッグ]** をクリックします。 パイプラインを発行すると、 **[Trigger Now]\(今すぐトリガー\)** によって、最後に発行されたパイプラインのオンデマンド実行が実行されます。 Power Query パイプラインは、既存のすべての Azure Data Factory トリガーを使用してスケジュールできます。

![Power Query データ ラングリング アクティビティを追加する方法を示すスクリーンショット。](media/wrangling-data-flow/tutorial3.png)

**[モニター]** タブにアクセスして、トリガーされた Power Query アクティビティの実行の出力を視覚化します。

![トリガーされたラングリング Power Query アクティビティの実行の出力を示すスクリーンショット。](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>次のステップ

[マッピング データ フローの作成](tutorial-data-flow.md)方法について確認します。
