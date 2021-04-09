---
title: C++/WinRT HoloLens アプリケーション用の Azure Spatial Anchors をインストールする
description: Azure Spatial Anchors を使用するために C++/WinRT HoloLens プロジェクトを構成します
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 69134ef87d90fe69de2d9e4e9222e90f65edc785
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95506998"
---
# <a name="configuring-azure-spatial-anchors-in-a-cwinrt-hololens-project"></a>C++/WinRT HoloLens プロジェクトでの Azure Spatial Anchors の構成

## <a name="requirements"></a>必要条件

* **ユニバーサル Windows プラットフォーム開発** ワークロードがインストールされた [Visual Studio 2019](https://www.visualstudio.com/downloads/) と **Windows 10 SDK (10.0.18362.0 以降)** コンポーネント。

## <a name="configuring-a-project"></a>プロジェクトの構成

HoloLens および C++/WinRT 用の Spatial Anchors は、[Microsoft.Azure.SpatialAnchors.WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet パッケージを使用して配布されます。

[こちら](/nuget/consume-packages/install-use-packages-visual-studio)の手順に従い、Visual Studio の NuGet パッケージ マネージャーを使用して、お使いのプロジェクトに [Microsoft.Azure.SpatialAnchors.WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet パッケージをインストールします。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [方法: C++/WinRT でアンカーを作成して配置する](./create-locate-anchors-cpp-winrt.md)