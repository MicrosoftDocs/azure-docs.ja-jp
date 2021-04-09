---
title: Azure Service Fabric CLI- sfctl
description: Azure Service Fabric のコマンド ライン インターフェイスである sfctl について説明します。 サブグループに対するコマンドの一覧が含まれています。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fc317345155a6807a20d342e2cefd0701b20f180
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257055"
---
# <a name="sfctl"></a>sfctl
Service Fabric のクラスターとエンティティを管理するためのコマンドです。 このバージョンは Service Fabric 7.0 ランタイムと互換性があります。

コマンドは、名詞と動詞のパターンに従います。 詳しくはサブグループをご覧ください。

## <a name="subgroups"></a>サブグループ
|サブグループ|説明|
| --- | --- |
| [application](service-fabric-sfctl-application.md) | アプリケーションおよびアプリケーションの種類を作成、削除、および管理します。 |
| [chaos](service-fabric-sfctl-chaos.md) | chaos テスト サービスの開始、停止、レポートを実行します。 |
| [cluster](service-fabric-sfctl-cluster.md) | Service Fabric クラスターの選択、管理、および操作を行います。 |
| [compose](service-fabric-sfctl-compose.md) | Docker Compose アプリケーションを作成、削除、および管理します。 |
| [container](service-fabric-sfctl-container.md) | クラスター ノード上でコンテナー関連のコマンドを実行します。 |
| [events](service-fabric-sfctl-events.md) | イベント ストアからイベントを取得します (EventStore サービスが既にインストールされている場合)。 |
| [is](service-fabric-sfctl-is.md) | クエリを実行し、インフラストラクチャ サービスにコマンドを送信します。 |
| [mesh](service-fabric-sfctl-mesh.md) | Azure Service Fabric Mesh アプリケーションを削除および管理します。 |
| [node](service-fabric-sfctl-node.md) | クラスターを形成するノードを管理します。 |
| [partition](service-fabric-sfctl-partition.md) | 任意のサービスで、クエリを実行し、パーティションを管理します。 |
| [property](service-fabric-sfctl-property.md) | Service Fabric の名前でプロパティを格納およびクエリします。 |
| [replica](service-fabric-sfctl-replica.md) | サービス パーティションに属しているレプリカを管理します。 |
| [rpm](service-fabric-sfctl-rpm.md) | クエリを実行し、修復マネージャー サービスにコマンドを送信します。 |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | スタンドアロンの Service Fabric クラスターを管理します。 |
| [service](service-fabric-sfctl-service.md) | サービス、サービスの種類、およびサービス パッケージを作成、削除、管理します。 |
| [設定](service-fabric-sfctl-settings.md) | sfctl のこのインスタンスに対してローカルな設定を構成します。 |
| [store](service-fabric-sfctl-store.md) | クラスター イメージ ストアで基本的なファイル レベルの操作を実行します。 |

## <a name="next-steps"></a>次のステップ
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](./scripts/sfctl-upgrade-application.md)を使用して、Service Fabric CLI の使用方法を学習します。
