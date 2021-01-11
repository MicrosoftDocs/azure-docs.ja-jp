---
title: azcopy remove | Microsoft Docs
description: この記事では、azcopy remove コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 1cdb49f6865afa4101468dc35b4e416d999b63f5
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285222"
---
# <a name="azcopy-remove"></a>azcopy remove

Azure ストレージ アカウントから BLOB またはファイルを削除します。

## <a name="synopsis"></a>概要

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>関連する概念に関する記事

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)
- [AzCopy と Blob Storage でデータを転送する](storage-use-azcopy-blobs.md)
- [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)
- [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)

## <a name="examples"></a>例

SAS トークンを使用して 1 つの BLOB を削除します。

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

SAS トークンを使用して仮想ディレクトリ全体を削除します。
 
```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

仮想ディレクトリ内の BLOB のみを削除しますが、サブディレクトリや、それらのサブディレクトリ内の BLOB は削除しないでください。

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

仮想ディレクトリ内の BLOB のサブセットを削除します (例: jpg ファイルおよび pdf ファイルのみを削除する、または BLOB 名が `exactName` の場合に削除する)。

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

仮想ディレクトリ全体を削除しますが、スコープから特定の BLOB を除外します (例: foo で始まるすべての BLOB や bar で終わるすべての BLOB)。

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
```

URL エンコードされていない相対パスをファイルに配置して、特定の BLOB と仮想ディレクトリを削除します。

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
- file content:
    dir1/dir2
    blob1
    blob2
```

階層型名前空間がある BLOB ストレージ アカウントから 1 つのファイルを削除します (include/exclude はサポートされていません)。

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

階層型名前空間がある BLOB ストレージ アカウントから 1 つのディレクトリを削除します (include/exclude はサポートされていません)。

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Options

**--delete-snapshots** string   既定では、BLOB にスナップショットがある場合、削除操作は失敗します。 ルート BLOB とそのすべてのスナップショットを削除する場合は `include` を指定します。一方、スナップショットだけを削除し、ルート BLOB は保持する場合は `only` を指定します。

**--exclude-path** string   削除時にこれらのパスを除外します。 このオプションでは、ワイルドカード文字 (*) はサポートされていません。 相対パスのプレフィックスを確認します。 例: `myFolder;myFolder/subDirName/file.pdf`

**--exclude-pattern** string   名前がパターンの一覧と一致するファイルを除外します。 例: `*.jpg`、`*.pdf`、`exactName`

**--force-if-read-only**   Azure Files のファイルまたはフォルダーを削除するときに、既存のオブジェクトに読み取り専用属性が設定されている場合でも削除を強制的に実行します。

**--help**   remove のヘルプ。

**--include-path** string   削除するときに、これらのパスのみを含めます。 このオプションでは、ワイルドカード文字 (*) はサポートされていません。 相対パスのプレフィックスを確認します。 例: `myFolder;myFolder/subDirName/file.pdf`

**--include-pattern** string  名前がパターンの一覧と一致するファイルをのみを含めます。 例: *`.jpg`、* `.pdf`、`exactName`

**--list-of-files** string  削除するファイルとディレクトリの一覧を含むファイルの場所を定義します。 相対パスは改行で区切る必要があり、パスを URL エンコードすることはできません。

**--log-level** string   ログ ファイルのログ詳細度を定義します。 指定できるレベルには、`INFO` (すべての要求/応答)、`WARNING` (低速な応答)、`ERROR` (失敗した要求のみ)、`NONE` (出力ログなし) が含まれます。 (既定値は `INFO`) (既定値は `INFO`)

**--recursive**    ディレクトリ間で同期するときに、サブディレクトリ内を再帰的に調べます。

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

|オプション|説明|
|---|---|
|--cap-mbps float|転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。|
|--output-type string|コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。|
|--trusted-microsoft-suffixes string   |Azure Active Directory ログイン トークンを送信できる追加のドメイン サフィックスを指定します。  既定値は " *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net" です。 ここに記載されているすべてが既定値に追加されます。 セキュリティのために、Microsoft Azure のドメインのみをここに入力してください。 複数のエンティティは、セミコロンで区切ります。|

## <a name="see-also"></a>関連項目

- [azcopy](storage-ref-azcopy.md)
