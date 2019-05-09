---
title: Azure Service Fabric CLI- sfctl node | Microsoft Docs
description: Service Fabric CLI sfctl node のコマンドについて説明します。
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
ms.openlocfilehash: 08ea0081c84ea31b2b71d03679b1b527cf94c075
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662892"
---
# <a name="sfctl-node"></a>sfctl node
クラスターを形成するノードを管理します。

## <a name="commands"></a>command

|command|説明|
| --- | --- |
| disable | 指定した非アクティブ化インテントを使用して、Service Fabric クラスターのノードを非アクティブ化します。 |
| enable | 現在非アクティブ化されている Service Fabric クラスター ノードをアクティブにします。 |
| health | Service Fabric ノードの正常性を取得します。 |
| info | Service Fabric クラスター内の特定のノードに関する情報を取得します。 |
| list | Service Fabric クラスター内のノードの一覧を取得します。 |
| load | Service Fabric ノードの読み込み情報を取得します。 |
| remove-state | ノード上の永続化状態が完全に削除または失われたことを Service Fabric に通知します。 |
| report-health | Service Fabric ノードの正常性レポートを送信します。 |
| restart | Service Fabric クラスター ノードを再起動します。 |
| transition | クラスター ノードを開始または停止します。 |
| transition-status | StartNodeTransition を使用して開始された操作の進行状況を取得します。 |

## <a name="sfctl-node-disable"></a>sfctl node disable
指定した非アクティブ化インテントを使用して、Service Fabric クラスターのノードを非アクティブ化します。

指定した非アクティブ化インテントを使用して、Service Fabric クラスターのノードを非アクティブ化します。 非アクティブ化の進行中は、非アクティブ化インテントを増やすことができますが、減らすことはできません (たとえば、Pause インテントを使用して非アクティブ化されたノードは、Restart を使用してさらに非アクティブ化できますが、逆はできません)。 ノードを非アクティブ化したら、後からノードのアクティブ化操作を使用していつでも再アクティブ化することができます。 非アクティブ化が完了していない場合、これは非アクティブ化をキャンセルします。 非アクティブ化中にダウンして復帰するノードは、再アクティブ化してからでないと、サービスをそのノードに配置できません。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --node-name [必須] | ノード名。 |
| --deactivation-intent | ノードを非アクティブ化するインテントまたは理由について記述します。 指定できる値は次のとおりです。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-node-enable"></a>sfctl node enable
現在非アクティブ化されている Service Fabric クラスター ノードをアクティブにします。

現在非アクティブ化されている Service Fabric クラスター ノードをアクティブにします。 アクティブ化すると、ノードは再び新しいレプリカを配置するための有効なターゲットになり、ノードに残っている非アクティブ化されたすべてのレプリカが再アクティブ化されます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --node-name [必須] | ノード名。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-node-health"></a>sfctl node health
Service Fabric ノードの正常性を取得します。

Service Fabric ノードの正常性を取得します。 正常性状態に基づいてノードで報告される正常性イベントのコレクションをフィルター処理するには、EventsHealthStateFilter を使用します。 名前を指定したノードが正常性ストアに存在しない場合、エラーが返されます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --node-name       [必須] | ノード名。 |
| --events-health-state-filter | 正常性の状態に基づいて返された HealthEvent オブジェクトのコレクションをフィルター処理できます。 このパラメーターに指定できる値には、次の正常性の状態のいずれかの整数値が含まれます。 フィルターに一致するイベントのみが返されます。 すべてのイベントが集計された正常性の状態を評価するために使用されます。 指定しない場合、すべてのエントリが返されます。 状態値はフラグベースの列挙型であるため、値はビット演算子 'OR' を使用して取得したこれらの値の組み合わせが可能です。 たとえば、指定した値が 6 の場合、HealthState の値が OK (2) と Warning (4) のすべてのイベントが返されます。  <br> - Default - 既定値。 任意の HealthState と一致します。 値は 0 です。  <br> - None - どの HealthState 値とも一致しないフィルター。 状態の特定のコレクションの結果が返されないようにするために使用されます。 値は 1 です。  <br> - Ok - HealthState 値が Ok の入力に一致するフィルター。 値は 2 です。  <br> - Warning - HealthState 値が Warning の入力に一致するフィルター。 値は 4 です。  <br> - Error - HealthState 値が Error の入力に一致するフィルター。 値は 8 です。  <br> - All - 任意の HealthState 値の入力に一致するフィルター。 値は 65535 です。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-node-info"></a>sfctl node info
Service Fabric クラスター内の特定のノードに関する情報を取得します。

応答には、名前、状態、ID、正常性、アップタイム、およびノードに関するその他の詳細が含まれます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --node-name [必須] | ノード名。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-node-list"></a>sfctl node list
Service Fabric クラスター内のノードの一覧を取得します。

