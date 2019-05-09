---
title: Azure Data Factory Mapping Data Flow の代理キー変換
description: Azure Data Factory Mapping Data Flow の代理キー変換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 6243905857f0450168541f556636d90bb4d855f7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734950"
---
# <a name="azure-data-factory-mapping-data-flow-surrogate-key-transformation"></a>Azure Data Factory Mapping Data Flow の代理キー変換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

代理キー変換を使用すると、任意の非ビジネス キーの増分する値をデータ フローの行セットに追加できます。 これは、スター スキーマ分析データ モデルでディメンション テーブルを設計するときに便利です。このモデルでは、ディメンション テーブル内の各メンバーが、一意の非ビジネス キーを持つ必要があります (キンボールの DW 方法論の一環として)。

![代理キー変換](media/data-flow/surrogate.png "代理キー変換")

[キー列] は、新しい代理キー列に付ける名前です。

[開始値] は、増分する値の開始ポイントです。
