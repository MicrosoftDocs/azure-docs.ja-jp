---
title: azcopy doc | Microsoft Docs
description: この記事では、azcopy doc コマンドの参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d1f57ae40b47c1d910ba20ae8a8f19cdc6908d6b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196837"
---
# <a name="azcopy-doc"></a>azcopy doc

ツールのドキュメントをマークダウン形式で生成します。

## <a name="synopsis"></a>概要

ツールのドキュメントをマークダウン形式で生成し、指定された場所に格納します。

既定では、ファイルは現在のディレクトリ内の "doc" という名前のフォルダーに格納されます。

```azcopy
azcopy doc [flags]
```

## <a name="options"></a>オプション

|オプション|説明|
|--|--|
|-h, --help|doc コマンドのヘルプ コンテンツを表示します。|

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

|オプション|説明|
|---|---|
|--cap-mbps uint32|転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。|
|--output-type string|コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。|

## <a name="see-also"></a>関連項目

- [azcopy](storage-ref-azcopy.md)
