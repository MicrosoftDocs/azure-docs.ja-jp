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
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: e0a7ff391e5a03ed563912dea54c7cfe73111bcf
ms.lasthandoff: 03/30/2017


---

# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Azure Import/Export ジョブの取り消しと削除

ジョブが `Packaging` 状態になる前に、[Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) 操作を呼び出して `CancelRequested` 要素を `true` に設定することで、ジョブの取り消しを要求できます。 ジョブの取り消しはベストエフォートで実行されます。 ドライブでデータ転送が実行中である場合は、取り消しが要求された後でも、データの転送が継続されることがあります。

 取り消されたジョブは `Completed` 状態に移行され、90 日間維持されます (その後、削除されます)。

 ジョブを削除するには、ジョブが発送される前 (*つまり*、ジョブが `Creating` 状態である間) に、[Delete Job](/rest/api/storageimportexport/jobs#Jobs_Delete) 操作を呼び出します。 ジョブの削除は、ジョブが `Completed` 状態のときにも実行できます。 ジョブが削除されると、REST API や Azure ポータルを通じてそのジョブの情報と状態にアクセスすることはできなくなります。

## <a name="next-steps"></a>次のステップ

* [Import/Export サービス REST API の使用](storage-import-export-using-the-rest-api.md)

