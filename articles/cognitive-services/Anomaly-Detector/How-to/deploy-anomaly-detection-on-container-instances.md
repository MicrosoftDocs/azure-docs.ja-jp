---
title: Anomaly Detector コンテナーを Azure Container Instances で実行する
titleSuffix: Azure Cognitive Services
description: Anomaly Detector コンテナーを Azure Container Instance にデプロイし、Web ブラウザーでテストします。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mbullwin
ms.openlocfilehash: 3979bb82bfa055cc2a134bf3119097c452ffb855
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94364124"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Anomaly Detector コンテナーを Azure Container Instances にデプロイする

Cognitive Services [Anomaly Detector](../anomaly-detector-container-howto.md) コンテナーを Azure [Container Instances](../../../container-instances/index.yml) にデプロイする方法について説明します。 この手順では、Anomaly Detector リソースの作成方法を実演します。 次に、関連するコンテナー イメージをプルする方法について説明します。 最後に、ブラウザーからこの 2 つのオーケストレーションを行う機能を取り上げます。 コンテナーを使用することで、開発者の関心をインフラストラクチャの管理から切り離し、アプリケーション開発に専念させることができます。

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>次のステップ

* コンテナー イメージのプルおよびコンテナーの実行の詳細については、[コンテナーのインストールおよび実行](../anomaly-detector-container-configuration.md)に関する説明を確認する
* 構成設定について、[コンテナーの構成](../anomaly-detector-container-configuration.md)を確認する
* [Anomaly Detector API サービスの詳細情報](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)