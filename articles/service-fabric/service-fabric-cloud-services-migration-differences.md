---
title: Cloud Services と Service Fabric の違い | Microsoft Docs
description: アプリケーションを Cloud Services から Service Fabric に移行する際の概念の概要。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 0b87b1d3-88ad-4658-a465-9f05a3376dee
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 4682e47e664384a6869e1a74e3de6d9083db082b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669454"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>アプリケーションの移行前に、Cloud Services と Service Fabric の違いについて学習する。
Microsoft Azure Service Fabric は、拡張性、信頼性の高い分散アプリケーションのための次世代クラウド アプリケーション プラットフォームです。 分散クラウド アプリケーションのパッケージ化、デプロイ、アップグレード、および管理用に、多数の新機能が導入されています。 

これは、アプリケーションを Cloud Services から Service Fabric に移行する入門ガイドです。 これでは主に Cloud Services と Service Fabric のアーキテクチャと設計の違いに重点を置いていています。

## <a name="applications-and-infrastructure"></a>アプリケーションとインフラストラクチャ
Cloud Services と Service Fabric の基本的な違いは、VM、ワークロード、およびアプリケーション間の関係です。 ここでのワークロードとは、特定のタスクを実行したり、サービスを提供したりするために記述されたコードと定義しています。

* **Cloud Services は、アプリケーションを VM としてデプロイすることと関係しています。**  記述するコードは、Web または Worker ロールなどの VM インスタンスに密結合されます。 Cloud Services にワークロードをデプロイすることは、ワークロードを実行する 1 つ以上の VM インスタンスをデプロイすることです。 アプリケーションと VM の区別はなく、従ってアプリケーションの正式な定義はありません。 アプリケーションは、Cloud Services デプロイメント内の Web または Worker ロール インスタンスのセット、または Cloud Services デプロイメント全体と考えることができます。 この例では、アプリケーションはロール インスタンスのセットとして示されています。

![Cloud Services アプリケーションおよびトポロジ][1]

* **Service Fabric は、Windows または Linux 上で Service Fabric を実行する既存の VM またはマシンにアプリケーションをデプロイすることと関係しています。** 記述するサービスは、Service Fabric アプリケーション プラットフォームの抽象化により、基礎になっているインフラストラクチャから完全に分離されています。これにより、アプリケーションを複数の環境にデプロイすることが可能になります。 Service Fabric のワークロードは "サービス" と呼ばれます。1 つ以上のサービスは Service Fabric アプリケーション プラットフォームで実行される正式に定義されたアプリケーションとしてグループ化されます。 1 つの Service Fabric クラスターに、複数のアプリケーションをデプロイできます。

![Service Fabric アプリケーションおよびトポロジ][2]

Service Fabric 自体は、Windows または Linux 上で実行されるアプリケーションのプラットフォーム層です。これに対して、Cloud Services は、ワークロードが接続された状態で、Azure が管理する VM をデプロイするためのシステムです。
Service Fabric アプリケーション モデルには、多数の利点があります。

* 高速にデプロイできます。 VM インスタンスの作成には、時間がかかります。 Service Fabric で VM は、Service Fabric アプリケーション プラットフォームをホストするクラスターを形成するために一度のみデプロイするので済みます。 このため、アプリケーション パッケージはクラスターに非常に高速にデプロイできます。
* 高密度にホスティングできます。 Cloud Services では、Worker ロール VM は、1 つのワークロードをホストします。 Service Fabric では、アプリケーションはそれらを実行する VM から分離されています。つまり、多数のアプリケーションを少数の VM にデプロイできるので、大規模なデプロイメントの全体コストを下げることができます。
* Service Fabric プラットフォームは、Azure でも、オンプレミスでも、Windows Server または Linux マシンがあるすべての環境で実行できます。 このプラットフォームは、基礎となっているインフラストラクチャに対する抽象化レイヤーをとなるので、アプリケーションをさまざまな環境で実行できます。 
* アプリケーションを分散管理できます。 Service Fabric は、分散アプリケーションをホストするプラットフォームであるのみではなく、ホストされている VM の個々のライフサイクルやマシンのライフ サイクルの管理を支援します。

