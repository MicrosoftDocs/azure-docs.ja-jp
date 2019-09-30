---
title: azcopy jobs show | Microsoft Docs
description: この記事では、azcopy jobs show コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d4369bd0c986ee20a0796436fea47509a711de4f
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196813"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

指定されたジョブ ID の詳細情報を表示します。

## <a name="synopsis"></a>概要

フラグを指定せずにジョブ ID だけを指定した場合は、ジョブの進行状況の概要が返されます。

`with-status` フラグが設定されている場合は、指定された値を持つジョブ内の転送の一覧が表示されます。

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="options"></a>オプション

|オプション|説明|
|--|--|
|-h, --help|show コマンドのヘルプ コンテンツを表示します。|
|--with-status string|この状態のジョブの転送のみを一覧表示します。使用できる値は次のとおりです。Started、Success、Failed|

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

|オプション|説明|
|---|---|
|--cap-mbps uint32|転送速度を制限します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。|
|--output-type string|コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。|

## <a name="see-also"></a>関連項目

- [azcopy jobs](storage-ref-azcopy-jobs.md)
