---
title: "スタンドアロン クラスターの構成 | Microsoft Docs"
description: "この記事では、スタンドアロンまたはプライベート Service Fabric クラスターを構成する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 0c5ec720-8f70-40bd-9f86-cd07b84a219d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/17/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 8192f9e36ebadd41d93ec3c2fa61b05e342d5bc1
ms.lasthandoff: 03/29/2017


---
# <a name="configuration-settings-for-standalone-windows-cluster"></a>スタンドアロン Windows クラスターの構成設定
この記事では、***ClusterConfig.JSON*** ファイルを使用して、スタンドアロン Service Fabric クラスターを構成する方法について説明します。 このファイルを使って、Service Fabric ノードとその IP アドレス、クラスターの各種ノード、セキュリティ構成などの情報と、障害/アップグレード ドメインの観点でのスタンドアロン クラスターのネットワーク トポロジを指定できます。

[スタンドアロン Service Fabric パッケージをダウンロード](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)すると、ClusterConfig.JSON ファイルのサンプルが作業コンピューターにダウンロードされます。 名前に *DevCluster* が含まれているサンプルを使用すると、論理ノードのように、同じコンピューター上に 3 つのノードすべてが配置されたクラスターを作成できます。 これらのうち、少なくとも 1 つのノードをプライマリ ノードとしてマークする必要があります。 このクラスターは、開発またはテスト環境で役立ちますが、運用環境のクラスターとしてはサポートされていません。 名前に *MultiMachine* が含まれているサンプルでは、それぞれのノードが別々のマシン上に配置された運用環境品質クラスターを作成できます。 これらのクラスターのプライマリ ノードの数は、[信頼性レベル](#reliability)に基づきます。

1. *ClusterConfig.Unsecure.DevCluster.JSON* と *ClusterConfig.Unsecure.MultiMachine.JSON* は、それぞれ、セキュリティ保護されていないテストと運用クラスターを作成する方法を示しています。 
2. *ClusterConfig.Windows.DevCluster.JSON* と *ClusterConfig.Windows.MultiMachine.JSON* は、[Windows セキュリティ](service-fabric-windows-cluster-windows-security.md)を使用してセキュリティで保護されたテストまたは運用環境のクラスターを作成する方法を示しています。
3. *ClusterConfig.X509.DevCluster.JSON* と *ClusterConfig.X509.MultiMachine.JSON* は、[X509 証明書ベースのセキュリティ](service-fabric-windows-cluster-x509-security.md)を使用してセキュリティで保護されたテストまたは運用環境のクラスターを作成する方法を示しています。 

次に、***ClusterConfig.JSON*** ファイルの各セクションについて説明します。

## <a name="general-cluster-configurations"></a>一般的なクラスター構成
次の JSON スニペットに示すように、ここでは、クラスター固有の一般的な構成について説明します。

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",

**name** 変数にフレンドリ名を割り当てることで、Service Fabric クラスターにフレンドリ名を付けることができます。 **clusterConfigurationVersion** はクラスターのバージョン番号です。Service Fabric クラスターをアップグレードするたびに増やす必要があります。 ただし、**apiVersion** は既定値のままにしなければいけません。

<a id="clusternodes"></a>

## <a name="nodes-on-the-cluster"></a>クラスターのノード
次のスニペットに示すように、 **nodes** セクションを使用して Service Fabric クラスターのノードを構成できます。

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

Service Fabric クラスターには、少なくとも 3 つのノードを含める必要があります。 セットアップに従って、このセクションにさらに多くのノードを追加できます。 各ノードの構成設定を次の表に示します。

| **ノード構成** | **説明** |
| --- | --- |
| nodeName |ノードにフレンドリ名を付けることができます。 |
| iPAddress |コマンド ウィンドウを開き、「 `ipconfig`」と入力することで、ノードの IP アドレスがわかります。 IPV4 アドレスをメモし、そのアドレスを **iPAddress** 変数に割り当てます。 |
| nodeTypeRef |ノードごとに異なるノード タイプを割り当てることができます。 [node types](#nodetypes) は、後述のセクションで定義されています。 |
| faultDomain |障害ドメインにより、クラスター管理者は、共有される物理的な依存関係によって同時に障害が発生する物理ノードを定義できます。 |
| upgradeDomain |アップグレード ドメインは、Service Fabric のアップグレードのためにほぼ同時にシャットダウンされる一連のノードを表します。 アップグレード ドメインは物理的な要件によって制限されないため、どのノードをどのアップグレード ドメインに割り当てるかを選択できます。 |

## <a name="cluster-properties"></a>クラスターのプロパティ
ClusterConfig.JSON の **properties** セクションは、以下のようにクラスターの構成に使用します。

<a id="reliability"></a>

### <a name="reliability"></a>信頼性
**reliabilityLevel** セクションでは、クラスターのプライマリ ノードで実行できるシステム サービスのコピーの数を定義します。 これにより、これらのサービスの信頼性が向上するため、クラスターの信頼性が向上します。 この変数は、システム サービスのコピーが 3 個の場合は *Bronze*、5 個の場合は *Silver*、7 個の場合は *Gold*、9 個の場合は *Platinum* に設定できます。 次の例をご覧ください。

    "reliabilityLevel": "Bronze",

1 つのプライマリ ノードで実行されるシステム サービスのコピーは 1 つであるため、信頼性レベルが *Bronze* の場合は 3 個、*Silver* の場合は 5 個、*Gold* の場合は 7 個、*Platinum* の場合は 9 個のプライマリ ノードが少なくとも必要になります。

### <a name="diagnostics"></a>診断
次のスニペットに示すように、**diagnosticsStore** セクションを使用すると、ノードまたはクラスターの障害に関する診断とトラブルシューティングを可能にするパラメーターを構成できます。 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

**metadata** は、クラスターの診断の説明であり、セットアップに従って設定できます。 これらの変数は、ETW トレース ログ、クラッシュ ダンプ、パフォーマンス カウンターの収集に役立ちます。 ETW トレース ログの詳細については、[「Tracelog」](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx)および [「ETW トレース」](https://msdn.microsoft.com/library/ms751538.aspx)を参照してください。 [クラッシュ ダンプ](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/)、[パフォーマンス カウンター](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx)など、すべてのログを、コンピューターの **connectionString** フォルダーに送信できます。 *AzureStorage* を使用して診断を格納することもできます。 サンプル スニペットを次に示します。

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>セキュリティ
**security** セクションは、セキュリティで保護されたスタンドアロン Service Fabric クラスターに必要です。 次のスニペットは、このセクションの一部を示しています。

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

**metadata** は、セキュリティで保護されたクラスターの説明であり、セットアップに従って設定できます。 **ClusterCredentialType** と **ServerCredentialType** によって、クラスターとノードが実装するセキュリティの種類が決まります。 これらのパラメーターは、証明書ベースのセキュリティの場合は *X509* に設定し、Azure Active Directory ベースのセキュリティの場合は *Windows* に設定します。 **security** セクションの残りの部分はセキュリティの種類に基づきます。 **security** セクションの残りの部分の入力方法については、[スタンドアロン クラスターの証明書ベースのセキュリティ](service-fabric-windows-cluster-x509-security.md)または[スタンドアロン クラスターの Windows セキュリティ](service-fabric-windows-cluster-windows-security.md)に関する記事をご覧ください。

<a id="nodetypes"></a>

### <a name="node-types"></a>ノード タイプ
**nodeTypes** セクションでは、クラスターのノードのタイプを記述します。 次のスニペットに示すように、クラスターにはノード タイプを少なくとも 1 つは指定する必要があります。 

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

**name** はこのノード タイプのフレンドリ名です。 このノード タイプのノードを作成するには、[上記](#clusternodes)で説明したように、そのフレンドリ名をそのタイプの **nodeTypeRef** 変数に指定します。 ノード タイプごとに、使用する接続エンドポイントを定義します。 このクラスターの他のエンドポイントと競合しない限り、これらの接続エンドポイントの任意のポート番号を選択できます。 マルチノード クラスターでは、プライマリ ノードは、[**reliabilityLevel**](#reliability) に応じて 1 つまたは複数 (すなわち **isPrimary** が *true* に設定されている) 存在します。 **nodeTypes** と **reliabilityLevel** の値の詳細、およびプライマリ ノード タイプと非プライマリ ノード タイプについては、「[Service Fabric クラスターの容量計画に関する考慮事項](service-fabric-cluster-capacity.md)」を参照してください。 

#### <a name="endpoints-used-to-configure-the-node-types"></a>ノード タイプの構成に使用するエンドポイント
* *clientConnectionEndpointPort* は、クライアント API の使用時にクライアントがクラスターへの接続に使用するポートです。 
* *clusterConnectionEndpointPort* は、ノードが互いに通信するポートです。
* *leaseDriverEndpointPort* は、ノードがアクティブかどうか確認するためにクライアント リース ドライバーが使用するポートです。 
* *serviceConnectionEndpointPort* は、ノード上にデプロイされているアプリケーションとサービスが当該ノード上の Service Fabric クライアントとの通信に使用するポートです。
* *httpGatewayEndpointPort* は、Service Fabric Explorer がクラスターに接続するために使用するポートです。
* *ephemeralPorts* は、[OS が使用する動的ポート](https://support.microsoft.com/kb/929851)です。 Service Fabric がこれらの一部をアプリケーション ポートとして使用し、残りは OS で使用できます。 また、Service Fabric ではこの範囲が OS にある既存の範囲にマップされるので、サンプル JSON ファイルで指定した範囲はあらゆる目的に使用することができます。 開始ポートと終了ポートの差は 255 以上にする必要があります。 この範囲はオペレーティング システムで共有されるため、この差が少なすぎる場合は、競合が発生する可能性があります。 `netsh int ipv4 show dynamicport tcp` を実行して、構成されている動的ポートの範囲を確認してください。
* *applicationPorts* は、Service Fabric のアプリケーションで使用するポートです。 アプリケーション ポートの範囲は、アプリケーションのエンドポイント要求に対応できるように十分な大きさにする必要があります。 この範囲は、コンピューター上の動的なポートの範囲、つまり構成内の設定として *ephemeralPorts* の範囲から排他的にする必要があります。  Service Fabric は、新しいポートが必要なときにこれらを使用するだけでなく、これらのポートに対してファイアウォールを開く処理も行います。 
* *reverseProxyEndpointPort* は、省略可能なリバース プロキシ エンドポイントです。 詳細については、「[Service Fabric Reverse Proxy](service-fabric-reverseproxy.md)」(Service Fabric リバース プロキシ) を参照してください。 

### <a name="log-settings"></a>ログの設定
**fabricSettings** セクションでは、Service Fabric のデータとログのルート ディレクトリを設定できます。 これらのディレクトリは、クラスターの最初の作成時にのみカスタマイズできます。 このセクションのサンプル スニペットを次に示します。

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

非 OS ドライブは OS のクラッシュに対する信頼性が高いため、FabricDataRoot および FabricLogRoot として使用することをお勧めします。 データ ルートだけをカスタマイズすると、ログ ルートはデータ ルートの 1 つ下のレベルに配置されます。

### <a name="stateful-reliable-service-settings"></a>ステートフル Reliable Services の設定
**KtlLogger** セクションでは、Reliable Services のグローバル構成の設定を行うことができます。 これらの設定の詳細については、「[ステートフル Reliable Services の構成](service-fabric-reliable-services-configuration.md)」を参照してください。
次の例は、ステートフル サービスの信頼性の高いコレクションを戻すために作成される共有トランザクション ログを変更する方法を示しています。

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

## <a name="next-steps"></a>次のステップ
スタンドアロン クラスターのセットアップに従って ClusterConfig.JSON ファイルの構成が完了したら、[スタンドアロンの Service Fabric クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)に関する記事に従ってクラスターをデプロイした後、「[Service Fabric Explorer を使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)」に進むことができます。


