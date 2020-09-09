---
title: C++/WinRT HoloLens アプリケーション用の Azure Spatial Anchors をインストールする
description: Azure Spatial Anchors を使用するために C++/WinRT HoloLens プロジェクトを構成します
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 710f023a64f66e486bb2db9c08807af3431d0016
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87812264"
---
# <a name="configuring-azure-spatial-anchors-in-a-cwinrt-hololens-project"></a>C++/WinRT HoloLens プロジェクトでの Azure Spatial Anchors の構成

## <a name="requirements"></a>必要条件

* **ユニバーサル Windows プラットフォーム開発**ワークロードがインストールされた [Visual Studio 2019](https://www.visualstudio.com/downloads/) と **Windows 10 SDK (10.0.18362.0 以降)** コンポーネント。

## <a name="configuring-a-project"></a>プロジェクトの構成

HoloLens および C++/WinRT 用の Spatial Anchors は、[Microsoft.Azure.SpatialAnchors.WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet パッケージを使用して配布されます。

[こちら](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio)の手順に従い、Visual Studio の NuGet パッケージ マネージャーを使用して、お使いのプロジェクトに [Microsoft.Azure.SpatialAnchors.WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet パッケージをインストールします。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [方法: C++/WinRT でアンカーを作成して配置する](./create-locate-anchors-cpp-winrt.md)
