---
title: すべての Azure Import/Export ジョブを列挙する | MicrosoftDocs
description: サブスクリプションに含まれるすべての Azure Import/Export サービス ジョブを列挙する方法について説明します。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 0ae9e7fa76c8ecbb724cf0f494e648df989dff30
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2019
ms.locfileid: "55726669"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Azure Import/Export サービス内のジョブの列挙
サブスクリプション内のすべてのジョブを列挙するには、[List Jobs](/rest/api/storageimportexport/jobs) 操作を呼び出します。 `List Jobs` は、ジョブの一覧と次の属性を返します。

-   ジョブのタイプ (インポートまたはエクスポート)

-   ジョブの現在の状態

-   ジョブに関連付けられたストレージ アカウント

## <a name="next-steps"></a>次の手順

* [Import/Export サービス REST API の使用](storage-import-export-using-the-rest-api.md)
