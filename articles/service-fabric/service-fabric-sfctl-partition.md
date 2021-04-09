---
title: Azure Service Fabric CLI- sfctl partition
description: Azure Service Fabric のコマンド ライン インターフェイスである sfctl について説明します。 サービスのパーティションを管理するためのコマンドの一覧が含まれています。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fff569c586548d84ed55018764363ad7f05e526d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257089"
---
# <a name="sfctl-partition"></a>sfctl partition
任意のサービスで、クエリを実行し、パーティションを管理します。

## <a name="commands"></a>コマンド

|コマンド|説明|
| --- | --- |
| data-loss | この API は、指定されたパーティションのデータ損失を誘発します。 |
| data-loss-status | StartDataLoss API を使用して開始されたパーティション データ損失操作の進行状況を取得します。 |
| health | 指定した Service Fabric パーティションの正常性を取得します。 |
| info | Service Fabric パーティションに関する情報を取得します。 |
| list | Service Fabric サービスのパーティションの一覧を取得します。 |
| load | 指定された Service Fabric パーティションの負荷情報を取得します。 |
| load-reset | Service Fabric パーティションの現在の負荷をリセットします。 |
| quorum-loss | 特定のステートフル サービス パーティションをクォーラム損失状態にします。 |
| quorum-loss-status | StartQuorumLoss API を使用して開始されたパーティションのクォーラム損失操作の進行状況を取得します。 |
| recover | クォーラム損失で現在スタックしている指定されたパーティションの復旧を試みる必要があることを Service Fabric クラスターに示します。 |
| recover-all | 現在クォーラム損失の状態に陥っているすべてのサービス (システム サービスを含む) の復旧を試みる必要があることを Service Fabric クラスターに示します。 |
| report-health | Service Fabric パーティションの正常性レポートを送信します。 |
| restart | この API は、指定されたパーティションのレプリカやインスタンスの一部またはすべてを再起動します。 |
| restart-status | StartPartitionRestart を使用して開始されたパーティション再起動操作の進行状況を取得します。 |
| svc-name | パーティションの Service Fabric サービスの名前を取得します。 |

## <a name="sfctl-partition-data-loss"></a>sfctl partition data-loss
この API は、指定されたパーティションのデータ損失を誘発します。

これにより、パーティションの OnDataLossAsync API の呼び出しがトリガーされます。  この API は、指定されたパーティションのデータ損失を誘発します。 これにより、パーティションの OnDataLoss API の呼び出しがトリガーされます。 実際のデータ損失は、指定された DataLossMode によって異なります。
- PartialDataLoss:レプリカのクォーラムのみが削除され、パーティションに対して OnDataLoss がトリガーされますが、実際のデータ損失は、実行中のレプリケーションの有無によって異なります。  
- FullDataLoss:すべてのレプリカが削除されるため、すべてのデータが失われ、OnDataLoss がトリガーされます。 この API の呼び出しでは、ターゲットにステートフル サービスのみを指定してください。 ターゲットにシステム サービスを指定してこの API を呼び出すことは推奨されていません。

> [!NOTE]   
> この API は、呼び出した後に元に戻すことはできません。 CancelOperation を呼び出すと、実行が停止され、内部システム状態がクリーンアップされるだけです。 データ損失を引き起こすほどコマンドが進行した場合、データは復元されません。 この API で開始された操作に関する情報を返すには、同じ OperationId を使用して GetDataLossProgress API を呼び出してください。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --data-loss-mode [必須] | この列挙型は、誘発するデータ損失の種類を示すために StartDataLoss API に渡されます。 |
| --operation-id   [必須] | この API の呼び出しを識別する GUID。  これは対応する GetProgress API に渡されます。 |
| --partition-id   [必須] | パーティションの ID。 |
| --service-id     [必須] | サービスの ID。 この ID は通常、"fabric\:" URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、サービス名が "fabric\:/myapp/app1/svc1" の場合、サービス ID は、6.0 以降では "myapp\~app1\~svc1" になり、それより前のバージョンでは "myapp/app1/svc1" になります。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-partition-data-loss-status"></a>sfctl partition data-loss-status
StartDataLoss API を使用して開始されたパーティション データ損失操作の進行状況を取得します。

