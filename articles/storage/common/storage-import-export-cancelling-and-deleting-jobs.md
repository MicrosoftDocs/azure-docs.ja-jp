---
title: Azure Import/Export ジョブの取り消しおよび削除 | Microsoft Docs
description: Microsoft Azure Import/Export サービスのジョブを取り消したり削除したりする方法について説明します。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 6eb56413319208feef1b4ab51296fe12a1e0bcf2
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700130"
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Azure Import/Export ジョブの取り消しと削除

 ジョブが `Packaging` 状態になる前に取り消しを要求するには、[ジョブ プロパティの更新](/rest/api/storageimportexport/jobs)操作を呼び出して `CancelRequested` 要素を `true` に設定します。 ジョブの取り消しはベストエフォートで実行されます。 ドライブでデータ転送が実行中である場合は、取り消しが要求された後でも、データの転送が継続されることがあります。

 取り消されたジョブは `Completed` 状態に移行し、90 日間維持された後、削除されます。

 ジョブを削除するには、ジョブが送られる前 (つまり、ジョブが `Creating` 状態である間) に、[ジョブの削除](/rest/api/storageimportexport/jobs)操作を呼び出します。 ジョブの削除は、ジョブが `Completed` 状態のときにも実行できます。 ジョブが削除された後は、REST API や Azure Portal を通じて、そのジョブの情報と状態にアクセスできなくなります。

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>次の手順

* [Import/Export サービス REST API の使用](storage-import-export-using-the-rest-api.md)
