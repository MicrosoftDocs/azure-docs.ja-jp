---
title: (非推奨) Azure DC/OS クラスターの監視 - Dynatrace
description: Dynatrace で Azure Container Service (DC/OS) クラスターを監視します。 DC/OS ダッシュボードを使用して Dynatrace OneAgent をデプロイします。
services: container-service
author: MartinGoodwell
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 8f34a00d9256c288a2842e905c06d5336522eece
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001992"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>(非推奨) Dynatrace SaaS/Managed を使用した Azure Container Service DC/OS クラスターの監視

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

この記事では、Azure Container Service クラスターのすべてのエージェント ノードを監視するための [Dynatrace](https://www.dynatrace.com/) OneAgent のデプロイ方法を説明します。 この設定には、Dynatrace SaaS/Managed のアカウントが必要です。 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/Managed
Dynatrace は、変化の激しいコンテナーとクラスター環境向けの、クラウド ネイティブな監視ソリューションです。 リアルタイムの使用状況データを使用して、コンテナーのデプロイやメモリの割り当てを最適化できます。 ベースラインの自動設定、問題の関連付け、根本原因の検出をすることにより、アプリケーションやインフラストラクチャの問題を自動的に特定できます。

次の図は、Dynatrace の UI です。

![Dynatrace の UI](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>前提条件 
Azure Container Service によって構成されたクラスターに[デプロイ](container-service-deployment.md)して[接続](./../container-service-connect.md)します。 [Marathon UI](container-service-mesos-marathon-ui.md)の詳細を確認してください。 [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) に移動して、Dynatrace SaaS アカウントを設定します。  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Marathon を使用した Dynatrace デプロイメントの構成
以下の手順は、Marathon を使用して Dynatrace アプリケーションを構成し、クラスターにデプロイする方法を示しています。

1. [http://localhost:80/](http://localhost:80/) で DC/OS の UI にアクセスします。 DC/OS の UI を開いたら、**[Universe (ユニバース)]** タブに移動して、**Dynatrace** を検索します。

    ![Dynatrace in DC/OS Universe](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. この構成作業には、Dynatrace SaaS アカウントまたは無料試用版アカウントが必要です。 Dynatrace ダッシュボードにログインしたら、**[Deploy Dynatrace (Dynatrace のデプロイ)]** を選択します。

    ![Dynatrace Set up PaaS integration](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. そのページで、**[Set up PaaS integration (PaaS の統合設定)]** を選択します。 

    ![Dynatrace API token](./media/container-service-monitoring-dynatrace/api-token.png) 

4. DC/OS Universe の Dynatrace OneAgent の設定に API トークンを入力します。 

    ![Dynatrace OneAgent configuration in the DC/OS Universe](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. 実行するノードの数にインスタンスを設定します。 大きい数値を設定することもできますが、DC/OS は実際にその数値に達するまで新しいインスタンスを探そうとします。 必要に応じて、1000000 のような値に設定することもできます。 この場合、新しいノードがクラスターに追加されるたびに、Dynatrace がそのノードに自動的にエージェントをデプロイしますが、DC/OS は常にインスタンスをデプロイしようとします。

    ![Dynatrace configuration in the DC/OS Universe-instances](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>次の手順

パッケージをインストールしたら、Dynatrace ダッシュ ボードに戻ります。 クラスター内のコンテナーのさまざまな利用状況指標をご覧ください。 