OperationId を使用して、StartDataLoss で開始されたデータ損失操作の進行状況を取得します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --operation-id [必須] | この API の呼び出しを識別する GUID。  これは対応する GetProgress API に渡されます。 |
| --partition-id [必須] | パーティションの ID。 |
| --service-id   [必須] | サービスの ID。 この ID は通常、"fabric\:" URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、サービス名が "fabric\:/myapp/app1/svc1" の場合、サービス ID は、6.0 以降では "myapp\~app1\~svc1" になり、それより前のバージョンでは "myapp/app1/svc1" になります。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-partition-health"></a>sfctl partition health
指定した Service Fabric パーティションの正常性を取得します。

正常性状態に基づいてサービスで報告される正常性イベントのコレクションをフィルター処理するには、EventsHealthStateFilter を使用します。 パーティションの ReplicaHealthState オブジェクトのコレクションをフィルター処理するには、ReplicasHealthStateFilter を使用します。 正常性ストアに存在しないパーティションを指定した場合、この要求からエラーが返されます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --partition-id [必須] | パーティションの ID。 |
| --events-health-state-filter | 正常性の状態に基づいて返された HealthEvent オブジェクトのコレクションをフィルター処理できます。 このパラメーターに指定できる値には、次の正常性の状態のいずれかの整数値が含まれます。 フィルターに一致するイベントのみが返されます。 すべてのイベントが集計された正常性の状態を評価するために使用されます。 指定しない場合、すべてのエントリが返されます。 状態値はフラグベースの列挙型であるため、値はビット演算子 'OR' を使用して取得したこれらの値の組み合わせが可能です。 たとえば、指定した値が 6 の場合、HealthState の値が OK (2) と Warning (4) のすべてのイベントが返されます。  <br> - Default - 既定値。 任意の HealthState と一致します。 値は 0 です。  <br> - None - どの HealthState 値とも一致しないフィルター。 状態の特定のコレクションの結果が返されないようにするために使用されます。 値は 1 です。  <br> - Ok - HealthState 値が Ok の入力に一致するフィルター。 値は 2 です。  <br> - Warning - HealthState 値が Warning の入力に一致するフィルター。 値は 4 です。  <br> - Error - HealthState 値が Error の入力に一致するフィルター。 値は 8 です。  <br> - All - 任意の HealthState 値の入力に一致するフィルター。 値は 65535 です。 |
| --exclude-health-statistics | 正常性の統計情報をクエリ結果の一部として返すかどうかを示します。 既定では false です。 統計情報は、正常性の状態が Ok、Warning、および Error の子エンティティの数を示します。 |
| --replicas-health-state-filter | パーティションの ReplicaHealthState オブジェクトのコレクションをフィルター処理できるようにします。 値は、HealthStateFilter のメンバーまたはメンバーのビットごとの演算から取得できます。 フィルターに一致するレプリカのみが返されます。 集計された正常性の状態を評価するには、すべてのレプリカが使用されます。 指定がない場合は、すべてのエントリが返されます。状態値はフラグベースの列挙型であるため、値はビット演算子 'OR' を使用して取得したこれらの値の組み合わせが可能です。 たとえば、指定した値が 6 の場合、HealthState 値が OK (2) と Warning (4) のすべてのイベントが返されます。 このパラメーターに指定できる値には、次の正常性の状態のいずれかの整数値が含まれます。  <br> - Default - 既定値。 任意の HealthState と一致します。 値は 0 です。  <br> - None - どの HealthState 値とも一致しないフィルター。 状態の特定のコレクションの結果が返されないようにするために使用されます。 値は 1 です。  <br> - Ok - HealthState 値が Ok の入力に一致するフィルター。 値は 2 です。  <br> - Warning - HealthState 値が Warning の入力に一致するフィルター。 値は 4 です。  <br> - Error - HealthState 値が Error の入力に一致するフィルター。 値は 8 です。  <br> - All - 任意の HealthState 値の入力に一致するフィルター。 値は 65535 です。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-partition-info"></a>sfctl partition info
Service Fabric パーティションに関する情報を取得します。

