---
title: Azure CDN from Verizon Premium ルール エンジンの条件式 | Microsoft Docs
description: Azure CDN from Verizon Premium ルール エンジンの一致条件と機能に関するリファレンス ドキュメント。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: f790e37ae876c0640d55ebfb51abb43c6a705f04
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593216"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Azure CDN from Verizon Premium ルール エンジンの条件式

この記事では、Azure Content Delivery Network (CDN) [ルール エンジン](cdn-verizon-premium-rules-engine.md)の条件式について詳しく説明します。

ルールの最初の部分は、条件式です。

条件式 | 説明
-----------------------|-------------
IF | IF 式は、常にルール内のステートメントの最初の部分です。 他のすべての条件式と同様に、この IF ステートメントは一致と関連付ける必要があります。 追加の条件式が定義されていない場合は、この一致によって、一連の機能が要求に適用される前に満たす必要がある条件が決定されます。
AND IF | AND IF 式は、次の種類の条件式の後にのみ追加できます: IF、AND IF。 これは、最初の IF ステートメントで別の条件を満たす必要があることを示します。
ELSE IF| ELSE IF 式では、この ELSE IF ステートメントで指定した一連の機能が行われる前に、満たす必要がある代替条件を指定します。 ELSE IF ステートメントの存在は、前のステートメントが終わったことを示します。 ELSE IF ステートメントの後に配置できる条件式は、別の ELSE IF ステートメントのみです。 つまり ELSE IF ステートメントは、満たす必要がある追加条件を 1 つ指定するときのみに使用される場合があります。

**例**:![CDN の一致条件](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > 前の規則で指定したアクションは、後続の規則でオーバーライドされます。
   > 例:キャッチ オール ルールで、すべての要求をトークン ベースの認証によってセキュリティで保護します。 特定の種類の要求で例外を設定するために、そのすぐ下に別のルールを作成する場合があります。

## <a name="next-steps"></a>次の手順

- [Azure CDN の概要](cdn-overview.md)
- [ルール エンジンのリファレンス](cdn-verizon-premium-rules-engine-reference.md)
- [ルール エンジンの一致条件](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [ルール エンジンの機能](cdn-verizon-premium-rules-engine-reference-features.md)
- [規則エンジンを使用した既定の HTTP 動作のオーバーライド](cdn-verizon-premium-rules-engine.md)