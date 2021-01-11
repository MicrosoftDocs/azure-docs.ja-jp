---
author: baanders
description: Azure Digital Twins 設定でのロールの割り当てを確認するためのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: fdb3bd034d93e623037be9fa0721a805924af5c3
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88864859"
---
ロールの割り当てを正常に設定したことを確認するための 1 つの方法として、[Azure portal](https://portal.azure.com) で Azure Digital Twins インスタンスのロールの割り当てを表示します。 Azure portal で Azure Digital Twins インスタンスに移動します ([Azure Digital Twins インスタンス](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances)のページで確認するか、ポータルの検索バーで名前を検索することができます)。

次に、 *[アクセス制御 (IAM)] > [ロールの割り当て]* で、それに割り当てられているすべてのロールを表示します。 ユーザーは、 *[Azure Digital Twins 所有者 (プレビュー)]* のロールで一覧に表示されます。 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Azure portal での Azure Digital Twins インスタンスのロールの割り当てのビュー":::