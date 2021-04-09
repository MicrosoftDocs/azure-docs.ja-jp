---
title: KVSActorStateProvider の設定を変更する
description: KVSActorStateProvider 型の Azure Service Fabric ステートフル アクターの構成について説明します。
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: cdb115bd57cf3d5af4388f4efa03c2522feef9ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75609776"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Reliable Actors の構成 - KVSActorStateProvider
KVSActorStateProvider の既定の構成を変更するには、指定されたアクターの Config フォルダーの下にある Microsoft Visual Studio パッケージ ルートで生成された settings.xml ファイルを変更します。

Azure Service Fabric ランタイムは settings.xml ファイルで定義済みのセクション名を検索し、基になるランタイム コンポーネントの作成中に構成値を使用します。

> [!NOTE]
> Visual Studio ソリューションで生成された settings.xml ファイルでは、次の構成のセクション名を削除/変更 **しない** でください。
> 
> 

## <a name="replicator-security-configuration"></a>レプリケーターのセキュリティ構成
レプリケーション時に使用される通信チャネルをセキュリティで保護するには、レプリケーターのセキュリティ構成を使用します。 これは、サービスは互いのレプリケーション トラフィックを確認できないため、高可用性データもセキュリティで保護されることを意味します。
既定では、セキュリティ構成セクションが空の場合、レプリケーション セキュリティは有効になりません。

> [!IMPORTANT]
> Linux ノードでは、証明書は PEM 形式でなければなりません。 Linux での証明書の場所と構成について詳しくは、[Linux 上での証明書の構成](./service-fabric-configure-certificates-linux.md)に関する記事をご覧ください。 
> 

### <a name="section-name"></a>セクション名
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>レプリケーター構成
レプリケーター構成では、アクター状態プロバイダーの状態の信頼性を高める役割を担うレプリケーターを構成します。
既定の構成は Visual Studio テンプレートによって生成され、これで十分なはずです。 このセクションでは、レプリケーターのチューニングに使用できる追加の構成について説明します。

### <a name="section-name"></a>セクション名
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>構成名
| 名前 | ユニット | 既定値 | 解説 |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Seconds |0.015 |操作を受信してからプライマリに受信確認を返すまで、セカンダリでレプリケーターが待機する期間です。 この期間内で処理された操作に対して送信される他の受信確認は、1 つの応答として送信されます。 |
| ReplicatorEndpoint |該当なし |既定値なし - 必須パラメーター |プライマリとセカンダリのレプリケーターがレプリカ セットの他のレプリケーターと通信するために使用する IP アドレスとポートです。 これは、サービス マニフェストの TCP リソース エンドポイントを参照する必要があります。 サービス マニフェストでのエンドポイント リソースの定義の詳細については、「 [サービス マニフェストでのリソースの指定](service-fabric-service-manifest-resources.md) 」をご覧ください。 |
| RetryInterval |Seconds |5 |レプリケーターが操作の受信確認を受信しなかった場合に、メッセージを再送信するまでの期間です。 |
| MaxReplicationMessageSize |バイト |50 MB |1 つのメッセージで送信できるレプリケーション データの最大サイズです。 |
| MaxPrimaryReplicationQueueSize |操作数 |1024 |プライマリ キューの操作の最大数です。 操作は、プライマリ レプリケーターがすべてのセカンダリ レプリケーターから受信確認を受信した後に解放されます。 この値は 64 より大きく、2 のべき乗である必要があります。 |
| MaxSecondaryReplicationQueueSize |操作数 |2048 |セカンダリ キューの操作の最大数です。 操作は、永続性によってその状態の高可用性が実現されてから解放されます。 この値は 64 より大きく、2 のべき乗である必要があります。 |

## <a name="store-configuration"></a>ストア構成
レプリケートされている状態を維持するために使用されるローカル ストアを構成するには、ストア構成を使用します。
既定の構成は Visual Studio テンプレートによって生成され、これで十分なはずです。 このセクションでは、ローカル ストアのチューニングに使用できる追加の構成について説明します。

### <a name="section-name"></a>セクション名
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>構成名
| 名前 | ユニット | 既定値 | 解説 |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |ミリ秒 |200 |持続性のあるローカル ストア コミットに、バッチ処理の最大間隔を設定します。 |
| MaxVerPages |ページ数 |16384 |ローカル ストア データベースにおけるバージョン ページの最大数です。 未処理のトランザクションの最大数を決定します。 |

## <a name="sample-configuration-file"></a>サンプル構成ファイル
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
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

