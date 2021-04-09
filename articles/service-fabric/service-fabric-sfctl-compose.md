---
title: Azure Service Fabric CLI- sfctl compose
description: Azure Service Fabric のコマンド ライン インターフェイスである sfctl について説明します。 Docker Compose アプリケーションのためのコマンドの一覧が含まれています。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: e84b35ff3dfa7be0c848a4d7b906c622d9b88914
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257335"
---
# <a name="sfctl-compose"></a>sfctl compose
Docker Compose アプリケーションを作成、削除、および管理します。

## <a name="commands"></a>コマンド

|コマンド|説明|
| --- | --- |
| 作成 | Service Fabric Compose のデプロイを作成します。 |
| list | Service Fabric クラスターで作成された Compose デプロイの一覧を取得します。 |
| remove | クラスターから既存の Service Fabric Compose デプロイを削除します。 |
| status | Service Fabric Compose デプロイの情報を取得します。 |
| upgrade | Service Fabric クラスターで、Compose デプロイのアップグレードを開始します。 |
| upgrade-rollback | Service Fabric クラスターで、Compose デプロイのアップグレードのロールバックを開始します。 |
| upgrade-status | この Service Fabric Compose のデプロイで実行された最新のアップグレードの詳細を取得します。 |

## <a name="sfctl-compose-create"></a>sfctl compose create
Service Fabric Compose のデプロイを作成します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --deployment-name [必須] | デプロイの名前。 |
| --file-path       [必須] | ターゲット Docker Compose ファイルへのパス。 |
| --encrypted-pass | コンテナー レジストリ パスワードの入力を求めるのではなく、既に暗号化されているパスフレーズを使用します。 |
| --has-pass | コンテナー レジストリへのパスワードを要求します。 |
| --timeout -t | 既定値\: 60。 |
| --user | コンテナー レジストリに接続するユーザー名。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-compose-list"></a>sfctl compose list
Service Fabric クラスターで作成された Compose デプロイの一覧を取得します。

Service Fabric クラスターで作成されたまたは作成中の Compose デプロイの状態を取得します。 応答には、名前、状態、および Compose デプロイに関するその他の詳細が含まれています。 デプロイのリストが 1 ページに収まらない場合、1 ページの結果と継続トークンが返されます。これは次のページを取得するために使用できます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --continuation-token | 継続トークンのパラメーターは、次の結果セットを取得するために使用されます。 システムからの結果が 1 つの応答に収まらない場合は、空以外の値を持つ継続トークンが API の応答に含まれます。 この値が次の API 呼び出しに渡されると、API が次の結果セットを返します。 それ以上の結果がない場合は、継続トークンに値が含まれません。 このパラメーターの値を URL にエンコードすることはできません。 |
| --max-results | ページング クエリの一部として返される結果の最大数。 このパラメーターは、返される結果の数に上限を定義します。 返された結果が、構成で定義したメッセージの最大サイズの制限に収まらない場合は、指定した最大結果数よりも少なくなる場合があります。 このパラメーターがゼロまたは指定されていない場合、ページング クエリには、応答メッセージに収まるできるだけ多くの結果が含まれます。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-compose-remove"></a>sfctl compose remove
クラスターから既存の Service Fabric Compose デプロイを削除します。

既存の Service Fabric Compose デプロイを削除します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --deployment-name [必須] | デプロイの ID。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-compose-status"></a>sfctl compose status
Service Fabric Compose デプロイの情報を取得します。

Service Fabric クラスターで作成された、または作成中の、パラメーターとして指定されているものと同じ名前の Compose デプロイの状態を返します。 応答には、名前、状態、およびデプロイに関するその他の詳細が含まれています。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --deployment-name [必須] | デプロイの ID。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-compose-upgrade"></a>sfctl compose upgrade
Service Fabric クラスターで、Compose デプロイのアップグレードを開始します。

