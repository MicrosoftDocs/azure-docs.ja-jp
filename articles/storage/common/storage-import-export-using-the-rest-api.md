---
title: "Azure Import/Export サービス REST API の使用 | Microsoft Docs"
description: "Azure Import/Export サービス REST API の使用に関する、方法や参考資料を含むリソースの場所について説明します。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 233f80e9-2e7f-48e0-9639-5c7785e7d743
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: b780385ad0af34bcb15639683d1aa5d689b38b50
ms.contentlocale: ja-jp
ms.lasthandoff: 08/23/2017

---
# <a name="using-the-azure-importexport-service-rest-api"></a>Azure Import/Export サービス REST API の使用

Microsoft Azure Import/Export サービスでは、インポート/エクスポート ジョブのプログラムによる制御を有効にする REST API を公開します。 REST API を使用すると、すべてのインポート/エクスポート操作を [Azure Portal](https://portal.azure.com/) で実行できます。 また、REST API を使用して、ジョブの完了率の照会 (現時点で Azure クラシック ポータルでは利用できません) などの詳細な操作を実行することもできます。

Import/Export サービスの概要、およびクラシック ポータルを使用してインポート/エクスポート ジョブを作成および管理する方法を示すチュートリアルについては、「[Microsoft Azure Import/Export サービスを使用した Blob Storage へのデータの転送](../storage-import-export-service.md)」を参照してください。

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

## <a name="next-steps"></a>次のステップ

* [ストレージの Import/Export REST](/rest/api/storageimportexport)

