---
title: Azure Service Fabric CLI- sfctl | Microsoft Docs
description: Service Fabric CLI sfctl のコマンドについて説明します。
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: c195d0c4250022102e735cf584370278e354bc41
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666677"
---
# <a name="sfctl"></a>sfctl
Service Fabric のクラスターとエンティティを管理するためのコマンドです。 このバージョンは Service Fabric 6.4 ランタイムと互換性があります。

コマンドは、名詞と動詞のパターンに従います。 詳しくはサブグループをご覧ください。

## <a name="subgroups"></a>サブグループ
|サブグループ|説明|
| --- | --- |
| [application](service-fabric-sfctl-application.md) | アプリケーションおよびアプリケーションの種類を作成、削除、および管理します。 |
| [chaos](service-fabric-sfctl-chaos.md) | chaos テスト サービスの開始、停止、レポートを実行します。 |
| [cluster](service-fabric-sfctl-cluster.md) | Service Fabric クラスターの選択、管理、および操作をします。 |
| [compose](service-fabric-sfctl-compose.md) | Docker Compose アプリケーションを作成、削除、および管理します。 |
| [container](service-fabric-sfctl-container.md) | クラスター ノード上でコンテナー関連のコマンドを実行します。 |
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

## <a name="next-steps"></a>次の手順
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。