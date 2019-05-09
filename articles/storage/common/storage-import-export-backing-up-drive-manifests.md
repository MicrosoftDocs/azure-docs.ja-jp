---
title: Azure Import/Export ドライブ マニフェストのバックアップ | Microsoft Docs
description: Microsoft Azure Import/Export サービスのドライブ マニフェストを自動的にバックアップする方法について説明します。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: ea223ea3ccd113014ceabff34cc4d0174abb1ddf
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55694348"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Azure Import/Export ジョブのドライブ マニフェストのバックアップ

ドライブ マニフェストは、[Put Job](/rest/api/storageimportexport/jobs) または [Update Job Properties](/rest/api/storageimportexport/jobs) REST API 操作で `BackupDriveManifest` プロパティを `true` に設定することで、BLOB に自動的にバックアップできます。 既定では、ドライブ マニフェストはバックアップされません。 ドライブ マニフェストのバックアップは、ジョブに関連付けられたストレージ アカウント内のコンテナーに、ブロック BLOB として保存されます。 既定では、コンテナー名は `waimportexport` ですが、`Put Job` または `Update Job Properties` 操作を呼び出す際に、`DiagnosticsPath` プロパティで異なる名前を指定することもできます。 バックアップ マニフェスト BLOB は、次の形式で命名されます: `waies/jobname_driveid_timestamp_manifest.xml`。

 ジョブのバックアップ ドライブ マニフェストの URI は、[Get Job](/rest/api/storageimportexport/jobs) 操作を呼び出すことで取得できます。 BLOB URI は、各ドライブの `ManifestUri` プロパティで返されます。

## <a name="next-steps"></a>次の手順

* [Import/Export サービス REST API の使用](storage-import-export-using-the-rest-api.md)
