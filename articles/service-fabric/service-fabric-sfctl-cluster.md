---
title: Azure Service Fabric CLI- sfctl cluster | Microsoft Docs
description: "Service Fabric CLI sfctl cluster のコマンドについて説明します。"
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: dc2ed59d6adaca97b23dddcb7ec968d90171b483
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-cluster"></a>sfctl cluster
Service Fabric クラスターの選択、管理、および操作を行います。

## <a name="commands"></a>コマンド

|コマンド|Description|
| --- | --- |
|    code-versions| Service Fabric クラスターでプロビジョニングされたファブリック コードのバージョンの一覧を取得します。|
|    config-versions | Service Fabric クラスターでプロビジョニングされた Fabric Config のバージョンの一覧を取得します。|
|    health       | Service Fabric クラスターの正常性を取得します。|
|    manifest     | Service Fabric クラスター マニフェストを取得します。|
|    operation-cancel| ユーザーが誘発したフォールト操作を取り消します。|
|    operationgit | 指定した入力でフィルター処理されたユーザーが誘発したフォールト操作の一覧を取得します。|
|    provision     | Service Fabric クラスターのコードまたは構成パッケージをプロビジョニングします。|
|    recover-system  | クォーラム損失で現在、スタックしているシステム サービスの回復を試みる必要があることを Service Fabric クラスターに示します。|
|report-health   | Service Fabric クラスターの正常性レポートを送信します。|
|    select       | Service Fabric クラスターのエンドポイントに接続します。|
| unprovision     | Service Fabric クラスターのコードまたは構成パッケージをプロビジョニング解除します。|
|    upgrade         | Service Fabric クラスターのコードまたは構成バージョンのアップグレードを開始します。|
|    upgrade-resume  | クラスターのアップグレードを次のアップグレード ドメインに進めます。|
|    upgrade-rollback| Service Fabric クラスターのアップグレードをロールバックします。|
|    upgrade-status  | 現在のクラスターのアップグレードの進行状況を取得します。|
|upgrade-update  | Service Fabric クラスター アップグレードのアップグレード パラメーターを更新します。|


## <a name="sfctl-cluster-health"></a>sfctl cluster health
Service Fabric クラスターの正常性を取得します。

Service Fabric クラスターの正常性を取得します。 正常性状態に基づいてクラスターで報告される正常性イベントのコレクションをフィルター処理するには、EventsHealthStateFilter を使用します。 同様に、集計された正常性の状態に基づいて返されるノードとアプリケーションのコレクションをフィルターするには、NodesHealthStateFilter と ApplicationsHealthStateFilter を使用します。 

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --applications-health-state-filter| アプリケーションの正常性の状態をフィルターできます。
                                                    objects returned in the result of cluster health
                                                    query based on their health state. The possible
                                                    values for this parameter include integer value
                                                    obtained from members or bitwise operations on
                                                    members of HealthStateFilter enumeration. Only
                                                    applications that match the filter are returned.
                                                    All applications are used to evaluate the
                                                    aggregated health state. If not specified, all
                                                    entries are returned. The state values are flag
                                                    based enumeration, so the value could be a
                                                    combination of these values obtained using
                                                    bitwise 'OR' operator. For example, if the
                                                    provided value is 6 then health state of
                                                    applications with HealthState value of OK (2)
                                                    and Warning (4) are returned. - Default -
                                                    Default value. Matches any HealthState. The
                                                    value is zero. - None - Filter that doesn't
                                                    match any HealthState value. Used in order to
                                                    return no results on a given collection of
                                                    states. The value is 1. - Ok - Filter that
                                                    matches input with HealthState value Ok. The
                                                    value is 2. - Warning - Filter that matches
                                                    input with HealthState value Warning. The value
                                                    is 4. - Error - Filter that matches input with
                                                    HealthState value Error. The value is 8. - All -
                                                    Filter that matches input with any HealthState value. The value is 65535.|
