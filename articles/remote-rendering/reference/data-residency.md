---
title: データの保存場所
description: Azure Remote Rendering を使用するときのデータの保存場所について説明します。
author: rapete
ms.author: rapete
ms.date: 02/04/2021
ms.topic: reference
ms.openlocfilehash: f20b3bb3de877ac627f5f6909c98cb9e1553f2a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "99577455"
---
# <a name="azure-remote-rendering-data-residency"></a>Azure Remote Rendering データの保存場所 
この記事では、データ所在地の概念と、それが Azure Remote Rendering にどのように適用されるかについて説明します。 

## <a name="data-residency"></a>データの保存場所 
Azure Remote Rendering では、ユーザーが指定した Azure Blob コンテナーをモデル ストレージに使用します。 モデルが使用されていない場合は、ユーザーが指定した Azure Blob Storage 領域にとどまります。 モデルがレンダリングに使用されると、レンダリング セッションを開始するときにユーザーが選択した領域にデータがコピーされます。

## <a name="next-steps"></a>次のステップ
Azure Remote Rendering 用の Azure Blob Storage コンテナーを設定する方法については、「[モデルをレンダリング用に変換する](../quickstarts/convert-model.md)」を参照してください。
