---
title: Azure Service Fabric CLI- sfctl コンテナー
description: Azure Service Fabric のコマンド ライン インターフェイスである sfctl について説明します。 コンテナーに対するコマンドの一覧が含まれています。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f82883b68ab911fb0b89fc117d9a9d77e05a781a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245893"
---
# <a name="sfctl-container"></a>sfctl container
クラスター ノード上でコンテナー関連のコマンドを実行します。

## <a name="commands"></a>コマンド

|コマンド|説明|
| --- | --- |
| invoke-api | 特定のコード パッケージの Service Fabric ノードにデプロイされているコンテナーでコンテナー API を呼び出します。 |
| logs | Service Fabric ノードに展開されているコンテナーのコンテナー ログを取得します。 |

## <a name="sfctl-container-invoke-api"></a>sfctl container invoke-api
特定のコード パッケージの Service Fabric ノードにデプロイされているコンテナーでコンテナー API を呼び出します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --application-id           [必須] | アプリケーションの ID。 <br><br> これは通常、"fabric\:" URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、アプリケーション名が "fabric\:/myapp/app1" の場合、アプリケーション ID は、6.0 以降では "myapp\~app1" になり、それより前のバージョンでは "myapp/app1" になります。 |
| --code-package-instance-id [必須] | サービス ファブリック ノードにデプロイされているコード パッケージ インスタンスを一意に識別する ID。 <br><br> 'service code-package-list' によって取得できます。 |
| --code-package-name        [必須] | Service Fabric クラスターでアプリケーションの種類の一部として登録されているサービス マニフェストで指定されているコード パッケージの名前。 |
| --container-api-uri-path   [必須] | コンテナー REST API URI のパス。コンテナー名/ID の代わりに "{ID}" を使用します。 |
| --node-name                [必須] | ノード名。 |
| --service-manifest-name    [必須] | Service Fabric クラスターでアプリケーションの種類の一部として登録されているサービス マニフェストの名前。 |
| --container-api-body | コンテナー REST API の HTTP 要求本文。 |
| --container-api-content-type | コンテナー REST API のコンテンツの種類。既定値は "application/json" です。 |
| --container-api-http-verb | コンテナー REST API の HTTP 動詞。既定値は GET です。 |
| --timeout -t | 既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-container-logs"></a>sfctl container logs
Service Fabric ノードに展開されているコンテナーのコンテナー ログを取得します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --application-id           [必須] | アプリケーションの ID。 <br><br> これは通常、"fabric\:" URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、アプリケーション名が "fabric\:/myapp/app1" の場合、アプリケーション ID は、6.0 以降では "myapp\~app1" になり、それより前のバージョンでは "myapp/app1" になります。 |
| --code-package-instance-id [必須] | コード パッケージ インスタンス ID。"service code-package-list" を使用して取得できます。 |
| --code-package-name        [必須] | Service Fabric クラスターでアプリケーションの種類の一部として登録されているサービス マニフェストで指定されているコード パッケージの名前。 |
| --node-name                [必須] | ノード名。 |
| --service-manifest-name    [必須] | Service Fabric クラスターでアプリケーションの種類の一部として登録されているサービス マニフェストの名前。 |
| --tail | ログの末尾から表示する行数。 既定値は 100 です。 ログ全体を表示する場合は "all" です。 |
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