応答には、名前、状態、ID、正常性、アップタイム、およびノードに関するその他の詳細が含まれます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --continuation-token | 継続トークンのパラメーターは、次の結果セットを取得するために使用されます。 システムからの結果が 1 つの応答に収まらない場合は、空以外の値を持つ継続トークンが API の応答に含まれます。 この値が次の API 呼び出しに渡されると、API が次の結果セットを返します。 それ以上の結果がない場合は、継続トークンに値が含まれません。 このパラメーターの値を URL にエンコードすることはできません。 |
| --max-results | ページング クエリの一部として返される結果の最大数。 このパラメーターは、返される結果の数に上限を定義します。 返された結果が、構成で定義したメッセージの最大サイズの制限に収まらない場合は、指定した最大結果数よりも少なくなる場合があります。 このパラメーターがゼロまたは指定されていない場合、ページング クエリには、応答メッセージに収まるできるだけ多くの結果が含まれます。 |
| --node-status-filter | NodeStatus に基づいてノードをフィルターできます。 指定したフィルター値に一致するノードだけが返されます。 フィルター値には、次のいずれかを指定できます。  既定値\: default。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-node-load"></a>sfctl node load
Service Fabric ノードの読み込み情報を取得します。

負荷または容量が定義されているすべてのメトリックに対する Service Fabric ノードの負荷情報を取得します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --node-name [必須] | ノード名。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-node-remove-state"></a>sfctl node remove-state
ノード上の永続化状態が完全に削除または失われたことを Service Fabric に通知します。

これは、そのノードの永続化状態を復旧できないことを意味します。 これは一般に、ハード ディスクがクリーンにワイプされた場合、またはハード ディスクがクラッシュした場合に発生します。 この操作が成功するには、ノードがダウンしている必要があります。 この操作を使用すると、Service Fabric は、そのノード上のレプリカが存在しなくなったこと、およびそれらのレプリカの復元の待機を停止する必要があることを認識します。 ノードの状態が削除されておらず、ノードがそのままの状態で復元する可能性がある場合は、このコマンドレットを実行しないでください。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --node-name [必須] | ノード名。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-node-report-health"></a>sfctl node report-health
Service Fabric ノードの正常性レポートを送信します。

指定された Service Fabric ノードの正常性の状態をレポートします。 レポートには、正常性レポートのソースとレポートの対象となるプロパティに関する情報が含まれる必要があります。 このレポートは Service Fabric ゲートウェイ ノードに送信され、これにより正常性ストアに転送されます。 レポートは、ゲートウェイで受け入れられても、追加の検証後に正常性ストアによって拒否される可能性があります。 たとえば、正常性ストアは、古いシーケンス番号などの無効なパラメーターが原因でレポートを拒否する場合があります。 正常性ストアでレポートが適用されたかどうかを確認するには、HealthEvents セクションにレポートが表示されていることを確認してください。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --health-property [必須] | 正常性情報のプロパティ。 <br><br> エンティティには、さまざまなプロパティの正常性レポートを含めることができます。 このプロパティは固定値列挙型ではなく文字列で、これにより、レポーターは、レポートをトリガーする状態の条件を柔軟に分類できます。 たとえば、SourceId が "LocalWatchdog" のレポーターは、ノード上で使用可能なディスクの状態を監視できるため、そのノードの "AvailableDisk" プロパティをレポートできます。 同じレポーターは、ノードの接続性を監視できるため、同じノードの "Connectivity" プロパティをレポートできます。 正常性ストアでは、これらのレポートは、指定されたノードの個々の正常性イベントとして扱われます。 このプロパティは SourceId と共に、正常性情報を一意に識別します。 |
| --health-state    [必須] | 指定可能な値\: "Invalid"、"Ok"、"Warning"、"Error"、"Unknown"。 |
| --node-name       [必須] | ノード名。 |
| --source-id       [必須] | 正常性に関する情報を生成したクライアント/ウォッチドッグ/システム コンポーネントを識別するソース名。 |
| --description | 正常性情報の説明。 <br><br> レポートについて人間が判読できる情報を追加するために使用するフリー テキストを表します。 この説明の文字列の最大長は 4,096 文字です。 指定された文字列が長い場合は、自動的に切り捨てられます。 切り捨てられると、説明の末尾の文字には "[切り捨て]" というマーカーが含まれ、文字列の合計サイズが 4,096 文字になります。 このマーカーの存在は、切り捨てが発生したことをユーザーに示します。 切り捨てられた場合、説明に含まれるのは元の文字列のうち 4,096 文字未満であることに注意してください。 |
| --immediate | レポートをすぐに送信する必要があるかどうかを示すフラグ。 <br><br> 正常性レポートは Service Fabric ゲートウェイ アプリケーションに送信され、これにより正常性ストアに転送されます。 Immediate が true に設定された場合、HTTP ゲートウェイ アプリケーションで使用されているファブリック クライアント設定に関係なく、このレポートはすぐに HTTP ゲートウェイから正常性ストアに送信されます。 これは、できるだけ早く送信する必要がある重要なレポートに役立ちます。 タイミングやその他の条件に応じて、レポートの送信は依然として失敗する可能性があります。たとえば、HTTP ゲートウェイが閉じている場合やメッセージがゲートウェイに到達しない場合です。 Immediate が false に設定された場合、レポートは、正常性クライアントの設定に基づいて HTTP ゲートウェイから送信されます。 その結果、HealthReportSendInterval の構成に従ってバッチ処理されます。 この設定が推奨されているのは、正常性クライアントが正常性ストアへの正常性レポート メッセージに加えて正常性レポート処理を最適化できるようになるためです。 既定では、レポートはすぐに送信されません。 |
| --remove-when-expired | 有効期限が切れたときにレポートを正常性ストアから削除するかどうかを示す値。 <br><br> true に設定した場合、レポートは、有効期限が切れると正常性ストアから削除されます。 false に設定した場合、レポートは、有効期限が切れるとエラーとして処理されます。 既定では、このプロパティの値は false です。 クライアントは、定期的にレポートする場合、RemoveWhenExpired を false (既定値) に設定する必要があります。 こうすることで、レポーターで問題 (デッドロックなど) が発生していてレポートできない場合に、正常性レポートの有効期限が切れると、エンティティはエラーと評価されます。 これにより、このエンティティの正常性はエラー状態と見なされます。 |
| --sequence-number | 数値文字列で表された、この正常性レポートのシーケンス番号。 <br><br> レポートのシーケンス番号は、古いレポートを検出するために正常性ストアによって使用されます。 指定しない場合、シーケンス番号は、レポートが追加されたときに正常性クライアントによって自動的に生成されます。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |
| --ttl | この正常性レポートの有効期間。 このフィールドでは、この期間の指定に ISO8601 形式が使用されます。 <br><br> クライアントは、定期的にレポートする場合、レポートの送信頻度を有効期間よりも高くする必要があります。 クライアントは、遷移時にレポートする場合、有効期間を無限に設定できます。 有効期間が期限切れになると、正常性に関する情報を含む正常性イベントは、正常性ストアから削除される (RemoveWhenExpired が true の場合) か、エラーと評価されます (RemoveWhenExpired が false の場合)。 指定しない場合、有効期間の既定値は無限になります。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-node-restart"></a>sfctl node restart
Service Fabric クラスター ノードを再起動します。

