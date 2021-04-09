---
title: Azure Service Fabric CLI - sfctl is
description: Azure Service Fabric のコマンド ライン インターフェイスである sfctl について説明します。 インフラストラクチャを管理するためのコマンドの一覧が含まれています。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 349f70c32ea4ebb4559f053d5ef05b4b37b6480f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260366"
---
# <a name="sfctl-is"></a>sfctl is
クエリを実行し、インフラストラクチャ サービスにコマンドを送信します。

## <a name="commands"></a>コマンド

|コマンド|説明|
| --- | --- |
| command | 特定の Infrastructure Service インスタンスで管理コマンドを呼び出します。 |
| query | 特定の Infrastructure Service インスタンスで読み取り専用クエリを呼び出します。 |

## <a name="sfctl-is-command"></a>sfctl is command
特定の Infrastructure Service インスタンスで管理コマンドを呼び出します。

構成済みの Infrastructure Service のインスタンスが 1 つ以上あるクラスターの場合、この API を使用して、Infrastructure Service の特定のインスタンスにインフラストラクチャ固有のコマンドを送信することができます。 使用可能なコマンドと、対応する応答の形式は、クラスターが実行されているインフラストラクチャによって異なります。 この API は、Service Fabric プラットフォームをサポートしています。コードから直接使用するものではありません。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --command [必須] | 呼び出されるコマンドのテキスト。 コマンドの内容は、インフラストラクチャに固有です。 |
| --service-id | Infrastructure Service の ID。 <br><br> これは "fabric\:" URI スキームのない Infrastructure Service の完全な名前です。 このパラメーターは、実行している Infrastructure Service のインスタンスを複数持つクラスターにのみ必要です。 |
| --timeout -t | 既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-is-query"></a>sfctl is query
特定の Infrastructure Service インスタンスで読み取り専用クエリを呼び出します。

構成済みの Infrastructure Service のインスタンスが 1 つ以上あるクラスターの場合、この API を使用して、Infrastructure Service の特定のインスタンスにインフラストラクチャ固有のクエリを送信することができます。 使用可能なコマンドと、対応する応答の形式は、クラスターが実行されているインフラストラクチャによって異なります。 この API は、Service Fabric プラットフォームをサポートしています。コードから直接使用するものではありません。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --command [必須] | 呼び出されるコマンドのテキスト。 コマンドの内容は、インフラストラクチャに固有です。 |
| --service-id | Infrastructure Service の ID。 <br><br> これは "fabric\:" URI スキームのない Infrastructure Service の完全な名前です。 このパラメーターは、実行している Infrastructure Service のインスタンスを複数持つクラスターにのみ必要です。 |
| --timeout -t | 既定値\: 60。 |

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
