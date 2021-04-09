---
title: Azure Service Fabric CLI - sfctl chaos
description: Azure Service Fabric のコマンド ライン インターフェイス、sfctl について説明します。 chaos を管理するためのコマンドの一覧が含まれています。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f59eb3296c27e64eb6a4644b2f455e3704381f49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260831"
---
# <a name="sfctl-chaos"></a>sfctl chaos
chaos テスト サービスの開始、停止、レポートを実行します。

## <a name="subgroups"></a>サブグループ
|サブグループ|説明|
| --- | --- |
| [schedule](service-fabric-sfctl-chaos-schedule.md) | chaos のスケジュールを取得および設定します。 |
## <a name="commands"></a>コマンド

|コマンド|説明|
| --- | --- |
| events | 継続トークンまたは時間範囲に基づいて Chaos イベントの次のセグメントを取得します。 |
| get | Chaos の状態を取得します。 |
| start | クラスターで Chaos を開始します。 |
| stop | Chaos がクラスターで実行している場合は停止し、Chaos のスケジュールを停止状態にします。 |

## <a name="sfctl-chaos-events"></a>sfctl chaos events
継続トークンまたは時間範囲に基づいて Chaos イベントの次のセグメントを取得します。

Chaos イベントの次のセグメントを取得するには、ContinuationToken を指定できます。 Chaos イベントの新しいセグメントの開始を取得するには、StartTimeUtc と EndTimeUtc を使って時間範囲を指定できます。 ContinuationToken と時間範囲の両方を同じ呼び出しで指定することはできません。 Chaos イベントの数が 100 を超える場合、Chaos イベントは複数のセグメントで返されます。各セグメントに含まれる Chaos イベントの数は 100 個以下で、次のセグメントを取得するには継続トークンを指定してこの API を呼び出します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --continuation-token | 継続トークンのパラメーターは、次の結果セットを取得するために使用されます。 システムからの結果が 1 つの応答に収まらない場合は、空以外の値を持つ継続トークンが API の応答に含まれます。 この値が次の API 呼び出しに渡されると、API が次の結果セットを返します。 それ以上の結果がない場合は、継続トークンに値が含まれません。 このパラメーターの値を URL にエンコードすることはできません。 |
| --end-time-utc | Chaos レポートが生成される時間範囲の終了時刻を表す Windows ファイル時間。 詳しくは、「[DateTime.ToFileTimeUtc Method](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx)」(DateTime.ToFileTimeUtc メソッド) をご覧ください。 |
| --max-results | ページング クエリの一部として返される結果の最大数。 このパラメーターは、返される結果の数に上限を定義します。 返された結果が、構成で定義したメッセージの最大サイズの制限に収まらない場合は、指定した最大結果数よりも少なくなる場合があります。 このパラメーターがゼロまたは指定されていない場合、ページング クエリには、応答メッセージに収まるできるだけ多くの結果が含まれます。 |
| --start-time-utc | Chaos レポートが生成される時間範囲の開始時刻を表す Windows ファイル時間。 詳しくは、「[DateTime.ToFileTimeUtc Method](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx)」(DateTime.ToFileTimeUtc メソッド) をご覧ください。 |
| --timeout -t | 操作を実行するためのサーバー タイムアウト (秒単位)。 このタイムアウトは、要求した操作が完了するまでクライアントが待機できる期間を指定します。 このパラメーターの既定値は 60 秒です。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-chaos-get"></a>sfctl chaos get
Chaos の状態を取得します。

Chaos が実行中かどうかを示す Chaos の状態、Chaos の実行に使用されている Chaos パラメーター、および Chaos スケジュールの状態を取得します。

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

## <a name="sfctl-chaos-start"></a>sfctl chaos start
クラスターで Chaos を開始します。

