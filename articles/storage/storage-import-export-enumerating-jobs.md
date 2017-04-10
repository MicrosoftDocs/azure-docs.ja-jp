---
title: "すべての Azure Import/Export ジョブを列挙する | MicrosoftDocs"
description: "サブスクリプションに含まれるすべての Azure Import/Export サービス ジョブを列挙する方法について説明します。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f2e619be-1bbd-4a54-9472-9e2f70a83b64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 1977bfc0e516088310f45ecdd960287eeed2c2d8
ms.lasthandoff: 03/30/2017


---

# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Azure Import/Export サービス内のジョブの列挙
サブスクリプション内のすべてのジョブを列挙するには、[List Jobs](/rest/api/storageimportexport/jobs#Jobs_List) 操作を呼び出します。 `List Jobs` は、ジョブの一覧と次の属性を返します。

-   ジョブのタイプ (インポートまたはエクスポート)

-   ジョブの現在の状態

-   ジョブに関連付けられたストレージ アカウント

## <a name="next-steps"></a>次のステップ

* [Import/Export サービス REST API の使用](storage-import-export-using-the-rest-api.md)

