---
title: azcopy bench | Microsoft Docs
description: この記事では、azcopy bench コマンドの参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c1028d0a4a458746c08fd6fa4f16aa952d9962a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87282009"
---
# <a name="azcopy-benchmark"></a>azcopy ベンチマーク

指定したコピー先との間でテスト データをアップロードまたはダウンロードすることで、パフォーマンス ベンチマークを実行します。 アップロードの場合、テスト データは自動的に生成されます。

ベンチマーク コマンドでは、以下を除き、"copy" と同じプロセスが実行されます。 

  - コピー元とコピー先の両方のパラメーターを必要とするのではなく、ベンチマークでは 1 つだけを受け取ります。 これは、アップロード先またはダウンロード元にする BLOB コンテナー、Azure Files 共有、または Azure Data Lake Storage Gen2 ファイル システムです。

  - "mode" パラメーターは、指定されたターゲットに対して AzCopy がアップロードとダウンロードのどちらをテストする必要があるかを示します。 有効な値は、"Upload" と "Download" です。 既定値は "Upload" です。

  - アップロード ベンチマークの場合、ペイロードは、自動生成されるファイルの数と、ファイルの重要性を制御するコマンド ライン パラメーターによって記述されます。 生成プロセスは、すべてメモリ内で行われます。 ディスクは使用されません。

  - ダウンロードの場合、ペイロードは、ダウンロード元に既に存在するいずれかのファイルで構成されます。 (必要な場合は、テスト ファイルを生成する方法に関する下記の例を参照してください)。
  
  - copy コマンドで使用できるオプションのパラメーターの一部のみがサポートされています。
  
  - 追加の診断が測定および報告されます。
  
  - アップロードの場合、既定の動作では、テスト実行の最後に、転送されたデータが削除されます。  ダウンロードの場合、データはローカルに一切保存されません。

ベンチマーク モードは、最大スループットを提供する並列 TCP 接続の数に自動的に調整されます。 最後にその数値が表示されます。 自動調整が行われないようにするには、AZCOPY_CONCURRENCY_VALUE 環境変数を特定の数の接続に設定します。 

一般的な認証の種類がすべてサポートされています。 ただし、アップロードのベンチマークを行う最も便利な方法は、一般に、SAS トークンを指定して空のコンテナーを作成し、SAS 認証を使用することです。 (ダウンロード モードでは、テスト データのセットがターゲット コンテナーに存在する必要があります。)

## <a name="examples"></a>例

```azcopy
azcopy benchmark [destination] [flags]
```

既定のパラメーターを使用してベンチマーク テストを実行します (最大 1 Gbps のネットワークのベンチマークに適しています)。

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"
```
サイズがそれぞれ 2 GiB の 100 個のファイルをアップロードするベンチマーク テストを実行します (10 Gbps などの高速ネットワークでのベンチマークに適しています)。

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"--file-count 100 --size-per-file 2G
```
ベンチマーク テストを実行しますが、サイズがそれぞれ 8 MiB の 50,000 個のファイルを使用し、それらの MD5 ハッシュを計算します (copy コマンドで `--put-md5` フラグが行う方法と同じです)。 ベンチマーク時の `--put-md5` の目的は、MD5 の計算が、選択されたファイルの数とサイズのスループットに影響を与えるかどうかをテストすることです。

```azcopy
azcopy bench --mode='Upload' "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5
```

ターゲットから既存のファイルをダウンロードするベンチマーク テストを実行します

```azcopy
azcopy bench --mode='Download' "https://[account].blob.core.windows.net/[container]?<SAS?"
```

転送されたファイルを削除しないアップロードを実行します。 (これらのファイルは次に、ダウンロード テストのペイロードとして機能します)

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --delete-test-data=false
```

## <a name="options"></a>Options

**--blob-type** string  宛先の BLOB の種類を定義します。 さまざまな種類の BLOB のベンチマークを許可するために使用されます。 copy コマンドの同一の名前のパラメーターと同じです (既定値は "Detect")。

**--block-size-mb** float  このブロック サイズ (MiB で指定) を使用します。 既定値は、ファイル サイズに基づいて自動的に計算されます。 小数を使用できます (例: 0.25)。 copy コマンドの同一の名前のパラメーターと同じです。

**--check-length**  転送後に転送先のファイルの長さを調べます。 コピー元とコピー先の間に不一致がある場合は、転送に失敗のマークが付けられます。 (既定値は true)

**--delete-test-data**  true の場合、ベンチマーク実行の最後にベンチマーク データが削除されます。  転送先でデータを保持する場合は false に設定します (既定値は true)。たとえば、ベンチマーク モード以外の手動テストに使用します。

**--file-count** uint。  使用するために自動生成されるデータ ファイルの数 (既定値は 100)。

**--help**  bench のヘルプ

**--log-level** string ログ ファイルのログの詳細度を定義します。使用できるレベルは次のとおりです。INFO (すべての要求/応答)、WARNING (遅い応答)、ERROR (失敗した要求のみ)、NONE (出力ログなし)。 (既定値は "INFO")

**--mode** string  Azcopy で、このターゲットに対してアップロードまたはダウンロードをテストする必要があるかどうかを定義します。 有効な値は、"upload" と "download" です。 既定で設定されるオプションは "upload" です。 (既定値は "upload")

**--number-of-folders** uint  0 より大きい場合は、データを分割するためのフォルダーを作成します。

**--put-md5**  各ファイルの MD5 ハッシュを作成し、そのハッシュを宛先の BLOB またはファイルの Content-MD5 プロパティとして保存します。 (既定では、ハッシュは作成されません)。copy コマンドの同一の名前のパラメーターと同じです。

**--size-per-file** string   自動生成される各データ ファイルのサイズ。 数値の直後に K、M、または G を指定する必要があります。例: 12k または 200G (既定値は "250M")。

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

**--cap-mbps float**  転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。

**--output-type** string  コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。 (既定値 "text")。

**--trusted-microsoft-suffixes** string   Azure Active Directory ログイン トークンを送信できる追加のドメイン サフィックスを指定します。  既定値は " *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net" です。 ここに記載されているすべてが既定値に追加されます。 セキュリティのために、Microsoft Azure のドメインのみをここに入力してください。 複数のエンティティは、セミコロンで区切ります。


## <a name="see-also"></a>関連項目

- [azcopy](storage-ref-azcopy.md)