クラスターで Chaos がまだ実行されていない場合、渡された Chaos パラメーターで Chaos を開始します。 Chaos が既に実行しているときにこの呼び出しを行った場合は、呼び出しはエラー コード FABRIC_E_CHAOS_ALREADY_RUNNING で失敗します。 詳細については、「[Service Fabric クラスターでの制御された混乱の誘発](https\://docs.microsoft.com/azure/service-fabric/service-fabric-controlled-chaos)」の記事を参照してください。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --app-type-health-policy-map | 特定のアプリケーションの種類の異常なアプリケーションの最大割合の JSON でエンコードされたディクショナリ (キーと値) エントリ配列。 各ディクショナリ エントリは、キーとしてアプリケーションの種類名を指定し、指定したアプリケーションの種類のアプリケーションを評価するために使用される MaxPercentUnhealthyApplications を表す値の整数を指定します。 <br><br> 特定のアプリケーションの種類の異常なアプリケーションの最大の割合でマップを定義します。 アプリケーションの種類の正常性ポリシー マップをクラスターの正常性評価時に使用して、個別のアプリケーションの種類を記述できます。 マップに含まれるアプリケーションの種類は、クラスターの正常性ポリシーで定義されているグローバルな MaxPercentUnhealthyApplications ではなく、マップで指定されている割合に対して評価されます。 マップで指定されているアプリケーションの種類のアプリケーションは、アプリケーションのグローバル プールに対してカウントされません。 たとえば、ある種類の一部のアプリケーションがクリティカルである場合、クラスター管理者はそのアプリケーションの種類のエントリをマップに追加し、値 0% を割り当てることができます (すべてのエラーは許容されません)。 他のすべてのアプリケーションは、20% に設定された MaxPercentUnhealthyApplications で評価して、多数のアプリケーション インスタンスのうち一部の障害を許容できます。 アプリケーションの種類の正常性ポリシー マップは、HealthManager/EnableApplicationTypeHealthEvaluation の構成エントリを使うアプリケーションの種類の正常性評価がクラスター マニフェストで有効になっている場合にのみ使われます。 <br><br> JSON でエンコードされた文字列の例: [{\"key\": \"fabric:/Voting\", \"value\":\"0\"}] |
| --chaos-target-filter | 2 つの文字列型キーを持つ JSON エンコードのディクショナリ。 2 つのキーは、NodeTypeInclusionList と ApplicationInclusionList です。 どちらのキーの値も、文字列のリストです。 chaos_target_filter では、対象を絞った Chaos 障害のためのすべてのフィルターが定義されています (たとえば、特定のノードの種類だけを障害にする、または特定のアプリケーションだけを障害にする)。 <br><br> chaos_target_filter が使われない場合、Chaos はすべてのクラスター エンティティを障害にします。 chaos_target_filter が使われた場合、Chaos は chaos_target_filter の指定を満たすエンティティのみを障害にします。 NodeTypeInclusionList と ApplicationInclusionList では、和集合セマンティクスのみが可能です。 NodeTypeInclusionList と ApplicationInclusionList の積集合を指定することはできません。 たとえば、"アプリケーションが特定のノードの種類上にある場合のみ障害を発生させる" と指定することはできません。 エンティティが NodeTypeInclusionList または ApplicationInclusionList のどちらかに含まれている時点で、ChaosTargetFilter を使用してエンティティを除外することはできません。 applicationX が ApplicationInclusionList に含まれていない場合でも、そのアプリケーションが NodeTypeInclusionList に含まれている nodeTypeY のノード上にあるという理由で、何らかの混乱の反復処理中にそのアプリケーションで障害が発生する可能性があります。 NodeTypeInclusionList と ApplicationInclusionList の両方が空の場合は、ArgumentException がスローされます。 すべての種類の障害 (ノードの再起動、コード パッケージの再起動、レプリカの削除、レプリカの再起動、プライマリの移動、およびセカンダリの移動) は、指定されたノードの種類のノードで有効になります。 ノードの種類 (たとえば NodeTypeX) が NodeTypeInclusionList に含まれていない場合、ノード レベルの障害 (ノードの再起動など) が NodeTypeX のノードで有効になることはありませんが、ApplicationInclusionList 内のアプリケーションが NodeTypeX のノードに存在する場合、コード パッケージ障害とレプリカ障害が NodeTypeX のノードで有効になる可能性があります。 この一覧には最大 100 種のノードの種類を含めることができ、この数を増やすには、MaxNumberOfNodeTypesInChaosEntityFilter 構成をアップグレードする必要があります。 指定されたアプリケーションのサービスに属すすべてのレプリカは、混乱によって誘発されるレプリカ障害 (レプリカの再起動、レプリカの削除、プライマリの移動、およびセカンダリの移動) を受け入れます。 コード パッケージがこれらのアプリケーションのレプリカをホストしている場合のみ、混乱は、コード パッケージを再起動できます。 アプリケーションがこの一覧に含まれていない場合でも、NodeTypeInclusionList に含まれるノードの種類のノード上にアプリケーションが存在する場合は、混乱の反復処理によって障害が発生する可能性があります。 ただし、applicationX が配置制約によって nodeTypeY に関連付けられているときに、applicationX が ApplicationInclusionList になく、nodeTypeY が NodeTypeInclusionList にない場合、applicationX で障害が発生することはありません。 この一覧には最大 1,000 個のアプリケーションの名前を含めることができ、この数を増やすには、MaxNumberOfApplicationsInChaosEntityFilter 構成をアップグレードする必要があります。 |
| --context | (文字列, 文字列) 型のキーと値のペアの JSON エンコード マップ。 マップを使用して、混乱の実行に関する情報を記録できます。 このようなペアが 100 を超えることはできませんし、各文字列 (キーまたは値) の最大は 4095 文字です。 このマップは、オプションで特定の実行に関するコンテキストを格納するために、混乱実行を開始するユーザーによって設定されます。 |
| --disable-move-replica-faults | move primary と move secondary の障害を無効にします。 |
| --max-cluster-stabilization | すべてのクラスター エンティティが安定し正常な状態になるまで待機する最長時間。  既定値\: 60。 <br><br> Chaos は、繰り返して実行し、各繰り返しの開始時にクラスターのエンティティの正常性を検証します。 検証中に、クラスターのエンティティが MaxClusterStabilizationTimeoutInSeconds の時間内に安定して正常にならない場合、Chaos は検証失敗イベントを生成します。 |
| --max-concurrent-faults | 各イテレーションで誘発される同時実行のエラーの最大数。 Chaos は繰り返して実行し、2 つの連続する繰り返しは検証フェーズで区切られます。 コンカレンシーが高いほど、障害の挿入は積極的になり、より複雑な一連の状態が誘発されて、バグが発見されます。 2 または 3 の値で始めて、注意しながら値を増やすことをお勧めします。  既定値\: 1。 |
| --max-percent-unhealthy-apps | Chaos 中にクラスターの正常性を評価するときに、エラーを報告する前の異常なアプリケーションの最大許容パーセンテージ。 <br><br> エラーを報告する前の異常なアプリケーションの最大許容パーセンテージ。 たとえば、アプリケーションの 10% の異常を許容するには、この値を 10 にします。 パーセンテージは、異常な可能性のあるアプリケーションの最大許容パーセンテージを表します。この値を超えるとクラスターはエラーの状態と見なされます。 許容パーセンテージ内であっても、1 つ以上の異常なアプリケーションがある場合は、正常性は Warning として評価されます。 これは、異常なアプリケーションの数をクラスター内のアプリケーション インスタンスの総数で割ることによって計算されます。ApplicationTypeHealthPolicyMap に含まれているアプリケーションの種類のアプリケーションは除外されます。 切り上げ計算が実行され、少数のアプリケーションに対する 1 つのエラーは許容されます。 既定のパーセンテージは 0 です。 |
| --max-percent-unhealthy-nodes | Chaos 中にクラスターの正常性を評価するときに、エラーを報告する前の異常なノードの最大許容パーセンテージ。 <br><br> エラーを報告する前の異常なノードの最大許容パーセンテージ。 たとえば、ノードの 10% の異常を許容するには、この値を 10 にします。 パーセンテージは、異常な可能性のあるノードの最大許容パーセンテージを表します。この値を超えるとクラスターはエラーの状態と見なされます。 許容パーセンテージ内であっても、1 つ以上の異常なノードがある場合は、正常性は Warning として評価されます。 パーセンテージは、異常なノードの数をクラスター内のノードの総数で割ることによって計算されます。 切り上げ計算が実行され、少数のノードに対する 1 つのエラーは許容されます。 既定のパーセンテージは 0 です。 大規模なクラスターでは、ダウンしているか修復を必要とするノードがいくつか必ず存在するため、それが許容されるようにこのパーセンテージを構成する必要があります。 |
| --time-to-run | 自動停止するまでに Chaos が実行される合計時間 (秒)。 最大許容値は、4,294,967,295 (System.UInt32.MaxValue) です。  既定値\: 4294967295。 |
| --timeout -t | 既定値\: 60。 |
| --wait-time-between-faults | 1 つのイテレーション内で連続する障害の間の時間 (秒単位)。  既定値\: 20。 <br><br> 値が大きいほど、障害間のオーバーラップは少なくなり、クラスターの状態遷移のシーケンスは単純になります。 1 ～ 5 の値で始めて、注意しながら値を増やすことをお勧めします。 |
| --wait-time-between-iterations | Chaos の 2 つの連続するイテレーション間の時間区切り (秒単位)。 値が大きいほど、障害挿入率は低くなります。  既定値\: 30。 |
| --warning-as-error | 警告がエラーと同じ重大度で処理されることを示します。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-chaos-stop"></a>sfctl chaos stop
Chaos がクラスターで実行している場合は停止し、Chaos のスケジュールを停止状態にします。

Chaos が新しい障害を実行するのを停止します。 転送中の障害は、完了するまで実行を続けられます。 現在の Chaos スケジュールは、停止状態になります。 停止したスケジュールはそのまま停止状態に留まり、Chaos の新しい実行の Chaos スケジュールには使用されません。 スケジュールを再開するには、新しい Chaos スケジュールを設定する必要があります。

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


## <a name="next-steps"></a>次のステップ
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](./scripts/sfctl-upgrade-application.md)を使用して、Service Fabric CLI の使用方法を学習します。
