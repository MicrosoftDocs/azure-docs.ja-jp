---
title: Azure Service Fabric クラスター設定の変更 | Microsoft Docs
description: この記事では、カスタマイズ可能な Fabric の設定と Fabric アップグレード ポリシーについて説明します。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/12/2019
ms.author: aljo
ms.openlocfilehash: a309b30fc9438ded280109691afd3bde0883dc3c
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144391"
---
# <a name="customize-service-fabric-cluster-settings"></a>Service Fabric クラスターの設定をカスタマイズする
この記事では、カスタマイズできる Service Fabric クラスターのさまざまなファブリック設定について説明します。 Azure でホストされているクラスターの場合、[Azure portal](https://portal.azure.com) または Azure Resource Manager テンプレートを使って設定をカスタマイズできます。 詳細については、[Azure クラスターの構成のアップグレード](service-fabric-cluster-config-upgrade-azure.md)に関するページを参照してください。 スタンドアロン クラスターでは、*ClusterConfig.json* ファイルを更新し、クラスターで構成のアップグレードを実行することによって設定をカスタマイズします。 詳細については、[スタンドアロン クラスターの構成のアップグレード](service-fabric-cluster-config-upgrade-windows-server.md)に関するページを参照してください。

アップグレード ポリシーには、次の 3 種類があります。

- **Dynamic**: 動的構成を変更しても、Service Fabric プロセスまたはサービス ホスト プロセスのプロセス再起動は発生しません。 
- **Static**: 静的構成を変更すると、変更を反映するために Service Fabric ノードが再起動されます。 ノード上のサービスは再起動されます。
- **NotAllowed**: これらの設定は変更できません。 これらの設定を変更するには、クラスターを破棄して新しいクラスターを作成する必要があります。 

次に、カスタマイズできる Fabric の設定の一覧をセクション別に整理して示します。

## <a name="applicationgatewayhttp"></a>ApplicationGateway/Http

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|string、既定値は "None"|静的| サーバー証明書の検証を行いません。要求に成功しました。 構成 ServiceCertificateThumbprints で、リバース プロキシが信頼できるリモート証明書の、サムプリントのコンマ区切りリストを参照してください。 構成 ServiceCommonNameAndIssuer で、リバース プロキシが信頼できるリモート証明書の、サブジェクト名と発行者サムプリントを参照してください。 詳細については、[リバース プロキシのセキュリティで保護された接続](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services)に関する記事を参照してください。 |
|BodyChunkSize |uint、既定値は 16384 |動的| 本文の読み取りに使用するチャンクのサイズをバイト単位で指定します。 |
|CrlCheckingFlag|uint、既定値は 0x40000000 |動的| アプリケーション/サービス証明書チェーン検証のフラグ。例: CRL checking 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY。0 に設定すると、CRL チェックが無効になります。サポートされている値の一覧については、CertGetCertificateChain に関するページ (https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx ) の「dwFlags」を参照してください  |
|DefaultHttpRequestTimeout |時間 (秒単位)、 既定値は 120 です |動的|timespan を秒単位で指定します。  HTTP アプリケーション ゲートウェイで処理される HTTP 要求の既定の要求タイムアウトを指定します。 |
|ForwardClientCertificate|ブール値、既定値は FALSE|動的|false に設定すると、リバース プロキシはクライアント証明書を要求しません。true に設定すると、リバース プロキシは SSL ハンドシェイク中にクライアント証明書を要求し、base64 でエンコードされた PEM 書式設定文字列を X-Client-Certificate というヘッダーのサービスに転送します。サービスは、証明書データを検査した後、適切な状態コードで要求に失敗する可能性があります。 この状況で、クライアントが証明書を提示しない場合、リバース プロキシは空のヘッダーを転送し、サービスによって処理されます。 リバース プロキシは透明なレイヤーとして機能します。 詳細については、[クライアント証明書の認証の設定](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy)に関する記事を参照してください。 |
|GatewayAuthCredentialType |string、既定値は "None" |静的| HTTP アプリケーション ゲートウェイ エンドポイントで使用するセキュリティ資格情報の種類を示します。有効な値は None/X509 です。 |
|GatewayX509CertificateFindType |string、既定値は "FindByThumbprint" |動的| GatewayX509CertificateStoreName で指定されたストア内での証明書の検索方法を示します。サポートされる値:FindByThumbprint、FindBySubjectName。 |
|GatewayX509CertificateFindValue | string、既定値は "" |動的| HTTP アプリケーション ゲートウェイの証明書の検索に使用する検索フィルター値。 この証明書は HTTPS エンドポイントで構成されます。サービスで必要な場合は、この証明書を使用してアプリケーションの ID を検証することもできます。 FindValue が最初に検索され、これが存在しない場合は、FindValueSecondary が検索されます。 |
|GatewayX509CertificateFindValueSecondary | string、既定値は "" |動的|HTTP アプリケーション ゲートウェイの証明書の検索に使用する検索フィルター値。 この証明書は HTTPS エンドポイントで構成されます。サービスで必要な場合は、この証明書を使用してアプリケーションの ID を検証することもできます。 FindValue が最初に検索され、これが存在しない場合は、FindValueSecondary が検索されます。|
|GatewayX509CertificateStoreName |string、既定値は "My" |動的| HTTP アプリケーション ゲートウェイの証明書を格納する X.509 証明書ストアの名前。 |
|HttpRequestConnectTimeout|TimeSpan、既定値は Common::TimeSpan::FromSeconds(5)|動的|timespan を秒単位で指定します。  HTTP アプリ ゲートウェイから送信される HTTP 要求の接続タイムアウトを指定します。  |
|IgnoreCrlOfflineError|ブール値、既定値は TRUE|動的|アプリケーション/サービス証明書の確認で CRL オフライン エラーを無視するかどうか。 |
|IsEnabled |ブール値、既定値は false |静的| HttpApplicationGateway を有効または無効にします。 HttpApplicationGateway は既定で無効になっています。有効にするには、この構成を設定する必要があります。 |
|NumberOfParallelOperations | uint、既定値は 5000 |静的|HTTP サーバー キューに送信する読み取りの数。 HttpGateway が対応できる同時要求の数を制御します。 |
|RemoveServiceResponseHeaders|string、既定値は "Date; Server"|静的|サービス応答がクライアントに転送される前に応答から削除される応答ヘッダーの、セミコロン/コンマ区切りのリスト。 空の文字列に設定されている場合は、サービスから返されたすべてのヘッダーをそのまま渡します。 つまり データとサーバーを上書きしないでください |
|ResolveServiceBackoffInterval |時間 (秒単位)、既定値は 5 |動的|timespan を秒単位で指定します。  失敗したサービス解決操作を再試行するまでの既定のバックオフ間隔を指定します。 |
|SecureOnlyMode|ブール値、既定値は FALSE|動的| SecureOnlyMode: true:リバース プロキシの転送先は、セキュリティで保護されたエンドポイントを公開するサービスだけです。 false:リバース プロキシは、セキュリティで保護された/セキュリティで保護されないエンドポイントに要求を転送できます。 詳細については、[リバース プロキシ エンドポイントの選択ロジック](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints)に関する記事を参照してください。  |
|ServiceCertificateThumbprints|string、既定値は ""|動的|リバース プロキシが信頼できるリモート証明書のサムプリントのコンマで区切られた一覧。 詳細については、[リバース プロキシのセキュリティで保護された接続](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services)に関する記事を参照してください。 |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/Http/ServiceCommonNameAndIssuer

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap、既定値は None|動的| リバース プロキシが信頼できるリモート証明書のサブジェクト名と発行者のサムプリント。 詳細については、[リバース プロキシのセキュリティで保護された接続](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services)に関する記事を参照してください。 |

## <a name="backuprestoreservice"></a>BackupRestoreService

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int、既定値は 0|静的|BackupRestoreService の MinReplicaSetSize |
|PlacementConstraints|string、既定値は ""|静的|  BackupRestore サービスの PlacementConstraints |
|SecretEncryptionCertThumbprint|string、既定値は ""|動的|シークレット暗号化 X509 証明書の拇印 |
|SecretEncryptionCertX509StoreName|string、既定値は "My"|   動的|    これは、バックアップ復元サービスで使用されるストアの資格情報を暗号化または暗号化解除に使用される、X.509 証明書ストアの資格情報名を暗号化したり暗号化解除したりするために使用する証明書を示します。 |
|TargetReplicaSetSize|int、既定値は 0|静的| BackupRestoreService の TargetReplicaSetSize |

## <a name="clustermanager"></a>ClusterManager

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|EnableDefaultServicesUpgrade | ブール値、既定値は false |動的|アプリケーションのアップグレード時に既定のサービスのアップグレードを有効にします。 アップグレード後、既定のサービスの記述が上書きされます。 |
|FabricUpgradeHealthCheckInterval |秒単位。既定値は 60 |動的|監視対象 Fabric をアップグレードしているときに、正常性状態をチェックする頻度 |
|FabricUpgradeStatusPollInterval |秒単位。既定値は 60 |動的|Fabric アップグレード状態をポーリングする頻度。 この値により、GetFabricUpgradeProgress 呼び出しの更新レートが決まります |
|ImageBuilderTimeoutBuffer |時間 (秒単位)、既定値は 3 |動的|timespan を秒単位で指定します。 Image Builder の特定のタイムアウト エラーをクライアントに返すことを可能にするための時間。 このバッファーが小さすぎると、サーバーの前にクライアントがタイムアウトし、一般的なタイムアウト エラーが発生します。 |
|InfrastructureTaskHealthCheckRetryTimeout | 秒単位。既定値は 60 |動的|timespan を秒単位で指定します。 インフラストラクチャ タスクの後処理中に、失敗した正常性チェックの再試行に費やす時間。 正常性チェックに合格したことが確認されると、このタイマーはリセットされます。 |
|InfrastructureTaskHealthCheckStableDuration | 時間 (秒単位)、既定値は 0|動的| timespan を秒単位で指定します。 インフラストラクチャ タスクの後処理が正常に完了するまでに、正常性チェックに連続して合格していることを監視する時間。 正常性チェックに失敗したことが確認されると、このタイマーはリセットされます。 |
|InfrastructureTaskHealthCheckWaitDuration |時間 (秒単位)、既定値は 0|動的| timespan を秒単位で指定します。 インフラストラクチャ タスクの後処理の後、正常性チェックを開始するまでの待機時間。 |
|InfrastructureTaskProcessingInterval | 秒単位。既定値は 10 |動的|timespan を秒単位で指定します。 ステート マシンを処理するインフラストラクチャ タスクで使用する処理間隔。 |
|MaxCommunicationTimeout |時間 (秒単位)、既定値は 600 |動的|timespan を秒単位で指定します。 ClusterManager と他のシステム サービス (ネーム サービス、Failover Manager など) 間の内部通信の最大タイムアウト。 (各クライアント操作でシステム コンポーネント間の複数の通信が発生する可能性があるので) このタイムアウトはグローバルな MaxOperationTimeout の値よりも小さくする必要があります。 |
|MaxDataMigrationTimeout |時間 (秒単位)、既定値は 600 |動的|timespan を秒単位で指定します。 Fabric のアップグレードが実行された後のデータ移行復旧操作の最大タイムアウト。 |
|MaxOperationRetryDelay |時間 (秒単位)、既定値は 5|動的| timespan を秒単位で指定します。 エラーが発生したときの内部再試行の最大遅延。 |
|MaxOperationTimeout |時間 (秒単位)、既定値は MaxValue |動的| timespan を秒単位で指定します。 ClusterManager での操作を内部的に処理する際の最大グローバル タイムアウト。 |
|MaxTimeoutRetryBuffer | 時間 (秒単位)、既定値は 600 |動的|timespan を秒単位で指定します。 タイムアウトによって内部的に再試行する際の操作の最大タイムアウトは `<Original Time out> + <MaxTimeoutRetryBuffer>` です。 その他のタイムアウトは、MinOperationTimeout 単位で追加されます。 |
|MinOperationTimeout | 秒単位。既定値は 60 |動的|timespan を秒単位で指定します。 ClusterManager での操作を内部的に処理する際の最小グローバル タイムアウト。 |
|MinReplicaSetSize |int、既定値は 3 |禁止|ClusterManager の MinReplicaSetSize。 |
|PlacementConstraints | string、既定値は "" |禁止|ClusterManager の PlacementConstraints。 |
|QuorumLossWaitDuration |時間 (秒単位)、既定値は MaxValue |禁止| timespan を秒単位で指定します。 ClusterManager の QuorumLossWaitDuration。 |
|ReplicaRestartWaitDuration |時間 (秒単位)、既定値は (60.0 * 30)|禁止|timespan を秒単位で指定します。 ClusterManager の ReplicaRestartWaitDuration。 |
|ReplicaSetCheckTimeoutRollbackOverride |時間 (秒単位)、既定値は 1200 |動的| timespan を秒単位で指定します。 ReplicaSetCheckTimeout が DWORD の最大値に設定されている場合、ロールバックのためにこの構成の値でオーバーライドされます。 ロールフォワードに使用する値がオーバーライドされることはありません。 |
|SkipRollbackUpdateDefaultService | ブール値、既定値は false |動的|CM は、アプリケーションのアップグレードのロールバック時に、更新された既定のサービスを元に戻す操作をスキップします。 |
|StandByReplicaKeepDuration | 時間 (秒単位)、既定値は (3600.0 * 2)|禁止|timespan を秒単位で指定します。 ClusterManager の StandByReplicaKeepDuration。 |
|TargetReplicaSetSize |int、既定値は 7 |禁止|ClusterManager の TargetReplicaSetSize。 |
|UpgradeHealthCheckInterval |秒単位。既定値は 60 |動的|監視対象アプリケーションをアップグレードしているときに、正常性状態をチェックする頻度 |
|UpgradeStatusPollInterval |秒単位。既定値は 60 |動的|アプリケーションのアップグレード状態をポーリングする頻度。 この値により、GetApplicationUpgradeProgress 呼び出しの更新レートが決まります |

## <a name="common"></a>一般

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|PerfMonitorInterval |時間 (秒単位)、既定値は 1 |動的|timespan を秒単位で指定します。 パフォーマンスの監視間隔。 0 または負の値に設定すると、監視が無効になります。 |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **パラメーター** | **使用できる値** |**アップグレード ポリシー**| **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap、既定値は None|動的|ノードを空にするとき、続いて行われるポリシーの最適化を指定します。 メトリック 0 が指定されている場合、SF は UD と FD で均等にノードを最適化しようとする必要があります。1 の場合は、ノードを最適化する必要があることのみを示します |

## <a name="defragmentationmetrics"></a>DefragmentationMetrics
| **パラメーター** | **使用できる値** |**アップグレード ポリシー**| **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap、既定値は None|動的|最適化に使用し、負荷分散に使用しないメトリック セットを決定します。 |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **パラメーター** | **使用できる値** |**アップグレード ポリシー**| **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap、既定値は None|動的|クラスターを最適化済みと見なすうえで必要な空きノード数を決定します。それには、空きノード数をパーセンテージの範囲 (0.0 ～ 1.0) または数値 (1.0 以上) として指定します |

## <a name="diagnostics"></a>診断

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|AdminOnlyHttpAudit |ブール値、既定値は true | 動的 | クラスターの状態に影響を与えない HTTP 要求を監査から除外します。 現在、"GET" の種類の要求のみが除外されます。ただし、これは変更される可能性があります。 |
|AppDiagnosticStoreAccessRequiresImpersonation |ブール値、既定値は true | 動的 |アプリケーションの代わりに診断ストアにアクセスするときに偽装が必要かどうかを示します。 |
|AppEtwTraceDeletionAgeInDays |int、既定値は 3 | 動的 |アプリケーションの ETW トレースが含まれた古い ETL ファイルを削除するまでの日数。 |
|ApplicationLogsFormatVersion |int、既定値は 0 | 動的 |アプリケーション ログの形式のバージョン。 サポートされる値は 0 と 1 です。 バージョン 1 には、バージョン 0 よりも多くの ETW イベント レコードのフィールドが含まれます。 |
|AuditHttpRequests |ブール値、既定値は false | 動的 | HTTP 監査をオンまたはオフにします。 監査の目的は、要求を開始したユーザーを含め、クラスターに対して実行されたアクティビティを確認することです。 これはログ記録の最適な試行であり、トレースの損失が発生する可能性があることに注意してください。 "ユーザー" 認証を使用した HTTP 要求は記録されません。 |
|CaptureHttpTelemetry|ブール値、既定値は false | 動的 | HTTP テレメトリをオンまたはオフにします。 テレメトリの目的は、Service Fabric でテレメトリ データをキャプチャして将来の作業を計画し、問題のある領域を特定できるようにすることです。 テレメトリには、個人データや要求の本文は記録されません。 特に構成されていない限り、テレメトリにはすべての HTTP 要求がキャプチャされます。 |
|ClusterId |string | 動的 |クラスターの一意の ID。 これはクラスターの作成時に生成されます。 |
|ConsumerInstances |string | 動的 |DCA コンシューマー インスタンスのリスト。 |
|DiskFullSafetySpaceInMB |int、既定値は 1024 | 動的 |DCA によって使用されないようにする残りのディスク領域 (MB)。 |
|EnableCircularTraceSession |ブール値、既定値は false | 静的 |循環トレース セッションを使用する必要があるかどうかを示すフラグ。 |
|EnableTelemetry |ブール値、既定値は true | 動的 |テレメトリを有効または無効にします。 |
|FailuresOnlyHttpTelemetry | ブール値、既定値は true | 動的 | HTTP テレメトリのキャプチャが有効な場合、失敗した要求のみがキャプチャされます。 これは、テレメトリ用に生成されるイベント数を減らすために役立ちます。 |
|HttpTelemetryCapturePercentage | int、既定値は 50 | 動的 | HTTP テレメトリのキャプチャが有効な場合、ランダムな割合の要求のみがキャプチャされます。 これは、テレメトリ用に生成されるイベント数を減らすために役立ちます。 |
|MaxDiskQuotaInMB |int、既定値は 65536 | 動的 |Windows Fabric のログ ファイルのディスク クォータ (MB)。 |
|ProducerInstances |string | 動的 |DCA プロデューサー インスタンスのリスト。 |

## <a name="dnsservice"></a>DnsService
| **パラメーター** | **使用できる値** |**アップグレード ポリシー**| **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|EnablePartitionedQuery|ブール値、既定値は FALSE|静的|パーティション分割されたサービスに対する DNS クエリのサポートを有効にするためのフラグ。 この機能は既定でオフになっています。 詳細については、[Service Fabric の DNS サービス](service-fabric-dnsservice.md)に関するページを参照してください。|
|InstanceCount|int、既定値は -1|静的|既定値は -1。つまり、DnsService はすべてのノードで実行されています。 DnsService は既知のポート 53 を使用するので、OneBox はこの値を 1 に設定する必要があります。そのため、同じマシン上に複数のインスタンスを持つことはできません。|
|IsEnabled|ブール値、既定値は FALSE|静的|DnsService を有効または無効にします。 DnsService は既定で無効になっています。有効にするには、この構成を設定する必要があります。 |
|PartitionPrefix|string、既定値は "--"|静的|パーティション分割されたサービスの DNS クエリのパーティション プレフィックス文字列値を制御します。 値には次の条件があります。 <ul><li>DNS クエリの一部なので、RFC に準拠している必要があります。</li><li>ドット '.' は DNS サフィックスの動作を妨げるため、使用しないでください。</li><li>長さの上限は 5 文字です。</li><li>空の文字列にすることはできません。</li><li>PartitionPrefix 設定がオーバーライドされている場合は、PartitionSuffix もオーバーライドされる必要があります。その逆も同様です。</li></ul>詳細については、「[Azure Service Fabric の DNS サービス](service-fabric-dnsservice.md)」を参照してください。|
|PartitionSuffix|string、既定値は ""|静的|パーティション分割されたサービスの DNS クエリのパーティション サフィックス文字列値を制御します。値には次の条件があります。 <ul><li>DNS クエリの一部なので、RFC に準拠している必要があります。</li><li>ドット '.' は DNS サフィックスの動作を妨げるため、使用しないでください。</li><li>長さの上限は 5 文字です。</li><li>PartitionPrefix 設定がオーバーライドされている場合は、PartitionSuffix もオーバーライドされる必要があります。その逆も同様です。</li></ul>詳細については、「[Azure Service Fabric の DNS サービス](service-fabric-dnsservice.md)」を参照してください。 |

## <a name="eventstoreservice"></a>EventStoreService

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int、既定値は 0|静的|EventStore サービスの MinReplicaSetSize |
|PlacementConstraints|string、既定値は ""|静的|  EventStore サービスの PlacementConstraints |
|TargetReplicaSetSize|int、既定値は 0|静的| EventStore サービスの TargetReplicaSetSize |

## <a name="fabricclient"></a>FabricClient

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |時間 (秒単位)、既定値は 2 |動的|timespan を秒単位で指定します。 クライアントがゲートウェイへの接続を開こうとするときの接続タイムアウト間隔。|
|HealthOperationTimeout |時間 (秒単位)、既定値は 120 |動的|timespan を秒単位で指定します。 Health Manager に送信されるレポート メッセージのタイムアウト。 |
|HealthReportRetrySendInterval |時間 (秒単位)、既定値は 30、最小値は 1 |動的|timespan を秒単位で指定します。 レポート コンポーネントが、累積した正常性レポートを Health Manager に再送信する間隔。 |
|HealthReportSendInterval |秒単位。既定値は 30 |動的|timespan を秒単位で指定します。 レポート コンポーネントが、累積した正常性レポートを Health Manager に送信する間隔。 |
|KeepAliveIntervalInSeconds |int、既定値は 20 |静的|FabricClient トランスポートが、ゲートウェイにキープアライブ メッセージを送信する間隔。 0 の場合、keepAlive は無効になります。 正の数を指定する必要があります。 |
|MaxFileSenderThreads |uint、既定値は 10 |静的|並列転送されるファイルの最大数。 |
|NodeAddresses |string、既定値は "" |静的|ネーム サービスとの通信に使用できるさまざまなノード上のアドレス (接続文字列) のコレクション。 最初に、クライアントはアドレスのいずれかをランダムに選択して接続します。 複数の接続文字列が指定されている場合、通信エラーやタイムアウト エラーによって接続が失敗すると、クライアントは次のアドレスに順次切り替えて使用します。 再試行セマンティクスの詳細については、ネーム サービス アドレスの再試行のセクションを参照してください。 |
|PartitionLocationCacheLimit |int、既定値は 100000 |静的|サービスの解決用にキャッシュされるパーティションの数 (無制限にするには、0 に設定します)。 |
|RetryBackoffInterval |時間 (秒単位)、既定値は 3 |動的|timespan を秒単位で指定します。 操作を再試行するまでのバックオフ間隔。 |
|ServiceChangePollInterval |時間 (秒単位)、既定値は 120 |動的|timespan を秒単位で指定します。 クライアントからゲートウェイに登録済みのサービス変更通知をコールバックするための、サービスの変更の連続するポーリングの間隔。 |

## <a name="fabrichost"></a>FabricHost

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |int、既定値は 10 |動的|システムが失敗したアクティブ化を再試行する最大回数。この回数に達すると、再試行が停止されます。 |
|ActivationMaxRetryInterval |秒単位。既定値は 300 |動的|timespan を秒単位で指定します。 アクティブ化の最大再試行間隔。 連続して失敗するたびに、再試行間隔が Min(ActivationMaxRetryInterval; Continuous Failure Count * ActivationRetryBackoffInterval) として計算されます。 |
|ActivationRetryBackoffInterval |時間 (秒単位)、既定値は 5 |動的|timespan を秒単位で指定します。 アクティブ化失敗時のバックオフ間隔。アクティブ化が連続して失敗するたびに、MaxActivationFailureCount で指定された回数までアクティブ化が再試行されます。 各試行の再試行間隔は、継続的なアクティブ化失敗とアクティブ化バックオフ間隔の積になります。 |
|EnableRestartManagement |ブール値、既定値は false |動的|サーバーの再起動を有効します。 |
|EnableServiceFabricAutomaticUpdates |ブール値、既定値は false |動的|Windows Update によるファブリックの自動更新を有効にします。 |
|EnableServiceFabricBaseUpgrade |ブール値、既定値は false |動的|サーバーの基本的な更新を有効にします。 |
|StartTimeout |秒単位。既定値は 300 |動的|timespan を秒単位で指定します。 fabricactivationmanager の起動のタイムアウト。 |
|StopTimeout |秒単位。既定値は 300 |動的|timespan を秒単位で指定します。 ホストされるサービスのアクティブ化、非アクティブ化、アップグレードのタイムアウト。 |

## <a name="fabricnode"></a>FabricNode

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |string、既定値は "FindByThumbprint" |動的|ClientAuthX509StoreName で指定されたストア内での証明書の検索方法を示します。サポートされる値:FindByThumbprint、FindBySubjectName。 |
|ClientAuthX509FindValue |string、既定値は "" | 動的|既定の管理者ロールの FabricClient の証明書を検索する際に使用する検索フィルター値。 |
|ClientAuthX509FindValueSecondary |string、既定値は "" |動的|既定の管理者ロールの FabricClient の証明書を検索する際に使用する検索フィルター値。 |
|ClientAuthX509StoreName |string、既定値は "My" |動的|既定の管理者ロールの FabricClient の証明書を格納する X.509 証明書ストアの名前。 |
|ClusterX509FindType |string、既定値は "FindByThumbprint" |動的|ClusterX509StoreName で指定されたストア内でのクラスター証明書の検索方法を示します。サポートされる値は"FindByThumbprint" と "FindBySubjectName" です。"FindBySubjectName" を指定すると、一致するものが複数ある場合に、有効期限が最も長いものが使用されます。 |
|ClusterX509FindValue |string、既定値は "" |動的|クラスター証明書の検索に使用する検索フィルター値。 |
|ClusterX509FindValueSecondary |string、既定値は "" |動的|クラスター証明書の検索に使用する検索フィルター値。 |
|ClusterX509StoreName |string、既定値は "My" |動的|クラスター内通信をセキュリティで保護するためのクラスター証明書を格納する X.509 証明書ストアの名前。 |
|EndApplicationPortRange |int、既定値は 0 |静的|ホスティング サブシステムによって管理されるアプリケーション ポートの終了 (この値を含まない)。 Hosting で EndpointFilteringEnabled が true の場合は必須です。 |
|ServerAuthX509FindType |string、既定値は "FindByThumbprint" |動的|ServerAuthX509StoreName で指定されたストア内でのサーバー証明書の検索方法を示します。サポートされる値:FindByThumbprint、FindBySubjectName。 |
|ServerAuthX509FindValue |string、既定値は "" |動的|サーバー証明書の検索に使用する検索フィルター値。 |
|ServerAuthX509FindValueSecondary |string、既定値は "" |動的|サーバー証明書の検索に使用する検索フィルター値。 |
|ServerAuthX509StoreName |string、既定値は "My" |動的|エントリ サービスのサーバー証明書を格納する X.509 証明書ストアの名前。 |
|StartApplicationPortRange |int、既定値は 0 |静的|ホスティング サブシステムによって管理されるアプリケーション ポートの開始。 Hosting で EndpointFilteringEnabled が true の場合は必須です。 |
|StateTraceInterval |秒単位。既定値は 300 |静的|timespan を秒単位で指定します。 FM/FMM で各ノードおよび実行中のノードの状態をトレースする間隔。 |
|UserRoleClientX509FindType |string、既定値は "FindByThumbprint" |動的|UserRoleClientX509StoreName で指定されたストア内での証明書の検索方法を示します。サポートされる値:FindByThumbprint、FindBySubjectName。 |
|UserRoleClientX509FindValue |string、既定値は "" |動的|既定のユーザー ロールの FabricClient の証明書を検索する際に使用する検索フィルター値。 |
|UserRoleClientX509FindValueSecondary |string、既定値は "" |動的|既定のユーザー ロールの FabricClient の証明書を検索する際に使用する検索フィルター値。 |
|UserRoleClientX509StoreName |string、既定値は "My" |動的|既定のユーザー ロールの FabricClient の証明書を格納する X.509 証明書ストアの名前。 |

## <a name="failovermanager"></a>FailoverManager

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|BuildReplicaTimeLimit|TimeSpan、既定値は Common::TimeSpan::FromSeconds(3600)|動的|timespan を秒単位で指定します。 ステートフル レプリカ作成の制限時間。この時間が経過した後、警告の正常性レポートが開始されます |
|ClusterPauseThreshold|int、既定値は 1|動的|システム内のノードの数がこの値を下回ると、配置、負荷分散、およびフェールオーバーが停止します。 |
|CreateInstanceTimeLimit|TimeSpan、既定値は Common::TimeSpan::FromSeconds(300)|動的|timespan を秒単位で指定します。 ステートレス インスタンス作成の制限時間。この時間が経過した後、警告の正常性レポートが開始されます |
|ExpectedClusterSize|int、既定値は 1|動的|クラスターが最初に起動するとき、FM は、ここで指定した数のノードの起動が報告されてから、他のサービス (名前付けなどのシステム サービスを含む) の提供を開始します。 この値を大きくすると、クラスターの起動に時間がかかりますが、ノードが早い段階で過負荷になるのを防ぐことができます。また、オンラインのノードが増えたときに必要になる追加の移動も回避できます。 この値は、一般的に、初期クラスター サイズの何分の一かに設定する必要があります。 |
|ExpectedNodeDeactivationDuration|TimeSpan、既定値は Common::TimeSpan::FromSeconds(60.0 * 30)|動的|timespan を秒単位で指定します。 これは、ノードの非アクティブ化の予想完了時間です。 |
|ExpectedNodeFabricUpgradeDuration|TimeSpan、既定値は Common::TimeSpan::FromSeconds(60.0 * 30)|動的|timespan を秒単位で指定します。 これは、Windows Fabric アップグレード中の、ノードの予想アップグレード時間です。 |
|ExpectedReplicaUpgradeDuration|TimeSpan、既定値は Common::TimeSpan::FromSeconds(60.0 * 30)|動的|timespan を秒単位で指定します。 これは、アプリケーション アップグレード中の、ノード上のすべてのレプリカの予想アップグレード時間です。 |
|IsSingletonReplicaMoveAllowedDuringUpgrade|ブール値、既定値は TRUE|動的|true に設定すると、ターゲット レプリカ セット サイズが 1 のレプリカに対して、アップグレード中の移動が許可されます。 |
|MinReplicaSetSize|int、既定値は 3|禁止|これは FM のレプリカ セットの最小サイズです。 アクティブな FM レプリカ数がこの値を下回ると、FM は、少なくともレプリカの最小数が復旧するまでは、クラスターへの変更を拒否します |
|PlacementConstraints|string、既定値は ""|禁止|フェールオーバー マネージャー レプリカの任意の配置の制約 |
|PlacementTimeLimit|TimeSpan、既定値は Common::TimeSpan::FromSeconds(600)|動的|timespan を秒単位で指定します。 ターゲット レプリカ数到達の制限時間。この時間が経過した後、警告の正常性レポートが開始されます |
|QuorumLossWaitDuration |時間 (秒単位)、既定値は MaxValue |動的|timespan を秒単位で指定します。 パーティションがクォーラム損失の状態であることを許可する最長期間。 この期間を過ぎてもパーティションが引き続きクォーラム損失の状態である場合、ダウンしたレプリカは失われたと見なされ、パーティションをクォーラム損失から回復させます。 この場合、データ損失が発生する可能性があります。 |
|ReconfigurationTimeLimit|TimeSpan、既定値は Common::TimeSpan::FromSeconds(300)|動的|timespan を秒単位で指定します。 再構成の制限時間。この時間が経過した後、警告の正常性レポートが開始されます |
|ReplicaRestartWaitDuration|TimeSpan、既定値は Common::TimeSpan::FromSeconds(60.0 * 30)|禁止|timespan を秒単位で指定します。 これは FMService の ReplicaRestartWaitDuration です |
|StandByReplicaKeepDuration|Timespan、既定値は Common::TimeSpan::FromSeconds(3600.0 * 24 * 7)|禁止|timespan を秒単位で指定します。 これは FMService の StandByReplicaKeepDuration です |
|TargetReplicaSetSize|int、既定値は 7|禁止|これは、Windows Fabric によって保持されるターゲット FM レプリカ数です。 値が大きいほど、小さなパフォーマンスのトレードオフで FM データの信頼性が向上します。 |
|UserMaxStandByReplicaCount |int、既定値は 1 |動的|ユーザー サービス用にシステムが保持するスタンバイ レプリカの既定の最大数。 |
|UserReplicaRestartWaitDuration |時間 (秒単位)、既定値は 60.0 * 30 |動的|timespan を秒単位で指定します。 永続化されたレプリカがダウンすると、Windows Fabric はレプリカが復帰するまでこの期間待機します。この期間が過ぎると、(状態のコピーを必要とする) 新しい代替レプリカが作成されます。 |
|UserStandByReplicaKeepDuration |秒単位。既定値は 3600.0 * 24 * 7 |動的|timespan を秒単位で指定します。 永続化されたレプリカがダウン状態から復帰したときに、既に置き換えられている場合があります。 このタイマーは、FM がスタンバイ レプリカを破棄するまでに保持する時間を決定します。 |

## <a name="faultanalysisservice"></a>FaultAnalysisService

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | int、既定値は 604800 |静的| 終了状態のアクションを保持するおおよその期間。 クリーンアップの処理は StoredActionCleanupIntervalInSeconds で指定された間隔でのみ実行されるため、このパラメーターは StoredActionCleanupIntervalInSeconds にも左右されます。 604800 は 7 日間です。 |
|DataLossCheckPollIntervalInSeconds|int、既定値は 5|静的|これは、システムがデータ損失の発生を待機している際にチェックを実行する間隔です。 内部イテレーションあたりのデータ損失数のチェック回数は、DataLossCheckWaitDurationInSeconds をこの数値で割った値です。 |
|DataLossCheckWaitDurationInSeconds|int、既定値は 25|静的|データ損失が発生するまでにシステムが待機する時間の合計 (秒単位)。 StartPartitionDataLossAsync() API が呼び出されたときに内部的に使用されます。 |
|MinReplicaSetSize |int、既定値は 0 |静的|FaultAnalysisService の MinReplicaSetSize。 |
|PlacementConstraints | string、既定値は ""|静的| FaultAnalysisService の PlacementConstraints。 |
|QuorumLossWaitDuration | 時間 (秒単位)、既定値は MaxValue |静的|timespan を秒単位で指定します。 FaultAnalysisService の QuorumLossWaitDuration。 |
|ReplicaDropWaitDurationInSeconds|int、既定値は 600|静的|このパラメーターは、データ損失 API が呼び出されたときに使用されます。 レプリカの削除がシステムで内部的に呼び出された後、レプリカが削除されるまでのシステムの待機時間を制御します。 |
|ReplicaRestartWaitDuration |時間 (秒単位)、既定値は 60 分|静的|timespan を秒単位で指定します。 FaultAnalysisService の ReplicaRestartWaitDuration。 |
|StandByReplicaKeepDuration| 時間 (秒単位)、既定値は (60*24*7) 分 |静的|timespan を秒単位で指定します。 FaultAnalysisService の StandByReplicaKeepDuration。 |
|StoredActionCleanupIntervalInSeconds | int、既定値は 3600 |静的|ストアをクリーンアップする頻度。 終了状態のアクションと、少なくとも CompletedActionKeepDurationInSeconds で指定された期間よりも前に完了したアクションだけが削除されます。 |
|StoredChaosEventCleanupIntervalInSeconds | int、既定値は 3600 |静的|クリーンアップのためにストアを監査する頻度。イベント数が 30000 を超えると、クリーンアップが開始されます。 |
|TargetReplicaSetSize |int、既定値は 0 |静的|NOT_PLATFORM_UNIX_START FaultAnalysisService の TargetReplicaSetSize。 |

## <a name="federation"></a>フェデレーション

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|LeaseDuration |秒単位。既定値は 30 |動的|ノードとその近隣ノードの間のリース期間。 |
|LeaseDurationAcrossFaultDomain |秒単位。既定値は 30 |動的|障害ドメイン全体におけるノードとその近隣ノードの間のリース期間。 |

## <a name="filestoreservice"></a>FileStoreService

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|AcceptChunkUpload|ブール値、既定値は TRUE|動的|ファイル ストア サービスが、コピー アプリケーション パッケージ中に、チャンク ベースのファイル アップロードを受け入れるかどうかを決定する構成です。 |
|AnonymousAccessEnabled | ブール値、既定値は true |静的|FileStoreService 共有への匿名アクセスを有効または無効にします。 |
|CommonName1Ntlmx509CommonName|string、既定値は ""|静的| NTLM 認証を使用するときに、CommonName1NtlmPasswordSecret で HMAC を生成するために使用する X509 証明書の共通名 |
|CommonName1Ntlmx509StoreLocation|string、既定値は "LocalMachine"|静的|NTLM 認証を使用するときに、CommonName1NtlmPasswordSecret で HMAC を生成するために使用する X509 証明書のストアの場所 |
|CommonName1Ntlmx509StoreName|string、既定値は "MY"| 静的|NTLM 認証を使用するときに、CommonName1NtlmPasswordSecret で HMAC を生成するために使用する X509 証明書のストアの名前 |
|CommonName2Ntlmx509CommonName|string、既定値は ""|静的|NTLM 認証を使用するときに、CommonName2NtlmPasswordSecret で HMAC を生成するために使用する X509 証明書の共通名 |
|CommonName2Ntlmx509StoreLocation|string、既定値は "LocalMachine"| 静的|NTLM 認証を使用するときに、CommonName2NtlmPasswordSecret で HMAC を生成するために使用する X509 証明書のストアの場所 |
|CommonName2Ntlmx509StoreName|string、既定値は "MY"|静的| NTLM 認証を使用するときに、CommonName2NtlmPasswordSecret で HMAC を生成するために使用する X509 証明書のストアの名前 |
|CommonNameNtlmPasswordSecret|SecureString、既定値は Common::SecureString("")| 静的|NTLM 認証を使用するときに、生成された同じパスワードに対するシードとして使用するパスワード シークレット |
|GenerateV1CommonNameAccount| ブール値、既定値は TRUE|静的|ユーザー名 V1 生成アルゴリズムを使用したアカウントを生成するかどうかを指定します。 Service Fabric バージョン 6.1 以降では、常に v2 生成を使用したアカウントが作成されます。 V1 アカウントは、V2 生成をサポートしていないバージョン (6.1 以前) からのアップグレード (またはそれらのバージョンへのアップグレード) に必要となります。|
|MaxCopyOperationThreads | uint、既定値は 0 |動的| セカンダリがプライマリからコピーできる並列ファイルの最大数。 '0' == コア数です。 |
|MaxFileOperationThreads | uint、既定値は 100 |静的| プライマリで FileOperations (コピー/移動) を実行する際に使用できる並列スレッドの最大数。 '0' == コア数です。 |
|MaxRequestProcessingThreads | uint、既定値は 200 |静的|プライマリで要求を処理する際に使用できる並列スレッドの最大数。 '0' == コア数です。 |
|MaxSecondaryFileCopyFailureThreshold | uint、既定値は 25|動的|セカンダリでのファイル コピーの再試行の最大回数。この回数に達すると、再試行が停止されます。 |
|MaxStoreOperations | uint、既定値は 4096 |静的|プライマリで許可される並列ストア トランザクション操作の最大数。 '0' == コア数です。 |
|NamingOperationTimeout |秒単位。既定値は 60 |動的|timespan を秒単位で指定します。 名前付け操作を実行する際のタイムアウト。 |
|PrimaryAccountNTLMPasswordSecret | SecureString、既定値は空 |静的| NTLM 認証を使用するときに、生成された同じパスワードに対するシードとして使用するパスワード シークレット。 |
|PrimaryAccountNTLMX509StoreLocation | string、既定値は "LocalMachine"|静的| NTLM 認証を使用するときに、PrimaryAccountNTLMPasswordSecret で HMAC を生成するために使用する X509 証明書のストアの場所。 |
|PrimaryAccountNTLMX509StoreName | string、既定値は "MY"|静的| NTLM 認証を使用するときに、PrimaryAccountNTLMPasswordSecret で HMAC を生成するために使用する X509 証明書のストアの名前。 |
|PrimaryAccountNTLMX509Thumbprint | string、既定値は ""|静的|NTLM 認証を使用するときに、PrimaryAccountNTLMPasswordSecret で HMAC を生成するために使用する X509 証明書の拇印。 |
|PrimaryAccountType | string、既定値は "" |静的|FileStoreService 共有の ACL の、プリンシパルのプライマリ AccountType。 |
|PrimaryAccountUserName | string、既定値は "" |静的|FileStoreService 共有の ACL の、プリンシパルのプライマリ アカウントの Username。 |
|PrimaryAccountUserPassword | SecureString、既定値は空 |静的|FileStoreService 共有の ACL の、プリンシパルのプライマリ アカウントのパスワード。 |
|QueryOperationTimeout | 秒単位。既定値は 60 |動的|timespan を秒単位で指定します。 クエリ操作を実行する際のタイムアウト。 |
|SecondaryAccountNTLMPasswordSecret | SecureString、既定値は空 |静的| NTLM 認証を使用するときに、生成された同じパスワードに対するシードとして使用するパスワード シークレット。 |
|SecondaryAccountNTLMX509StoreLocation | string、既定値は "LocalMachine" |静的|NTLM 認証を使用するときに、SecondaryAccountNTLMPasswordSecret で HMAC を生成するために使用する X509 証明書のストアの場所。 |
|SecondaryAccountNTLMX509StoreName | string、既定値は "MY" |静的|NTLM 認証を使用するときに、SecondaryAccountNTLMPasswordSecret で HMAC を生成するために使用する X509 証明書のストアの名前。 |
|SecondaryAccountNTLMX509Thumbprint | string、既定値は ""| 静的|NTLM 認証を使用するときに、SecondaryAccountNTLMPasswordSecret で HMAC を生成するために使用する X509 証明書の拇印。 |
|SecondaryAccountType | string、既定値は ""|静的| FileStoreService 共有の ACL の、プリンシパルのセカンダリ AccountType。 |
|SecondaryAccountUserName | string、既定値は ""| 静的|FileStoreService 共有の ACL の、プリンシパルのセカンダリ アカウントの Username。 |
|SecondaryAccountUserPassword | SecureString、既定値は空 |静的|FileStoreService 共有の ACL の、プリンシパルのセカンダリ アカウントのパスワード。 |
|SecondaryFileCopyRetryDelayMilliseconds|uint、既定値は 500|動的|ファイル コピー再試行の遅延 (ミリ秒単位)。|
|UseChunkContentInTransportMessage|ブール値、既定値は TRUE|動的|v6.4 で導入されたアップロード プロトコルの新しいバージョンを使用するためのフラグ。 このプロトコル バージョンでは、ファイルをイメージ ストアにアップロードするために、Service Fabric トランスポートが使用されます。これは、以前のバージョンで使用されていた SMB プロトコルよりも高いパフォーマンスを提供します。 |

## <a name="healthmanager"></a>HealthManager

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|EnableApplicationTypeHealthEvaluation |ブール値、既定値は false |静的|クラスターの正常性評価ポリシー: アプリケーションの種類ごとの正常性評価を有効にします。 |
|MaxSuggestedNumberOfEntityHealthReports|int、既定値は 500 |動的|ウォッチドッグの正常性報告ロジックに関する問題を発生させる前にエンティティが持つことができる正常性レポートの最大数。 各正常性エンティティは、比較的少ない数の正常性レポートを持つと想定されます。 レポートの数がこの数を上回った場合、ウォッチドッグの実装に問題がある可能性があります。 レポートの数が多すぎるエンティティは、エンティティの評価時に警告正常性レポート全体でフラグが設定されます。 |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|ConsiderWarningAsError |ブール値、既定値は false |静的|クラスターの正常性評価ポリシー: 警告をエラーとして処理します。 |
|MaxPercentUnhealthyApplications | int、既定値は 0 |静的|クラスターの正常性評価ポリシー: 異常なアプリケーションの最大許容パーセンテージ。この値を超えると、クラスターは異常と見なされます。 |
|MaxPercentUnhealthyNodes | int、既定値は 0 |静的|クラスターの正常性評価ポリシー: 異常なノードの最大許容パーセンテージ。この値を超えると、クラスターは異常と見なされます。 |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|int、既定値は 10|静的|クラスターのアップグレードの正常性評価ポリシー: 異常な差分ノードの最大許容パーセンテージ。この値を超えると、クラスターは異常と見なされます |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int、既定値は 15|静的|クラスターのアップグレードの正常性評価ポリシー: アップグレード ドメインの異常な差分ノードの最大許容パーセンテージ。この値を超えると、クラスターは異常と見なされます |

## <a name="hosting"></a>Hosting

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |整数。既定値は 10 |動的|失敗したアクティブ化の再試行が行われる回数。再試行は、この回数だけ実行されてから停止します |
|ActivationMaxRetryInterval |秒単位。既定値は 300 |動的|アクティブ化が連続して失敗するたびに、ActivationMaxFailureCount で指定された回数までアクティブ化が再試行されます。 ActivationMaxRetryInterval は、アクティブ化が失敗してから再試行されるまでの待機時間を指定します |
|ActivationRetryBackoffInterval |秒単位。既定値は 5 |動的|アクティブ化失敗のバックオフ間隔。アクティブ化が連続して失敗するたびに、MaxActivationFailureCount で指定された回数までアクティブ化が再試行されます。 各試行の再試行間隔は、継続的なアクティブ化失敗とアクティブ化バックオフ間隔の積になります。 |
|ActivationTimeout| TimeSpan、既定値は Common::TimeSpan::FromSeconds(180)|動的| timespan を秒単位で指定します。 アプリケーションのアクティブ化、非アクティブ化、アップグレードのタイムアウト。 |
|ApplicationHostCloseTimeout| TimeSpan、既定値は Common::TimeSpan::FromSeconds(120)|動的| timespan を秒単位で指定します。 セルフ アクティブ化されたプロセスで Fabric の終了が検出された場合、FabricRuntime は、ユーザーのホスト (applicationhost) プロセスですべてのレプリカを閉じます。 これは、閉じる操作のタイムアウトです。 |
|ApplicationUpgradeTimeout| TimeSpan、既定値は Common::TimeSpan::FromSeconds(360)|動的| timespan を秒単位で指定します。 アプリケーション アップグレードのタイムアウト。 タイムアウトが "ActivationTimeout" を下回る場合、デプロイ機能は失敗します。 |
|ContainerServiceArguments|string、既定値は "-H localhost:2375 -H npipe://"|静的|Service Fabric (SF) が Docker デーモンを管理します (Win10 のような Windows クライアント コンピューターを除く)。 この構成により、ユーザーは Docker デーモンの起動時に渡す必要があるカスタム引数を指定できます。 カスタム引数が指定された場合、Service Fabric は、他の引数を一切 Docker エンジンに渡しませんが、'--pidfile' 引数は例外です。 そのため、ユーザーはカスタム引数の一部として '--pidfile' 引数を指定することはできません。 また、Service Fabric が Docker デーモンと通信できるようにするために、Docker デーモンに既定の名前付きパイプ (Windows の場合) または UNIX ドメイン ソケット (Linux の場合) でリッスンさせるようにカスタム引数で指定する必要があります。|
|ContainerServiceLogFileMaxSizeInKb|int、既定値は 32768|静的|Docker コンテナーで生成されるログ ファイルの最大ファイル サイズ。  Windows のみ。|
|ContainerImageDownloadTimeout|int、秒数、既定値は 1,200 (20 分)|動的|イメージのダウンロードがタイムアウトするまでの秒数。|
|ContainerImagesToSkip|string、イメージ名を縦線で区切ります。既定値は ""|静的|削除していはいけない 1 つまたは複数のコンテナー イメージの名前。  PruneContainerImages パラメーターと共に使用します。|
|ContainerServiceLogFileNamePrefix|string、既定値は "sfcontainerlogs"|静的|Docker コンテナーで生成されるログ ファイルのファイル名プレフィックス。  Windows のみ。|
|ContainerServiceLogFileRetentionCount|int、既定値は 10|静的|ログ ファイルを上書きする前に Docker コンテナーによって生成されたログ ファイルの数。  Windows のみ。|
|CreateFabricRuntimeTimeout|TimeSpan、既定値は Common::TimeSpan::FromSeconds(120)|動的| timespan を秒単位で指定します。 同期 FabricCreateRuntime 呼び出しのタイムアウト値 |
|DefaultContainerRepositoryAccountName|string、既定値は ""|静的|ApplicationManifest.xml に指定されている資格情報の代わりに使用される既定の資格情報 |
|DefaultContainerRepositoryPassword|string、既定値は ""|静的|ApplicationManifest.xml に指定されている資格情報の代わりに使用される既定のパスワード資格情報|
|DefaultContainerRepositoryPasswordType|string、既定値は ""|静的|空の文字列でない場合、値は "Encrypted" または "SecretsStoreRef" です。|
|DeploymentMaxFailureCount|int、既定値は 20| 動的|ノードへのアプリケーションのデプロイは、DeploymentMaxFailureCount 回、再試行された後に失敗します。| 
|DeploymentMaxRetryInterval| TimeSpan、既定値は Common::TimeSpan::FromSeconds(3600)|動的| timespan を秒単位で指定します。 デプロイの最大再試行間隔。 連続して失敗するたびに、再試行間隔が Min(DeploymentMaxRetryInterval; Continuous Failure Count * DeploymentRetryBackoffInterval) として計算されます |
|DeploymentRetryBackoffInterval| TimeSpan、既定値は Common::TimeSpan::FromSeconds(10)|動的|timespan を秒単位で指定します。 デプロイ エラーのバックオフ間隔。 継続的なデプロイ エラーのたびに、システムによってデプロイが最大 MaxDeploymentFailureCount 回、再試行されます。 再試行間隔は、継続的なデプロイ エラーとデプロイ バックオフ間隔の積です。 |
|DisableContainers|ブール値、既定値は FALSE|静的|コンテナーを無効にするための構成 - 使用されなくなった構成である DisableContainerServiceStartOnContainerActivatorOpen の代わりに使用します |
|DisableDockerRequestRetry|ブール値、既定値は FALSE |動的| 既定では、SF は、送信される各 http 要求のタイムアウトを "DockerRequestTimeout" として DD (docker デーモン) と通信します。 DD がこの期間内に応答しない場合、SF は、最上位レベルの操作にまだ残り時間があれば要求を再送信します。  hyperv コンテナーと共に使用します。DD がコンテナーを起動または非アクティブ化するのに時間がかかることがあります。 そのような場合、DD 要求が SF パースペクティブからタイムアウトし、SF は操作を再試行します。 これは DD にさらに圧力をかけるように見えることがあります。 この構成により、この再試行が無効になり、DD が応答するまで待機します。 |
|EnableActivateNoWindow| ブール値、既定値は FALSE|動的| アクティブ化されたプロセスは、コンソールを使用せずに、バックグラウンドで作成されます。 |
|EnableContainerServiceDebugMode|ブール値、既定値は TRUE|静的|Docker コンテナーのログを有効または無効にします。  Windows のみ。|
|EnableDockerHealthCheckIntegration|ブール値、既定値は TRUE|静的|Docker HEALTHCHECK イベントと Service Fabric システム正常性レポートの統合を有効にする |
|EnableProcessDebugging|ブール値、既定値は FALSE|動的| デバッガーでのアプリケーション ホストの起動を有効にします |
|EndpointProviderEnabled| ブール値、既定値は FALSE|静的| Fabric によるエンドポイント リソースの管理を有効にします。 FabricNode でのアプリケーション ポート範囲の開始と終了を指定する必要があります。 |
|FabricContainerAppsEnabled| ブール値、既定値は FALSE|静的| |
|FirewallPolicyEnabled|ブール値、既定値は FALSE|静的| ServiceManifest で明示的に指定されているポートを使用して、エンドポイント リソースのファイアウォール ポートを開くことができるようにします |
|GetCodePackageActivationContextTimeout|TimeSpan、既定値は Common::TimeSpan::FromSeconds(120)|動的|timespan を秒単位で指定します。 CodePackageActivationContext 呼び出しのタイムアウト値。 これはアドホック サービスには適用されません。 |
|GovernOnlyMainMemoryForProcesses|ブール値、既定値は FALSE|静的|リソース管理の既定の動作では、プロセスが使用する合計メモリ量 (RAM + スワップ) に対して、MemoryInMB で指定された制限を設定します。 制限を超えた場合、プロセスは OutOfMemory 例外を受け取ります。 このパラメーターが true に設定されている場合、制限は、プロセスが使用する RAM メモリの量にのみ適用されます。 この制限を超え、この設定が true である場合は、OS によってメイン メモリがディスクにスワップされます。 |
|IPProviderEnabled|ブール値、既定値は FALSE|静的|IP アドレスの管理を有効にします。 |
|IsDefaultContainerRepositoryPasswordEncrypted|ブール値、既定値は FALSE|静的|DefaultContainerRepositoryPassword を暗号化するかどうか。|
|LinuxExternalExecutablePath|string、既定値は "/usr/bin/" |静的|ノード上の外部の実行可能なコマンドのプライマリ ディレクトリ。|
|NTLMAuthenticationEnabled|ブール値、既定値は FALSE|静的| 他のユーザーとして実行されているコード パッケージによる NTLM 使用のサポートを有効にします。これによりマシン間のプロセスが安全に通信できます。 |
|NTLMAuthenticationPasswordSecret|SecureString、既定値は Common::SecureString("")|静的|NTLM ユーザーのパスワード生成に使用される暗号化されたハッシュです。 NTLMAuthenticationEnabled が true の場合、設定する必要があります。 デプロイ担当者によって検証されます。 |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan、既定値は Common::TimeSpan::FromMinutes(3)|動的|timespan を秒単位で指定します。 環境固有の設定。FileStoreService NTLM 構成に使用する新しい証明書が、この間隔でホスティングによって定期的にスキャンされます。 |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan、既定値は Common::TimeSpan::FromMinutes(4)|動的| timespan を秒単位で指定します。 証明書共通名を使用して NTLM ユーザーを構成するためのタイムアウト。 NTLM ユーザーは、FileStoreService 共有に必要です。 |
|PruneContainerImages|ブール値、既定値は FALSE|動的| 使用されていないアプリケーション コンテナー イメージをノードから削除します。 ApplicationType が Service Fabric クラスターから登録解除されると、このアプリケーションによって使用されていたコンテナー イメージは、Service Fabric によってダウンロードされたノード上で削除されます。 この削除操作は 1 時間ごとに実行されるため、クラスターからイメージを削除するのに最大で 1 時間かかることがあります (さらに、イメージを取り除く時間も必要です)。<br>アプリケーションに関連しないイメージが Service Fabric によってダウンロードまたは削除されることはありません。  手動またはその他の方法でダウンロードされた無関係のイメージは、明示的に削除する必要があります。<br>削除してはいけないイメージは、ContainerImagesToSkip パラメーターで指定できます。| 
|RegisterCodePackageHostTimeout|TimeSpan、既定値は Common::TimeSpan::FromSeconds(120)|動的| timespan を秒単位で指定します。 FabricRegisterCodePackageHost 同期呼び出しのタイムアウト値。 これは、FWP のようなマルチコード パッケージ アプリケーション ホストにのみ適用されます |
|RequestTimeout|TimeSpan、既定値は Common::TimeSpan::FromSeconds(30)|動的| timespan を秒単位で指定します。 これは、ファクトリ登録、ランタイム登録など、さまざまなホスティング関連操作について、ユーザーのアプリケーション ホストと Fabric プロセスの間の通信タイムアウトを表します。 |
|RunAsPolicyEnabled| ブール値、既定値は FALSE|静的| ローカル ユーザーとしてのコード パッケージの実行を有効にします。ただし、ファブリック プロセスが実行されているユーザーを除きます。 このポリシーを有効にするには、Fabric が、システムとして、または SeAssignPrimaryTokenPrivilege を持つユーザーとして実行されている必要があります。 |
|ServiceFactoryRegistrationTimeout| TimeSpan、既定値は Common::TimeSpan::FromSeconds(120)|動的|timespan を秒単位で指定します。 同期 Register(Stateless/Stateful)ServiceFactory 呼び出しのタイムアウト値 |
|ServiceTypeDisableFailureThreshold |整数。既定値は 1 |動的|失敗回数のしきい値。この回数を失敗した後、そのノード上のサービスの種類を無効にして、別のノードへの配置を試行するよう FailoverManager (FM) に通知されます。 |
|ServiceTypeDisableGraceInterval|TimeSpan、既定値は Common::TimeSpan::FromSeconds(30)|動的|timespan を秒単位で指定します。 この時間が経過した後、サービスの種類を無効にできます |
|ServiceTypeRegistrationTimeout |秒単位。既定値は 300 |動的|Fabric に登録する ServiceType の最大許容時間 |
|UseContainerServiceArguments|ブール値、既定値は TRUE|静的|この構成は、ホスティングに Docker デーモンに (構成 ContainerServiceArguments で指定された) 引数を渡すことをスキップするように指示します。|

## <a name="httpgateway"></a>HttpGateway

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|ActiveListeners |uint、既定値は 50 |静的| HTTP サーバー キューに送信する読み取りの数。 HttpGateway が対応できる同時要求の数を制御します。 |
|HttpGatewayHealthReportSendInterval |秒単位。既定値は 30 |静的|timespan を秒単位で指定します。 HTTP ゲートウェイが、累積した正常性レポートを Health Manager に送信する間隔。 |
|HttpStrictTransportSecurityHeader|string、既定値は ""|動的| HttpGateway によって送信されるすべての応答に含められる HTTP Strict Transport Security ヘッダーの値を指定します。 空の文字列に設定した場合、このヘッダーはゲートウェイ応答に含められません。|
|IsEnabled|ブール値、既定値は false |静的| HttpGateway を有効または無効にします。 HttpGateway は、既定では無効になっています。 |
|MaxEntityBodySize |uint、既定値は 4194304 |動的|HTTP 要求の予想される本文の最大サイズを指定します。 既定値は 4 MB です。 本文のサイズがこの値を超えている場合、HttpGateway は要求を失敗させます。 最小読み取りチャンク サイズは 4096 バイトです。 そのため、この値は 4096 以上にする必要があります。 |

## <a name="imagestoreservice"></a>ImageStoreService

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|Enabled |ブール値、既定値は false |静的|ImageStoreService の Enabled フラグ。 既定値: false |
|MinReplicaSetSize | int、既定値は 3 |静的|ImageStoreService の MinReplicaSetSize。 |
|PlacementConstraints | string、既定値は "" |静的| ImageStoreService の PlacementConstraints。 |
|QuorumLossWaitDuration | 時間 (秒単位)、既定値は MaxValue |静的| timespan を秒単位で指定します。 ImageStoreService の QuorumLossWaitDuration。 |
|ReplicaRestartWaitDuration | 時間 (秒単位)、既定値は 60.0 * 30 |静的|timespan を秒単位で指定します。 ImageStoreService の ReplicaRestartWaitDuration。 |
|StandByReplicaKeepDuration | 時間 (秒単位)、既定値は 3600.0 * 2 |静的| timespan を秒単位で指定します。 ImageStoreService の StandByReplicaKeepDuration。 |
|TargetReplicaSetSize | int、既定値は 7 |静的|ImageStoreService の TargetReplicaSetSize。 |

## <a name="ktllogger"></a>KtlLogger

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |int、既定値は 1 |動的|メモリ設定を自動的に構成するか、動的に構成するかを示すフラグ。 0 の場合は、メモリ構成設定が使用され、システムの状態に基づいて変更されることはありません。 1 の場合は、メモリ設定が自動的に構成され、システムの状態に基づいて変更される場合があります。 |
|MaximumDestagingWriteOutstandingInKB | int、既定値は 0 |動的|専用ログの前に共有ログを使用できる KB 数。 無制限であることを示すには 0 を使用します。
|SharedLogId |string、既定値は "" |静的|共有ログ コンテナーの一意の GUID。 ファブリック データ ルート下の既定のパスを使用する場合は、"" を使用します。 |
|SharedLogPath |string、既定値は "" |静的|共有ログ コンテナーを配置する場所のパスとファイル名。 ファブリック データ ルート下の既定のパスを使用する場合は、"" を使用します。 |
|SharedLogSizeInMB |int、既定値は 8192 |静的|共有ログ コンテナーに割り当てる MB 数。 |
|SharedLogThrottleLimitInPercentUsed|int、既定値は 0 | 静的 | 調整が誘発される共有ログの使用率。 値は 0 から 100 の範囲内である必要があります。 値 0 は、既定の割合の値を使用することを意味します。 値 100 は、調整が行われないことを暗示します。 1 から 99 の範囲内の値はログの使用パーセンテージを指定し、この値を超えると調整が発生します。たとえば、10 GB の共有ログに対して値 90 を指定した場合、9 GB が使用されると調整が発生します。 既定値を使用することをお勧めします。|
|WriteBufferMemoryPoolMaximumInKB | int、既定値は 0 |動的|書き込みバッファー メモリ プールを拡張できる最大 KB 数。 無制限であることを示すには 0 を使用します。 |
|WriteBufferMemoryPoolMinimumInKB |int、既定値は 8388608 |動的|書き込みバッファー メモリ プールに最初に割り当てる KB 数。 無制限であることを示すには 0 を使用します。既定値は、後述の SharedLogSizeInMB と一致する必要があります。 |

## <a name="management"></a>管理

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|AutomaticUnprovisionInterval|TimeSpan、既定値は Common::TimeSpan::FromMinutes(5)|動的|timespan を秒単位で指定します。 アプリケーションの種類の自動クリーンアップ中にアプリケーションの種類を登録解除するために許可されたクリーンアップ間隔。|
|AzureStorageMaxConnections | int、既定値は 5000 |動的|Azure Storage へのコンカレント接続の最大数。 |
|AzureStorageMaxWorkerThreads | int、既定値は 25 |動的|並列 worker スレッドの最大数。 |
|AzureStorageOperationTimeout | 時間 (秒単位)、既定値は 6000 |動的|timespan を秒単位で指定します。 xstore 操作が完了するまでのタイムアウト。 |
|CleanupApplicationPackageOnProvisionSuccess|ブール値、既定値は FALSE |動的|成功したプロビジョニングでアプリケーション パッケージの自動クリーンアップを有効または無効にします。 |
|CleanupUnusedApplicationTypes|ブール値、既定値は FALSE |動的|この構成が有効な場合は、未使用のアプリケーションの種類のバージョンが自動的に登録解除され、直近の未使用の 3 バージョンがスキップされるため、イメージ ストアが占有するディスク領域が削減されます。 自動クリーンアップはその特定のアプリケーションの種類のプロビジョニングが成功した後にトリガーされるだけでなく、すべてのアプリケーションの種類で、1 日に 1 回定期的に実行されます。 スキップする未使用のバージョンの数は、パラメーター "MaxUnusedAppTypeVersionsToKeep" を使用して構成できます。 |
|DisableChecksumValidation | ブール値、既定値は false |静的| この構成により、アプリケーションのプロビジョニング時におけるチェックサムの検証を有効または無効にすることができます。 |
|DisableServerSideCopy | ブール値、既定値は false |静的|この構成により、アプリケーションのプロビジョニング時における、ImageStore でのアプリケーション パッケージのサーバー側のコピーを有効または無効にできます。 |
|ImageCachingEnabled | ブール値、既定値は true |静的|この構成により、キャッシュを有効または無効にすることができます。 |
|ImageStoreConnectionString |SecureString |静的|ImageStore のルートへの接続文字列。 |
|ImageStoreMinimumTransferBPS | int、既定値は 1024 |動的|クラスターと ImageStore 間の最小転送速度。 この値を使用して、外部 ImageStore にアクセスするときのタイムアウトを決定します。 クラスターが外部 ImageStore からダウンロードする際により多くの時間を確保できるように、クラスターと ImageStore 間の待機時間が長い場合にのみ、この値を変更します。 |
|MaxUnusedAppTypeVersionsToKeep | int、既定値は 3 |動的|この構成により、クリーンアップでスキップされる未使用のアプリケーションの種類のバージョンの数が定義されます。 このパラメーターは、パラメーター CleanupUnusedApplicationTypes が有効になっている場合にのみ適用できます。 |


## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **パラメーター** | **使用できる値** |**アップグレード ポリシー**| **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap、既定値は None|動的|クラスター内のメトリックの MetricActivityThresholds セットを決定します。 maxNodeLoad が MetricActivityThresholds より大きい場合、分散が機能します。 最適化メトリックについては、負荷の量を定義し、この負荷以下になると、Service Fabric によって、ノードが空であると見なされます |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **パラメーター** | **使用できる値** |**アップグレード ポリシー**| **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap、既定値は None|動的|クラスター内のメトリックの MetricBalancingThresholds セットを決定します。 maxNodeLoad/minNodeLoad が MetricBalancingThresholds より大きい場合、分散が機能します。 少なくとも 1 つの FD または UD の maxNodeLoad/minNodeLoad が MetricBalancingThresholds より小さい場合、最適化が機能します。 |

## <a name="namingservice"></a>NamingService

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |int、既定値は 0 |静的|Naming Gateway の LRU サービス記述キャッシュに保持するエントリの最大数 (無制限の場合は 0 に設定します)。 |
|MaxClientConnections |int、既定値は 1000 |動的|ゲートウェイごとのクライアント接続の最大許容数。 |
|MaxFileOperationTimeout |秒単位。既定値は 30 |動的|timespan を秒単位で指定します。 ファイル ストア サービス操作で許容される最大タイムアウト。 これよりも長いタイムアウトを指定した要求は拒否されます。 |
|MaxIndexedEmptyPartitions |int、既定値は 1000 |動的|再接続するクライアントを同期するために、インデックスが設定された状態で通知キャッシュ内に残される空のパーティションの最大数。 空のパーティションがこの数を超えた場合、参照バージョンの昇順でインデックスから削除されます。 再接続するクライアントは引き続き同期され、欠落した空のパーティションの最新情報を受け取ることができますが、同期プロトコルのコストが高くなります。 |
|MaxMessageSize |int、既定値は 4\*1024\*1024 |静的|名前付け使用時のクライアント ノード通信の最大メッセージ サイズ。 DOS 攻撃を軽減します。既定値は 4 MB です。 |
|MaxNamingServiceHealthReports | int、既定値は 10 |動的|ネーム ストア サービスが異常として一度に報告する低速操作の最大数。 0 の場合、低速の操作がすべて送信されます。 |
|MaxOperationTimeout |時間 (秒単位)、既定値は 600 |動的|timespan を秒単位で指定します。 クライアント操作で許容される最大タイムアウト。 これよりも長いタイムアウトを指定した要求は拒否されます。 |
|MaxOutstandingNotificationsPerClient |int、既定値は 1000 |動的|クライアント登録がゲートウェイによって強制終了されるまでの未処理の通知の最大数。 |
|MinReplicaSetSize | int、既定値は 3 |禁止| 更新の完了のために書き込みを行う必要があるネーム サービス レプリカの最小数。 システム内のアクティブなレプリカがこの数よりも少ない場合、信頼性システムはレプリカが復元されるまでネーム サービス ストアの更新を拒否します。 この値は、TargetReplicaSetSize の値を超えることはできません。 |
|PartitionCount |int、既定値は 3 |禁止|作成するネーム サービス ストアのパーティションの数。 各パーティションは、インデックスに対応する 1 つのパーティション キーを所有します。そのため、パーティション キー [0; PartitionCount) が存在します。 ネーム サービスのパーティション数が増えると、バックアップ レプリカ セットで保持される平均データ量が減るため、ネーム サービスが実行できるスケールが増加します。ただし、その代償として (PartitionCount*ReplicaSetSize のサービス レプリカを保持する必要があるため) リソース使用率が増加します。|
|PlacementConstraints | string、既定値は "" |禁止| ネーム サービスの配置制約。 |
|QuorumLossWaitDuration | 時間 (秒単位)、既定値は MaxValue |禁止| timespan を秒単位で指定します。 ネーム サービスがクォーラム損失の状態になると、このタイマーが開始されます。 タイマーの有効期限が切れると、FM はダウンしたレプリカを失われたと見なし、クォーラムの回復を試みます。 この回復によりデータが失われる可能性があることに注意してください。 |
|RepairInterval | 時間 (秒単位)、既定値は 5 |静的| timespan を秒単位で指定します。 機関所有者と名前所有者間での名前付けの不一致の修復が開始される間隔。 |
|ReplicaRestartWaitDuration | 時間 (秒単位)、既定値は (60.0 * 30)|禁止| timespan を秒単位で指定します。 ネーム サービスのレプリカがダウンすると、このタイマーが開始されます。 タイマーの期限が切れると、FM により、ダウンしたレプリカの置き換えが開始されます (この場合でも、レプリカが失われたとはみなされません)。 |
|ServiceDescriptionCacheLimit | int、既定値は 0 |静的| ネーム ストア サービスの LRU サービス記述キャッシュに保持するエントリの最大数 (無制限の場合は 0 に設定します)。 |
|ServiceNotificationTimeout |秒単位。既定値は 30 |動的|timespan を秒単位で指定します。 サービス通知をクライアントに配信するときに使用されるタイムアウト。 |
|StandByReplicaKeepDuration | 時間 (秒単位)、既定値は 3600.0 * 2 |禁止| timespan を秒単位で指定します。 ネーム サービスのレプリカがダウン状態から復帰したときに、既に置き換えられている場合があります。 このタイマーは、FM がスタンバイ レプリカを破棄するまでに保持する時間を決定します。 |
|TargetReplicaSetSize |int、既定値は 7 |禁止|ネーム サービス ストアのパーティションごとのレプリカ セットの数。 レプリカ セットの数が増えると、ノードの障害によって情報が失われる可能性が低減されるため、ネーム サービス ストア内の情報の信頼性レベルが高まります。ただし、その代償として Windows Fabric の負荷が増加し、名前付けデータの更新の実行に時間がかかるようになります。|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **パラメーター** | **使用できる値** |**アップグレード ポリシー**| **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap、既定値は None|動的|メトリック名ごとのノード容量のパーセンテージ。フェールオーバーの場合、ノード上に空き領域を確保するためのバッファーとして使用されます。 |

## <a name="nodecapacities"></a>NodeCapacities

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |静的|さまざまなメトリックのノード容量のコレクション。 |

## <a name="nodedomainids"></a>NodeDomainIds

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|PropertyGroup |NodeFaultDomainIdCollection |静的|ノードが属する障害ドメインを示します。 障害ドメインは、データセンター内のノードの場所を示す URI によって定義されます。  障害ドメインの URI は、fd:/fd/ の後に URI パス セグメントが続く形式になります。|
|UpgradeDomainId |string、既定値は "" |静的|ノードが属するアップグレード ドメインを示します。 |

## <a name="nodeproperties"></a>NodeProperties

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |静的|ノードのプロパティの文字列キーと値のペアのコレクション。 |

## <a name="paas"></a>Paas

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|ClusterId |string、既定値は "" |禁止|ファブリックで構成の保護に使用される X509 証明書ストア。 |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|Counters |string | 動的 |収集するパフォーマンス カウンターのコンマ区切りリスト。 |
|IsEnabled |ブール値、既定値は true | 動的 |ローカル ノードでのパフォーマンス カウンターの収集が有効になっているかどうかを示すフラグ。 |
|MaxCounterBinaryFileSizeInMB |int、既定値は 1 | 動的 |各パフォーマンス カウンター バイナリ ファイルの最大サイズ (MB)。 |
|NewCounterBinaryFileCreationIntervalInMinutes |int、既定値は 10 | 動的 |新しいパフォーマンス カウンター バイナリ ファイルを作成するまでの最大間隔 (秒単位)。 |
|SamplingIntervalInSeconds |int、既定値は 60 | 動的 |収集するパフォーマンス カウンターのサンプリング間隔。 |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | int、既定値は 0 | 動的|アフィニティの制約の優先順位を指定します:0:ハード、1:ソフト、負の値:無視。 |
|ApplicationCapacityConstraintPriority | int、既定値は 0 | 動的|容量の制約の優先順位を指定します:0:ハード、1:ソフト、負の値:無視。 |
|AutoDetectAvailableResources|ブール値、既定値は TRUE|静的|この構成は、ノードで使用可能なリソース (CPU およびメモリ) の自動検出をトリガーします。この構成が true に設定されている場合は、実際の容量を読み取り、ユーザーによって不適切なノード容量が指定されているとき、または容量が定義されていないときは、それを修正します。この構成が false の場合は、ユーザーによって不適切なノード容量が指定されているという警告をトレースしますが、修正しません。つまり、ユーザーは、実際のノードの容量よりも大きな値を指定しようとしています。容量が未定義の場合は、無制限の容量と見なされます |
|BalancingDelayAfterNewNode | 時間 (秒単位)、既定値は 120 |動的|timespan を秒単位で指定します。 新しいノードの追加後、この期間内に均衡化アクティビティを開始しないでください。 |
|BalancingDelayAfterNodeDown | 時間 (秒単位)、既定値は 120 |動的|timespan を秒単位で指定します。 ノード ダウン イベント後、この期間内に均衡化アクティビティを開始しないでください。 |
|CapacityConstraintPriority | int、既定値は 0 | 動的|容量の制約の優先順位を指定します:0:ハード、1:ソフト、負の値:無視。 |
|ConsecutiveDroppedMovementsHealthReportLimit | int、既定値は 20 | 動的|ResourceBalancer が発行した移動が連続して破棄された回数がここで定義した連続回数に達すると、診断が行われ、正常性の警告が出力されます。 負の値:この条件下では警告は出力されません。 |
|ConstraintFixPartialDelayAfterNewNode | 時間 (秒単位)、既定値は 120 |動的| timespan を秒単位で指定します。 新しいノードの追加後、この期間内に FaultDomain および UpgradeDomain の制約違反を修正しないでください。 |
|ConstraintFixPartialDelayAfterNodeDown | 時間 (秒単位)、既定値は 120 |動的| timespan を秒単位で指定します。 ノード ダウン イベント後、この期間内に FaultDomain および UpgradeDomain の制約違反を修正しないでください。 |
|ConstraintViolationHealthReportLimit | int、既定値は 50 |動的| 制約に違反しているレプリカが永続的に未修正の状態になった回数がここで定義した回数に達すると、診断が行われ、正常性レポートが出力されます。 |
|DetailedConstraintViolationHealthReportLimit | int、既定値は 200 |動的| 制約に違反しているレプリカが永続的に未修正の状態になった回数がここで定義した回数に達すると、診断が行われ、詳細な正常性レポートが出力されます。 |
|DetailedDiagnosticsInfoListLimit | int、既定値は 15 |動的| 診断での切り捨て前に含める、制約ごとの (詳細情報を含む) 診断エントリの数を定義します。|
|DetailedNodeListLimit | int、既定値は 15 |動的| 未配置レプリカ レポートでの切り捨て前に含める、制約ごとのノードの数を定義します。 |
|DetailedPartitionListLimit | int、既定値は 15 |動的| 診断での切り捨て前に含める、制約の診断エントリごとのパーティションの数を定義します。 |
|DetailedVerboseHealthReportLimit | int、既定値は 200 | 動的|未配置レプリカが永続的に未配置の状態になった回数がここで定義した回数に達すると、詳細な正常性レポートが出力されます。 |
|FaultDomainConstraintPriority | int、既定値は 0 |動的| 障害ドメインの制約の優先順位を指定します:0:ハード、1:ソフト、負の値:無視。 |
|GlobalMovementThrottleCountingInterval | 時間 (秒単位)、既定値は 600 |静的| timespan を秒単位で指定します。 ドメイン レプリカの移動ごとに追跡する、過去の間隔の長さを示します (GlobalMovementThrottleThreshold と共に使用)。 0 に設定すると、グローバルな調整を完全に無視できます。 |
|GlobalMovementThrottleThreshold | uint、既定値は 1000 |動的| 均衡化フェーズにおいて、GlobalMovementThrottleCountingInterval で示される過去の間隔での移動の最大許容数。 |
|GlobalMovementThrottleThresholdForBalancing | uint、既定値は 0 | 動的|均衡化フェーズにおいて、GlobalMovementThrottleCountingInterval で示される過去の間隔での移動の最大許容数。 0 は無制限であることを示します。 |
|GlobalMovementThrottleThresholdForPlacement | uint、既定値は 0 |動的| 配置フェーズにおいて、GlobalMovementThrottleCountingInterval で示される過去の間隔での移動の最大許容数。0 は無制限であることを示します。|
|GlobalMovementThrottleThresholdPercentage|double、既定値は 0|動的|分散および配置フェーズにおいて、GlobalMovementThrottleCountingInterval で示される過去の間隔での移動の合計最大許容数。クラスター内のレプリカの合計数に対するパーセンテージとして表されます。 0 は無制限であることを示します。 これと GlobalMovementThrottleThreshold の両方が指定されている場合は、より控え目な制限が使用されます。|
|GlobalMovementThrottleThresholdPercentageForBalancing|double、既定値は 0|動的|分散フェーズにおいて、GlobalMovementThrottleCountingInterval で示される過去の間隔での移動の最大許容数。PLB 内のレプリカの合計数に対するパーセンテージとして表されます。 0 は無制限であることを示します。 これと GlobalMovementThrottleThresholdForBalancing の両方が指定されている場合は、より控え目な制限が使用されます。|
|InBuildThrottlingAssociatedMetric | string、既定値は "" |静的| この調整に関連付けられたメトリックの名前。 |
|InBuildThrottlingEnabled | ブール値、既定値は false |動的| 組み込みの調整を有効にするかどうかを指定します。 |
|InBuildThrottlingGlobalMaxValue | int、既定値は 0 |動的|グローバルに使用できる組み込みレプリカの最大数。 |
|InterruptBalancingForAllFailoverUnitUpdates | ブール値、既定値は false | 動的|各種フェールオーバー ユニットの更新時に、高速または低速の均衡化の実行を中断させる必要があるかどうかを指定します。 "false" を指定すると、FailoverUnit が作成または削除された場合、FailoverUnit でレプリカが不足している場合、プライマリ レプリカの場所が変更された場合、またはレプリカの数が変更された場合に、均衡化の実行は中断されます。 それ以外の場合は、均衡化の実行が中断されることはありません。たとえば、FailoverUnit に追加のレプリカがある場合、レプリカ フラグが変更された場合、パーティションのバージョンだけが変更された場合などです。 |
|MinConstraintCheckInterval | 時間 (秒単位)、既定値は 1 |動的| timespan を秒単位で指定します。 2 つの連続する制約チェック ラウンドまでに必要な最小経過時間を定義します。 |
|MinLoadBalancingInterval | 時間 (秒単位)、既定値は 5 |動的| timespan を秒単位で指定します。 2 つの連続する均衡化ラウンドまでに必要な最小経過時間を定義します。 |
|MinPlacementInterval | 時間 (秒単位)、既定値は 1 |動的| timespan を秒単位で指定します。 2 つの連続する配置ラウンドまでに必要な最小経過時間を定義します。 |
|MoveExistingReplicaForPlacement | ブール値、既定値は true |動的|配置中に既存のレプリカを移動するかどうかを指定します。 |
|MovementPerPartitionThrottleCountingInterval | 時間 (秒単位)、既定値は 600 |静的| timespan を秒単位で指定します。 各パーティションのレプリカの移動を追跡する、過去の間隔の長さを示します (MovementPerPartitionThrottleThreshold と共に使用)。 |
|MovementPerPartitionThrottleThreshold | uint、既定値は 50 |動的| パーティションのレプリカの均衡化に関連する移動数が、MovementPerPartitionThrottleCountingInterval で示されている過去の間隔で MovementPerFailoverUnitThrottleThreshold の値に達するか、この値を超えると、そのパーティションで均衡化に関連する移動は発生しなくなります。 |
|MoveParentToFixAffinityViolation | ブール値、既定値は false |動的| アフィニティの制約を修正するために親レプリカを移動できるかどうかを指定します。|
|PartiallyPlaceServices | ブール値、既定値は true |動的| サービス レプリカに適したノードの数が限られている場合に、クラスター内のすべてのサービス レプリカを "全部かゼロか" 方式で配置するかどうかを指定します。|
|PlaceChildWithoutParent | ブール値、既定値は true | 動的|親レプリカが稼働していない場合に、子サービス レプリカを配置できるかどうかを指定します。 |
|PlacementConstraintPriority | int、既定値は 0 | 動的|配置の制約の優先順位を指定します:0:ハード、1:ソフト、負の値:無視。 |
|PlacementConstraintValidationCacheSize | int、既定値は 10000 |動的| 配置制約式の簡易検証とキャッシュに使用するテーブルのサイズを制限します。 |
|PlacementSearchTimeout | 時間 (秒単位)、既定値は 0.5 |動的| timespan を秒単位で指定します。 サービスを配置するときに、結果を返すまでに最長でこの時間の間、検索します。 |
|PLBRefreshGap | 時間 (秒単位)、既定値は 1 |動的| timespan を秒単位で指定します。 PLB が状態を再度更新するまでに必要な最小経過時間を定義します。 |
|PreferredLocationConstraintPriority | int、既定値は 2| 動的|優先される場所の制約の優先順位を指定します:0:ハード、1:ソフト、2:最適化、負の値:Ignore |
|PreferUpgradedUDs|ブール値、既定値は TRUE|動的|既にアップグレードされている UD への移動を優先するロジックをオンまたはオフにします。|
|PreventTransientOvercommit | ブール値、既定値は false | 動的|開始された移動によって解放されるリソースを PLB が即座に利用するかどうかを指定します。 既定では、PLB は同じノード上で移動を開始できるので、一時的なオーバーコミットが発生する可能性があります。 このパラメーターを true に設定すると、このようなオーバーコミットを防ぐことができ、オンデマンドのデフラグ (placementWithMove) が無効になります。 |
|ScaleoutCountConstraintPriority | int、既定値は 0 |動的| スケールアウト数の制約の優先順位を指定します:0:ハード、1:ソフト、負の値:無視。 |
|SwapPrimaryThrottlingAssociatedMetric | string、既定値は ""|静的| この調整に関連付けられたメトリックの名前。 |
|SwapPrimaryThrottlingEnabled | ブール値、既定値は false|動的| スワップ プライマリ調整を有効にするかどうかを指定します。 |
|SwapPrimaryThrottlingGlobalMaxValue | int、既定値は 0 |動的| グローバルに使用できるスワップ プライマリ レプリカの最大数。 |
|TraceCRMReasons |ブール値、既定値は true |動的|CRM が発行した、操作イベント チャネルへの移動の理由をトレースするかどうかを指定します。 |
|UpgradeDomainConstraintPriority | int、既定値は 1| 動的|アップグレード ドメインの制約の優先順位を指定します:0:ハード、1:ソフト、負の値:無視。 |
|UseMoveCostReports | ブール値、既定値は false | 動的|よりバランスの取れた配置を実現するために多数の移動が発生する可能性のある、スコア付け関数のコスト要素を無視するよう LB に指示します。 |
|UseSeparateSecondaryLoad | ブール値、既定値は true | 動的|別のセカンダリ負荷を使用するかどうかを指定します。 |
|ValidatePlacementConstraint | ブール値、既定値は true |動的| サービスの ServiceDescription が更新されたときに、サービスの PlacementConstraint 式を検証するかどうかを指定します。 |
|VerboseHealthReportLimit | int、既定値は 20 | 動的|レプリカが未配置の状態になった回数がここで定義した回数に達すると、正常性の警告が報告されます (詳細な正常性レポートが有効になっている場合)。 |

## <a name="reconfigurationagent"></a>ReconfigurationAgent

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | 時間 (秒単位)、既定値は 900 |動的|timespan を秒単位で指定します。 アプリケーションのアップグレード中、レプリカが終了途中で停止しているサービス ホストを終了するまでのシステムの待機期間。|
|FabricUpgradeMaxReplicaCloseDuration | 時間 (秒単位)、既定値は 900 |動的| timespan を秒単位で指定します。 ファブリックのアップグレード中、レプリカが終了途中で停止しているサービス ホストを終了するまでのシステムの待機期間。 |
|GracefulReplicaShutdownMaxDuration|TimeSpan、既定値は Common::TimeSpan::FromSeconds(120)|動的|timespan を秒単位で指定します。 レプリカが終了途中で停止しているサービス ホストを終了するまでのシステムの待機期間。 この値が 0 に設定されている場合は、レプリカは閉じる指示を受けません。|
|NodeDeactivationMaxReplicaCloseDuration | 時間 (秒単位)、既定値は 900 |動的|timespan を秒単位で指定します。 ノードの非アクティブ化中、レプリカが終了途中で停止しているサービス ホストを終了するまでのシステムの待機期間。 |
|PeriodicApiSlowTraceInterval | 時間 (秒単位)、既定値は 5 分 |動的| timespan を秒単位で指定します。 PeriodicApiSlowTraceInterval は、時間がかかる API 呼び出しを API モニターで再トレースする間隔を定義します。 |
|ReplicaChangeRoleFailureRestartThreshold|int、既定値は 10|動的| 整数。 プライマリ昇格中、自動軽減アクション (レプリカの再起動) が適用されるまでの API エラーの数を指定します。 |
|ReplicaChangeRoleFailureWarningReportThreshold|int、既定値は 2147483647|動的| 整数。 プライマリ昇格中、警告の正常性レポートが開始されるまでの API エラーの数を指定します。|
|ServiceApiHealthDuration | 時間 (秒単位)、既定値は 30 分 |動的| timespan を秒単位で指定します。 ServiceApiHealthDuration は、開発者がサービス API を異常と報告するまでにサービス API の実行を待機する時間を定義します。 |
|ServiceReconfigurationApiHealthDuration | 秒単位。既定値は 30 |動的| timespan を秒単位で指定します。 ServiceReconfigurationApiHealthDuration は、開発者がサービス API を実行するまでの待ち時間を定義します。この時間の経過後、開発者は異常と報告します。 これは、可用性に影響する API 呼び出しに適用されます。|

## <a name="replication"></a>レプリケーション
| **パラメーター** | **使用できる値** | **アップグレード ポリシー**| **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|TimeSpan、既定値は Common::TimeSpan::FromMilliseconds(15)|静的|timespan を秒単位で指定します。 操作を受信してから受信確認を返すまで、レプリケーターが待機する時間を指定します。 この期間に受信した他の操作の受信確認は 1 つのメッセージで返されます。これにより、ネットワーク トラフィックは減少しますが、レプリケーターのスループットが低下する可能性があります。|
|MaxCopyQueueSize|uint、既定値は 1024|静的|レプリケーション操作を保持するキューの初期サイズの最大値を定義します。 この値は 2 の累乗にする必要があります。 ランタイムにキューがこのサイズに達すると、プライマリ レプリケーターとセカンダリ レプリケーターの間で操作が調整されます。|
|MaxPrimaryReplicationQueueMemorySize|uint、既定値は 0|静的|プライマリ レプリケーション キューの最大値 (バイト単位)。|
|MaxPrimaryReplicationQueueSize|uint、既定値は 1024|静的|プライマリ レプリケーション キューに存在する可能性がある操作の最大数。 この値は 2 の累乗にする必要があります。|
|MaxReplicationMessageSize|uint、既定値は 52428800|静的|レプリケーション操作の最大メッセージ サイズ。 既定値は 50 MB です。|
|MaxSecondaryReplicationQueueMemorySize|uint、既定値は 0|静的|セカンダリ レプリケーション キューの最大値 (バイト単位)。|
|MaxSecondaryReplicationQueueSize|uint、既定値は 2048|静的|セカンダリ レプリケーション キューに存在する可能性がある操作の最大数。 この値は 2 の累乗にする必要があります。|
|QueueHealthMonitoringInterval|TimeSpan、既定値は Common::TimeSpan::FromSeconds(30)|静的|timespan を秒単位で指定します。 この値は、レプリケーターが、レプリケーション操作キューで警告/エラーの正常性イベントを監視するときに使用する時間を決定します。 値 "0" の場合、正常性の監視は無効になります |
|QueueHealthWarningAtUsagePercent|uint、既定値は 80|静的|この値は、レプリケーション キューの使用率の基準を決定します。このパーセンテージを上回ると、キューの使用率が高いことが警告されます。 警告は、QueueHealthMonitoringInterval の猶予期間の後にレポートされます。 キューの使用率が猶予期間にこのパーセンテージを下回った場合|
|ReplicatorAddress|string、既定値は "localhost:0"|静的|文字列 "IP:Port" の形式のエンドポイント。この文字列は、Windows Fabric Replicator が操作を送受信するために、他のレプリカとの接続を確立する際に使用されます。|
|ReplicatorListenAddress|string、既定値は "localhost:0"|静的|文字列 "IP:Port" の形式のエンドポイント。この文字列は、Windows Fabric Replicator が他のレプリカから操作を受信するために使用します。|
|ReplicatorPublishAddress|string、既定値は "localhost:0"|静的|文字列 "IP:Port" の形式のエンドポイント。この文字列は、Windows Fabric Replicator が他のレプリカに操作を送信するために使用します。|
|RetryInterval|TimeSpan、既定値は Common::TimeSpan::FromSeconds(5)|静的|timespan を秒単位で指定します。 このタイマーは、操作が失われた場合、または拒否された場合に、レプリケーターが操作の送信の再試行をどのくらいの頻度で行うかを決定します。|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **パラメーター** | **使用できる値** | **アップグレード ポリシー**| **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|IsEnabled|ブール値、既定値は FALSE |静的|クラスターでサービスを有効化するかどうかを制御します。 |

## <a name="runas"></a>RunAs

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|RunAsAccountName |string、既定値は "" |動的|RunAs アカウントの名前を示します。 これはアカウントの種類が "DomainUser" または "ManagedServiceAccount" の場合にのみ必要です。 有効な値は、"domain\user" または "user@domain" です。 |
|RunAsAccountType|string、既定値は "" |動的|RunAs アカウントの種類を示します。 これはすべての RunAs セクションに必要です。有効な値は、"DomainUser/NetworkService/ManagedServiceAccount/LocalSystem" です。|
|RunAsPassword|string、既定値は "" |動的|RunAs アカウントのパスワードを示します。 これはアカウントの種類が "DomainUser" の場合にのみ必要です。 |

## <a name="runasdca"></a>RunAs_DCA

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|RunAsAccountName |string、既定値は "" |動的|RunAs アカウントの名前を示します。 これはアカウントの種類が "DomainUser" または "ManagedServiceAccount" の場合にのみ必要です。 有効な値は、"domain\user" または "user@domain" です。 |
|RunAsAccountType|string、既定値は "" |動的|RunAs アカウントの種類を示します。 これはすべての RunAs セクションに必要です。有効な値は、"LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem" です。 |
|RunAsPassword|string、既定値は "" |動的|RunAs アカウントのパスワードを示します。 これはアカウントの種類が "DomainUser" の場合にのみ必要です。 |

## <a name="runasfabric"></a>RunAs_Fabric

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|RunAsAccountName |string、既定値は "" |動的|RunAs アカウントの名前を示します。 これはアカウントの種類が "DomainUser" または "ManagedServiceAccount" の場合にのみ必要です。 有効な値は、"domain\user" または "user@domain" です。 |
|RunAsAccountType|string、既定値は "" |動的|RunAs アカウントの種類を示します。 これはすべての RunAs セクションに必要です。有効な値は、"LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem" です。 |
|RunAsPassword|string、既定値は "" |動的|RunAs アカウントのパスワードを示します。 これはアカウントの種類が "DomainUser" の場合にのみ必要です。 |

## <a name="runashttpgateway"></a>RunAs_HttpGateway

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|RunAsAccountName |string、既定値は "" |動的|RunAs アカウントの名前を示します。 これはアカウントの種類が "DomainUser" または "ManagedServiceAccount" の場合にのみ必要です。 有効な値は、"domain\user" または "user@domain" です。 |
|RunAsAccountType|string、既定値は "" |動的|RunAs アカウントの種類を示します。 これはすべての RunAs セクションに必要です。有効な値は、"LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem" です。 |
|RunAsPassword|string、既定値は "" |動的|RunAs アカウントのパスワードを示します。 これはアカウントの種類が "DomainUser" の場合にのみ必要です。 |

## <a name="security"></a>セキュリティ
| **パラメーター** | **使用できる値** |**アップグレード ポリシー**| **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|AADCertEndpointFormat|string、既定値は ""|静的|Azure Government "https:\//login.microsoftonline.us/{0}/federationmetadata/2007-06/federationmetadata.xml" のような既定以外の環境用に指定される AAD 証明書エンドポイントの形式。既定値は Azure Commercial。 |
|AADClientApplication|string、既定値は ""|静的|Fabric クライアントを表すネイティブ クライアント アプリケーションの名前または ID |
|AADClusterApplication|string、既定値は ""|静的|クラスターを表す Web API アプリケーションの名前または ID |
|AADLoginEndpoint|string、既定値は ""|静的|Azure Government "https:\//login.microsoftonline.us" のような既定以外の環境用に指定される AAD ログイン エンドポイント。既定値は Azure Commercial。 |
|AADTenantId|string、既定値は ""|静的|テナント ID (GUID) |
|AdminClientCertThumbprints|string、既定値は ""|動的|管理者ロールでクライアントによって使用される証明書のサムプリント。 コンマ区切りの名前リストです。 |
|AADTokenEndpointFormat|string、既定値は ""|静的|Azure Government "https:\//login.microsoftonline.us/{0}" のような既定以外の環境用に指定される AAD トークン エンドポイント。既定値は Azure Commercial。 |
|AdminClientClaims|string、既定値は ""|動的|管理クライアントから期待される、考えられるすべての要求。ClientClaims と同じ形式です。この一覧は ClientClaims に内部的に追加されるため、同じエントリを ClientClaims に追加する必要はありません。 |
|AdminClientIdentities|string、既定値は ""|動的|管理者ロールのファブリック クライアントの Windows ID。特権ファブリック操作の承認に使用されます。 これはコンマ区切りリストで、各エントリがドメイン アカウント名またはグループ名です。 便宜上、fabric.exe を実行するアカウントは自動的に管理者ロールを割り当てられます。グループ ServiceFabricAdministrators も同様です。 |
|AppRunAsAccountGroupX509Folder|string、既定値は /home/sfuser/sfusercerts |静的|AppRunAsAccountGroup X509 証明書と秘密キーがあるフォルダー |
|CertificateExpirySafetyMargin|TimeSpan、既定値は Common::TimeSpan::FromMinutes(43200)|静的|timespan を秒単位で指定します。 証明書の有効期限の安全マージン。有効期限がこれより近くなると、証明書の正常性レポートの状態が OK から警告に変わります。 既定値は 30 日です。 |
|CertificateHealthReportingInterval|TimeSpan、既定値は Common::TimeSpan::FromSeconds(3600 * 8)|静的|timespan を秒単位で指定します。 証明書の正常性レポートの間隔を指定します。既定値は 8 時間です。0 に設定すると、証明書の正常性レポートが無効になります |
|ClientCertThumbprints|string、既定値は ""|動的|クラスターと対話するために、クライアントによって使用される証明書のサムプリント。クラスターはこれを使用して、受診接続を承認します。 コンマ区切りの名前リストです。 |
|ClientClaimAuthEnabled|ブール値、既定値は FALSE|静的|クライアントで要求ベースの認証が有効になっているかどうかを示します。この設定を true にすると、ClientRoleEnabled が暗黙的に設定されます。 |
|ClientClaims|string、既定値は ""|動的|ゲートウェイへの接続に対して、クライアントから期待される使用可能なすべての要求。 これは "OR" リストです:ClaimsEntry \|\| ClaimsEntry \|\| ClaimsEntry ...。各 ClaimsEntry は "AND" リストです:ClaimType=ClaimValue && ClaimType=ClaimValue && ClaimType=ClaimValue ...。 |
|ClientIdentities|string、既定値は ""|動的|FabricClient の Windows ID。Naming Gateway は、これを使用して受診接続を承認します。 これはコンマ区切りリストで、各エントリがドメイン アカウント名またはグループ名です。 便宜上、fabric.exe を実行するアカウントは自動的に許可されます。グループ ServiceFabricAllowedUsers と ServiceFabricAdministrators も同様です。 |
|ClientRoleEnabled|ブール値、既定値は FALSE|静的|クライアント ロールが有効かどうかを示します。true の場合、クライアントには、クライアント自身の ID に基づいてロールが割り当てられます。 V2 の場合、これを有効にすると、AdminClientCommonNames/AdminClientIdentities にないクライアントが実行できるのは、読み取り専用の操作のみです。 |
|ClusterCertThumbprints|string、既定値は ""|動的|クラスターへの参加が許可されている証明書のサムプリント。コンマ区切りの名前リスト。 |
|ClusterCredentialType|string、既定値は "None"|禁止|クラスターをセキュリティで保護するために使用する、セキュリティ資格情報の種類を示します。 有効な値は "None/X509/Windows" です |
|ClusterIdentities|string、既定値は ""|動的|クラスターのノードの Windows ID。クラスター メンバーシップの承認に使用されます。 これはコンマ区切りリストで、各エントリがドメイン アカウント名またはグループ名です |
|ClusterSpn|string、既定値は ""|禁止|ファブリックが 1 人のドメイン ユーザー (gMSA/ドメイン ユーザー アカウント) として実行されているときの、クラスターのサービス プリンシパル名。 fabric.exe のリース リスナーとリスナーの SPN です: フェデレーション リスナー、内部レプリケーション リスナー、ランタイム サービスのリスナー、および Naming Gateway リスナー。 ファブリックがマシン アカウントとして実行されている場合は、空のままにします。この場合は、接続側が、リスナー転送アドレスからリスナー SPN を計算します。 |
|CrlCheckingFlag|uint、既定値は 0x40000000|動的|既定の証明書チェーン検証フラグ。コンポーネント固有のフラグにオーバーライドされる場合があります。例: Federation/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY。0 に設定すると、CRL チェックが無効になります。サポートされている値の一覧については、CertGetCertificateChain に関するページ (https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx ) の「dwFlags」を参照してください。 |
|CrlDisablePeriod|TimeSpan、既定値は Common::TimeSpan::FromMinutes(15)|動的|timespan を秒単位で指定します。 CRL オフライン エラーを無視できる場合、オフライン エラーの発生後、指定されている証明書に対して CRL チェックが無効になっている時間。 |
|CrlOfflineHealthReportTtl|TimeSpan、既定値は Common::TimeSpan::FromMinutes(1440)|動的|timespan を秒単位で指定します。 |
|DisableFirewallRuleForDomainProfile| ブール値、既定値は TRUE |静的| ドメイン プロファイルに対して、ファイアウォール規則を有効にするべきではないかどうかを示します |
|DisableFirewallRuleForPrivateProfile| ブール値、既定値は TRUE |静的| プライベート プロファイルに対して、ファイアウォール規則を有効にするべきではないかどうかを示します | 
|DisableFirewallRuleForPublicProfile| ブール値、既定値は TRUE | 静的|パブリック プロファイルに対して、ファイアウォール規則を有効にするべきではないかどうかを示します |
|FabricHostSpn| string、既定値は "" |静的| ファブリックが 1 人のドメイン ユーザー (gMSA/ドメイン ユーザー アカウント) として実行され、FabricHost がマシン アカウントで実行されているときの、FabricHost のサービス プリンシパル名。 これは FabricHost の IPC リスナーの SPN です。FabricHost がマシン アカウントで実行されているため、既定では空のままです |
|IgnoreCrlOfflineError|ブール値、既定値は FALSE|動的|サーバー側が受信クライアント証明書を確認するとき、CRL オフライン エラーを無視するかどうか |
|IgnoreSvrCrlOfflineError|ブール値、既定値は TRUE|動的|クライアント側が受信サーバー証明書を確認するとき、CRL オフライン エラーを無視するかどうか。既定値は true です。 失効したサーバー証明書で攻撃するには DNS を侵害する必要があります。これは、失効したクライアント証明書による攻撃よりも困難です。 |
|ServerAuthCredentialType|string、既定値は "None"|静的|FabricClient とクラスターの間の通信をセキュリティで保護するために使用する、セキュリティ資格情報の種類を示します。 有効な値は "None/X509/Windows" です |
|ServerCertThumbprints|string、既定値は ""|動的|クライアントと対話するために、クラスターによって使用されるサーバー証明書のサムプリント。クライアントはこれを使用して、クラスターを認証します。 コンマ区切りの名前リストです。 |
|SettingsX509StoreName| string、既定値は "MY"| 動的|ファブリックで構成の保護に使用される X509 証明書ストア |
|UseClusterCertForIpcServerTlsSecurity|ブール値、既定値は FALSE|静的|IPC Server TLS トランスポート ユニットをセキュリティで保護するためにクラスター証明書を使用するかどうか |
|X509Folder|string、既定値は /var/lib/waagent|静的|X509 証明書と秘密キーがあるフォルダー |

## <a name="securityadminclientx509names"></a>Security/AdminClientX509Names

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap、既定値は None|動的|これは "Name" と "Value" のペアの一覧です。 それぞれの "Name" は、サブジェクトの共通名または管理クライアント操作で承認された X509 証明書の DnsName です。 "Name" に対する "Value" は、発行者を固定する証明書のサムプリントのカンマで区切られた一覧であり、空でない場合は、管理クライアント証明書の直接的な発行者が一覧に含まれている必要があります。 |

## <a name="securityclientaccess"></a>Security/ClientAccess

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|ActivateNode |string、既定値は "Admin" |動的| ノードをアクティブ化するためのセキュリティ構成。 |
|CancelTestCommand |string、既定値は "Admin" |動的| 実行中の特定の TestCommand を取り消します。 |
|CodePackageControl |string、既定値は "Admin" |動的| コード パッケージを再開するためのセキュリティ構成。 |
|CreateApplication |string、既定値は "Admin" | 動的|アプリケーションを作成するためのセキュリティ構成。 |
|CreateComposeDeployment|string、既定値は "Admin"| 動的|compose ファイルで記述されている compose デプロイを作成します |
|CreateGatewayResource|string、既定値は "Admin"| 動的|ゲートウェイ リソースを作成する |
|CreateName |string、既定値は "Admin" |動的|名前付け URI を作成するためのセキュリティ構成。 |
|CreateNetwork|string、既定値は "Admin" |動的|コンテナー ネットワークを作成します |
|CreateService |string、既定値は "Admin" |動的| サービスを作成するためのセキュリティ構成。 |
|CreateServiceFromTemplate |string、既定値は "Admin" |動的|テンプレートからサービスを作成するためのセキュリティ構成。 |
|CreateVolume|string、既定値は "Admin"|動的|ボリュームを作成します |
|DeactivateNode |string、既定値は "Admin" |動的| ノードを非アクティブ化するためのセキュリティ構成。 |
|DeactivateNodesBatch |string、既定値は "Admin" |動的| 複数のノードを非アクティブ化するためのセキュリティ構成。 |
|削除 |string、既定値は "Admin" |動的| イメージ ストア クライアントの削除操作のセキュリティ構成。 |
|DeleteApplication |string、既定値は "Admin" |動的| アプリケーションを削除するためのセキュリティ構成。 |
|DeleteComposeDeployment|string、既定値は "Admin"| 動的|compose デプロイを削除します |
|DeleteGatewayResource|string、既定値は "Admin"| 動的|ゲートウェイ リソースを削除します |
|DeleteName |string、既定値は "Admin" |動的|名前付け URI を削除するためのセキュリティ構成。 |
|DeleteNetwork|string、既定値は "Admin" |動的|コンテナー ネットワークを削除します |
|DeleteService |string、既定値は "Admin" |動的|サービスを削除するためのセキュリティ構成。 |
|DeleteVolume|string、既定値は "Admin"|動的|ボリュームを削除します。| 
|EnumerateProperties |string、既定値は "Admin\|\|User" | 動的|名前付けプロパティを列挙するためのセキュリティ構成。 |
|EnumerateSubnames |string、既定値は "Admin\|\|User" |動的| 名前付け URI を列挙するためのセキュリティ構成。 |
|FileContent |string、既定値は "Admin" |動的| イメージ ストア クライアント ファイルの転送 (クラスターの外部) のセキュリティ構成。 |
|FileDownload |string、既定値は "Admin" |動的| イメージ ストア クライアント ファイルのダウンロードの開始 (クラスターの外部) のセキュリティ構成。 |
|FinishInfrastructureTask |string、既定値は "Admin" |動的| インフラストラクチャ タスクを終了するためのセキュリティ構成。 |
|GetChaosReport | string、既定値は "Admin\|\|User" |動的| 指定された時間範囲内の混乱の状態を取得します。 |
|GetClusterConfiguration | string、既定値は "Admin\|\|User" | 動的|パーティションで GetClusterConfiguration を誘発します。 |
|GetClusterConfigurationUpgradeStatus | string、既定値は "Admin\|\|User" |動的| パーティションで GetClusterConfigurationUpgradeStatus を誘発します。 |
|GetFabricUpgradeStatus |string、既定値は "Admin\|\|User" |動的| クラスターのアップグレード状態をポーリングするためのセキュリティ構成。 |
|GetNodeDeactivationStatus |string、既定値は "Admin" |動的| 非アクティブ化の状態を確認するためのセキュリティ構成。 |
|GetNodeTransitionProgress | string、既定値は "Admin\|\|User" |動的| ノード切り替えコマンドの進行状況を取得するためのセキュリティ構成。 |
|GetPartitionDataLossProgress | string、既定値は "Admin\|\|User" | 動的|データ損失を発生させる API 呼び出しの進行状況を取得します。 |
|GetPartitionQuorumLossProgress | string、既定値は "Admin\|\|User" |動的| クォーラム損失を発生させる API 呼び出しの進行状況を取得します。 |
|GetPartitionRestartProgress | string、既定値は "Admin\|\|User" |動的| パーティションを再起動する API 呼び出しの進行状況を取得します。 |
|GetSecrets|string、既定値は "Admin"|動的|シークレット値を取得します |
|GetServiceDescription |string、既定値は "Admin\|\|User" |動的| 長いポーリングのサービス通知と読み取りサービスの説明のセキュリティ構成。 |
|GetStagingLocation |string、既定値は "Admin" |動的| イメージ ストア クライアントのステージングの場所を取得するためのセキュリティ構成。 |
|GetStoreLocation |string、既定値は "Admin" |動的| イメージ ストア クライアントのストアの場所を取得するためのセキュリティ構成。 |
|GetUpgradeOrchestrationServiceState|string、既定値は "Admin"| 動的|パーティションで GetUpgradeOrchestrationServiceState を誘発します |
|GetUpgradesPendingApproval |string、既定値は "Admin" |動的| パーティションで GetUpgradesPendingApproval を誘発します。 |
|GetUpgradeStatus |string、既定値は "Admin\|\|User" |動的| アプリケーションのアップグレード状態をポーリングするためのセキュリティ構成。 |
|InternalList |string、既定値は "Admin" | 動的|イメージ ストア クライアント ファイルのリスト操作 (内部) のセキュリティ構成。 |
|InvokeContainerApi|string、既定値は "Admin"|動的|コンテナー API を呼び出す |
|InvokeInfrastructureCommand |string、既定値は "Admin" |動的| インフラストラクチャ タスクの管理コマンドのセキュリティ構成。 |
|InvokeInfrastructureQuery |string、既定値は "Admin\|\|User" | 動的|インフラストラクチャ タスクを照会するためのセキュリティ構成。 |
|List |string、既定値は "Admin\|\|User" | 動的|イメージ ストア クライアント ファイルのリスト操作のセキュリティ構成。 |
|MoveNextFabricUpgradeDomain |string、既定値は "Admin" |動的| 明示的なアップグレード ドメインでクラスターのアップグレードを再開するためのセキュリティ構成。 |
|MoveNextUpgradeDomain |string、既定値は "Admin" |動的| 明示的なアップグレード ドメインでアプリケーションのアップグレードを再開するためのセキュリティ構成。 |
|MoveReplicaControl |string、既定値は "Admin" | 動的|レプリカを移動します。 |
|NameExists |string、既定値は "Admin\|\|User" | 動的|名前付け URI の存在確認のセキュリティ構成。 |
|NodeControl |string、既定値は "Admin" |動的| ノードを起動、停止、再起動するためのセキュリティ構成。 |
|NodeStateRemoved |string、既定値は "Admin" |動的| 削除されたノードの状態を報告するためのセキュリティ構成。 |
|ping |string、既定値は "Admin\|\|User" |動的| クライアントの ping のセキュリティ構成。 |
|PredeployPackageToNode |string、既定値は "Admin" |動的| デプロイメント前の API。 |
|PrefixResolveService |string、既定値は "Admin\|\|User" |動的| クレーム ベースのサービス プレフィックス解決のセキュリティ構成。 |
|PropertyReadBatch |string、既定値は "Admin\|\|User" |動的| 名前付けプロパティの読み取り操作のセキュリティ構成。 |
|PropertyWriteBatch |string、既定値は "Admin" |動的|名前付けプロパティの書き込み操作のセキュリティ構成。 |
|ProvisionApplicationType |string、既定値は "Admin" |動的| アプリケーションの種類をプロビジョニングするためのセキュリティ構成。 |
|ProvisionFabric |string、既定値は "Admin" |動的| MSI またはクラスター マニフェストをプロビジョニングするためのセキュリティ構成。 |
|Query |string、既定値は "Admin\|\|User" |動的| クエリのセキュリティ構成。 |
|RecoverPartition |string、既定値は "Admin" | 動的|パーティションを復旧するためのセキュリティ構成。 |
|RecoverPartitions |string、既定値は "Admin" | 動的|複数のパーティションを復旧するためのセキュリティ構成。 |
|RecoverServicePartitions |string、既定値は "Admin" |動的| サービス パーティションを復旧するためのセキュリティ構成。 |
|RecoverSystemPartitions |string、既定値は "Admin" |動的| システム サービス パーティションを復旧するためのセキュリティ構成。 |
|RemoveNodeDeactivations |string、既定値は "Admin" |動的| 複数のノードの非アクティブ化を取り消すためのセキュリティ構成。 |
|ReportFabricUpgradeHealth |string、既定値は "Admin" |動的| 現在のアップグレードの進行状況でクラスターのアップグレードを再開するためのセキュリティ構成。 |
|ReportFault |string、既定値は "Admin" |動的| 障害を報告するためのセキュリティ構成。 |
|ReportHealth |string、既定値は "Admin" |動的| 正常性を報告するためのセキュリティ構成。 |
|ReportUpgradeHealth |string、既定値は "Admin" |動的| 現在のアップグレードの進行状況でアプリケーションのアップグレードを再開するためのセキュリティ構成。 |
|ResetPartitionLoad |string、既定値は "Admin\|\|User" |動的| failoverUnit の負荷をリセットするためのセキュリティ構成。 |
|ResolveNameOwner |string、既定値は "Admin\|\|User" | 動的|名前付け URI の所有者を解決するためのセキュリティ構成。 |
|ResolvePartition |string、既定値は "Admin\|\|User" | 動的|システム サービスを解決するためのセキュリティ構成。 |
|ResolveService |string、既定値は "Admin\|\|User" |動的| クレーム ベースのサービス解決のセキュリティ構成。 |
|ResolveSystemService|string、既定値は "Admin\|\|User"|動的| システム サービスを解決するためのセキュリティ構成 |
|RollbackApplicationUpgrade |string、既定値は "Admin" |動的| アプリケーションのアップグレードをロールバックするためのセキュリティ構成。 |
|RollbackFabricUpgrade |string、既定値は "Admin" |動的| クラスターのアップグレードをロールバックするためのセキュリティ構成。 |
|ServiceNotifications |string、既定値は "Admin\|\|User" |動的| イベント ベースのサービス通知のセキュリティ構成。 |
|SetUpgradeOrchestrationServiceState|string、既定値は "Admin"| 動的|パーティションで SetUpgradeOrchestrationServiceState を誘発します |
|StartApprovedUpgrades |string、既定値は "Admin" |動的| パーティションで StartApprovedUpgrades を誘発します。 |
|StartChaos |string、既定値は "Admin" |動的| 混乱を開始します (まだ開始されていない場合)。 |
|StartClusterConfigurationUpgrade |string、既定値は "Admin" |動的| パーティションで StartClusterConfigurationUpgrade を誘発します。 |
|StartInfrastructureTask |string、既定値は "Admin" | 動的|インフラストラクチャ タスクを開始するためのセキュリティ構成。 |
|StartNodeTransition |string、既定値は "Admin" |動的| ノードの切り替えを開始するためのセキュリティ構成。 |
|StartPartitionDataLoss |string、既定値は "Admin" |動的| パーティションでデータ損失を誘発します。 |
|StartPartitionQuorumLoss |string、既定値は "Admin" |動的| パーティションでクォーラム損失を誘発します。 |
|StartPartitionRestart |string、既定値は "Admin" |動的| パーティションのレプリカの一部またはすべてを同時に再起動します。 |
|StopChaos |string、既定値は "Admin" |動的| 混乱を停止します (開始されている場合)。 |
|ToggleVerboseServicePlacementHealthReporting | string、既定値は "Admin\|\|User" |動的| 詳細な ServicePlacement HealthReporting を切り替えるためのセキュリティ構成。 |
|UnprovisionApplicationType |string、既定値は "Admin" |動的| アプリケーションの種類のプロビジョニングを解除するためのセキュリティ構成。 |
|UnprovisionFabric |string、既定値は "Admin" |動的| MSI またはクラスター マニフェストのプロビジョニングを解除するためのセキュリティ構成。 |
|UnreliableTransportControl |string、既定値は "Admin" |動的| 追加および削除動作の信頼性のないトランスポート。 |
|UpdateService |string、既定値は "Admin" |動的|サービス更新のセキュリティ構成。 |
|UpgradeApplication |string、既定値は "Admin" |動的| アプリケーションのアップグレードを開始または中断するためのセキュリティ構成。 |
|UpgradeComposeDeployment|string、既定値は "Admin"| 動的|compose デプロイをアップグレードします |
|UpgradeFabric |string、既定値は "Admin" |動的| クラスターのアップグレードを開始するためのセキュリティ構成。 |
|アップロード |string、既定値は "Admin" | 動的|イメージ ストア クライアントのアップロード操作のセキュリティ構成。 |

## <a name="securityclientcertificateissuerstores"></a>Security/ClientCertificateIssuerStores

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap、既定値は None |動的|クライアント証明書の X509 発行者証明書ストア。名前 = clientIssuerCN、値 = ストアのコンマ区切りの一覧 |

## <a name="securityclientx509names"></a>Security/ClientX509Names

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap、既定値は None|動的|これは "Name" と "Value" のペアの一覧です。 それぞれの "Name" は、サブジェクトの共通名またはクライアント操作で承認された X509 証明書の DnsName です。 "Name" に対する "Value" は、発行者を固定する証明書のサムプリントのカンマで区切られた一覧であり、空でない場合は、クライアント証明書の直接的な発行者が一覧に含まれている必要があります。|

## <a name="securityclustercertificateissuerstores"></a>Security/ClusterCertificateIssuerStores

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap、既定値は None |動的|クラスター証明書の X509 発行者証明書ストア。名前 = clusterIssuerCN、値 = ストアのコンマ区切りの一覧 |

## <a name="securityclusterx509names"></a>Security/ClusterX509Names

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap、既定値は None|動的|これは "Name" と "Value" のペアの一覧です。 それぞれの "Name" は、サブジェクトの共通名またはクラスター操作で承認された X509 証明書の DnsName です。 "Name" に対する "Value" は、発行者を固定する証明書のサムプリントのカンマで区切られた一覧であり、空でない場合は、クラスター証明書の直接的な発行者が一覧に含まれている必要があります。|

## <a name="securityservercertificateissuerstores"></a>Security/ServerCertificateIssuerStores

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap、既定値は None |動的|サーバー証明書の X509 発行者証明書ストア。名前 = serverIssuerCN、値 = ストアのコンマ区切りの一覧 |

## <a name="securityserverx509names"></a>Security/ServerX509Names

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap、既定値は None|動的|これは "Name" と "Value" のペアの一覧です。 それぞれの "Name" は、サブジェクトの共通名またはサーバー操作で承認された X509 証明書の DnsName です。 "Name" に対する "Value" は、発行者を固定する証明書のサムプリントのカンマで区切られた一覧であり、空でない場合は、サーバー証明書の直接的な発行者が一覧に含まれている必要があります。|

## <a name="setup"></a>セットアップ

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|ContainerNetworkName|string、既定値は ""| 静的 |コンテナー ネットワークを設定するときに使用するネットワーク名。|
|ContainerNetworkSetup|ブール値、既定値は FALSE| 静的 |コンテナー ネットワークを設定するかどうか。|
|FabricDataRoot |string | 禁止 |Service Fabric のデータ ルート ディレクトリ。 Azure の場合、既定値は d:\svcfab です。 |
|FabricLogRoot |string | 禁止 |Service Fabric のログ ルート ディレクトリ。 このディレクトリには、SF のログとトレースが配置されます。 |
|NodesToBeRemoved|string、既定値は ""| 動的 |構成のアップグレードの一環として削除する必要があるノード (スタンドアロンのデプロイのみ)。|
|ServiceRunAsAccountName |string | 禁止 |Fabric ホスト サービスを実行するアカウント名。 |
|SkipContainerNetworkResetOnReboot|ブール値、既定値は FALSE|禁止|再起動時のコンテナー ネットワークのリセットをスキップするかどうか。|
|SkipFirewallConfiguration |ブール値、既定値は false | 禁止 |ファイアウォールの設定をシステムで設定する必要があるかどうかを指定します。 これは、Windows ファイアウォールを使用する場合のみ適用されます。 サード パーティ製のファイアウォールを使用する場合、システムとアプリケーション用のポートを開く必要があります。 |

## <a name="tokenvalidationservice"></a>TokenValidationService

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|プロバイダー |string、既定値は "DSTS" |静的|有効にするトークン検証プロバイダーのコンマ区切りリスト (有効なプロバイダー:DSTS、AAD)。 現時点では、1 つのプロバイダーだけをいつでも有効にすることができます。 |

## <a name="traceetw"></a>Trace/Etw

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|Level |int、既定値は 4 | 動的 |Trace/Etw のレベルには、1、2、3、4 の値を指定できます。 サポート対象となるために、トレース レベルは 4 のままにする必要があります。 |

## <a name="transactionalreplicator"></a>TransactionalReplicator

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | 時間 (秒単位)、既定値は 0.015 | 静的 | timespan を秒単位で指定します。 操作を受信してから受信確認を返すまで、レプリケーターが待機する時間を指定します。 この期間に受信した他の操作の受信確認は 1 つのメッセージで返されます。これにより、ネットワーク トラフィックは減少しますが、レプリケーターのスループットが低下する可能性があります。 |
|MaxCopyQueueSize |uint、既定値は 16384 | 静的 |レプリケーション操作を保持するキューの初期サイズの最大値を定義します。 この値は 2 の累乗にする必要があります。 ランタイムにキューがこのサイズに達すると、プライマリ レプリケーターとセカンダリ レプリケーターの間で操作が調整されます。 |
|MaxPrimaryReplicationQueueMemorySize |uint、既定値は 0 | 静的 |プライマリ レプリケーション キューの最大値 (バイト単位)。 |
|MaxPrimaryReplicationQueueSize |uint、既定値は 8192 | 静的 |プライマリ レプリケーション キューに存在する可能性がある操作の最大数。 この値は 2 の累乗にする必要があります。 |
|MaxReplicationMessageSize |uint、既定値は 52428800 | 静的 | レプリケーション操作の最大メッセージ サイズ。 既定値は 50 MB です。 |
|MaxSecondaryReplicationQueueMemorySize |uint、既定値は 0 | 静的 |セカンダリ レプリケーション キューの最大値 (バイト単位)。 |
|MaxSecondaryReplicationQueueSize |uint、既定値は 16384 | 静的 |セカンダリ レプリケーション キューに存在する可能性がある操作の最大数。 この値は 2 の累乗にする必要があります。 |
|ReplicatorAddress |string、既定値は "localhost:0" | 静的 | 文字列 "IP:Port" の形式のエンドポイント。この文字列は、Windows Fabric Replicator が操作を送受信するために、他のレプリカとの接続を確立する際に使用されます。 |

## <a name="transport"></a>トランスポート
| **パラメーター** | **使用できる値** |**アップグレード ポリシー** |**ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|TimeSpan、既定値は Common::TimeSpan::FromSeconds(60)|静的|timespan を秒単位で指定します。 受信および受け入れ側の両方における接続設定のタイムアウト (セキュア モードでのセキュリティ ネゴシエーションを含む) |
|FrameHeaderErrorCheckingEnabled|ブール値、既定値は TRUE|静的|非セキュア モードのフレーム ヘッダーに対するエラー チェックの既定設定。この設定は、コンポーネント設定でオーバーライドされます。 |
|MessageErrorCheckingEnabled|ブール値、既定値は FALSE|静的|非セキュア モードのメッセージ ヘッダーと本文に対するエラー チェックの既定設定。この設定は、コンポーネント設定でオーバーライドされます。 |
|ResolveOption|string、既定値は "unspecified"|静的|FQDN の解決方法を決定します。  有効な値は "未指定/ipv4/ipv6" です。 |
|SendTimeout|TimeSpan、既定値は Common::TimeSpan::FromSeconds(300)|動的|timespan を秒単位で指定します。 スタック接続を検出するためのタイムアウトを送信します。 TCP エラー レポートは、一部の環境では信頼できません。 使用可能なネットワーク帯域幅と送信データのサイズ (\*MaxMessageSize\/\*SendQueueSizeLimit) に従って調整する必要があります。 |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|AutoupgradeEnabled | ブール値、既定値は true |静的| 目標状態ファイルに基づく自動ポーリングとアップグレード アクション。 |
|AutoupgradeInstallEnabled|ブール値、既定値は FALSE|静的|目標状態ファイルに基づくコード アップグレード アクションの自動ポーリング、プロビジョニング、およびインストール。|
|GoalStateExpirationReminderInDays|int、既定値は 30|静的|目標状態のアラームを表示し始める残り日数を設定します。|
|MinReplicaSetSize |int、既定値は 0 |静的 |UpgradeOrchestrationService の MinReplicaSetSize。|
|PlacementConstraints | string、既定値は "" |静的| UpgradeOrchestrationService の PlacementConstraints。 |
|QuorumLossWaitDuration | 時間 (秒単位)、既定値は MaxValue |静的| timespan を秒単位で指定します。 UpgradeOrchestrationService の QuorumLossWaitDuration。 |
|ReplicaRestartWaitDuration | 時間 (秒単位)、既定値は 60 分|静的| timespan を秒単位で指定します。 UpgradeOrchestrationService の ReplicaRestartWaitDuration。 |
|StandByReplicaKeepDuration | 時間 (秒単位)、既定値は 60*24*7 分 |静的| timespan を秒単位で指定します。 UpgradeOrchestrationService の StandByReplicaKeepDuration。 |
|TargetReplicaSetSize |int、既定値は 0 |静的 |UpgradeOrchestrationService の TargetReplicaSetSize。 |
|UpgradeApprovalRequired | ブール値、既定値は false | 静的|コードのアップグレードを続行する前に管理者の承認を必須にするための設定。 |

## <a name="upgradeservice"></a>UpgradeService

| **パラメーター** | **使用できる値** | **アップグレード ポリシー** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- | --- |
|BaseUrl | string、既定値は "" |静的|UpgradeService の BaseUrl。 |
|ClusterId | string、既定値は "" |静的|UpgradeService の ClusterId。 |
|CoordinatorType | string、既定値は "WUTest"|禁止|UpgradeService の CoordinatorType。 |
|MinReplicaSetSize | int、既定値は 2 |禁止| UpgradeService の MinReplicaSetSize。 |
|OnlyBaseUpgrade | ブール値、既定値は false |動的|UpgradeService の OnlyBaseUpgrade。 |
|PlacementConstraints |string、既定値は "" |禁止|アップグレード サービスの PlacementConstraints。 |
|PollIntervalInSeconds|Timespan、既定値は Common::TimeSpan::FromSeconds(60) |動的|timespan を秒単位で指定します。 ARM 管理操作のための UpgradeService ポーリングの間隔。 |
|TargetReplicaSetSize | int、既定値は 3 |禁止| UpgradeService の TargetReplicaSetSize。 |
|TestCabFolder | string、既定値は "" |静的| UpgradeService の TestCabFolder。 |
|X509FindType | string、既定値は ""|動的| UpgradeService の X509FindType。 |
|X509FindValue | string、既定値は "" |動的| UpgradeService の X509FindValue。 |
|X509SecondaryFindValue | string、既定値は "" |動的| UpgradeService の X509SecondaryFindValue。 |
|X509StoreLocation | string、既定値は "" |動的| UpgradeService の X509StoreLocation。 |
|X509StoreName | string、既定値は "My"|動的|UpgradeService の X509StoreName。 |

## <a name="next-steps"></a>次の手順
詳細については、[Azure クラスターの構成のアップグレード](service-fabric-cluster-config-upgrade-azure.md)および[スタンドアロン クラスターの構成のアップグレード](service-fabric-cluster-config-upgrade-windows-server.md)に関するページを参照してください。
