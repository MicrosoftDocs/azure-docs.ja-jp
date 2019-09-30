---
title: azcopy logout | Microsoft Docs
description: この記事では、azcopy logout コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f42e2517f8c40855e56bd062fe8bc9b22634d4dc
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196801"
---
# <a name="azcopy-logout"></a>azcopy logout

ユーザーをログアウトし、Azure Storage リソースへのアクセスを終了します。

## <a name="synopsis"></a>概要

このコマンドは、現在のユーザーのキャッシュされているすべてのログイン情報を削除します。

```azcopy
azcopy logout [flags]
```

## <a name="options"></a>オプション

|オプション|説明|
|--|--|
|-h, --help|logout コマンドのヘルプ コンテンツを表示します。|

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

|オプション|説明|
|---|---|
|--cap-mbps uint32|転送速度を制限します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。|
|--output-type string|コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。|

## <a name="see-also"></a>関連項目

- [azcopy](storage-ref-azcopy.md)
