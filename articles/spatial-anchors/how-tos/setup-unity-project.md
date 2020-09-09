---
title: Unity 向けの Azure Spatial Anchors をインストールする
description: Azure Spatial Anchors を使用するように Unity プロジェクトを構成する
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/31/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: a23cff8bcef2d62b1e415997ffd6afc80cf47793
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87812263"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Unity プロジェクトでの Azure Spatial Anchors の構成

## <a name="requirements"></a>必要条件

Azure Spatial Anchors では現在、次の構成を持つ Unity 2019.4 (LTS) がサポートされています。

* AR Foundation 3.1 を備えた Unity 2019.4 が Azure Spatial Anchors 2.4.0 以降でサポートされています。

## <a name="configuring-a-project"></a>プロジェクトの構成

Unity 向けの Azure Spatial Anchors は現在、Unity アセット パッケージ (`.unitypackage`) を使用して配布されています。これは、[GitHub リリース](https://github.com/Azure/azure-spatial-anchors-samples/releases)にあります。

### <a name="import-the-asset-package"></a>アセット パッケージをインポートする

1. [GitHub リリース](https://github.com/Azure/azure-spatial-anchors-samples/releases)から対象とするバージョンの `AzureSpatialAnchors.unitypackage` ファイルをダウンロードします。
2. [こちら](https://docs.unity3d.com/Manual/AssetPackagesImport.html)の指示に従って、Unity アセット パッケージをプロジェクトにインポートします。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [方法: Unity でアンカーを作成して配置する](./create-locate-anchors-unity.md)
