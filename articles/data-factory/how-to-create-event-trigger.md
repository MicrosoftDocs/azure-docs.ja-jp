---
title: Azure Data Factory でイベントベースのトリガーを作成する | Microsoft Docs
description: イベントに応答してパイプラインを実行するトリガーを Azure Data Factory で作成する方法について説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/18/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 94c9c3f997143d72262c1ba3d8dbfea90d6f920c
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576618"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>イベントに応答してパイプラインを実行するトリガーを作成する

この記事では、Data Factory のパイプラインで作成できるイベントベースのトリガーについて説明します。

イベントドリブン アーキテクチャ (EDA) は、イベントの運用、検出、使用、および応答を含む一般的なデータ統合パターンです。 多くの場合、データ統合シナリオでは、Data Factory ユーザーがイベントに基づいてパイプラインをトリガーする必要があります。 Data Factory は [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) と統合され、イベントに対してパイプラインをトリガーすることができるようになりました。

この機能の概要とデモンストレーションについては、以下の 10 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> この記事で説明されている統合は、[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) に依存しています。 サブスクリプションが Event Grid リソース プロバイダーに登録されていることを確認してください。 詳細については、「[リソース プロバイダーと種類](../azure-resource-manager/resource-manager-supported-services.md#azure-portal)」を参照してください。

## <a name="data-factory-ui"></a>Data Factory UI

### <a name="create-a-new-event-trigger"></a>新しいイベント トリガーを作成する

一般的なイベントは、Azure Storage アカウントでのファイルの到着、またはファイルの削除です。 Data Factory のパイプラインでこのイベントに応答するトリガーを作成できます。

> [!NOTE]
> この統合では、バージョン 2 のストレージ アカウント (汎用) のみがサポートされます。

![新しいイベント トリガーを作成する](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="configure-the-event-trigger"></a>イベント トリガーを構成する

**[Blob path begins with]\(Blob パスの先頭\)** と **[Blob path ends with]\(Blob パスの末尾\)** のプロパティでは、イベントを受け取るコンテナー、フォルダー、および BLOB の名前を指定できます。 この記事で後述する例に示すように、**[Blob path begins with]\(Blob パスの先頭\)** と **[Blob path ends with]\(Blob パスの末尾\)** のプロパティにはさまざまなパターンを使用できます。 これらのプロパティの少なくとも 1 つを指定する必要があります。

![イベント トリガーを構成する](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="select-the-event-trigger-type"></a>イベント トリガーの種類を選択する

ファイルがストレージの場所に到着し、対応する BLOB が作成されるとすぐに、このイベントがトリガーされ、Data Factory パイプラインが実行されます。 Data Factory のパイプラインで、BLOB 作成イベント、BLOB 削除イベント、または両方のイベントに応答するトリガーを作成できます。

![イベントとしてトリガーの種類を選択する](media/how-to-create-event-trigger/event-based-trigger-image3.png)

### <a name="map-trigger-properties-to-pipeline-parameters"></a>トリガー プロパティをパイプライン パラメーターにマップする

特定の BLOB に対してイベント トリガーが発生した場合、イベントはその BLOB のフォルダー パスとファイル名を、プロパティ `@triggerBody().folderPath` および `@triggerBody().fileName` にキャプチャします。 パイプラインでこれらのプロパティの値を使用するには、プロパティをパイプライン パラメーターにマップする必要があります。 プロパティをパラメーターにマップしたら、パイプライン全体で `@pipeline().parameters.parameterName` 式を使用して、トリガーでキャプチャされた値にアクセスできます。

![パイプライン パラメーターへのプロパティのマッピング](media/how-to-create-event-trigger/event-based-trigger-image4.png)

たとえば、前のスクリーンショットでは、 `.csv` で終わる BLOB パスがストレージ アカウントで作成されたときに発生するようにトリガーが構成されています。 その結果、`.csv` 拡張子を持つ BLOB がストレージ アカウント内の任意の場所で作成されたときに、`folderPath` および `fileName` プロパティでは新しい BLOB の場所がキャプチャされます。 たとえば、`@triggerBody().folderPath` が `/containername/foldername/nestedfoldername` などの値を持ち、`@triggerBody().fileName` が `filename.csv` などの値を持つとします。 この例では、これらの値はパイプライン パラメーターの `sourceFolder` および `sourceFile` にマップされます。 これらをそれぞれ `@pipeline().parameters.sourceFolder` および `@pipeline().parameters.sourceFile` として、パイプライン全体で使用することができます。

## <a name="json-schema"></a>JSON スキーマ

次の表に、イベントベースのトリガーに関連するスキーマ要素の概要を示します。

| **JSON 要素** | **説明** | **Type** | **使用できる値** | **必須** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | ストレージ アカウントの Azure Resource Manager リソース ID。 | String | Azure Resource Manager ID | はい |
| **events** | このトリガーを起動するイベントの種類。 | Array    | Microsoft.Storage.BlobCreated、Microsoft.Storage.BlobDeleted | はい、これらの値の任意の組み合わせが必須です。 |
| **blobPathBeginsWith** | BLOB パスは、トリガーを起動するために指定されているパターンで始まる必要があります。 たとえば、`/records/blobs/december/` のみが、`records` コンテナー下の `december` フォルダーにあるブロブのトリガーを起動します。 | String   | | これらのプロパティ (`blobPathBeginsWith` または `blobPathEndsWith`) の少なくとも 1 つの値を指定する必要があります。 |
| **blobPathEndsWith** | BLOB パスは、トリガーを起動するために指定されているパターンで終わる必要があります。 たとえば、`december/boxes.csv` のみが、`december` フォルダー内の `boxes` というブロブのトリガーを起動します。 | String   | | これらのプロパティ (`blobPathBeginsWith` または `blobPathEndsWith`) の少なくとも 1 つの値を指定する必要があります。 |

## <a name="examples-of-event-based-triggers"></a>イベントベース トリガーの例

このセクションでは、イベントベースのトリガー設定の例を示します。

> [!IMPORTANT]
> 以下の例に示すように、コンテナーとフォルダー、コンテナーとファイル、またはコンテナー、フォルダー、およびファイルを指定するたびに、パスの `/blobs/` セグメントを含める必要があります。

| プロパティ | 例 | 説明 |
|---|---|---|
| **次で始まる BLOB パス** | `/containername/` | コンテナー内の任意の BLOB のイベントを受信します。 |
| **次で始まる BLOB パス** | `/containername/blobs/foldername/` | `containername`コンテナーおよび `foldername` フォルダー内の任意の BLOB のイベントを受信します。 |
| **次で始まる BLOB パス** | `/containername/blobs/foldername/subfoldername/` | サブフォルダーを参照することもできます。 |
| **次で始まる BLOB パス** | `/containername/blobs/foldername/file.txt` | `containername` コンテナー下の `foldername` フォルダー内にある `file.txt` という名前の BLOB のイベントを受信します。 |
| **次で終わる BLOB パス** | `file.txt` | 任意のパスで `file.txt` という名前の BLOB のイベントを受信します。 |
| **次で終わる BLOB パス** | `/containername/blobs/file.txt` | コンテナー `containername` 下の `file.txt` という名前の BLOB のイベントを受信します。 |
| **次で終わる BLOB パス** | `foldername/file.txt` | 任意のコンテナー下の `foldername` フォルダーにある `file.txt` という名前の BLOB のイベントを受信します。 |

## <a name="next-steps"></a>次の手順
トリガーについて詳しくは、「[Azure Data Factory でのパイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md#triggers)」をご覧ください。
