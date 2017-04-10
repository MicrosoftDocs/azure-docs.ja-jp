---
title: "Azure CDN ルール エンジンの条件式 | Microsoft Docs"
description: "Azure CDN ルール エンジンの一致条件と機能に関するリファレンス ドキュメント。"
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
translationtype: Human Translation
ms.sourcegitcommit: dccb945e170bd3e3f23283359db25e574a2d4296
ms.openlocfilehash: 57e56c38e003cb83dcf44f455c4451d159db8a59


---

# <a name="azure-cdn-rules-engine-conditional-expressions"></a>Azure CDN ルール エンジンの条件式
このトピックでは、Azure Content Delivery Network (CDN) [ルール エンジン](cdn-rules-engine.md)の条件式について詳しく説明します。

ルールの最初の部分は、条件式です。

条件式 | Description
-----------------------|-------------
IF | IF 式は、常にルール内のステートメントの最初の部分です。 他のすべての条件式と同様に、この IF ステートメントは一致と関連付ける必要があります。 その他の条件式が定義されていない場合、一連の機能が要求に適用される前に、この一致で満たされる必要がある条件を決定します。
AND IF | AND IF 式は、次の種類の条件式の後にのみ追加できます: IF、AND IF。 これは、最初の IF ステートメントで別の条件を満たす必要があることを示します。
ELSE IF| ELSE IF 式では、この ELSE IF ステートメントで指定した一連の機能が行われる前に、満たす必要がある代替条件を指定します。 ELSE IF ステートメントの存在は、前のステートメントが終わったことを示します。 ELSE IF ステートメントの後に配置できる条件式は、別の ELSE IF ステートメントのみです。 つまり ELSE IF ステートメントは、満たす必要がある追加条件を&1; つ指定するときのみに使用される場合があります。

**例**: ![CDN の一致条件](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > 前の規則で指定したアクションは、後続の規則でオーバーライドされます。 例: キャッチ オール ルールでは、トークン ベースの認証からすべての要求をセキュリティで保護します。 特定の種類の要求で例外を設定するために、そのすぐ下に別のルールを作成する場合があります。

### <a name="next-steps"></a>次のステップ
* [Azure CDN の概要](cdn-overview.md)
* [ルール エンジンのリファレンス](cdn-rules-engine-reference.md)
* [ルール エンジンの一致条件](cdn-rules-engine-reference-match-conditions.md)
* [ルール エンジンの機能](cdn-rules-engine-reference-features.md)
* [規則エンジンを使用した既定の HTTP 動作のオーバーライド](cdn-rules-engine.md)



<!--HONumber=Jan17_HO4-->


