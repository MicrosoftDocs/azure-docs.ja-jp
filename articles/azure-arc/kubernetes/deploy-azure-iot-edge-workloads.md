---
title: Azure IoT Edge ワークロードをデプロイする (プレビュー)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure IoT Edge ワークロードをデプロイする
keywords: Kubernetes, Arc, Azure, K8s, コンテナー
ms.openlocfilehash: 88c480f93bfe28a424441a1c5857c623efb4e1d3
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091649"
---
# <a name="deploy-azure-iot-edge-workloads-preview"></a>Azure IoT Edge ワークロードをデプロイする (プレビュー)

## <a name="overview"></a>概要

Azure Arc と Azure IoT Edge は互いの機能を十分に補完するものです。 Azure Arc からは、クラスターの基礎的構成要素を構成し、クラスター ポリシーを適用し、強制するためのクラスター オペレーター メカニズムが与えられます。 一方、アプリケーションのオペレーターは IoT Edge の便利なクラウド インジェストと双方向通信プリミティブを利用することで、大規模ワークロードを離れた場所からデプロイし、管理できます。 これを図解したものが下の画像です。

![IoT Arc 構成](./media/edge-arc.png)

## <a name="pre-requisites"></a>前提条件

* [IoT Edge デバイスの登録](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device)と[シミュレートされた温度センサー モジュールのデプロイ](../../iot-edge/quickstart-linux.md#deploy-a-module)。 デバイスの接続文字列を必ず書き留めておいてください。

* [Kubernetes 向けの IoT Edge のサポート](https://aka.ms/edgek8sdoc)を利用し、Azure Arc の Flux オペレーター経由でデプロイします。

* IoT Edge Helm グラフの [**values.yaml**](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) ファイルをダウンロードし、ファイルの終わりにある **deviceConnectionString** プレースホルダーを手順 1 で書き留めておいた値に変更します。 サポートされている他のグラフ インストール オプションは、必要に応じて設定できます。 IoT Edge ワークロードの名前空間を作成し、その中にシークレットを作成します。

    ```
    $ kubectl create ns iotedge

    $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
    ```

    これは、[クラスター構成例](./use-gitops-connected-cluster.md)を利用し、離れた場所から設定することもできます。

## <a name="connect-a-cluster"></a>クラスターを接続する

`az` CLI `connectedk8s` 拡張機能を使用し、Kubernetes クラスターを Azure Arc に接続します。

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>IoT Edge の構成を作成する

サンプル リポジトリ: https://github.com/veyalla/edgearc

このリポジトリは IoT Edge Helm グラフを指しており、前提条件セクションで作成したシークレットを参照します。

1. `az` CLI `k8sconfiguration` 拡張機能を使用し、接続されているクラスターを Git リポジトリにリンクするための構成を作成します。

    ```
    az k8sconfiguration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
    ```

    1、2 分後、IoT Edge ワークロード モジュールがクラスターの `iotedge` 名前空間にデプロイされていることがわかります。 その名前空間には `SimulatedTemperatureSensor` ポッドのログが表示されるため、サンプル値が生成されていることを確認できます。 [Visual Studio Code 用の Azure IoT Hub Toolkit の拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)を使用して、IoT ハブに到着したメッセージを監視することもできます。

## <a name="cleanup"></a>クリーンアップ

次を使用して構成を削除できます。

```
az k8sconfiguration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>次のステップ

[Azure Policy を使用してクラスター構成を管理する](./use-azure-policy.md)
