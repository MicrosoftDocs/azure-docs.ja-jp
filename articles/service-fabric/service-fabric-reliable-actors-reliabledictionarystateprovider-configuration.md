---
title: ReliableDictionaryActorStateProvider の設定を変更する
description: Azure Service Fabric の ReliableDictionaryActorStateProvider という型のステートフル アクターの構成について説明します。
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: fbd6f7cd3ade753c659464522408aa715cce48f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75609742"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Reliable Actors の構成 -- ReliableDictionaryActorStateProvider
ReliableDictionaryActorStateProvider の既定の構成を変更するには、対象のアクターの Config フォルダーの下にある Visual Studio パッケージ ルートに生成された settings.xml ファイルを変更します。

Azure Service Fabric ランタイムは settings.xml ファイルで定義済みのセクション名を検索し、基になるランタイム コンポーネントの作成中に構成値を使用します。

> [!NOTE]
> Visual Studio ソリューションで生成された settings.xml ファイルでは、次の構成のセクション名を削除/変更 **しない** でください。
> 
> 

ReliableDictionaryActorStateProvider の構成に影響を与えるグローバル設定もあります。

## <a name="global-configuration"></a>グローバル構成
グローバル構成は、クラスターのクラスター マニフェストの KtlLogger セクションで指定されています。 この構成を使用すると、共有ログの場所とサイズに加えて、ロガーによって使用されるグローバル メモリ制限を構成できます。 クラスター マニフェストを変更すると、ReliableDictionaryActorStateProvider を使用するすべてのサービスおよび信頼性できるステートフル サービスに影響があることに注意してください。

クラスター マニフェストは、クラスター内のすべてのノードとサービスに適用される設定と構成を保持する単一の XML ファイルです。 通常、このファイルは ClusterManifest.xml という名前です。 Get-ServiceFabricClusterManifest PowerShell コマンドを使用して、クラスターのクラスター マニフェストを確認できます。

### <a name="configuration-names"></a>構成名
| 名前 | ユニット | 既定値 | 解説 |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |キロバイト |8388608 |ロガー書き込みバッファー メモリ プールに対してカーネル モードで割り当てる最小 KB 数。 このメモリ プールは、ディスクに書き込む前の状態情報のキャッシュに使用されます。 |
| WriteBufferMemoryPoolMaximumInKB |キロバイト |制限なし |ロガー書き込みバッファー メモリ プールを拡張できる最大サイズ。 |
| SharedLogId |GUID |"" |クラスター内のすべてのノードの Reliable Services のうち、サービス固有の構成で SharedLogId が指定されていないすべてのサービスによって使用される既定の共有ログ ファイルの指定に使用される、一意の GUID を指定します。 SharedLogId を指定する場合は、SharedLogPath も指定する必要があります。 |
| SharedLogPath |完全修飾パス名 |"" |クラスター内のすべてのノードの Reliable Services のうち、サービス固有の構成で SharedLogPath が指定されていないすべてのサービスによって使用される共有ログ ファイルの完全修飾パスを指定します。 SharedLogPath を指定した場合は、SharedLogId も指定する必要があります。 |
| SharedLogSizeInMB |メガバイト |8192 |共有ログ用に静的に割り当てるディスク領域の MB 数を指定します。 2048 以上の値を指定する必要があります。 |

### <a name="sample-cluster-manifest-section"></a>クラスター マニフェストのセクションの例
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>解説
ロガーには、非ページ カーネル メモリから割り当てられるメモリのグローバル プールがあります。ノード上のすべての Reliable Services は、Reliable Services レプリカに関連付けられている専用ログに書き込まれる前の状態データのキャッシュに、このプールを使用できます。 プールのサイズは、WriteBufferMemoryPoolMinimumInKB および WriteBufferMemoryPoolMaximumInKB の設定によって制御されます。 WriteBufferMemoryPoolMinimumInKB は、このメモリ プールの初期サイズと、メモリ プールを縮小できる最小サイズの両方を指定します。 WriteBufferMemoryPoolMaximumInKB は、メモリ プールが拡大できる最大サイズです。 開かれている各 Reliable Services レプリカは、WriteBufferMemoryPoolMaximumInKB 以下のシステムによって決定される大きさまで、メモリ プールのサイズを増やすことができます。 メモリ プールのメモリをさらに使用する必要がある場合、メモリの要求はメモリが使用可能になるまで遅延されます。 そのため、書き込みバッファー メモリ プールが特定の構成に対して小さすぎる場合、パフォーマンスが低下します。

SharedLogId と SharedLogPath の設定は常に一緒に使用されて、クラスター内のすべてのノードに対する既定の共有ログの GUID と場所を定義します。 既定の共有ログは、settings.xml でサービス固有の設定が指定されていないすべての Reliable Services に使用されます。 最善のパフォーマンスを得るには、競合が減るように、共有ログ ファイルを専用のディスクに配置する必要があります。

SharedLogSizeInMB では、すべてのノードで既定の共有ログに前もって割り当てるディスク領域の量を指定します。  SharedLogSizeInMB を指定するために SharedLogId と SharedLogPath を指定する必要はありません。

## <a name="replicator-security-configuration"></a>レプリケーターのセキュリティ構成
レプリケーション時に使用される通信チャネルをセキュリティで保護するには、レプリケーターのセキュリティ構成を使用します。 これは、サービスは互いのレプリケーション トラフィックを表示できないため、高可用性データもセキュリティ保護されることを意味します。
既定では、セキュリティ構成セクションが空の場合、レプリケーション セキュリティは有効になりません。

> [!IMPORTANT]
> Linux ノードでは、証明書は PEM 形式でなければなりません。 Linux での証明書の場所と構成について詳しくは、[Linux 上での証明書の構成](./service-fabric-configure-certificates-linux.md)に関する記事をご覧ください。 
> 