指定されたパーティションに関する情報を取得します。 応答には、パーティション ID、パーティション構成情報、パーティションでサポートされるキー、状態、正常性、およびパーティションに関するその他の詳細が含まれています。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --partition-id [必須] | パーティションの ID。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-partition-list"></a>sfctl partition list
Service Fabric サービスのパーティションの一覧を取得します。

応答には、パーティション ID、パーティション構成情報、パーティションでサポートされるキー、状態、正常性、およびパーティションに関するその他の詳細が含まれています。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --service-id [必須] | サービスの ID。 この ID は通常、"fabric\:" URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、サービス名が "fabric\:/myapp/app1/svc1" の場合、サービス ID は、6.0 以降では "myapp\~app1\~svc1" になり、それより前のバージョンでは "myapp/app1/svc1" になります。 |
| --continuation-token | 継続トークンのパラメーターは、次の結果セットを取得するために使用されます。 システムからの結果が 1 つの応答に収まらない場合は、空以外の値を持つ継続トークンが API の応答に含まれます。 この値が次の API 呼び出しに渡されると、API が次の結果セットを返します。 それ以上の結果がない場合は、継続トークンに値が含まれません。 このパラメーターの値を URL にエンコードすることはできません。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-partition-load"></a>sfctl partition load
指定された Service Fabric パーティションの負荷情報を取得します。

指定されたパーティションの負荷に関する情報を返します。 応答には、Service Fabric パーティションの負荷レポートの一覧が含まれます。 各レポートには、負荷メトリック名、値、および最終報告時刻 (UTC) が含まれます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --partition-id [必須] | パーティションの ID。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-partition-load-reset"></a>sfctl partition load-reset
Service Fabric パーティションの現在の負荷をリセットします。

Service Fabric パーティションの現在の負荷をサービスの既定の負荷にリセットします。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --partition-id [必須] | パーティションの ID。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-partition-quorum-loss"></a>sfctl partition quorum-loss
特定のステートフル サービス パーティションをクォーラム損失状態にします。

この API は、サービスで一時的にクォーラム損失が発生した場合に役立ちます。 この API で開始された操作に関する情報を返すには、同じ OperationId を使用して GetQuorumLossProgress API を呼び出してください。 これは、ステートフルで永続化された (HasPersistedState = = true) サービスのみで呼び出すことができます。  ステートレス サービスまたはメモリ内のみのステートフル サービスでは、この API を使用しないでください。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --operation-id         [必須] | この API の呼び出しを識別する GUID。  これは対応する GetProgress API に渡されます。 |
| --partition-id         [必須] | パーティションの ID。 |
| --quorum-loss-duration [必須] | パーティションがクォーラム損失の状態で保持される期間。  これは、秒数で指定する必要があります。 |
| --quorum-loss-mode     [必須] | この列挙型は、誘発するクォーラム損失の種類を示すために StartQuorumLoss API に渡されます。 |
| --service-id           [必須] | サービスの ID。 この ID は通常、"fabric\:" URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、サービス名が "fabric\:/myapp/app1/svc1" の場合、サービス ID は、6.0 以降では "myapp\~app1\~svc1" になり、それより前のバージョンでは "myapp/app1/svc1" になります。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl partition quorum-loss-status
StartQuorumLoss API を使用して開始されたパーティションのクォーラム損失操作の進行状況を取得します。

指定された OperationId を使用して、StartQuorumLoss で開始されたクォーラム損失操作の進行状況を取得します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --operation-id [必須] | この API の呼び出しを識別する GUID。  これは対応する GetProgress API に渡されます。 |
| --partition-id [必須] | パーティションの ID。 |
| --service-id   [必須] | サービスの ID。 この ID は通常、"fabric\:" URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、サービス名が "fabric\:/myapp/app1/svc1" の場合、サービス ID は、6.0 以降では "myapp\~app1\~svc1" になり、それより前のバージョンでは "myapp/app1/svc1" になります。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-partition-recover"></a>sfctl partition recover
クォーラム損失で現在スタックしている指定されたパーティションの復旧を試みる必要があることを Service Fabric クラスターに示します。

