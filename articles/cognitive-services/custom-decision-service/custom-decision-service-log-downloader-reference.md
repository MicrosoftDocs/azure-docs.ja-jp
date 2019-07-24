---
title: LogDownloader - Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Azure Custom Decision Service によって生成されたログ ファイルをダウンロードします。
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: marossi
ms.openlocfilehash: 8a8f669c33f40fb80dc826ec04203880dee74d82
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58109411"
---
# <a name="logdownloader"></a>LogDownloader

Azure Custom Decision Service によって生成されたログ ファイルをダウンロードし、実験で使用される *.gz* ファイルを生成します。

## <a name="prerequisites"></a>前提条件

- Python 3:インストールされてパス上にあること。 大きなファイルを処理するには、64 ビット バージョンをお勧めします。
- *Microsoft/mwt ds*リポジトリ:[リポジトリを複製](https://github.com/Microsoft/mwt-ds)します。
- *azure-storage-blob* パッケージ:インストールの詳細については、[Python 用 Microsoft Azure Storage ライブラリ](https://github.com/Azure/azure-storage-python#option-1-via-pypi)に関するページを参照してください。
- Azure Storage 接続文字列を *mwt-ds/DataScience/ds.config* に入力します。*my_app_id: my_connectionString* テンプレートに従います。 複数の `app_id` を指定できます。 `LogDownloader.py` を実行したときに `ds.config` に入力 `app_id` が見つからない場合、`LogDownloader.py` は `$Default` 接続文字列を使用します。

## <a name="usage"></a>使用法

`mwt-ds/DataScience` に移動し、次のコードに説明するように関連する引数を使用して `LogDownloader.py` を実行します。

```cmd
python LogDownloader.py [-h] -a APP_ID -l LOG_DIR [-s START_DATE]
                        [-e END_DATE] [-o OVERWRITE_MODE] [--dry_run]
                        [--create_gzip] [--delta_mod_t DELTA_MOD_T]
                        [--verbose] [-v VERSION]
```

### <a name="parameters"></a>parameters

| 入力 | 説明 | 既定値 |
| --- | --- | --- |
| `-h`、`--help` | ヘルプ メッセージを表示して終了します。 | |
| `-a APP_ID`、`--app_id APP_ID` | アプリ ID (つまり、Azure Storage BLOB コンテナー名)。 | 必須 |
| `-l LOG_DIR`、`--log_dir LOG_DIR` | データをダウンロードするためのベース ディレクトリ (サブフォルダーが作成されます)。  | 必須 |
| `-s START_DATE`、`--start_date START_DATE` | *YYYY-MM-DD* 形式のダウンロード開始日 (当日を含む)。 | `None` |
| `-e END_DATE`、`--end_date END_DATE` | *YYYY-MM-DD* 形式のダウンロード終了日 (当日を含む)。 | `None` |
| `-o OVERWRITE_MODE`、`--overwrite_mode OVERWRITE_MODE` | 使用する上書きモード。 | |
| | `0`:上書きしません。BLOB が現在使用されているかどうかをユーザーに問い合わせます。 | 既定値 |
| | `1`:ファイルのサイズが異なる場合や BLOB が現在使用されている場合の処理方法をユーザーに問い合わせます。 | |
| | `2`:常に上書きします。現在使用されている BLOB をダウンロードします。 | |
| | `3`:上書きしません。サイズがより大きい場合は問い合わせなしで追加します。現在使用されている BLOB をダウンロードします。 | |
| | `4`:上書きしません。サイズがより大きい場合は問い合わせなしで追加します。現在使用されている BLOB をスキップします。 | |
| `--dry_run` | ダウンロードすることなく、ダウンロードの対象となる BLOB を印刷します。 | `False` |
| `--create_gzip` | Vowpal Wabbit 用の *gzip* ファイルを作成します。 | `False` |
| `--delta_mod_t DELTA_MOD_T` | ファイルが現在使用中かどうかを検出するための時間枠 (秒単位)。 | `3600` 秒 (`1` 時間) |
| `--verbose` | 詳細を印刷します。 | `False` |
| `-v VERSION`、`--version VERSION` | 使用するログ ダウンローダーのバージョン。 | |
| | `1`:未加工のログ (下位互換性のみの目的)。 | 非推奨 |
| | `2`:加工されたログ。 | 既定値 |

### <a name="examples"></a>例

Azure Storage BLOB コンテナーのすべてのデータのダウンロードのドライ ランには、次のコードを使用します。
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

`overwrite_mode=4` を使用して 2018 年 1 月 1 日以降作成されたログのみをダウンロードするには、次のコードを使用します。
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

ダウンロードしたすべてのファイルをマージした *gzip* ファイルを作成するには、次のコードを使用します。
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```
