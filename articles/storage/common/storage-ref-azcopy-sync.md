---
title: azcopy sync | Microsoft Docs
description: この記事では、azcopy sync コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: fb6c3b711a89ae7e4ef403a75927c4c6172523d0
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196753"
---
# <a name="azcopy-sync"></a>azcopy sync

元の場所を同期先の場所にレプリケートします。

## <a name="synopsis"></a>概要

最後に変更された時刻が比較に使用されます。 同期先の最終更新時刻の方が新しい場合、ファイルはスキップされます。

サポートされているペアは次のとおりです。

- ローカル <-> Azure BLOB (SAS または OAuth 認証のいずれかを使用できます)

sync コマンドは、いくつかの点で copy コマンドと異なります。

  1. 再帰フラグは既定でオンになっています。
  2. 同期元と同期先には、パターン (* や? など) を含めることはできません。
  3. include フラグと exclude フラグには、ファイル名に一致するパターンの一覧を指定できます。 説明については、例のセクションを参照してください。
  4. 同期元に存在しないファイルまたは BLOB が同期先に存在する場合、ユーザーはそれらを削除することを求められます。

     このプロンプトは、対応するフラグを使用して、削除の質問に自動的に応答することで、通知しないようにすることができます。

### <a name="advanced"></a>詳細

AzCopy は、ファイルの拡張子またはコンテンツ (拡張子が指定されていない場合) に基づいて、ローカル ディスクからアップロードするときにファイルのコンテンツの種類を自動的に検出します。

組み込みのルックアップ テーブルは小さいですが、Unix では、次の 1 つ以上の名前において使用可能な場合は、ローカル システムの mime.types ファイルによって拡張されます。

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows では、MIME の種類はレジストリから抽出されます。

```azcopy
azcopy sync [flags]
```

## <a name="examples"></a>例

1 つのファイルを同期する

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

上記と同じですが、今度はファイル コンテンツの MD5 ハッシュも計算し、BLOB の Content-MD5 プロパティとして保存します。

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

サブディレクトリを含むディレクトリ全体を同期します (再帰は既定でオンになっていることに注意してください)。

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

or

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

ディレクトリ内の上位のファイルのみを同期しますが、サブディレクトリは同期しません。

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

ディレクトリ内のファイルのサブセットを削除します (例: jpg ファイルおよび pdf ファイルのみ、またはファイル名が "exactName" の場合)。

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

ディレクトリ全体を同期しますが、スコープから特定のファイルを除外します (たとえば、foo で始まるすべてのファイルや、bar で終わるすべてのファイル)。

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

> [!NOTE]
> include/exclude フラグが一緒に使用されている場合は、include パターンに一致するファイルだけが検索されますが、exclude パターンに一致するファイルは常に無視されます。

## <a name="options"></a>オプション

|オプション|説明|
|--|--|
|--block-size-mb float|Azure Storage にアップロードする場合、または Azure Storage からダウンロードする場合に、このブロック サイズ (MiB で指定) を使用します。 既定値は、ファイル サイズに基づいて自動的に計算されます。 小数を使用できます (例: 0.25)。|
|--check-md5 string|ダウンロード時に MD5 ハッシュを検証する厳密さを指定します。 このオプションはダウンロード時にのみ使用できます。 指定できる値は、NoCheck、LogOnly、FailIfDifferent、FailIfDifferentOrMissing などです。 (既定値は "FailIfDifferent")。|
|--delete-destination string|同期元に存在しない余分なファイルを同期先から削除するかどうかを定義します。 true、false、または prompt に設定できます。 prompt に設定すると、ファイルと BLOB の削除をスケジュールする前に、ユーザーに質問が表示されます。 (既定値は "false" です)。|
|--exclude string|名前がパターンの一覧と一致するファイルを除外します。 例: *.jpg;* .pdf;exactName。|
|-h, --help|sync コマンドのヘルプ コンテンツを表示します。|
|--include string|名前がパターンの一覧と一致するファイルのみを含めます。 例: *.jpg;* .pdf;exactName。|
|--log-level string|ログ ファイルのログの詳細度を定義します。使用できるレベルは次のとおりです。INFO (すべての要求/応答)、WARNING (低速応答)、ERROR (失敗した要求のみ)、NONE (出力ログなし)。 (既定値は "INFO")。|
|--put-md5|各ファイルの MD5 ハッシュを作成し、ハッシュを同期先の BLOB またはファイルの Content-MD5 プロパティとして保存します。 (既定では、ハッシュは作成されません)。アップロード時にのみ使用できます。|
|--recursive|既定では true で、ディレクトリ間で同期するときにサブディレクトリを再帰的に検索します。 (既定値は true)。|

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

|オプション|説明|
|---|---|
|--cap-mbps uint32|転送速度を制限します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。|
|--output-type string|コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。|

## <a name="see-also"></a>関連項目

- [azcopy](storage-ref-azcopy.md)
