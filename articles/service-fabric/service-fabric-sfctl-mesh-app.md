---
title: Azure Service Fabric CLI- sfctl mesh app
description: Azure Service Fabric のコマンド ライン インターフェイスである sfctl について説明します。 Service Fabric Mesh のアプリケーション リソースを管理するためのコマンドの一覧が含まれます。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 835369116b07b74c666fba271476f1cba5a708b8
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259945"
---
# <a name="sfctl-mesh-app"></a>sfctl mesh app
アプリケーション リソースを取得および削除します。

## <a name="commands"></a>コマンド

|command|説明|
| --- | --- |
| delete | アプリケーション リソースを削除します。 |
| list | アプリケーションのすべてのリソースを一覧表示します。 |
| show | 指定した名前のアプリケーション リソースを取得します。 |

## <a name="sfctl-mesh-app-delete"></a>sfctl mesh app delete
アプリケーション リソースを削除します。

名前で識別されるアプリケーション リソースを削除します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --name -n (必須) | アプリケーションの名前です。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-mesh-app-list"></a>sfctl mesh app list
アプリケーションのすべてのリソースを一覧表示します。

特定のリソース グループ内のすべてのアプリケーション リソースに関する情報を取得します。 この情報には、アプリケーションの説明とその他のプロパティが含まれます。

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-mesh-app-show"></a>sfctl mesh app show
指定した名前のアプリケーション リソースを取得します。

指定した名前のアプリケーション リソースに関する情報を取得します。 この情報には、アプリケーションの説明とその他のプロパティが含まれます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --name -n (必須) | アプリケーションの名前です。 |

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
