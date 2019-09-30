---
title: ストリーミング インジェストを使用して Azure Data Explorer にデータを取り込む
description: ストリーミング インジェストを使用して、Azure Data Explorer にデータを取り込む (読み込む) 方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 712273ddfb8b6f781627e2cc7915a1f538f57b4d
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090627"
---
# <a name="streaming-ingestion-preview"></a>ストリーミング インジェスト (プレビュー)

ストリーミング インジェストは、多様なボリューム データでインジェスト時間が 10 秒未満の低待機時間を必要とするシナリオを対象とします。 各テーブルへのデータ ストリームが比較的小さい (1 秒あたりのレコード数が少ない) 1 つまたは複数のデータベースで、数多くのテーブルの操作処理を最適化するために使用されますが、データ インジェスト ボリューム全体が高くなります (1 秒あたり数千レコード)。

データ量がテーブルごとに 1 MB/秒を超える場合は、ストリーミング インジェストではなく、従来の (一括) インジェストを使用します。 さまざまなインジェスト方法の詳細については、「[Azure データ エクスプローラーでのデータ インジェスト](/azure/data-explorer/ingest-data-overview)」を参照してください。

> [!NOTE]
> ストリーミング インジェストでは、次の機能はサポートされていません。
> * [データベース カーソル](/azure/kusto/management/databasecursor)。
> * [データ マッピング](/azure/kusto/management/mappings)。 [事前に作成された](/azure/kusto/management/tables#create-ingestion-mapping)データ マッピングのみがサポートされています。 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Web UI](https://dataexplorer.azure.com/) にサインインします。
* [Azure Data Explorer クラスターとデータベース](create-cluster-database-portal.md)を作成する

## <a name="enable-streaming-ingestion-on-your-cluster"></a>クラスターでストリーミング インジェストを有効にする

1. Azure portal で、Azure Data Explorer クラスターに移動します。 **[設定]** で **[構成]** を選択します。 
1. **[構成]** ウィンドウで、 **[オン]** を選択して **[ストリーミング インジェスト]** を有効にします。
1. **[保存]** を選択します。
 
    ![ストリーミング インジェストのオン](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. [Web UI](https://dataexplorer.azure.com/) で、ストリーミング データを受信するテーブルまたはデータベースに[ストリーミング インジェスト ポリシー](/azure/kusto/concepts/streamingingestionpolicy)を定義します。 

    > [!NOTE]
    > * ポリシーがデータベース レベルで定義されている場合、データベース内のすべてのテーブルでストリーミング インジェストが有効になります。
    > * 適用されるポリシーでは、新しく取り込まれたたデータのみを参照でき、データベース内の他のテーブルは参照できません。

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>ストリーミング インジェストを使用してクラスターにデータを取り込む

2 種類のストリーミング インジェストがサポートされています。

* データ ソースとして使用される[イベント ハブ](/azure/data-explorer/ingest-data-event-hub)
* カスタム インジェストでは、Azure Data Explorer クライアント ライブラリのいずれかを使用するアプリケーションを作成する必要があります。 サンプル アプリケーションについては、[ストリーミング インジェストのサンプル](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample)を参照してください。

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>適切なストリーミング インジェストの種類を選択する

|   |イベント ハブ  |カスタム インジェスト  |
|---------|---------|---------|
|インジェスト開始からクエリでデータが使用可能になるまでのデータ遅延   |    より長い遅延     |   より短い遅延      |
|開発のオーバーヘッド    |   高速で簡単なセットアップ、開発オーバーヘッドなし    |   アプリケーションでエラーを処理し、データの一貫性を確保するための高い開発オーバーヘッド     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>クラスターでストリーミング インジェストを無効にする

> [!WARNING]
> ストリーミング インジェストの無効化には数時間かかることがあります。

1. 関連するすべてのテーブルとデータベースから[ストリーミング インジェスト ポリシー](/azure/kusto/concepts/streamingingestionpolicy)をドロップします。 ストリーミング インジェスト ポリシーの削除によって、初期ストレージから列ストア (エクステントまたはシャード) 内の永続的なストレージへのストリーミング インジェストのデータ移動がトリガーされます。 データ移動には、初期ストレージのデータ量とクラスターの CPU およびメモリ使用率に応じて、数秒から数時間かかります。
1. Azure portal で、Azure Data Explorer クラスターに移動します。 **[設定]** で **[構成]** を選択します。 
1. **[構成]** ウィンドウで、 **[オフ]** を選択して **[ストリーミング インジェスト]** を無効にします。
1. **[保存]** を選択します。

    ![ストリーミング インジェストのオフ](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>制限事項

* ストリーミング インジェストのパフォーマンスと容量は、VM とクラスターのサイズを増やして拡張されます。 1 つの D14 ノードの場合、推奨される負荷は 1 秒あたり最大 150 個の要求です。
* 現時点で、サポートは 8 および 16 コア SKU (D13、D14、L8、L16) に対してのみです。
* インジェスト要求ごとのデータ サイズの制限は 4 MB です。
* テーブルとインジェスト マッピングの作成や変更など、スキーマの更新には、ストリーミング インジェスト サービスに最大 5 分かかることがあります。
* クラスターでストリーミング インジェストを有効にすると、データがストリーミング経由で取り込まれていない場合でも、インジェスト データをストリーミングするためにクラスター マシンのローカル SSD ディスクの一部を使用して、ホット キャッシュに使用できるストレージを減らします。

## <a name="next-steps"></a>次の手順

* [Azure Data Explorer でデータのクエリを実行する](web-query-data.md)
