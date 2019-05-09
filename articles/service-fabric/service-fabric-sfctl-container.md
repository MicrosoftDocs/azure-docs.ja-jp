---
title: Azure Service Fabric CLI- sfctl container | Microsoft Docs
description: Service Fabric CLI sfctl container のコマンドについて説明します。
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: a5037c535737946a50d8af6fa60d0815120276d9
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663708"
---
# <a name="sfctl-container"></a>sfctl container
クラスター ノード上でコンテナー関連のコマンドを実行します。

## <a name="commands"></a>command

|command|説明|
| --- | --- |
| invoke-api | 特定のコード パッケージの Service Fabric ノードにデプロイされているコンテナーでコンテナー API を呼び出します。 |
| logs | 特定のコード パッケージの Service Fabric ノードにデプロイされているコンテナーのコンテナー ログを取得します。 |

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
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-container-logs"></a>sfctl container logs
特定のコード パッケージの Service Fabric ノードにデプロイされているコンテナーのコンテナー ログを取得します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --application-id           [必須] | アプリケーションの ID。 <br><br> これは通常、"fabric\:" URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、アプリケーション名が "fabric\:/myapp/app1" の場合、アプリケーション ID は、6.0 以降では "myapp\~app1" になり、それより前のバージョンでは "myapp/app1" になります。 |
| --code-package-instance-id [必須] | コード パッケージ インスタンス ID。"service code-package-list" を使用して取得できます。 |
| --code-package-name        [必須] | Service Fabric クラスターでアプリケーションの種類の一部として登録されているサービス マニフェストで指定されているコード パッケージの名前。 |
| --node-name                [必須] | ノード名。 |
| --service-manifest-name    [必須] | Service Fabric クラスターでアプリケーションの種類の一部として登録されているサービス マニフェストの名前。 |
| --tail | ログの末尾から表示する行数。 既定値は 100 です。 ログ全体を表示する場合は "all" です。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |


## <a name="next-steps"></a>次の手順
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。