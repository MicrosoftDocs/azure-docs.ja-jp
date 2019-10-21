---
title: azcopy doc | Microsoft Docs
description: この記事では、azcopy doc コマンドの参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b11d831be847716d4edfac4f9a0725e07a60c52a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513854"
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
