---
title: Azure Service Fabric CLI - sfctl rpm
description: Azure Service Fabric のコマンド ライン インターフェイス、sfctl について説明します。 修復マネージャー サービスのためのコマンドの一覧が含まれています。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 7317fd66303aaabf5232106aa7391439880bebaf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260289"
---
# <a name="sfctl-rpm"></a>sfctl rpm
クエリを実行し、修復マネージャー サービスにコマンドを送信します。

## <a name="commands"></a>コマンド

|コマンド|説明|
| --- | --- |
| approve-force | 指定した修復タスクの承認を強制します。 |
| delete | 完了した修復タスクを削除します。 |
| list | 指定されたフィルターに一致する修復タスクの一覧を取得します。 |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm approve-force
指定した修復タスクの承認を強制します。

この API は、Service Fabric プラットフォームをサポートしています。コードから直接使用するものではありません。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --task-id [必須] | 修復タスクの ID。 |
| --version | 修復タスクの現在のバージョン番号。 0 以外の場合、この値が修復タスクの実際の現行バージョンと一致する場合にのみ要求が成功します。 0 の場合、バージョン チェックは実行されません。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-rpm-delete"></a>sfctl rpm delete
完了した修復タスクを削除します。

この API は、Service Fabric プラットフォームをサポートしています。コードから直接使用するものではありません。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --task-id [必須] | 削除する完了した修復タスクの ID。 |
| --version | 修復タスクの現在のバージョン番号。 0 以外の場合、この値が修復タスクの実際の現行バージョンと一致する場合にのみ要求が成功します。 0 の場合、バージョン チェックは実行されません。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-rpm-list"></a>sfctl rpm list
指定されたフィルターに一致する修復タスクの一覧を取得します。

この API は、Service Fabric プラットフォームをサポートしています。コードから直接使用するものではありません。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --executor-filter | 要求したタスクを一覧に含める必要がある修復 Executor の名前。 |
| --state-filter | どのタスクの状態を結果リストに含める必要があるかを指定する、次の値の bitwise-OR。 <ul><li>1 - 作成済み</li><li>2 - 要求済み</li><li>4 - 準備中</li><li>8 - 承認済み</li><li>16 - 実行中</li><li>32 - 復元中</li><li>64 - 完了済み</li></ul>
| --task-id-filter | 照合される修復タスク ID プレフィックス。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |


## <a name="next-steps"></a>次のステップ
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](./scripts/sfctl-upgrade-application.md)を使用して、Service Fabric CLI の使用方法を学習します。