既に起動している Service Fabric クラスター ノードを再起動します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --node-name [必須] | ノード名。 |
| --create-fabric-dump | Fabric ノード プロセスのダンプを作成する場合は True を指定します。 これは大文字小文字を区別します。  既定値\: False。 |
| --node-instance-id | ターゲット ノードのインスタンス ID。 インスタンス ID が指定されている場合、ノードの現在のインスタンスと一致する場合にのみ、ノードが再起動されます。 既定値の "0" は任意のインスタンス ID に一致します。 インスタンス ID は、get node query を使用して取得できます。  既定値\: 0。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-node-transition"></a>sfctl node transition
クラスター ノードを開始または停止します。

クラスター ノードを開始または停止します。  クラスター ノードはプロセスで、OS インスタンスそのものではありません。  ノードを開始するには、NodeTransitionType パラメーターに "Start" を渡します。 ノードを停止するには、NodeTransitionType パラメーターに "Stop" を渡します。 この API は、移行がまだ完了していない可能性のあるノードを API が返したときに操作を開始します。 操作の進行状況を取得するには、同じ OperationId を使用して GetNodeTransitionProgress を呼び出します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --node-instance-id         [必須] | ターゲット ノードのノード インスタンス ID。 これは GetNodeInfo API を通じて決定できます。 |
| --node-name                [必須] | ノード名。 |
| --node-transition-type     [必須] | 実行する移行の種類を示します。  NodeTransitionType.Start は、停止したノードを開始します。 NodeTransitionType.Stop は、稼働しているノードを停止します。 |
| --operation-id             [必須] | この API の呼び出しを識別する GUID。  これは対応する GetProgress API に渡されます。 |
| --stop-duration-in-seconds [必須] | ノードを停止させたままにする期間 (秒単位)。  最小値は 600、最大値は 14400 です。  この時間を過ぎると、ノードは自動的に復帰します。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-node-transition-status"></a>sfctl node transition-status
StartNodeTransition を使用して開始された操作の進行状況を取得します。

指定された OperationId を使用して、StartNodeTransition で開始された操作の進行状況を取得します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --node-name    [必須] | ノード名。 |
| --operation-id [必須] | この API の呼び出しを識別する GUID。  これは対応する GetProgress API に渡されます。 |
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