指定されたアップグレード パラメーターを検証し、パラメーターが有効な場合はデプロイのアップグレードを開始します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --deployment-name [必須] | デプロイの名前。 |
| --file-path        [必須] | ターゲット Docker Compose ファイルへのパス。 |
| --default-svc-type-health-map | サービスの正常性を評価するために使用する正常性ポリシーについて説明している JSON でエンコードされたディクショナリ。 |
| --encrypted-pass | コンテナー レジストリ パスワードの入力を求めるのではなく、既に暗号化されているパスフレーズを使用します。 |
| --failure-action | 指定可能な値\: "Invalid"、"Rollback"、"Manual"。 |
| --force-restart | コードのバージョンが変更されていない場合でも、アップグレード時にプロセスを強制的に再起動します。 <br><br> アップグレードでは、構成またはデータのみが変更されます。 |
| --has-pass | コンテナー レジストリへのパスワードを要求します。 |
| --health-check-retry | アプリケーションまたはクラスターが正常ではない場合に正常性チェックを実行する間隔。 |
| --health-check-stable | アプリケーションまたはクラスターが正常な状態である必要がある時間。この時間を超えると、アップグレードが次のアップグレード ドメインに進みます。 <br><br> 最初に、ISO 8601 の期間を表す文字列として解釈されます。 それが失敗した場合、ミリ秒単位の合計数を表す数値として解釈されます。 |
| --health-check-wait | アップグレード ドメインの完了後、正常性チェック プロセスを開始するまでの、待機時間の長さ。 |
| --replica-set-check | アップグレード ドメインの処理をブロックし、予期しない問題が発生したときに可用性の損失を防ぐための最長時間。 <br><br> このタイムアウトが期限切れになると、可用性の損失問題に関係なく、アップグレード ドメインの処理が続行されます。 タイムアウトは、各アップグレード ドメインの開始時にリセットされます。 有効な値は 0 から 42949672925 の範囲です (0 と 42949672925 を含みます)。 |
| --svc-type-health-map | さまざまなサービスの種類の正常性を評価するために使用される正常性ポリシーを記述するオブジェクトの JSON でエンコードされた一覧。 |
| --timeout -t | 既定値\: 60。 |
| --unhealthy-app | エラーを報告する前の異常なアプリケーションの最大許容パーセンテージ。 <br><br> たとえば、アプリケーションの 10% の異常を許容するには、この値を 10 にします。 パーセンテージは、異常な可能性のあるアプリケーションの最大許容パーセンテージを表します。この値を超えるとクラスターはエラーの状態と見なされます。 許容パーセンテージ内であっても、1 つ以上の異常なアプリケーションがある場合は、正常性は Warning として評価されます。 これは、異常なアプリケーションの数をクラスター内のアプリケーション インスタンスの総数で割ることによって計算されます。 |
| --upgrade-domain-timeout | 各ドメインがアップグレードを完了する必要がある時間。この時間を超えると、FailureAction が実行されます。 <br><br> 最初に、ISO 8601 の期間を表す文字列として解釈されます。 それが失敗した場合、ミリ秒単位の合計数を表す数値として解釈されます。 |
| --upgrade-kind | 既定値\: Rolling。 |
| --upgrade-mode | 指定可能な値\: "Invalid"、"UnmonitoredAuto"、"UnmonitoredManual"、"Monitored"。  既定値\: UnmonitoredAuto。 |
| --upgrade-timeout | アップグレード全体を完了する必要がある時間。この時間を超えると、FailureAction が実行されます。 <br><br> 最初に、ISO 8601 の期間を表す文字列として解釈されます。 それが失敗した場合、ミリ秒単位の合計数を表す数値として解釈されます。 |
| --user | コンテナー レジストリに接続するユーザー名。 |
| --warning-as-error | 警告がエラーと同じ重大度で処理されることを示します。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-compose-upgrade-rollback"></a>sfctl compose upgrade-rollback
Service Fabric クラスターで、Compose デプロイのアップグレードのロールバックを開始します。

Service Fabric Compose のデプロイのアップグレードをロールバックします。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --deployment-name [必須] | デプロイの ID。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-compose-upgrade-status"></a>sfctl compose upgrade-status
この Service Fabric Compose のデプロイで実行された最新のアップグレードの詳細を取得します。

Compose デプロイ アップグレードの状態、およびアプリケーションの正常性の問題のデバッグに役立つ詳細に関する情報を返します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --deployment-name [必須] | デプロイの ID。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

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
