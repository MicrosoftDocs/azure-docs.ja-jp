---
title: Azure Import/Export サービス REST API の使用 | Microsoft Docs
description: Azure Import/Export サービス REST API の使用に関する、方法や参考資料を含むリソースの場所について説明します。
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 833b8c79fba57b7129092e084381c0671c396496
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978868"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Azure Import/Export サービス REST API の使用

Microsoft Azure Import/Export サービスでは、インポート/エクスポート ジョブのプログラムによる制御を有効にする REST API を公開します。 REST API を使用すると、すべてのインポート/エクスポート操作を [Azure Portal](https://portal.azure.com/) で実行できます。 また、REST API を使用して、ジョブの完了率の照会 (現時点で Azure Portal では利用できません) などの詳細な操作を実行することもできます。

Import/Export サービスの概要については、「[Microsoft Azure Import/Export サービスを使用した Blob Storage へのデータの転送](../storage-import-export-service.md)」を参照してください。ポータルを使用してインポート ジョブやエクスポート ジョブを作成および管理する方法も説明されています。

## <a name="service-endpoints"></a>サービス エンドポイント

Azure Import/Export サービスは、Azure Resource Manager のリソース プロバイダーであり、インポート/エクスポート ジョブを管理するために次の HTTPS エンドポイントで REST API のセットを提供します。

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>バージョン管理

Import/Export サービスに対する要求では、`api-version` パラメーターを指定し、その値を `2016-11-01` に設定する必要があります。

## <a name="importexport-service-operations"></a>インポート/エクスポート サービス操作

[インポート ジョブの作成](../storage-import-export-creating-an-import-job.md)

[エクスポート ジョブの作成](../storage-import-export-creating-an-export-job.md)

[ジョブの状態情報の取得](storage-import-export-retrieving-state-info-for-a-job.md)

[ジョブの列挙](../storage-import-export-enumerating-jobs.md)

[ジョブのキャンセルと削除](storage-import-export-cancelling-and-deleting-jobs.md)

[ドライブ マニフェストのバックアップ](../storage-import-export-backing-up-drive-manifests.md)

[Import/Export ジョブの診断とエラーからの回復](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>次の手順

* [ストレージの Import/Export REST](/rest/api/storageimportexport)
