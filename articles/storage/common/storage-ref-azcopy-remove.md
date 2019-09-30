---
title: azcopy remove | Microsoft Docs
description: この記事では、azcopy remove コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0cc366ab2cdad9c7258dca905d8f4a06472119fe
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196797"
---
# <a name="azcopy-remove"></a>azcopy remove

Azure Storage Blob、Azure ファイル、および Azure Data Lake Storage Gen2 からエンティティを削除します。

## <a name="synopsis"></a>概要

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="examples"></a>例

SAS を使用して 1 つの BLOB を削除します。

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

SAS を使用して仮想ディレクトリ全体を削除します。

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

仮想ディレクトリ内の上位の BLOB のみを削除し、サブディレクトリは削除しません。

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

仮想ディレクトリ内の BLOB のサブセットを削除します (例: jpg ファイルおよび pdf ファイルのみ、または BLOB 名が "exactName" の場合)。

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

仮想ディレクトリ全体を削除しますが、スコープから特定の BLOB を除外します (たとえば、foo で始まるすべての BLOB や、bar で終わるすべての BLOB)。

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

1 つのファイルを Data Lake Storage Gen2 から削除します (include および exclude はサポートされていません)。

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

1 つのディレクトリを Data Lake Storage Gen2 から削除します (include および exclude はサポートされていません)。

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>オプション

|オプション|説明|
|--|--|
|--exclude string|名前がパターンの一覧と一致するファイルを除外します。 例: *.jpg;* .pdf;exactName|
|-h, --help|remove コマンドのヘルプ コンテンツを表示します。|
|--include string|名前がパターンの一覧と一致するファイルのみを含めます。 例: *.jpg;* .pdf;exactName|
|--log-level string|ログ ファイルのログの詳細度を定義します。 利用可能なレベルは次のとおりです。INFO (すべての要求/応答)、WARNING (遅い応答)、ERROR (失敗した要求のみ)、NONE (出力ログなし)。 (既定値は "INFO")|
|--recursive|ディレクトリ間で同期するときにサブディレクトリを再帰的に検索します。|

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

|オプション|説明|
|---|---|
|--cap-mbps uint32|転送速度を制限します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。|
|--output-type string|コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。|

## <a name="see-also"></a>関連項目

- [azcopy](storage-ref-azcopy.md)
