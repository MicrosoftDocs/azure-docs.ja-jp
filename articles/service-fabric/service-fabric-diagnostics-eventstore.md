---
title: Azure Service Fabric のイベント ストア | Microsoft Docs
description: Azure Service Fabric のイベント ストアについて
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/6/2019
ms.author: srrengar
ms.openlocfilehash: e7ae4c77f958bacabea50b7193817cd41ea54aa9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449783"
---
# <a name="eventstore-overview"></a>EventStore の概要

>[!NOTE]
>Service Fabric バージョン 6.4 の時点で、 EventStore API は現在、Azure 上で実行されている Windows クラスターでに対してのみ使用できます。 この機能を Linux およびスタンドアロン クラスターに移植する作業を進めています。

## <a name="overview"></a>概要

バージョン 6.2 で導入された EventStore サービスは、Service Fabric の監視オプションです。 EventStore では、任意の時点でのクラスターまたはワークロードの状態を理解する手段が提供されます。
EventStore は、クラスターからのイベントを保持するステートフルな Service Fabric サービスです。 イベントは、Service Fabric Explorer や REST API を介して公開されます。 EventStore では、クラスターに対してクエリが直接行われて、クラスター内の任意のエンティティについて診断データが取得されます。EventStore は、以下に役立てるために使用します。

* 開発またはテストにおける問題の診断や、監視パイプラインを使用できる場所の診断
* クラスターに対して行っている管理アクションが、正しく処理されていることの確認
* Service Fabric が特定のエンティティと対話している方法の "スナップショット" の取得

![EventStore](media/service-fabric-diagnostics-eventstore/eventstore.png)

EventStore で使用できるイベントの完全な一覧を確認するには、「[Service Fabric イベント](service-fabric-diagnostics-event-generation-operational.md)」を参照してください。

>[!NOTE]
>Service Fabric バージョン 6.4 の時点で、 EventStore API と UX は、Azure Windows クラスターに対して一般公開されています。 この機能を Linux およびスタンドアロン クラスターに移植する作業を進めています。

EventStore サービスでは、クラスター内のエンティティとエンティティ型のそれぞれで使用できるイベントのクエリを実行できます。 これは、以下のレベルでイベントのクエリを実行できることを意味します。
* クラスター: クラスター自体に固有のイベント (クラスターのアップグレードなど)
* ノード: ノード レベルのすべてのイベント
* ノード: `nodeName` によって示される 1 つのノードに固有のイベント
* アプリケーション: アプリケーション レベルのすべてのイベント
* アプリケーション: `applicationId` によって示される 1 つのアプリケーションに固有のイベント
* サービス: クラスター内のすべてのサービスのイベント
* サービス: `serviceId` によって示される特定のサービスのイベント
* パーティション: すべてのパーティションのイベント
* パーティション: `partitionId` によって示される特定のパーティションのイベント
* パーティション レプリカ: `partitionId` によって示される特定のパーティション内のすべてのレプリカ/インスタンスからのイベント
* パーティション レプリカ: `replicaId` および `partitionId` によって示される特定のレプリカ/インスタンスのイベント

API について詳しくは、[EventStore API のリファレンス](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)に関するページをご覧ください。

EventStore サービスは、クラスター内のイベントを関連付ける機能も備えています。 EventStore サービスは、相互に影響を与えた可能性がある異なるエンティティから同時に書き込まれたイベントを調べてそれらのイベントをリンクし、クラスター内で発生したアクティビティの原因の識別に役立てることができます。 たとえば、いずれかのアプリケーションが、誘発された変更を伴わず正常に動作しなくなった場合、EventStore で、プラットフォームによって公開されている他のイベントも調べて、これを `Error` または `Warning` イベントと関連付けることができます。 これは、より迅速な障害の検出と根本原因の分析に役立ちます。

## <a name="enable-eventstore-on-your-cluster"></a>クラスターで EventStore を有効にする

### <a name="local-cluster"></a>ローカル クラスター

[自分のクラスターの fabricSettings.json](service-fabric-cluster-fabric-settings.md) で、アドオン機能として EventStoreService を追加し、クラスターのアップグレードを実行します。

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster-version-65"></a>Azure クラスター バージョン 6.5 以降
Azure クラスターをバージョン 6.5 以降にアップグレードした場合、EventStore はクラスター上で自動的に有効になります。 オプトアウトするには、ご利用のクラスター テンプレートを次の手順で更新する必要があります。

* `2019-03-01` 以降の API バージョンを使用する 
* クラスター内の [プロパティ] セクションに次のコードを追加する
  ```json  
    "fabricSettings": [
      …
    ],
    "eventStoreServiceEnabled": false
  ```

### <a name="azure-cluster-version-64"></a>Azure クラスター バージョン 6.4

バージョン 6.4 を使用している場合、Azure Resource Manager テンプレートを編集して、EventStore サービスを有効にすることができます。 これを行うには、[クラスター構成のアップグレード](service-fabric-cluster-config-upgrade-azure.md)を実行して次のコードを追加します。また、PlacementConstraints を使用して EventStore サービスのレプリカを特定の NodeType (システム サービス専用の NodeType など) に置くことができます。 `upgradeDescription` セクションで、ノードでの再起動をトリガーする構成のアップグレードを構成します。 別の更新ではそのセクションを削除できます。

```json
    "fabricSettings": [
          …
          …
          …,
         {
            "name": "EventStoreService",
            "parameters": [
              {
                "name": "TargetReplicaSetSize",
                "value": "3"
              },
              {
                "name": "MinReplicaSetSize",
                "value": "1"
              },
              {
                "name": "PlacementConstraints",
                "value": "(NodeType==<node_type_name_here>)"
              }
            ]
          }
        ],
        "upgradeDescription": {
          "forceRestart": true,
          "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807",
          "healthCheckWaitDuration": "00:01:00",
          "healthCheckStableDuration": "00:01:00",
          "healthCheckRetryTimeout": "00:5:00",
          "upgradeTimeout": "1:00:00",
          "upgradeDomainTimeout": "00:10:00",
          "healthPolicy": {
            "maxPercentUnhealthyNodes": 100,
            "maxPercentUnhealthyApplications": 100
          },
          "deltaHealthPolicy": {
            "maxPercentDeltaUnhealthyNodes": 0,
            "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0,
            "maxPercentDeltaUnhealthyApplications": 0
          }
        }
```


## <a name="next-steps"></a>次の手順
* EventStore API の概要 - [Azure Service Fabric クラスターでの EventStore API の使用](service-fabric-diagnostics-eventstore-query.md)
* EventStore によって提供されるイベントの一覧の詳細 - [Service Fabric イベント](service-fabric-diagnostics-event-generation-operational.md)
* Service Fabric での監視と診断の概要 - [Service Fabric の監視と診断に関するページ](service-fabric-diagnostics-overview.md)
* API 呼び出しの完全な一覧の表示 - [EventStore REST API リファレンス](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)
* クラスターの監視についての詳細を表示 - [クラスターとプラットフォームの監視](service-fabric-diagnostics-event-generation-infra.md)。
