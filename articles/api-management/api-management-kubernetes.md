---
title: Azure Kubernetes Service にデプロイされたマイクロサービスで Azure API Management を使用する | Microsoft Docs
description: この記事では、AKS で API Management をデプロイするためのオプションについて説明します
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: apimpm
ms.openlocfilehash: 293a47bc3e8499d7eda4e64bb68bc95eb4c85ab0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98108400"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Azure Kubernetes Service にデプロイされたマイクロサービスで Azure API Management を使用する

マイクロサービスは API を構築するのに最適です。 [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) (AKS) を使うと、クラウドに[マイクロサービス ベースのアーキテクチャ](/azure/architecture/guide/architecture-styles/microservices)をすばやくデプロイして運用することができます。 その後、[Azure API Management](https://aka.ms/apimrocks) (API Management) を利用して、内部および外部で使用するためにマイクロサービスを API として公開できます。 この記事では、AKS で API Management をデプロイするためのオプションについて説明します。 Kubernetes、API Management、Azure ネットワークに関する基本的な知識があることを前提としています。 

## <a name="background"></a>バックグラウンド

マイクロサービスを使用するために API として公開する場合、マイクロサービスとそれらを使用するクライアント間の通信を管理するのが困難な場合があります。 認証、承認、調整、キャッシュ、変換、監視など、さまざまな横断的問題があります。 これらの問題は、マイクロサービスの公開対象のクライアントが内部か外部かに関係なく当てはまります。 

[API ゲートウェイ](/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern) パターンによって、これらの問題に対処します。 マイクロサービスへのフロント ドアとして機能する API ゲートウェイにより、マイクロサービスからクライアントが分離され、セキュリティ レイヤーが追加されて、横断的問題を処理する負担を排除することでマイクロサービスの複雑さが軽減されます。 

[Azure API Management](https://aka.ms/apimrocks) は、API ゲートウェイのニーズを解決するためのターンキー ソリューションです。 時間をかけずに、マイクロサービス用の一貫性のある最新のゲートウェイを作成し、マイクロサービスを API として公開することができます。 ライフサイクル全体の API 管理ソリューションとして、API 検出、API ライフサイクル管理、API 分析のためのセルフサービス開発者ポータルなどの追加機能も提供されます。

AKS と API Management を一緒に使用すると、マイクロサービス ベースの API のデプロイ、公開、セキュリティ保護、監視、管理を行うためのプラットフォームが提供されます。 この記事では、API Management と組み合わせて AKS をデプロイするいくつかのオプションについて説明します。 

## <a name="kubernetes-services-and-apis"></a>Kubernetes Services と API

Kubernetes クラスターでは、コンテナーは[ポッド](https://kubernetes.io/docs/concepts/workloads/pods/pod/)にデプロイされます。ポッドは一時的なもので、ライフサイクルがあります。 ワーカー ノードが停止した場合、ノードで実行されているポッドは失われます。 そのため、ポッドの IP アドレスは常に変化する可能性があります。 それに頼ってポッドと通信することはできません。 

この問題を解決するため、Kubernetes には[サービス](https://kubernetes.io/docs/concepts/services-networking/service/)の概念が導入されています。 Kubernetes Service は、ポッドのロジック グループを定義する抽象化レイヤーであり、それらのポッドでの外部トラフィック露出、負荷分散、サービス検出を可能にします。 

API Management を通じてマイクロサービスを API として公開する準備ができたら、Kubernetes でのサービスを API Management での API にマップする方法について検討する必要があります。 特定の規則はありません。 最初に、ビジネス機能またはドメインをマイクロサービスに設計およびパーティション分割した方法によって異なります。 たとえば、サービスの背後にあるポッドが特定のリソース (顧客など) に対するすべての操作を担当する場合、サービスは 1 つの API にマップされることがあります。 リソースに対する操作が複数のマイクロサービスに分割されている場合 (GetOrder、PlaceOrder など)、API management では複数のサービスが 1 つの API に論理的に集約される可能性があります (図 1 を参照)。 

マッピングも進化する可能性があります。 API Management によってマイクロサービスの前にファサードが作成されるため、時間の経過と共にマイクロサービスをリファクタリングして適切なサイズにすることができます。 

![サービスを API にマップする](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>AKS の前面に API Management をデプロイする

AKS クラスターの前面に API Management をデプロイするには、いくつかのオプションがあります。 

AKS クラスターは常に仮想ネットワーク (VNet) にデプロイされますが、API Management インスタンスは VNet にデプロイする必要はありません。 API Management がクラスターの VNet 内に存在しない場合は、AKS クラスターで API Management が接続するためのパブリック エンドポイントを公開する必要があります。 その場合は、API Management と AKS の間の接続をセキュリティで保護する必要があります。 つまり、API Management 経由でのみクラスターにアクセスできるようにする必要があります。 それでは、オプションを見ていきましょう。 

### <a name="option-1-expose-services-publicly"></a>オプション 1: サービスをパブリックに公開する

AKS クラスター内のサービスは、NodePort、LoadBalancer、ExternalName の[サービスの種類](../aks/concepts-network.md)を使用して、パブリックに公開できます。 この場合、サービスにはパブリック インターネットから直接アクセスできます。 クラスターの前面に API Management をデプロイした後、マイクロサービスで認証を適用することにより、すべての受信トラフィックが API Management を通過するようにする必要があります。 たとえば、API Management では、クラスターに対して行われる各要求にアクセス トークンを組み込むことができます。 各マイクロサービスでは、要求を処理する前にトークンを検証する必要があります。 


これは、特にマイクロサービスに認証ロジックが既に実装されている場合は、AKS の前面に API Management をデプロイする最も簡単なオプションである場合があります。 

![サービスを直接公開する](./media/api-management-aks/direct.png)

長所:
* クラスターの VNet に挿入する必要がないため、API Management 側での構成が簡単です
* サービスが既に公開されていて、認証ロジックがマイクロサービスに既に存在する場合、AKS 側を変更する必要はありません

短所:
* サービス エンドポイントがパブリックに公開されるため、セキュリティ リスクの可能性があります
* 受信クラスター トラフィックに対する単一のエントリ ポイントがありません
* 重複する認証ロジックでマイクロサービスが複雑になります

### <a name="option-2-install-an-ingress-controller"></a>オプション 2:イングレス コントローラーをインストールする

オプション 1 の方が簡単かもしれませんが、前述のような重要な欠点があります。 API Management のインスタンスがクラスターの VNet に存在しない場合、相互 TLS 認証 (mTLS) は、トラフィックがセキュリティで保護され、API Management インスタンスと AKS クラスター間の双方向で信頼されていることを保証するための、堅牢な方法です。 

相互 TLS 認証は、API Management によって[ネイティブにサポート](./api-management-howto-mutual-certificates.md)されており、[イングレス コントローラーをインストールする](../aks/ingress-own-tls.md)ことによって Kubernetes で有効にすることができます (図 3)。 その結果、イングレス コントローラーで認証が行われるため、マイクロサービスが簡単になります。 さらに、API Management の IP アドレスをイングレスによる許可リストに追加することで、API Management だけがクラスターにアクセスできるようにすることもできます。  

 
![イングレス コントローラーを使って公開する](./media/api-management-aks/ingress-controller.png)


長所:
* クラスターの VNet に挿入する必要がなく、mTLS がネイティブにサポートされているため、API Management 側での構成が簡単です
* 受信クラスター トラフィックの保護がイングレス コントローラー レイヤーに一元化されます
* 公開さるクラスター エンドポイントが最小になるので、セキュリティ リスクが軽減されます

短所:
* インストール、イングレス コントローラーの構成と管理、mTLS に使用される証明書の管理に関する作業が増えるため、クラスター構成の複雑さが増大します
* イングレス コントローラーのエンドポイントがパブリックに公開されるため、セキュリティ リスクがあります


API Management を介して API を公開する場合、これらの API へのアクセスをセキュリティで保護する簡単で一般的な方法は、サブスクリプション キーを使用することです。 公開された API を使用する必要がある開発者は、これらの API を呼び出すときに、有効なサブスクリプション キーを HTTP 要求に含める必要があります。 そうしないと、呼び出しは API Management ゲートウェイによってすぐに拒否されます。 それらはバックエンド サービスに転送されません。

API にアクセスするためのサブスクリプション キーを取得するには、サブスクリプションが必要です。 サブスクリプションは、基本的にサブスクリプション キーのペアの名前付きコンテナーです。 公開されている API を使用する必要がある開発者は、サブスクリプションを取得できます。 API の公開元による承認は必要ありません。 API の公開元は、API ユーザー用のサブスクリプションを直接作成することもできます。

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>オプション 3:クラスター VNet 内に APIM をデプロイする

規制上の制約または厳密なセキュリティ要件を持つ顧客の場合、オプション 1 と 2 は、エンドポイントが公開されるため、ソリューションとして使用できないことがあります。 また、AKS クラスターとマイクロサービスを使用するアプリケーションが同じ VNet 内に存在し、すべての API トラフィックが VNet 内に留まるため、クラスターを公開する必要がない場合があります。 これらのシナリオでは、API Management をクラスターの VNet にデプロイできます。 [API Management Premium レベル](https://aka.ms/apimpricing)では、VNet のデプロイがサポートされています。 

[VNet への API Management のデプロイ](./api-management-using-with-vnet.md)には、外部と内部の 2 つのモードがあります。 

API コンシューマーがクラスター VNet に存在しない場合は、外部モード (図 4) を使用する必要があります。 このモードでは、API Management ゲートウェイはクラスター VNet に挿入されますが、外部ロード バランサー経由でパブリック インターネットからアクセスできます。 外部クライアントでマイクロサービスを使用できるようにしながら、クラスターを完全に隠すことができます。 さらに、ネットワーク セキュリティ グループ (NSG) などの Azure のネットワーク機能を使用して、ネットワーク トラフィックを制限することもできます。

![外部 VNet モード](./media/api-management-aks/vnet-external.png)

すべての API コンシューマーがクラスター VNet 内に存在する場合は、内部モード (図 5) を使用できます。 このモードでは、API Management ゲートウェイはクラスター VNet に挿入され、内部ロード バランサー経由でこの VNet 内からのみアクセスできます。 パブリック インターネットから API Management ゲートウェイまたは AKS クラスターにアクセスする方法はありません。 

![内部 VNet モード](./media/api-management-aks/vnet-internal.png)

 どちらの場合も、AKS クラスターは公開されません。 オプション 2 と比較すると、イングレス コントローラーは必要ない場合があります。 シナリオと構成によっては、API Management とマイクロサービスの間でやはり認証が必要になる場合があります。 たとえば、サービス メッシュが採用されている場合は、常に相互 TLS 認証が必要です。 

長所:
* AKS クラスターにパブリック エンドポイントがないため、最も安全なオプションです
* パブリック エンドポイントがないため、クラスターの構成が簡単になります
* 内部モードを使用すると、VNet 内の API Management と AKS の両方を隠ぺいできます
* ネットワーク セキュリティ グループ (NSG) などの Azure のネットワーク機能を使用して、ネットワーク トラフィックを制御できます

短所:
* VNet 内で動作させるため、API Management のデプロイと構成の複雑さが増します

## <a name="next-steps"></a>次のステップ

* [AKS でのアプリケーションに対するネットワークの概念](../aks/concepts-network.md)についてさらに学習します
* [仮想ネットワークで API Management を使用する方法](./api-management-using-with-vnet.md)についてさらに学習します
