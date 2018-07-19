---
title: Azure Data Factory でイベントベースのトリガーを作成する | Microsoft Docs
description: イベントに応答してパイプラインを実行するトリガーを Azure Data Factory で作成する方法について説明します。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: douglasl
ms.openlocfilehash: a9c15b239ee0bd0dde0b1f11691565b2676e3d07
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062123"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>イベントに応答してパイプラインを実行するトリガーを作成する

この記事では、Data Factory のパイプラインで作成できるイベントベースのトリガーについて説明します。

イベントドリブン アーキテクチャ (EDA) は、イベントの運用、検出、使用、および応答を含む一般的なデータ統合パターンです。 多くの場合、データ統合シナリオでは、Data Factory ユーザーがイベントに基づいてパイプラインをトリガーする必要があります。 Data Factory は [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) と統合され、イベントに対してパイプラインをトリガーすることができるようになりました。

> [!NOTE]
> Event Gridに関しては[Azure Event Grid](https://azure.microsoft.com/services/event-grid/)を確認してください。本機能を利用するにあたっては、サブスクリプションがEvent Gridリソースプロバイダを登録していることを確認してください。詳細については、[リソース プロバイダーと種類](../azure-resource-manager/resource-manager-supported-services.md#portal)をご覧ください。

## <a name="data-factory-ui"></a>Data Factory UI

### <a name="create-a-new-event-trigger"></a>新しいイベント トリガーを作成する

一般的なイベントは、Azure Storage アカウントでのファイルの到着、またはファイルの削除です。 Data Factory のパイプラインでこのイベントに応答するトリガーを作成できます。

> [!NOTE]
> この統合では、バージョン 2 のストレージ アカウント (汎用) のみがサポートされます。

![新しいイベント トリガーを作成する](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="select-the-event-trigger-type"></a>イベント トリガーの種類を選択する

ファイルがストレージの場所に到着し、対応する BLOB が作成されるとすぐに、このイベントがトリガーされ、Data Factory パイプラインが実行されます。 Data Factory のパイプラインで、BLOB 作成イベント、BLOB 削除イベント、または両方のイベントに応答するトリガーを作成できます。

![イベントとしてトリガーの種類を選択する](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="configure-the-event-trigger"></a>イベント トリガーを構成する

**[Blob path begins with]\(Blob パスの先頭\)** と **[Blob path ends with]\(Blob パスの末尾\)** のプロパティでは、イベントを受け取るコンテナー、フォルダー、および BLOB の名前を指定できます。 この記事で後述する例に示すように、**[Blob path begins with]\(Blob パスの先頭\)** と **[Blob path ends with]\(Blob パスの末尾\)** のプロパティにはさまざまなパターンを使用できます。 これらのプロパティの少なくとも 1 つを指定する必要があります。

![イベント トリガーを構成する](media/how-to-create-event-trigger/event-based-trigger-image3.png)

## <a name="json-schema"></a>JSON スキーマ

次の表に、イベントベースのトリガーに関連するスキーマ要素の概要を示します。

| **JSON 要素** | **説明** | **種類** | **使用できる値** | **必須** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | ストレージ アカウントの Azure Resource Manager リソース ID。 | String | Azure Resource Manager ID | [はい] |
| **events** | このトリガーを起動するイベントの種類。 | Array    | Microsoft.Storage.BlobCreated、Microsoft.Storage.BlobDeleted | はい (任意の組み合わせ)。 |
| **blobPathBeginsWith** | BLOB パスは、トリガーを起動するために指定されているパターンで始まる必要があります。 たとえば、'/records/blobs/december/' の場合、レコード コンテナーの december フォルダー内にある BLOB のトリガーのみが起動されます。 | String   | | blobPathBeginsWith と blobPathEndsWith のプロパティの少なくとも 1 つを指定する必要があります。 |
| **blobPathEndsWith** | BLOB パスは、トリガーを起動するために指定されているパターンで終わる必要があります。 たとえば、'december/boxes.csv' の場合、december フォルダー内の boxes という名前の BLOB のトリガーのみが起動されます。 | String   | | blobPathBeginsWith と blobPathEndsWith のプロパティの少なくとも 1 つを指定する必要があります。 |

## <a name="examples-of-event-based-triggers"></a>イベントベース トリガーの例

このセクションでは、イベントベースのトリガー設定の例を示します。

-   **[Blob path begins with]\(Blob パスの先頭\)**('/containername/'): コンテナー内のすべての BLOB のイベントを受け取ります。
-   **[Blob path begins with]\(Blob パスの先頭\)**('/containername/blobs/foldername'): containername コンテナーと foldername フォルダー内のすべての BLOB のイベントを受け取ります。
-   **[Blob path begins with]\(Blob パスの先頭\)**('/containername/blobs/foldername/file.txt'): containername コンテナー以下の foldername フォルダー内にある file.txt という BLOB のイベントを受け取ります。
-   **[Blob path ends with]\(Blob パスの末尾\)**('file.txt'): 任意のパスにある file.txt という BLOB のイベントを受け取ります。
-   **[Blob path ends with]\(Blob パスの末尾\)**('/containername/blobs/file.txt'): containername コンテナー以下の foldername フォルダー内にある file.txt という BLOB のイベントを受け取ります。
-   **[Blob path ends with]\(Blob パスの末尾\)**('foldername/file.txt'): 任意のコンテナー以下の foldername フォルダー内にある file.txt という BLOB のイベントを受け取ります。

> [!NOTE]
> コンテナーとフォルダー、コンテナーとファイル、またはコンテナー、フォルダー、およびファイルを指定するたびに、パスの `/blobs/` セグメントを含める必要があります。

## <a name="using-blob-events-trigger-properties"></a>BLOB イベント トリガー プロパティの使用

BLOB イベント トリガーが発生すると、パイプラインに *folderPath* と *fileName* という 2 つの変数を使用できます。 これらの変数にアクセスするには、`@triggerBody().fileName` または `@triggerBody().folderPath` の式を使用します。

たとえば、`blobPathEndsWith` の値として `.csv` を使用して BLOB が作成されたときに発生するように構成されたトリガーがあるとします。 .csv ファイルがストレージ アカウントにドロップされるときに、*folderPath* と *fileName* で .csv ファイルの場所を示します。 たとえば、*folderPath* の値は `/containername/foldername/nestedfoldername` で、*fileName* の値は `filename.csv` です。

## <a name="next-steps"></a>次の手順
トリガーについて詳しくは、「[Azure Data Factory でのパイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md#triggers)」をご覧ください。
