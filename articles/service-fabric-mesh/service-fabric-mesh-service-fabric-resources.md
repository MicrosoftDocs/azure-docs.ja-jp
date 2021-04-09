---
title: Azure Service Fabric リソース モデルの概要
description: Service Fabric Mesh アプリケーションの定義を簡易化した方法である Service Fabric リソース モデルについて説明します。
author: vturecek
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 650755cf2692b613b2998e8ef7b706e09022b178
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626911"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Service Fabric リソース モデルの概要

> [!IMPORTANT]
> Azure Service Fabric Mesh のプレビューは廃止されました。 Service Fabric Mesh API を介した新しいデプロイは許可されなくなります。 既存のデプロイのサポートは、2021 年 4 月 28 日まで継続されます。
> 
> 詳細については、「[Azure Service Fabric Mesh のプレビューの廃止](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)」を参照してください。

Service Fabric リソース モデルには、Service Fabric Mesh アプリケーションを構成するリソースを定義する簡単な方法が示されています。 個々のリソースは任意の Service Fabric 環境にデプロイできます。  Service Fabric リソース モデルは、Azure Resource Manager モデルとも互換性があります。 現在、このモデルでは次の種類のリソースがサポートされています。

- アプリケーションとサービス
- ネットワーク
- ゲートウェイ
- シークレットとシークレット/値
- ボリューム

各リソースは、リソース ファイルで宣言的に示されています。リソース ファイルは Mesh アプリケーションを示す簡単な YAML または JSON ドキュメントであり、Service Fabric プラットフォームによってプロビジョニングされます。

## <a name="applications-and-services"></a>アプリケーションとサービス

アプリケーション リソースは、Mesh アプリケーションのデプロイ、バージョン管理、有効期間の単位です。 マイクロサービスを表す 1 つまたは複数のサービス リソースで構成されています。 さらに各サービス リソースは 1 つまたは複数のコード パッケージで構成されます。コード パッケージには、コード パッケージに関連付けられたコンテナー イメージの実行に必要なすべてが示されています。

![アプリとサービス][Image1]

サービス リソースでは以下を宣言します。

- コンテナー名、バージョン、およびレジストリ
- 各コンテナーに必要な CPU およびメモリ リソース
- ネットワーク エンドポイント
- ネットワーク、ボリューム、シークレットなどの他のリソースへの参照 

サービス リソースの一部として定義されたすべてのコード パッケージは、グループとしてまとめてデプロイおよびアクティブ化されます。 また、サービス リソースには、実行するサービスのインスタンス数も示され、それが依存する他のリソース (ネットワーク リソースなど) を参照します。

Mesh アプリケーションが複数のサービスで構成されている場合、同じノード上で同時に動作することは保証されません。 また、アプリケーションのアップグレード中に、単一のサービスのアップグレードが失敗すると、すべてのサービスが以前のバージョンにロールバックされます。

前述のように、各アプリケーション インスタンスのライフサイクルは個別に管理できます。 たとえば、1 つのアプリケーション インスタンスを他のアプリケーション インスタンスとは関係なくアップグレードすることができます。 通常、アプリケーションに含めるサービス数が多くなるほど、各サービスを個別に管理することが困難になるため、アプリケーション内のサービスはごく少数に抑えます。

## <a name="networks"></a>ネットワーク

ネットワーク リソースは、依存関係として参照している可能性があるアプリケーション リソースまたはサービス リソースとは関係なく、個別にデプロイできるリソースです。 アプリケーション用のネットワークを作成するために使用されます。 異なるアプリケーションの複数のサービスは、同じネットワークに属することができます。  詳細については、[Service Fabric Mesh アプリケーションのネットワーク](service-fabric-mesh-networks-and-gateways.md)に関するページを参照してください。

> [!NOTE]
> 現在のプレビューでは、アプリケーションとネットワーク間の 1 対 1 のマッピングのみをサポートしています。

![ネットワークとゲートウェイ][Image2]

