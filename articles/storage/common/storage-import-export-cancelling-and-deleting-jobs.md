---
title: "Azure Import/Export ジョブの取り消しおよび削除 | Microsoft Docs"
description: "Microsoft Azure Import/Export サービスのジョブを取り消したり削除したりする方法について説明します。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: fd3d66f0-1dbb-4c75-9223-307d5abaeefc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 1e989c72fc03697bf6d2e515ff53003703665d1a
ms.contentlocale: ja-jp
ms.lasthandoff: 08/23/2017

---

# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Azure Import/Export ジョブの取り消しと削除

 ジョブが `Packaging` 状態になる前に取り消しを要求するには、[ジョブ プロパティの更新](/rest/api/storageimportexport/jobs#Jobs_Update)操作を呼び出して `CancelRequested` 要素を `true` に設定します。 ジョブの取り消しはベストエフォートで実行されます。 ドライブでデータ転送が実行中である場合は、取り消しが要求された後でも、データの転送が継続されることがあります。

 取り消されたジョブは `Completed` 状態に移行し、90 日間維持された後、削除されます。

 ジョブを削除するには、ジョブが送られる前 (つまり、ジョブが `Creating` 状態である間) に、[ジョブの削除](/rest/api/storageimportexport/jobs#Jobs_Delete)操作を呼び出します。 ジョブの削除は、ジョブが `Completed` 状態のときにも実行できます。 ジョブが削除された後は、REST API や Azure Portal を通じて、そのジョブの情報と状態にアクセスできなくなります。

## <a name="next-steps"></a>次のステップ

* [Import/Export サービス REST API の使用](storage-import-export-using-the-rest-api.md)

