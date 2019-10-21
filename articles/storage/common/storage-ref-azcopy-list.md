---
title: azcopy list | Microsoft Docs
description: この記事では、azcopy list コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b7348e3790166e1a1aecab422e571b8f2fc7cd5f
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513526"
---
# <a name="azcopy-list"></a>azcopy list

指定されたリソース内のエンティティを一覧表示します。

## <a name="synopsis"></a>概要

現在のリリースでは、BLOB コンテナーのみがサポートされています。

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="examples"></a>例

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>オプション

|オプション|説明|
|--|--|
|-h, --help|list コマンドのヘルプ コンテンツを表示します。|
|--machine-readable|ファイル サイズをバイト単位で一覧表示します。|
|--mega-units|単位を 1000 の桁で表示します (1024 ではない)。|
|--running-tally|ファイルの合計数とそのサイズをカウントします。|

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

|オプション|説明|
|---|---|
|--cap-mbps uint32|転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。|
|--output-type string|コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。|

## <a name="see-also"></a>関連項目

- [azcopy](storage-ref-azcopy.md)
