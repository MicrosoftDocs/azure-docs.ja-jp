---
title: azcopy make | Microsoft Docs
description: この記事では、azcopy make コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 9afcd8de1af42424649dd8e44fc07f7bfd881257
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196737"
---
# <a name="azcopy-make"></a>azcopy make

コンテナーまたはファイル共有を作成します。

## <a name="synopsis"></a>概要

指定されたリソース URL で表されるコンテナーまたはファイル共有を作成します。

```azcopy
azcopy make [resourceURL] [flags]
```

## <a name="examples"></a>例

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## <a name="options"></a>オプション

|オプション|説明|
|--|--|
|-h, --help|make コマンドのヘルプ コンテンツを表示します。 |
|--quota-gb uint32|共有の最大サイズをギガバイト (GiB) 単位で指定します。0 は、ファイルサービスの既定のクォータを受け入れることを示します。|

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

|オプション|説明|
|---|---|
|--cap-mbps uint32|転送速度を制限します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。|
|--output-type string|コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。|

## <a name="see-also"></a>関連項目

- [azcopy](storage-ref-azcopy.md)