この操作は、停止しているレプリカを復旧できないことがわかっている場合にのみ実行してください。 この API を不適切に使用すると、データ損失が発生する可能性があります。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --partition-id [必須] | パーティションの ID。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-partition-recover-all"></a>sfctl partition recover-all
現在クォーラム損失の状態に陥っているすべてのサービス (システム サービスを含む) の復旧を試みる必要があることを Service Fabric クラスターに示します。

この操作は、停止しているレプリカを復旧できないことがわかっている場合にのみ実行してください。 この API を不適切に使用すると、データ損失が発生する可能性があります。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-partition-report-health"></a>sfctl partition report-health
Service Fabric パーティションの正常性レポートを送信します。

指定された Service Fabric パーティションの正常性の状態をレポートします。 レポートには、正常性レポートのソースとレポートの対象となるプロパティに関する情報が含まれる必要があります。 このレポートは Service Fabric ゲートウェイ パーティションに送信され、これにより正常性ストアに転送されます。 レポートは、ゲートウェイで受け入れられても、追加の検証後に正常性ストアによって拒否される可能性があります。 たとえば、正常性ストアは、古いシーケンス番号などの無効なパラメーターが原因でレポートを拒否する場合があります。 正常性ストアでレポートが適用されたかどうかを確認するには、イベント セクションにレポートが表示されていることを確認してください。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --health-property [必須] | 正常性情報のプロパティ。 <br><br> エンティティには、さまざまなプロパティの正常性レポートを含めることができます。 このプロパティは固定値列挙型ではなく文字列で、これにより、レポーターは、レポートをトリガーする状態の条件を柔軟に分類できます。 たとえば、SourceId が "LocalWatchdog" のレポーターは、ノード上で使用可能なディスクの状態を監視できるため、そのノードの "AvailableDisk" プロパティをレポートできます。 同じレポーターは、ノードの接続性を監視できるため、同じノードの "Connectivity" プロパティをレポートできます。 正常性ストアでは、これらのレポートは、指定されたノードの個々の正常性イベントとして扱われます。 このプロパティは SourceId と共に、正常性情報を一意に識別します。 |
| --health-state    [必須] | 指定可能な値\: "Invalid"、"Ok"、"Warning"、"Error"、"Unknown"。 |
| --partition-id    [必須] | パーティションの ID。 |
| --source-id       [必須] | 正常性に関する情報を生成したクライアント/ウォッチドッグ/システム コンポーネントを識別するソース名。 |
| --description | 正常性情報の説明。 <br><br> レポートについて人間が判読できる情報を追加するために使用するフリー テキストを表します。 この説明の文字列の最大長は 4,096 文字です。 指定された文字列が長い場合は、自動的に切り捨てられます。 切り捨てられると、説明の末尾の文字には "[切り捨て]" というマーカーが含まれ、文字列の合計サイズが 4,096 文字になります。 このマーカーの存在は、切り捨てが発生したことをユーザーに示します。 切り捨てられた場合、説明に含まれるのは元の文字列のうち 4,096 文字未満であることに注意してください。 |
| --immediate | レポートをすぐに送信する必要があるかどうかを示すフラグ。 <br><br> 正常性レポートは Service Fabric ゲートウェイ アプリケーションに送信され、これにより正常性ストアに転送されます。 Immediate が true に設定された場合、HTTP ゲートウェイ アプリケーションで使用されているファブリック クライアント設定に関係なく、このレポートはすぐに HTTP ゲートウェイから正常性ストアに送信されます。 これは、できるだけ早く送信する必要がある重要なレポートに役立ちます。 タイミングやその他の条件に応じて、レポートの送信は依然として失敗する可能性があります。たとえば、HTTP ゲートウェイが閉じている場合やメッセージがゲートウェイに到達しない場合です。 Immediate が false に設定された場合、レポートは、正常性クライアントの設定に基づいて HTTP ゲートウェイから送信されます。 その結果、HealthReportSendInterval の構成に従ってバッチ処理されます。 この設定が推奨されているのは、正常性クライアントが正常性ストアへの正常性レポート メッセージに加えて正常性レポート処理を最適化できるようになるためです。 既定では、レポートはすぐに送信されません。 |
| --remove-when-expired | 有効期限が切れたときにレポートを正常性ストアから削除するかどうかを示す値。 <br><br> true に設定した場合、レポートは、有効期限が切れると正常性ストアから削除されます。 false に設定した場合、レポートは、有効期限が切れるとエラーとして処理されます。 既定では、このプロパティの値は false です。 クライアントは、定期的にレポートする場合、RemoveWhenExpired を false (既定値) に設定する必要があります。 こうすることで、レポーターで問題 (デッドロックなど) が発生していてレポートできない場合に、正常性レポートの有効期限が切れると、エンティティはエラーと評価されます。 これにより、このエンティティの正常性はエラー状態と見なされます。 |
| --sequence-number | 数値文字列で表された、この正常性レポートのシーケンス番号。 <br><br> レポートのシーケンス番号は、古いレポートを検出するために正常性ストアによって使用されます。 指定しない場合、シーケンス番号は、レポートが追加されたときに正常性クライアントによって自動的に生成されます。 |
| --timeout -t | 既定値\: 60。 |
| --ttl | この正常性レポートの有効期間。 このフィールドでは、この期間の指定に ISO8601 形式が使用されます。 <br><br> クライアントは、定期的にレポートする場合、レポートの送信頻度を有効期間よりも高くする必要があります。 クライアントは、遷移時にレポートする場合、有効期間を無限に設定できます。 有効期間が期限切れになると、正常性に関する情報を含む正常性イベントは、正常性ストアから削除される (RemoveWhenExpired が true の場合) か、エラーと評価されます (RemoveWhenExpired が false の場合)。 指定しない場合、有効期間の既定値は無限になります。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-partition-restart"></a>sfctl partition restart
この API は、指定されたパーティションのレプリカやインスタンスの一部またはすべてを再起動します。

