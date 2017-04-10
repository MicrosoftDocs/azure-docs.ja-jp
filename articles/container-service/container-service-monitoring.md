---
title: "Azure DC/OS クラスターの監視 - Datadog | Microsoft Docs"
description: "Datadog を使用して Azure コンテナー サービス クラスターを監視します。 DC/OS の Web UI を使用して、クラスターに Datadog エージェントをデプロイします。"
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "コンテナー, DC/OS, Docker Swarm, Azure"
ms.assetid: ed00635d-4569-4f87-ab63-e307b2690b42
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 07/28/2016
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: 0aa9b3ae14f586fc79e6ebee898e794d526c19bd
ms.openlocfilehash: 8dfd53346b6198bf5ecab4b4b73bfed93fa46b18


---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>Datadog を使用した Azure Container Service DC/OS クラスターの監視
この記事では、Azure コンテナー サービス クラスター内のすべてのエージェント ノードに Datadog エージェントをデプロイする方法について説明します。 この構成を行うには、Datadog のアカウントが必要です。 

## <a name="prerequisites"></a>前提条件
Azure Container Service によって構成されたクラスターを[デプロイ](container-service-deployment.md)して[接続](container-service-connect.md)してください。 [Marathon UI](container-service-mesos-marathon-ui.md)の詳細を確認してください。 [http://datadoghq.com](http://datadoghq.com) にアクセスし、Datadog アカウントをセットアップしてください。 

## <a name="datadog"></a>Datadog
Datadog は監視サービスです。Azure コンテナー サービス クラスター内のコンテナーから監視データを収集することができます。 Datadog には、コンテナー内の特定のメトリックを表示できる Docker 統合ダッシュボードがあります。 コンテナーから収集されたメトリックは、CPU、メモリ、ネットワーク、I/O 別に整理されます。 メトリックは Datadog によってコンテナーとイメージに分割されます。 CPU 使用率の UI の例を以下に示します。

![Datadog UI](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Marathon を使用した Datadog デプロイメントの構成
以下の手順は、Marathon を使用して Datadog アプリケーションを構成し、クラスターにデプロイする方法を示しています。 

[http://localhost:80/](http://localhost:80/)で DC/OS の UI にアクセスします。 DC/OS の UI が表示されたら、左下にある [Universe (ユニバース)] に移動し、[Datadog] を検索して、[Install (インストール)] をクリックします。

![Datadog package within the DC/OS Universe](./media/container-service-monitoring/datadog1.png)

この構成作業には、Datadog アカウントまたは無料試用版アカウントが必要です。 Datadog Web サイトにログインしたら、左側の [Integrations (統合)]、[[APIs (API)]](https://app.datadoghq.com/account/settings#api) の順にクリックします。 

![Datadog API key](./media/container-service-monitoring/datadog2.png)

次に、DC/OS Universe の Datadog 構成に自分の API キーを入力します。 

![Datadog configuration in the DC/OS Universe](./media/container-service-monitoring/datadog3.png) 

上記の構成で、インスタンス数を「10000000」に設定します。これでクラスターに新しいノードが追加されると、そのノードに対して自動的にエージェントがデプロイされます。 これは暫定的な措置です。 パッケージをインストールしたら、Datadog Web サイトに戻り、[[Dashboards (ダッシュ ボード)]](https://app.datadoghq.com/dash/list) を見つけます。 ここには、カスタム ダッシュボードと統合ダッシュボードが表示されます。 [Docker ダッシュボード](https://app.datadoghq.com/screen/integration/docker)には、クラスターの監視に必要なすべてのコンテナー メトリックが表示されます。 




<!--HONumber=Jan17_HO4-->


