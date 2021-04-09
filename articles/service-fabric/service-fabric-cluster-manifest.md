---
title: Azure Service Fabric スタンドアロン クラスターを構成する
description: スタンドアロンまたはオンプレミス Azure Service Fabric クラスターを構成する方法について説明します。
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: fd93263b38340ce080cca1aecb98f3a599ff1861
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91843160"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>スタンドアロン Windows クラスターの構成設定
この記事では、*ClusterConfig.json* ファイルで設定できる、スタンドアロン Azure Service Fabric クラスターの構成設定について説明します。 このファイルを使って、クラスターのノード、セキュリティ構成、およびフォールト ドメインとアップグレード ドメインに関するネットワーク トポロジに関する情報を指定します。  構成設定を変更または追加した後、[スタンドアロン クラスターを作成する](service-fabric-cluster-creation-for-windows-server.md)か、[スタンドアロン クラスターの構成をアップグレードする](service-fabric-cluster-config-upgrade-windows-server.md)かのいずれかを行うことができます。

[スタンドアロンの Service Fabric パッケージをダウンロード](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)すると、ClusterConfig.json サンプルも含まれます。 名前に "DevCluster" が含まれているサンプルは、論理ノードを使って、同じマシン上に 3 つのノードすべてが配置されたクラスターを作成します。 これらのノードのうち、少なくとも 1 つのノードをプライマリ ノードとしてマークする必要があります。 この種類のクラスターは、開発環境またはテスト環境で役立ちます。 運用環境クラスターとしてはサポートされていません。 名前に "MultiMachine" が含まれているサンプルでは、それぞれのノードが別々のマシン上に配置された運用環境グレードのクラスターを作成できます。 これらのクラスターのプライマリ ノードの数は、クラスターの[信頼性レベル](#reliability)に基づきます。 リリース 5.7 の API バージョン 05-2017 では、信頼性レベルのプロパティが削除されました。 代わりに、クラスターに最適な信頼性レベルがコードによって計算されます。 バージョン 5.7 以降では、このプロパティの値を設定しないでください。

* ClusterConfig.Unsecure.DevCluster.json と ClusterConfig.Unsecure.MultiMachine.json は、それぞれがセキュリティ保護されていないテスト クラスターと運用環境クラスターを作成する方法を示しています。

* ClusterConfig.Windows.DevCluster.json と ClusterConfig.Windows.MultiMachine.json は、[Windows セキュリティ](service-fabric-windows-cluster-windows-security.md)を使用してセキュリティ保護されたテスト クラスターまたは運用環境クラスターを作成する方法を示しています。

* ClusterConfig.X509.DevCluster.json と ClusterConfig.X509.MultiMachine.json は、[X509 証明書ベースのセキュリティ](service-fabric-windows-cluster-x509-security.md)を使用してセキュリティ保護されたテスト クラスターまたは運用環境クラスターを作成する方法を示しています。

次に、ClusterConfig.json ファイルの各セクションについて説明します。

## <a name="general-cluster-configurations"></a>一般的なクラスター構成
一般的なクラスター構成では、次の JSON スニペットで示すように、クラスター固有の広範な構成に対応できます。

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

フレンドリ名は、name 変数に割り当てることで、Service Fabric クラスターに付けることができます。 clusterConfigurationVersion は、クラスターのバージョン番号です。 Service Fabric クラスターをアップグレードするたびに大きくします。 apiVersion の設定は、既定値のままにします。

## <a name="nodes-on-the-cluster"></a>クラスターのノード
次のスニペットに示すように、nodes セクションを使用して Service Fabric クラスターのノードを構成できます。
```json
"nodes": [{
    "nodeName": "vm0",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD0"
}, {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType1",
    "faultDomain": "fd:/dc1/r1",
    "upgradeDomain": "UD1"
}, {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType2",
    "faultDomain": "fd:/dc1/r2",
    "upgradeDomain": "UD2"
}],
```

Service Fabric クラスターには、少なくとも 3 つのノードを含める必要があります。 設定に従って、このセクションにさらに多くのノードを追加できます。 各ノードの構成設定を次の表に示します。

| **ノード構成** | **説明** |
| --- | --- |
| nodeName |ノードにフレンドリ名を付けることができます。 |
| iPAddress |コマンド ウィンドウを開き、「 `ipconfig`」と入力することで、ノードの IP アドレスがわかります。 IPV4 アドレスをメモし、そのアドレスを iPAddress 変数に割り当てます。 |
| nodeTypeRef |ノードごとに異なるノード タイプを割り当てることができます。 [ノード タイプ](#node-types)は、後のセクションで定義されています。 |
| faultDomain |障害ドメインにより、クラスター管理者は、共有される物理的な依存関係によって同時に障害が発生する物理ノードを定義できます。 |
| upgradeDomain |アップグレード ドメインは、Service Fabric のアップグレードのためにほぼ同時にシャットダウンされる一連のノードを表します。 アップグレード ドメインは物理的な要件によって制限されないため、どのノードをどのアップグレード ドメインに割り当てるかを選択できます。 |

## <a name="cluster-properties"></a>クラスターのプロパティ
ClusterConfig.json の properties セクションは、以下のようにクラスターの構成に使用します。

### <a name="reliability"></a>[信頼性]
reliabilityLevel の概念を使用して、クラスターのプライマリ ノードで実行できる Service Fabric システム サービスのレプリカ数やインスタンス数を定義します。 これにより、これらのサービスの信頼性が向上するため、クラスターの信頼性が決まります。 この値は、クラスターの作成時およびアップグレード時にシステムによって計算されます。

### <a name="diagnostics"></a>診断
diagnosticsStore セクションでは、次のスニペットに示すように、ノードまたはクラスターの障害に関する診断とトラブルシューティングを可能にするパラメーターを構成できます。 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

metadata はクラスターの診断の説明であり、セットアップに従って設定できます。 これらの変数は、ETW トレース ログ、クラッシュ ダンプ、パフォーマンス カウンターの収集に役立ちます。 ETW トレース ログの詳細については、「[Tracelog](/windows-hardware/drivers/devtest/tracelog)」および「[ETW トレース](/dotnet/framework/wcf/samples/etw-tracing)」を参照してください。 [クラッシュ ダンプ](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf)、[パフォーマンス カウンター](/windows/win32/perfctrs/performance-counters-portal)など、すべてのログは、マシンの connectionString フォルダーに送信できます。 また、AzureStorage を使用して診断を格納することもできます。 次のサンプル スニペットを参照してください。

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "AzureStorage",
    "IsEncrypted": "false",
    "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
}
```

### <a name="security"></a>Security
security セクションは、セキュリティで保護されたスタンドアロン Service Fabric クラスターに必要です。 次のスニペットは、このセクションの一部を示しています。

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

metadata はセキュリティで保護されたクラスターの説明であり、セットアップに従って設定できます。 ClusterCredentialType と ServerCredentialType によって、クラスターとノードが実装するセキュリティの種類が決まります。 これらは、証明書ベースのセキュリティの場合は *X509* に、Active Directory ベースのセキュリティの場合は *Windows* に設定できます。 security セクションの残りの部分は、セキュリティの種類に基づきます。 security セクションの残りの部分の記述方法については、[スタンドアロン クラスターの証明書ベースのセキュリティ](service-fabric-windows-cluster-x509-security.md)に関する記事または[スタンドアロン クラスターの Windows セキュリティ](service-fabric-windows-cluster-windows-security.md)に関する記事を参照してください。

### <a name="node-types"></a>ノード タイプ
nodeTypes セクションでは、クラスターに含まれるノードのタイプを記述します。 次のスニペットに示すように、クラスターにはノード タイプを少なくとも 1 つ指定する必要があります。 

```json
"nodeTypes": [{
    "name": "NodeType0",
    "clientConnectionEndpointPort": "19000",
    "clusterConnectionEndpointPort": "19001",
    "leaseDriverEndpointPort": "19002"
    "serviceConnectionEndpointPort": "19003",
    "httpGatewayEndpointPort": "19080",
    "reverseProxyEndpointPort": "19081",
    "applicationPorts": {
        "startPort": "20575",
        "endPort": "20605"
    },
    "ephemeralPorts": {
        "startPort": "20606",
        "endPort": "20861"
    },
    "isPrimary": true
}]
```

name は、この特定のノード タイプのフレンドリ名です。 このノード タイプのノードを作成するには、[前述](#nodes-on-the-cluster)のとおり、そのフレンドリ名をそのノードの nodeTypeRef 変数に指定します。 ノード タイプごとに、使用する接続エンドポイントを定義します。 このクラスター内の他のエンドポイントと競合しない限り、これらの接続エンドポイントには任意のポート番号を選択することができます。 マルチノード クラスターでは、[reliabilityLevel](#reliability) に応じて、1 つまたは複数のプライマリ ノードが存在します (つまり、isPrimary が *true* に設定されています)。 プライマリ ノード タイプと非プライマリ ノード タイプの詳細については、「[Service Fabric クラスターの容量計画に関する考慮事項](service-fabric-cluster-capacity.md)」で nodeTypes と reliabilityLevel の情報を参照してください。 

#### <a name="endpoints-used-to-configure-the-node-types"></a>ノード タイプの構成に使用するエンドポイント
* clientConnectionEndpointPort は、クライアント API の使用時にクライアントがクラスターへの接続に使用するポートです。 
* clusterConnectionEndpointPort は、ノードが互いに通信するポートです。
* leaseDriverEndpointPort は、ノードがまだアクティブかどうかを確認するためにクラスター リース ドライバーが使用するポートです。 
* serviceConnectionEndpointPort は、ノード上にデプロイされているアプリケーションとサービスが当該ノード上の Service Fabric クライアントとの通信に使用するポートです。
* httpGatewayEndpointPort は、Service Fabric Explorer がクラスターに接続するために使用するポートです。
* ephemeralPorts により、[OS で使用される動的ポート](https://support.microsoft.com/kb/929851)がオーバーライドされます。 Service Fabric がこれらのポートの一部をアプリケーション ポートとして使用し、残りは OS に使用できます。 また、この範囲が OS にある既存の範囲にマップされるため、サンプル JSON ファイルで指定した範囲はあらゆる用途で使用できます。 開始ポートと終了ポートの差は 255 以上にしてください。 この範囲は OS と共有されるため、この差が小さすぎると競合が発生する場合があります。 構成されている動的ポートの範囲を確認するには、`netsh int ipv4 show dynamicport tcp` を実行します。
* applicationPorts は、Service Fabric のアプリケーションで使用するポートです。 アプリケーション ポートの範囲は、アプリケーションのエンドポイント要求に対応できるように十分な大きさにする必要があります。 この範囲は、マシン上の動的なポートの範囲 (つまり、構成で設定されている ephemeralPorts の範囲) とは排他的である必要があります。 Service Fabric は、新しいポートが必要になるとこれらのポートを使用し、これらのポートに対してファイアウォールを開く処理を行います。 
* reverseProxyEndpointPort は、省略可能なリバース プロキシ エンドポイントです。 詳細については、[Service Fabric のリバース プロキシ](service-fabric-reverseproxy.md)に関する記事を参照してください。 

### <a name="log-settings"></a>ログの設定
fabricSettings セクションでは、Service Fabric のデータとログのルート ディレクトリを設定できます。 これらのディレクトリをカスタマイズできるのは、最初にクラスターを作成するときだけです。 このセクションの次のサンプル スニペットを参照してください。

```json
"fabricSettings": [{
    "name": "Setup",
    "parameters": [{
        "name": "FabricDataRoot",
        "value": "C:\\ProgramData\\SF"
    }, {
        "name": "FabricLogRoot",
        "value": "C:\\ProgramData\\SF\\Log"
}]
```

非 OS ドライブは FabricDataRoot および FabricLogRoot として使用することをお勧めします。 OS が応答を停止する状況を回避することで、信頼性が向上します。 データ ルートだけをカスタマイズすると、ログ ルートはデータ ルートの 1 つ下のレベルに配置されます。

### <a name="stateful-reliable-services-settings"></a>ステートフル Reliable Services の設定
KtlLogger セクションでは、Reliable Services のグローバル構成設定を行うことができます。 これらの設定の詳細については、「[ステートフル Reliable Services の構成](service-fabric-reliable-services-configuration.md)」を参照してください。 次の例は、ステートフル サービス用に信頼性の高いコレクションを戻すために作成される共有トランザクション ログを変更する方法を示しています。

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="add-on-features"></a>アドオン機能
アドオン機能を構成するには、apiVersion を 04-2017 以降に構成し、addonFeatures を次のように構成します。

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```
使用可能なすべてのアドオン機能は、[Service Fabric REST API リファレンス](/rest/api/servicefabric/sfrp-model-addonfeatures)に関するページで確認できます。

### <a name="container-support"></a>コンテナー サポート
スタンドアロン クラスターの Windows Server コンテナーと Hyper-V コンテナーの両方でコンテナー サポートを有効にするには、DnsService アドオン機能を有効にする必要があります。

## <a name="next-steps"></a>次のステップ
スタンドアロン クラスターの設定に従って *ClusterConfig.json* ファイル全体を構成したら、クラスターをデプロイできます。 [スタンドアロン Service Fabric クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)に関するページの手順に従います。 

スタンドアロン クラスターをデプロイしている場合、[スタンドアロン クラスターの構成をアップグレード](service-fabric-cluster-config-upgrade-windows-server.md)することもできます。 

[Service Fabric Explorer を使用してクラスターを視覚化](service-fabric-visualizing-your-cluster.md)する方法を説明します。
