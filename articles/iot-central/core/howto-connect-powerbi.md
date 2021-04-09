---
title: Azure IoT Central データを Power BI ダッシュボードに視覚化する |Microsoft Docs
description: Azure IoT Central 用の Power BI ソリューション、IoT Central データを視覚化して分析します。
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/4/2019
ms.topic: conceptual
ms.openlocfilehash: 191b57b08ba04844824dd5cf26875c21e494c5ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92123339"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Azure IoT Central データを Power BI ダッシュボードに視覚化する

*このトピックは、管理者とソリューション開発者を対象としています。*

:::image type="content" source="media/howto-connect-powerbi/iot-continuous-data-export.png" alt-text="Power BI ソリューションのパイプライン":::

Azure IoT Central V3 用の Power BI ソリューションを使用して、IoT デバイスのパフォーマンスを監視する強力な Power BI ダッシュボードを作成します。 Power BI ダッシュ ボードでは、以下を実行できます。

- デバイスが特定の時間に送信しているデータの量を追跡する
- 異なるテレメトリ ストリーム間でデータ ボリュームを比較する
- フィルターを適用して、特定のデバイスによって送信されたデータに絞り込む
- テーブル内の最新のテレメトリ データを表示する

このソリューションは、[継続的データ エクスポート](./howto-export-data.md)の Azure BLOB ストレージ アカウントからデータを読み取るパイプラインを設定します。 このパイプランでは、Azure Functions、Azure Data Factory、Azure SQL Database を使用して、データの処理と変換が行われます。 PBIX ファイルとしてダウンロードする Power BI レポートで、データの視覚化と分析が可能です。 リソースはすべて Azure サブスクリプション内に作成されるため、ニーズに合わせて各コンポーネントをカスタマイズできます。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を完了するには、アクティブな Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

ソリューションを設定するには、以下のリソースが必要です。

- バージョン 3 の IoT Central アプリケーション。 アプリケーションのバージョンを確認する方法については、「[アプリケーションに関する情報](./howto-get-app-info.md)」を参照してください。 IoT Central アプリケーションを作成する方法については、「[Azure IoT Central アプリケーションの作成](./quick-deploy-iot-central.md)」を参照してください。
- テレメトリ、デバイス、デバイス テンプレートを Azure BLOB ストレージにエクスポートするように構成された継続的データ エクスポート。 詳細については、[Azure 内の宛先に IoT データをエクスポートする方法](howto-export-data.md)に関するページを参照してください。
  - BLOB コンテナーにデータをエクスポートしているのは IoT Central アプリケーションのみであることを確認します。
  - デバイスからは、[JSON でエンコードされたメッセージを送信する必要があります](../../iot-hub/iot-hub-devguide-messages-d2c.md)。 デバイスのメッセージ システム プロパティでは、`contentType:application/JSON` に加え、`contentEncoding:utf-8`、`contentEncoding:utf-16`、または `contentEncoding:utf-32` を指定する必要があります。
- Power BI Desktop (最新バージョン)。 [Power BI のダウンロード](https://powerbi.microsoft.com/downloads/)に関するページを参照してください。
- Power BI Pro (ダッシュボードを他のユーザーと共有する場合)。

> [!NOTE]
> バージョン 2 の IoT Central アプリケーションを使おうとしている場合は、以前のバージョンのドキュメントのサイトで、「[Azure IoT Central データを Power BI ダッシュボードに視覚化する](/previous-versions/azure/iot-central/core/howto-connect-powerbi)」を参照してください。

## <a name="install"></a>インストール

パイプラインを設定するには、**Microsoft AppSource** サイトの「[Azure IoT Central V3 向け Power BI ソリューション](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central)」ページに移動します。 **[今すぐ入手する]** を選択し、指示に従います。

PBIX ファイルを開いたら、必ずカバー ページの指示を読んで従ってください。 これらの指示では、レポートを SQL データベースに接続する方法を説明しています。

## <a name="report"></a>レポート

PBIX ファイルには、デバイスによって送信されたテレメトリの履歴ビューが表示される **[Devices and Telemetry] (デバイスとテレメトリ)** レポートが含まれています。 ここにはさまざまな種類のテレメトリの内訳が示され、デバイスによって送信された最新のテレメトリも表示されます。

:::image type="content" source="media/howto-connect-powerbi/report.png" alt-text="Power BI の [Devices and Telemetry] (デバイスとテレメトリ) レポート":::

## <a name="pipeline-resources"></a>パイプライン リソース

Azure portal で、パイプラインを構成するすべての Azure リソースにアクセスできます。 すべてのリソースは、パイプラインを設定したときに作成したリソース グループ内にあります。

:::image type="content" source="media/howto-connect-powerbi/azure-deployment.png" alt-text="リソース グループの Azure portal ビュー":::

次の一覧で、パイプライン内の各リソースの役割について説明します。

### <a name="azure-functions"></a>Azure Functions

Azure 関数アプリは、IoT Central が BLOB ストレージに新しいファイルを書き込むたびにトリガーされます。 これらの関数は、テレメトリ、デバイス、およびデバイス テンプレートの BLOB からデータを抽出して、Azure Data Factory が使用する中間 SQL テーブルを作成します。

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory は、リンクされたサービスとして、SQL Database に接続します。 それがストアド プロシージャを実行し、データを処理して分析テーブルに格納します。

Azure Data Factory は 15 分ごとに実行されて、最新のデータ バッチを変換し、SQL テーブルに読み込みます (これが **タンブリング ウィンドウ トリガー** の現在の最小数です)。

### <a name="azure-sql-database"></a>Azure SQL データベース

Azure Data Factory によって、Power BI 用の一連の分析テーブルが生成されます。 これらのスキーマを Power BI で詳しく調べ、それを使用して独自の視覚化を構築することができます。

## <a name="estimated-costs"></a>概算コスト

Microsoft AppSource サイトの「[Azure IoT Central V3 向け Power BI ソリューション](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central)」ページには、デプロイするリソースのコスト見積もりツールへのリンクが記載されています。

## <a name="next-steps"></a>次のステップ

Power BI でデータを視覚化する方法について学習したので、次は、[デバイスを管理する方法](howto-manage-devices.md)について学習することをお勧めします。