### <a name="section-name"></a>セクション名
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>レプリケーター構成
状態をローカルにレプリケートして永続化することでアクターの状態プロバイダーの状態の信頼性を高める必要があるレプリケーターを構成する場合は、レプリケーター構成を使用します。
既定の構成は Visual Studio テンプレートによって生成され、これで十分なはずです。 このセクションでは、レプリケーターのチューニングに使用できる追加の構成について説明します。

### <a name="section-name"></a>セクション名
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>構成名
| 名前 | ユニット | 既定値 | 解説 |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Seconds |0.015 |操作を受信してからプライマリに受信確認を返すまで、セカンダリでレプリケーターが待機する期間です。 この期間内で処理された操作に対して送信される他の受信確認は、1 つの応答として送信されます。 |
| ReplicatorEndpoint |該当なし |既定値なし - 必須パラメーター |プライマリとセカンダリのレプリケーターがレプリカ セットの他のレプリケーターと通信するために使用する IP アドレスとポートです。 これは、サービス マニフェストの TCP リソース エンドポイントを参照する必要があります。 サービス マニフェストでのエンドポイント リソース定義の詳細については、「 [サービス マニフェストにリソースを指定する](service-fabric-service-manifest-resources.md) 」を参照してください。 |
| MaxReplicationMessageSize |バイト |50 MB |1 つのメッセージで送信できるレプリケーション データの最大サイズです。 |
| MaxPrimaryReplicationQueueSize |操作数 |8192 |プライマリ キューの操作の最大数です。 操作は、プライマリ レプリケーターがすべてのセカンダリ レプリケーターから受信確認を受信した後に解放されます。 この値は 64 より大きく、2 のべき乗である必要があります。 |
| MaxSecondaryReplicationQueueSize |操作数 |16384 |セカンダリ キューの操作の最大数です。 操作は、永続性によってその状態の高可用性が実現されてから解放されます。 この値は 64 より大きく、2 のべき乗である必要があります。 |
| CheckpointThresholdInMB |MB |200 |その後で状態がチェックポイントされるログ ファイル領域の量。 |
| MaxRecordSizeInKB |KB |1024 |レプリケーターがログに書き込むことができるレコードの最大サイズです。 この値は 4 の倍数で 16 より大きい必要があります。 |
| OptimizeLogForLowerDiskUsage |Boolean |true |true の場合、NTFS スパース ファイルを使用してレプリカの専用ログ ファイルが作成されるように、ログが構成されます。 これにより、ファイルで使用される実際のディスク領域が削減されます。 false の場合、最適な書き込みパフォーマンスを提供する固定の割り当てがファイルに作成されます。 |
| SharedLogId |guid |"" |このレプリカで使用される共有ログ ファイルの識別に使用する一意の guid を指定します。 通常、サービスではこの設定を使用しないはずですが、 SharedLogId を指定した場合は、SharedLogPath も指定する必要があります。 |
| SharedLogPath |完全修飾パス名 |"" |このレプリカの共有ログ ファイルが作成される完全修飾パスを指定します。 通常、サービスではこの設定を使用しないはずですが、 SharedLogPath を指定した場合は、SharedLogId も指定する必要があります。 |

## <a name="sample-configuration-file"></a>サンプル構成ファイル
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```

## <a name="remarks"></a>解説
BatchAcknowledgementInterval パラメーターは、レプリケーションの待機時間を制御します。 値が '0' の場合、待機時間は最短になりますが、スループットに影響します (送信および処理が必要な受信確認メッセージが増え、それぞれに含まれる受信確認が少なくなります)。
BatchAcknowledgementInterval の値が大きいほど、全体的なレプリケーションのスループットが高くなり、操作の待機時間が長くなります。 これは、トランザクションのコミットの待機時間に直結します。

CheckpointThresholdInMB パラメーターは、レプリケーターがレプリカの専用ログ ファイルへの状態情報の保存に使用できるディスク領域の量を制御します。 この値を既定値よりも大きくすると、セットに新しいレプリカを追加したときの再構成時間が短くなります。 これは、ログで使用できる操作履歴が増えることで部分的な状態転送が発生するためです。 ただし、クラッシュ後のレプリカの復旧時間は長くなる可能性があります。

OptimizeForLowerDiskUsage を true に設定すると、ログ ファイルの領域が過剰プロビジョニングされるため、アクティブなレプリカはログ ファイルにより多くの状態情報を保存できますが、非アクティブなレプリカで使用されるディスク領域は少なくなります。 これにより、ノードでより多くのレプリカをホストできます。 OptimizeForLowerDiskUsage を false に設定すると、状態情報はより迅速にログ ファイルに書き込まれます。

MaxRecordSizeInKB 設定は、レプリケーターがログ ファイルに書き込むことのできるレコードの最大サイズを定義します。 ほとんどすべてのケースで、最適なレコードのサイズは既定の 1,024 KB ですが、 サービスで大きなデータ項目を状態情報に含めようとしている場合、この値を増やさなければならない場合があります。 小さいレコードは小さいレコードに必要な領域しか使用しないため、MaxRecordSizeInKB を 1024 より小さくしてもほとんど効果はありません。 これを変更する必要があるのは、まれなケースだけであると予想されます。

SharedLogId と SharedLogPath の設定は常に一緒に使用して、サービスがノードの既定の共有ログとは別の共有ログを使用できるようにします。 最適な効率を得るため、できるだけ多くのサービスで同じ共有ログを指定してください。 共有ログ ファイルは、ヘッドの移動の競合が減るように、共有ログ ファイル専用に使用されるディスクに配置する必要があります。 これを変更する必要があるのは、まれなケースだけであると予想されます。