この API は、フェールオーバーをテストするために役立ちます。 ステートレス サービス パーティションを対象に使用する場合、RestartPartitionMode を AllReplicasOrInstances にする必要があります。 同じ OperationId を使用して GetPartitionRestartProgress API を呼び出し、進行状況を取得します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --operation-id [必須] | この API の呼び出しを識別する GUID。  これは対応する GetProgress API に渡されます。 |
| --partition-id [必須] | パーティションの ID。 |
| --restart-partition-mode [必須] | 再起動するパーティションを記述します。 |
| --service-id [必須] | サービスの ID。 この ID は通常、"fabric\:" URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、サービス名が "fabric\:/myapp/app1/svc1" の場合、サービス ID は、6.0 以降では "myapp\~app1\~svc1" になり、それより前のバージョンでは "myapp/app1/svc1" になります。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-partition-restart-status"></a>sfctl partition restart-status
StartPartitionRestart を使用して開始されたパーティション再起動操作の進行状況を取得します。

指定された OperationId を使用して、StartPartitionRestart で開始されたパーティション再起動の進行状況を取得します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --operation-id [必須] | この API の呼び出しを識別する GUID。  これは対応する GetProgress API に渡されます。 |
| --partition-id [必須] | パーティションの ID。 |
| --service-id   [必須] | サービスの ID。 この ID は通常、"fabric\:" URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、サービス名が "fabric\:/myapp/app1/svc1" の場合、サービス ID は、6.0 以降では "myapp\~app1\~svc1" になり、それより前のバージョンでは "myapp/app1/svc1" になります。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-partition-svc-name"></a>sfctl partition svc-name
パーティションの Service Fabric サービスの名前を取得します。

指定されたパーティションのサービスの名前を取得します。 クラスターにパーティション ID が存在しない場合、404 エラーが返されます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --partition-id [必須] | パーティションの ID。 |
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
