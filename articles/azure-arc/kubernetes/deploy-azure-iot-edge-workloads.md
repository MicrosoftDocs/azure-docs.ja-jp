---
title: Azure IoT Edge ワークロードをデプロイする
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure IoT Edge ワークロードをデプロイする
keywords: Kubernetes, Arc, Azure, K8s, コンテナー
ms.openlocfilehash: e77446170e5a6adac995394d66640fd183f453b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121730"
---
# <a name="deploy-azure-iot-edge-workloads"></a>Azure IoT Edge ワークロードをデプロイする

## <a name="overview"></a>概要

Azure Arc と Azure IoT Edge は互いの機能を簡単に補完するものです。 

Azure Arc からは、クラスターの基礎的構成要素を構成し、クラスター ポリシーを適用し、強制するためのクラスター オペレーター メカニズムが与えられます。 

Azure IoT Edge では、アプリケーションのオペレーターが、便利なクラウド インジェストと双方向通信プリミティブを利用することで、大規模ワークロードを離れた場所からデプロイし、管理できます。 

次の図は、Azure Arc と Azure IoT Edge の関係を示しています。

![IoT Arc 構成](./media/edge-arc.png)

## <a name="pre-requisites"></a>前提条件

* [IoT Edge デバイスの登録](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device)と[シミュレートされた温度センサー モジュールのデプロイ](../../iot-edge/quickstart-linux.md#deploy-a-module)。 以下に記載されている *values.yaml* のデバイスの接続文字列を書き留めてください。

* [Kubernetes 向けの IoT Edge のサポート](https://aka.ms/edgek8sdoc)を利用し、Azure Arc の Flux オペレーター経由でデプロイします。

* IoT Edge Helm グラフの [*values.yaml*](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) ファイルをダウンロードし、ファイルの終わりにある `deviceConnectionString` プレースホルダーを前に書き留めておいた接続文字列に変更します。 必要に応じて、サポートされている他のグラフ インストール オプションを設定します。 IoT Edge ワークロードの名前空間を作成し、その中にシークレットを生成します。

  ```
  $ kubectl create ns iotedge

  $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
  ```

  また、[クラスター構成例](./tutorial-use-gitops-connected-cluster.md)を利用し、離れた場所から設定することもできます。

## <a name="connect-a-cluster"></a>クラスターを接続する

`az` Azure CLI `connectedk8s` 拡張機能を使用し、Kubernetes クラスターを Azure Arc に接続します。

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>IoT Edge の構成を作成する

この[サンプル Git リポジトリ](https://github.com/veyalla/edgearc)は IoT Edge Helm グラフを指しており、前提条件セクションで作成したシークレットを参照します。

`az` Azure CLI `k8s-configuration` 拡張機能を使用して、接続されているクラスターを Git リポジトリにリンクする構成を作成します。

  ```
  az k8s-configuration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
  ```

数分後、IoT Edge ワークロード モジュールがクラスターの `iotedge` 名前空間にデプロイされていることがわかります。 

その名前空間には `SimulatedTemperatureSensor` ポッドのログが表示されるため、サンプル値が生成されていることがわかります。 [Visual Studio Code 用の Azure IoT Hub Toolkit の拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)を使用して、IoT ハブに到着したメッセージを監視することもできます。

## <a name="cleanup"></a>クリーンアップ

次を使用して構成を削除します。

```
az k8s-configuration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>次のステップ

[Azure Policy を使用してクラスター構成を管理する](./use-azure-policy.md)方法について学びます。