| --events-health-state-filter   | 正常性の状態に基づいて返された HealthEvent オブジェクトのコレクションをフィルターできます。 このパラメーターに指定できる値には、次の正常性の状態のいずれかの整数値が含まれます。 フィルターに一致するイベントのみが返されます。 すべてのイベントが集計された正常性の状態を評価するために使用されます。 指定しない場合、すべてのエントリが返されます。 状態値はフラグベースの列挙型であるため、値はビット演算子 'OR' を使用して取得したこれらの値の組み合わせが可能です。 たとえば、指定した値が 6 の場合、HealthState の値が OK (2) と Warning (4) のすべてのイベントが返されます。 - Default - 既定値。 任意の HealthState と一致します。 値は 0 です。 - None - どの HealthState 値とも一致しないフィルター。 状態の特定のコレクションの結果が返されないようにするために使用されます。 値は 1 です。 - Ok - HealthState 値が Ok の入力に一致するフィルター。 値は 2 です。 - Warning - HealthState 値が Warning の入力に一致するフィルター。
値は 4 です。 - Error - HealthState 値が Error の入力に一致するフィルター。 値は 8 です。 - All - 任意の HealthState 値の入力に一致するフィルター。 値は 65535 です。| | --exclude-health-statistics | 正常性の統計情報がクエリ結果の一部として返されるかどうかを示します。 既定では false です。 統計情報は正常性の状態が Ok、Warning、および Error の子エンティティの数を示します。| |   --include-system-application-health-statistics| 正常性の統計情報に fabric:/System アプリケーションの正常性の統計情報を含めるかどうかを示します。 既定では false です。 IncludeSystemApplicationHealthStatistics が true に設定されている場合、正常性の統計情報には fabric:/System アプリケーションに属しているエンティティが含まれます。 そうでない場合、クエリの結果には、ユーザー アプリケーションのみの正常性の統計情報が含まれます。 正常性の統計情報はこのパラメーターを適用したクエリの結果に含まれる必要があります。| | --nodes-health-state-filter    | 正常性の状態に基づいたクラスターの正常性クエリの結果で返されたノードの正常性の状態オブジェクトをフィルター処理できます。 このパラメーターに指定できる値には、次の正常性の状態のいずれかの整数値が含まれます。 フィルターに一致するノードのみが返されます。 すべてのノードが集計された正常性の状態を評価するために使用されます。 指定しない場合、すべてのエントリが返されます。 状態値はフラグベースの列挙型であるため、値はビット演算子 'OR' を使用して取得したこれらの値の組み合わせが可能です。 たとえば、指定した値が 6 の場合、HealthState の値が OK (2) と Warning (4) のノードの正常性の状態が返されます。 - Default - 既定値。 任意の HealthState と一致します。 値は 0 です。 - None - どの HealthState 値とも一致しないフィルター。 状態の特定のコレクションの結果が返されないようにするために使用されます。 値は 1 です。 - Ok - HealthState 値が Ok の入力に一致するフィルター。 値は 2 です。 - Warning - HealthState 値が Warning の入力に一致するフィルター。
値は 4 です。 - Error - HealthState 値が Error の入力に一致するフィルター。 値は 8 です。 - All - 任意の HealthState 値の入力に一致するフィルター。 値は 65535 です。| | --timeout -t                   | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug                        | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h                      | このヘルプ メッセージを表示して終了します。|
| --output -o                    | 出力形式。  使用できる値は、json、jsonc、table、tsv です。                    既定値は json です。|
| --query                        | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose                      | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-cluster-manifest"></a>sfctl cluster manifest
Service Fabric クラスター マニフェストを取得します。