## <a name="gateways"></a>ゲートウェイ
ゲートウェイ リソースで 2 つのネットワークが接続され、トラフィックがルーティングされます。  ゲートウェイによってサービスは外部クライアントと通信できるようになります。また、ゲートウェイはサービスへの入り口として機能します。  ゲートウェイは、Mesh アプリケーションとお客様の既存の仮想ネットワークを接続する場合にも使用できます。 詳細については、[Service Fabric Mesh アプリケーションのネットワーク](service-fabric-mesh-networks-and-gateways.md)に関するページを参照してください。

![ネットワークとゲートウェイ][Image2]

## <a name="secrets"></a>シークレット

シークレット リソースは、依存関係として参照されている可能性があるアプリケーション リソースまたはサービス リソースとは関係なく、デプロイできます。 アプリケーションに安全にシークレットを配信するために使用されます。 異なるアプリケーションの複数のサービスが同じシークレットの値を参照できます。

## <a name="volumes"></a>ボリューム

多くの場合、コンテナーは一時ディスクを利用可能にしています。 ただし、一時ディスクは一時的なので、コンテナーがクラッシュすると、新しい一時ディスクが取得され、情報は失われます。 また、一時ディスク上の情報を他のコンテナーと共有することは困難でもあります。 ボリュームは、コンテナー インスタンス内にマウントされ、状態を保持するために使用できるディレクトリです。 ボリュームを利用することで、汎用目的のファイル ストレージが提供され、通常のディスク I/O ファイル API を利用してファイルを読み書きすることができます。 ボリューム リソースは、ディレクトリのマウント方法とそのバッキング ストレージを示す宣言的な方法です (Azure Files Volume または Service Fabric Reliable Volume)。  詳細については、[状態の格納](service-fabric-mesh-storing-state.md#volumes)に関するページを参照してください。

![ディスク ボリュームに送信されるサービスを示す図。Service Fabric Reliable ボリュームに送信されてからレプリケートされたローカル ディスクへ、Azure Files ボリュームに送信されてからネットワーク ストレージへと送信されます。][Image3]

## <a name="programming-models"></a>プログラミング モデル
サービス リソースは、リソースに関連付けられたコード パッケージで参照されているコンテナー イメージのみを実行する必要があります。 コンテナー内の任意のフレームワークを使用して、任意の言語で記述した任意のコードを実行できます。Service Fabric Mesh 固有の API を理解したり使用したりする必要はありません。 

Service Fabric Mesh の外部でもアプリケーション コードは移植性があり、サービスの実装に使用される言語やフレームワークに関係なく、アプリケーションのデプロイは一貫したままです。 アプリケーションが ASP.NET Core、Go、または一連のプロセスとスクリプトのいずれであっても、Service Fabric Mesh リソースのデプロイ モデルは変わりません。 

## <a name="packaging-and-deployment"></a>パッケージとデプロイ

リソース モデルに基づく Service Fabric Mesh アプリケーションは Docker コンテナーとしてパッケージ化されています。  Service Fabric Mesh は共有されたマルチテナント環境であり、コンテナーによって高いレベルの分離を実現できます。  これらのアプリケーションは、JSON 形式または YAML 形式 (この場合は JSON に変換されます) を使用して記述されます。 Mesh アプリケーションを Azure Service Fabric Mesh にデプロイすると、アプリケーションの記述に使用された JSON は Azure Resource Manager テンプレートになります。 リソースは Azure リソースにマップされます。  Mesh アプリケーションを Service Fabric クラスター (スタンドアロンまたは Azure ホスト式) にデプロイすると、アプリケーションの記述に使用された JSON は Azure Resource Manager テンプレートと似た形式になります。  デプロイ後は、Mesh アプリケーションを HTTP インターフェイスまたは Azure CLI を介して管理できます。 


## <a name="next-steps"></a>次のステップ 
Service Fabric Mesh の詳細については、以下の概要ページを参照してください。
- [Service Fabric Mesh の概要](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
