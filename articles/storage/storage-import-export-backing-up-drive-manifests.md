---
title: "Azure Import/Export ドライブ マニフェストのバックアップ | Microsoft Docs"
description: "Microsoft Azure Import/Export サービスのドライブ マニフェストを自動的にバックアップする方法について説明します。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 594abd80-b834-4077-a474-d8a0f4b7928a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 33eb8e1eea8f8aa7b79ef3e54f2b1ed88dc794ae
ms.lasthandoff: 03/30/2017


---

# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Azure Import/Export ジョブのドライブ マニフェストのバックアップ

ドライブ マニフェストは、[Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) または [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) REST API 操作で `BackupDriveManifest` プロパティを `true` に設定することで、BLOB に自動的にバックアップできます。 既定では、ドライブ マニフェストはバックアップされません。 ドライブ マニフェストのバックアップは、ジョブに関連付けられたストレージ アカウント内のコンテナーに、ブロック BLOB として保存されます。 既定では、コンテナー名は `waimportexport` ですが、`Put Job` または `Update Job Properties` 操作を呼び出す際に、`DiagnosticsPath` プロパティで異なる名前を指定することもできます。 バックアップ マニフェスト BLOB は、次の形式で命名されます: `waies/jobname_driveid_timestamp_manifest.xml`。

 ジョブのバックアップ ドライブ マニフェストの URI は、[Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) 操作を呼び出すことで取得できます。 BLOB URI は、各ドライブの `ManifestUri` プロパティで返されます。

## <a name="next-steps"></a>次のステップ

* [Import/Export サービス REST API の使用](storage-import-export-using-the-rest-api.md)