Service Fabric クラスター マニフェストを取得します。 クラスター マニフェストには、クラスターでのさまざまなノードの種類、セキュリティの構成、障害、アップグレード ドメイン トポロジなどを含む、クラスターのプロパティが含まれています。これらのプロパティは、スタンドアロンのクラスターをデプロイするときに、ClusterConfig.JSON ファイルの一部として指定されます。 ただし、クラスター マニフェスト内のほとんどの情報は、他のデプロイ シナリオ (たとえば [Azure Portal](https://portal.azure.com) を使用している場合など) でクラスターのデプロイ中に、Service Fabric によって内部的に生成されます。 クラスター マニフェストの内容は情報提供だけを目的としており、ユーザーがファイルの内容またはその解釈の形式で依存関係を取得することを想定していません。 

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --timeout -t| サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug  | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h| このヘルプ メッセージを表示して終了します。|
| --output -o | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query  | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose| ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-cluster-provision"></a>sfctl cluster provision
Service Fabric クラスターのコードまたは構成パッケージをプロビジョニングします。

        Validate and provision the code or configuration packages of a Service Fabric cluster.

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
|--cluster-manifest-file-path| クラスター マニフェスト ファイルのパス。|
|    --code-file-path            | クラスター コード パッケージ ファイルのパス。|
|    --timeout -t                | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h  | このヘルプ メッセージを表示して終了します。|
| --output -o| 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose  | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-cluster-select"></a>sfctl cluster select
Service Fabric クラスターのエンドポイントに接続します。

セキュリティで保護されたクラスターに接続している場合は、証明書 (.crt) とキー ファイル (.key) を指定するか、両方 (.pem) を持つ 1 つのファイルを指定します。 両方は指定できません。 セキュリティで保護されたクラスターに接続している場合、必要に応じて、CA バンドル ファイルまたは信頼された CA 証明書のディレクトリへのパスも指定します。

### <a name="arguments"></a>引数

|引数|Description|
| --- | --- |
| --endpoint [必須]| ポートと HTTP または HTTPS プレフィックスを含むクラスター エンドポイント URL。|
| --aad             | 認証に Azure Active Directory を使用します。|
| --ca              | 有効なファイルまたは CA バンドル ファイルとして処理する CA 証明書ディレクトリへのパス。|
| --cert            | クライアント証明書ファイルへのパス。|
| --key             | クライアント証明書のキー ファイルへのパス。|
| --no-verify       | HTTPS を使用する場合は、証明書の検証を無効にします。注: これは安全でないオプションのため、運用環境では使用できません。|
| --pem             | .pem ファイルとしてのクライアント証明書へのパス。|

### <a name="global-arguments"></a>グローバル引数

|引数|Description|
| --- | --- |
| --debug           | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
| --help -h         | このヘルプ メッセージを表示して終了します。|
| --output -o       | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
| --query           | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を参照してください。|
| --verbose         | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|

## <a name="sfctl-cluster-unprovision"></a>sfctl cluster unprovision
Service Fabric クラスターのコードまたは構成パッケージをプロビジョニング解除します。

        Unprovision the code or configuration packages of a Service Fabric cluster.

### <a name="arguments"></a>引数
|引数|Description|
| --- | --- |
|--code-version  | クラスター コード パッケージのバージョン。|
|    --config-version| クラスター マニフェストのバージョン。|
|    --timeout -t    | サーバー タイムアウト (秒)。  既定値は 60 です。|

### <a name="global-arguments"></a>グローバル引数
|引数|Description|
| --- | --- |
|--debug         | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
 |   --help -h       | このヘルプ メッセージを表示して終了します。|
 |   --output -o     | 出力形式。  使用できる値は、json、jsonc、table、tsv です。  既定値は json です。|
 |   --query         | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を
                      参照してください。|
 |   --verbose       | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。|


## <a name="sfctl-cluster-upgrade"></a>sfctl cluster upgrade
Service Fabric クラスターのコードまたは構成バージョンのアップグレードを開始します。

        Validate the supplied upgrade parameters and start upgrading the code or configuration
        version of a Service Fabric cluster if the parameters are valid.

### <a name="arguments"></a>引数
|引数|Description|
| --- | --- |
|    --app-health-map                      | アプリケーション名とエラーが報告されるまでの異常の最大パーセンテージのペアの JSON で
                                            エンコードされたディクショナリ。|
 |   --app-type-health-map                 | アプリケーションの種類のペアの JSON でエンコードされたディクショナリ
                                            name and maximum percentage unhealthy before raising
                                            error.|
 |   --code-version                        | クラスターのコードのバージョン。| |   --config-version                      | クラスター構成のバージョン。| |   --delta-health-evaluation             | 各アップグレード ドメインの完了後に、絶対正常性評価ではなく、差分正常性評価を有効にします。| |   --delta-unhealthy-nodes               | クラスターのアップグレード中に許容されるノードの正常性の低下の最大許容パーセンテージ。  既定値: 10。
差分は、アップグレードの開始時のノードの状態と、正常性評価の時のノードの状態の間で測定されます。 クラスターのグローバル状態が許容制限内かどうかを確認するため、すべてのアップグレード ドメインのアップグレードの完了後にチェックが実行されます。| |   --failure-action                      | 使用可能な値には、Invalid'、'Rollback'、'Manual' が含まれます。| |   --force-restart                       | 強制的に再起動します。| |   --health-check-retry                  | 正常性チェックの再試行のタイムアウト (ミリ秒単位)。| |   --health-check-stable                 | 正常性チェックの安定期間 (ミリ秒単位)。| |  --health-check-wait                   | 正常性チェックの待機期間 (ミリ秒単位)。| |  --replica-set-check-timeout           | アップグレードするレプリカ セット チェックのタイムアウト (秒単位)。| |   --rolling-upgrade-mode                | 使用可能な値には、'Invalid'、'UnmonitoredAuto'、'UnmonitoredManual'、'Monitored' が含まれます。  既定値は UnmonitoredAuto です。| |  --timeout -t                          | サーバー タイムアウト (秒)。  既定値は 60 です。| |  --unhealthy-applications              | エラーを報告する前の異常なアプリケーションの最大許容パーセンテージ。
たとえば、アプリケーションの 10% の異常を許容するには、この値を 10 にします。 パーセンテージは、異常な可能性のあるアプリケーションの最大許容パーセンテージを表します。この値を超えるとクラスターはエラーの状態と見なされます。 許容パーセンテージ内であっても、1 つ以上の異常なアプリケーションがある場合は、正常性は Warning として評価されます。 これは、異常なアプリケーションの数をクラスター内のアプリケーション インスタンスの総数で割ることによって計算されます。ApplicationTypeHealthPolicyMap に含まれているアプリケーションの種類のアプリケーションは除外されます。 切り上げ計算が実行され、少数のアプリケーションに対する 1 つのエラーは許容されます。| |   --unhealthy-nodes                     | エラーを報告する前に許容される異常なノードの最大許容パーセンテージ。
たとえば、ノードの 10% の異常を許容するには、この値を 10 にします。 パーセンテージは、異常な可能性のあるノードの最大許容パーセンテージを表します。この値を超えるとクラスターはエラーの状態と見なされます。 許容パーセンテージ内であっても、1 つ以上の異常なノードがある場合は、正常性は Warning として評価されます。 パーセンテージは、異常なノードの数をクラスター内のノードの総数で割ることによって計算されます。 切り上げ計算が実行され、少数のノードに対する 1 つのエラーは許容されます。 大規模なクラスターでは、いくつかのノードは常にダウンしているか修理中であるため、そうしたことを許容するためにこのパーセンテージを構成する必要があります。| |   --upgrade-domain-delta-unhealthy-nodes| クラスターのアップグレード中に許容されるアップグレード ドメイン ノードの正常性の低下の最大許容パーセンテージ。
既定値は 15 です。
差分は、アップグレードの開始時のアップグレード ドメイン ノードの状態と、正常性評価の時のアップグレード ドメイン ノードの状態の間で測定されます。 アップグレード ドメインの状態が許容制限内かどうかを確認するため、すべてのアップグレード ドメインのアップグレードの完了後にチェックが実行されます。| |   --upgrade-domain-timeout              | アップグレード ドメインのタイムアウト (ミリ秒単位)。| |   --upgrade-timeout                     | アップグレードのタイムアウト (ミリ秒単位)。| |   --warning-as-error                    | Warnings は Error と同じ重大度で扱われます。|

### <a name="global-arguments"></a>グローバル引数
    |引数|Description|
| --- | --- |
|--debug                               | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。|
|    --help -h                             | このヘルプ メッセージを表示して終了します。|
|    --output -o                           | 出力形式。  使用できる値は、json、jsonc、table、tsv です。
                                            既定値は json です。|
|    --query                               | JMESPath クエリ文字列。 詳細と例については、http://jmespath.org/ を
                                            参照してください。|
|    --verbose                             | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を
                                            使用します。|

## <a name="next-steps"></a>次のステップ
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。