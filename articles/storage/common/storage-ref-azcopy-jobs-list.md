---
title: azcopy jobs list | Microsoft Docs
description: この記事では、azcopy jobs list コマンドの参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ff10a1e4544f80d618d6989ab251096e6cb77baf
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196821"
---
# <a name="azcopy-jobs-list"></a>azcopy jobs list

すべてのジョブに関する情報を表示します。

## <a name="synopsis"></a>概要

```azcopy
azcopy jobs list [flags]
```

## <a name="options"></a>オプション

|オプション|説明|
|--|--|
|-h, --help|list コマンドのヘルプ コンテンツを表示します。|

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

|オプション|説明|
|---|---|
|--cap-mbps uint32|転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。|
|--output-type string|コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。|

## <a name="see-also"></a>関連項目

- [azcopy jobs](storage-ref-azcopy-jobs.md)
