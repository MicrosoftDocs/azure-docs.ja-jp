---
title: Azure Kinect ライブラリを Visual Studio プロジェクトに追加する
description: Azure Kinect NuGet パッケージを Visual Studio プロジェクトに追加する方法について説明します。
author: wes-b
ms.author: wesbarc
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect、Azure、センサー、SDK、Visual Studio 2017、Visual Studio 2019、NuGet
ms.openlocfilehash: b0395118481cbaecd5ad0b6a3a6b3e89cc29dfaf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276716"
---
# <a name="add-azure-kinect-library-to-your-visual-studio-project"></a>Azure Kinect ライブラリを Visual Studio プロジェクトに追加する

この記事では、Azure Kinect NuGet パッケージを Visual Studio プロジェクトに追加するプロセスについて説明します。

## <a name="install-azure-kinect-nuget-package"></a>Azure Kinect NuGet パッケージをインストールする

Azure Kinect NuGet パッケージをインストールするには:

1. Visual Studio に NuGet パッケージをインストールする詳細な手順については、[Visual Studio でのパッケージのインストールと使用](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio)に関するクイック スタートをご覧ください。
2. パッケージを追加するには、[参照] を右クリックし、[ソリューション エクスプローラー] から [NuGet パッケージの管理] を選択して、パッケージ マネージャー UI を使用します。
3. [パッケージ ソース] として "[nuget.org](https://www.nuget.org)" を選択し、[参照] タブを選択し、`Microsoft.Azure.Kinect.Sensor` を検索します。
4. 一覧からそのパッケージを選択してインストールします。

## <a name="use-azure-kinect-nuget-package"></a>Azure Kinect NuGet パッケージを使用する

パッケージが追加されたら、次のように、ソース コードにヘッダー ファイル インクルードを追加します。

- `#include <k4a/k4a.h>`
- `#include <k4arecord/record.h>`
- `#include <k4arecord/playback.h>`

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[最初のアプリケーションをビルドする準備が整いました](build-first-app.md)