## <a name="application-architecture"></a>アプリケーションのアーキテクチャ
Cloud Services アプリケーションのアーキテクチャには、アプリケーションの状態およびデータ、Cloud Services デプロイメントでの Web ロールと Worker ロール間の通信を管理するための、Service Bus、Azure テーブルと Blob ストレージ、SQL、Redis などの多数の外部サービスとの依存関係が含まれています。 完全な Cloud Services アプリケーションの例を、次に示します。  

![Cloud Services のアーキテクチャ][9]

Service Fabric アプリケーションの完全なアプリケーションでも、同じ外部サービスを使用する選択ができます。 この Cloud Services の例での、Cloud Services から Service Fabric への最も単純な移行パスは、Cloud Services デプロイメントを Service Fabric アプリケーションに置き換えるものです。このとき、全体的なアーキテクチャは同じに維持されます。 Web および Worker ロールは、最小限のコード変更で Service Fabric ステートレス サービスに移植できます。

![単純な移行後の Service Fabric のアーキテクチャ][10]

この段階で、システムは以前と同じように動作するはずです。 Service Fabric のステートフルな機能を利用して、可能な場合、外部の状態ストアをステートフル サービスとして内部に取り込むことができます。 これは、これまでの外部サービスと同等の機能をアプリケーションに持たせるために、カスタム サービスの記述が必要なため、Web ロールと Worker ロールを Service Fabric ステートレス サービスに単純に移行する場合よりも、より複雑です。 これには、次のような利点があります。 

* 外部の依存関係が削除される。 
* デプロイ モデル、管理モデル、アップグレード モデルが統合される。 

これらのサービスを内部に取り込んだ後のアーキテクチャの例は次のようになります。

![完全な移行後の Service Fabric のアーキテクチャ][11]

## <a name="communication-and-workflow"></a>通信とワークフロー
多くのクラウド サービス アプリケーションは、複数の階層で構成されます。 同様に、Service Fabric アプリケーションも、複数のサービス (通常は多数のサービス) で構成されます。 通信モデルには、一般的に、直接的な通信と、外部の持続性ストレージを経由した通信の 2 つがあります。

### <a name="direct-communication"></a>直接的な通信
直接的な通信では、階層は、各階層が公開しているエンドポイント経由で直接的に通信します。 これは Cloud Services などのステートレスな環境では、VM ロールのインスタンスをランダムに、または負荷分散のためにラウンドロビン方式で選択して、そのエンドポイントに直接接続することを意味します。

![Cloud Services の直接的な通信][5]

 直接的な通信は、Service Fabric の一般的な通信モデルです。 Service Fabric と Cloud Services の主な違いは、Cloud Services の場合は VM に接続するのに対し、Service Fabric では、サービスに接続します。 この違いは次のいくつかの理由により重要です。

* Service Fabric 内のサービスは、それをホストする VM にバインドされていません。サービスはクラスター内を移動する可能性があり、実際、次のようなさまざまな理由で移動することが予想されます: リソースの分散、フェールオーバー、アプリケーションとインフラストラクチャのアップグレード、配置または負荷の制約など。 これは、サービス インスタンスのアドレスが常に変わることを意味します。 
* Service Fabric の VM は、それぞれが固有のエンドポイントを持つ複数のサービスをホストできます。

Service Fabric には、サービスのエンドポイント アドレスを解決するために使用できる、命名サービスというサービス検出メカニズムがあります。 

![Service Fabric の直接的な通信][6]

### <a name="queues"></a>キュー
Cloud Services などのステートレスな環境での階層間の一般的な通信メカニズムでは、1 つの階層から別の階層に作業タスクを持続的に保存する外部ストレージ キューが使用されます。 一般的なシナリオは、Azure キューまたは Service Bus にジョブを送信する Web 層です。これでは Worker ロール インスタンスが、ジョブをキューから削除し、処理します。

![Cloud Services のキューの通信][7]

Service Fabric でも同じ通信モデルを使用できます。 これは、既存の Cloud Services アプリケーションを Service Fabric に移行するときに便利です。 

![Service Fabric の直接的な通信][8]

## <a name="next-steps"></a>次の手順
Cloud Services から Service Fabric への最も単純な移行パスは、Cloud Services デプロイメントのみを Service Fabric アプリケーションに置き換えるものです。これでは、アプリケーションの全体的なアーキテクチャはほぼ同じに維持されます。 次の記事では、Web または Worker ロールを Service Fabric ステートレス サービスに変換する方法について説明します。

* [単純な移行: Web または Worker ロールを Service Fabric ステートレス サービスに変換する](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
