---
title: SSIS コネクタを使用して BLOB ストレージのデータを移動する - Team Data Science Process
description: Azure 用の SQL Server Integration Services Feature Pack を使用して、Azure Blob Storage との間でデータを移動する方法について説明します。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ad87272749011c81c1040825da3f3c53858a55bd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322862"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>SSIS コネクタを使用して Azure Blob Storage 間でデータを移動する
[SQL Server Integration Services Feature Pack for Azure](https://msdn.microsoft.com/library/mt146770.aspx) には、Azure への接続、Azure とオンプレミスのデータ ソースとの間でのデータ転送、Azure に格納したデータの処理に必要なコンポーネントが用意されています。

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

ユーザーは、オンプレミスのデータをクラウドに移動すると、そのデータにすべての Azure サービスからアクセスできるようになり、一連の Azure テクノロジを最大限に活用できます。 その後、データは Azure Machine Learning または HDInsight クラスター上で使用される場合があります。

これらの Azure リソースを使用する例については、[SQL](sql-walkthrough.md) と [HDInsight](hive-walkthrough.md) のチュートリアルを参照してください。

ハイブリッド データ統合のシナリオに共通するビジネス ニーズを満たすために SSIS を使用する標準的なシナリオの詳細については、 [SQL Server Integration Services Feature Pack for Azure をさらに活用する方法](https://techcommunity.microsoft.com/t5/sql-server-integration-services/doing-more-with-sql-server-integration-services-feature-pack-for/ba-p/388238) に関するブログを参照してください。

> [!NOTE]
> Azure BLOB ストレージの概要については、[Azure BLOB の基礎](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)に関する記事および [Azure BLOB サービス](https://msdn.microsoft.com/library/azure/dd179376.aspx)に関するページをご覧ください。
> 
> 

## <a name="prerequisites"></a>前提条件
この記事で説明するタスクを実行するには、Azure サブスクリプションと Azure Storage アカウントの設定が必要です。 データをアップロードまたはダウンロードするために、Azure Storage アカウント名とアカウント キーを確認しておく必要があります。

* **Azure サブスクリプション**を設定するには、 [1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。
* **ストレージ アカウント**の作成と、アカウントとキー情報の取得についての手順については、[Azure Storage アカウントについて](../../storage/common/storage-create-storage-account.md)の記事を参照してください。

**SSIS コネクタ**を使用するには、次のものをダウンロードする必要があります。

* **SQL Server 2014 または 2016 Standard (またはそれ以降)** :インストールには SQL Server Integration Services が含まれます。
* **Microsoft SQL Server 2014 または 2016 Integration Services Feature Pack for Azure**:これらのコネクタは、[SQL Server 2014 Integration Services](https://www.microsoft.com/download/details.aspx?id=47366) および [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) のページからそれぞれダウンロードできます。

> [!NOTE]
> SSIS は SQL Server と共にインストールされますが、Express バージョンには含まれていません。 SQL Server の各種エディションに含まれるアプリケーションについては、「[SQL Server のエディション](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)」をご覧ください
> 
> 

SSIS のトレーニング資料については、 [SSIS の実践的トレーニング](https://www.microsoft.com/sql-server/training-certification)

SISS を使用して稼働状態にし、簡単な ETL (抽出、変換、読み込み) パッケージを作成する方法については、[SSIS チュートリアル:簡単な ETL パッケージの作成](https://msdn.microsoft.com/library/ms169917.aspx)に関する記事をご覧ください。

## <a name="download-nyc-taxi-dataset"></a>NYC タクシー データセットをダウンロードする
ここで説明する例では、一般公開されているデータセットである、 [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) データセットを使用します。 このデータセットは、2013 年のニューヨーク市における約 1 億 7300 万件のタクシー乗車情報で構成されています。 データには、乗車の詳細データと料金データの 2 種類があります。 月ごとにファイルが用意されているため、24 ファイルになり、各ファイルは圧縮されていない状態で約 2 GB になります。

## <a name="upload-data-to-azure-blob-storage"></a>Azure BLOB ストレージにデータをアップロードする
SSIS 機能パックを使用して、データをオンプレミスから Azure BLOB ストレージに移動するには、次に示す [**Azure BLOB アップロード タスク**](https://msdn.microsoft.com/library/mt146776.aspx)のインスタンスを使用します。

![configure-data-science-vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

タスクで使用されるパラメーターについて、ここで説明します。

| フィールド | 説明 |
| --- | --- |
| **[AzureStorageConnection]** |既存の Azure Storage 接続マネージャーを指定するか、BLOB ファイルがホストされる場所を指す Azure Storage アカウントを参照する、新しい Azure Storage 接続マネージャーを作成します。 |
| **BlobContainer** |アップロードしたファイルを BLOB として保持する BLOB コンテナーの名前を指定します。 |
| **BlobDirectory** |アップロードしたファイルがブロック BLOB として格納される BLOB ディレクトリを指定します。 BLOB ディレクトリは仮想階層構造です。 BLOB が既に存在する場合は置き換えられます。 |
| **LocalDirectory** |アップロードするファイルが含まれているローカル ディレクトリを指定します。 |
| **FileName** |指定された名前のパターンを使用したファイルを選択するための名前フィルターを指定します。 たとえば、MySheet\*.xls\* には、MySheet001.xls や MySheetABC.xlsx のようなファイルが含まれます。 |
| **TimeRangeFrom/TimeRangeTo** |時間範囲フィルターを指定します。 *TimeRangeFrom* から *TimeRangeTo* までの間に変更されたファイルが含まれます。 |

> [!NOTE]
> 転送を試す前に、**AzureStorageConnection** の資格情報が正しく、**BlobContainer** が存在することを確認する必要があります。
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Azure BLOB ストレージからデータをダウンロードする
SSIS を使用して Azure Blob Storage からオンプレミスのストレージにデータをダウンロードするには、[Azure BLOB ダウンロード タスク](https://msdn.microsoft.com/library/mt146779.aspx)のインスタンスを使用します。

## <a name="more-advanced-ssis-azure-scenarios"></a>さらに高度な SSIS と Azure のシナリオ
SSIS 機能パックでは、複数のタスクを 1 つにパッケージ化することで、さらに複雑なフローを処理できます。 たとえば、BLOB データを HDInsight クラスターに直接フィードし、HDInsight クラスターの出力を BLOB にダウンロードしてから、さらにオンプレミス ストレージにダウンロードすることができます。 SSIS は、追加の SSIS コネクタを使用することで、HDInsight クラスター上で Hive ジョブおよび Pig ジョブを実行できます。

* SSIS を使用して Azure HDInsight クラスター上で Hive スクリプトを実行するには、 [Azure HDInsight Hive タスク](https://msdn.microsoft.com/library/mt146771.aspx)を使用します。
* SSIS を使用して Azure HDInsight クラスター上で Pig スクリプトを実行するには、 [Azure HDInsight Pig タスク](https://msdn.microsoft.com/library/mt146781.aspx)を使用します。

