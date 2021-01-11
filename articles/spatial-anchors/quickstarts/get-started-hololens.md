---
title: クイック スタート:DirectX を使用して HoloLens アプリを作成する
description: このクイック スタートでは、Spatial Anchors を使用して HoloLens アプリをビルドする方法について説明します。
author: craigktreasure
manager: virivera
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: e6d728647bb654ee2645bb1e3eb5968dcab337a8
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810352"
---
# <a name="quickstart-create-a-hololens-app-with-azure-spatial-anchors-in-cwinrt-and-directx"></a>クイック スタート:Azure Spatial Anchors を使用して HoloLens アプリを C++/WinRT と DirectX で作成する

このクイック スタートでは、[Azure Spatial Anchors](../overview.md) を使用する HoloLens アプリを C++/WinRT と DirectX で作成する方法について説明します。 Azure Spatial Anchors は、クロスプラットフォーム対応の開発者向けサービスです。このサービスを使用すると、時間が経過した後でも複数のデバイス間で位置情報を保持するオブジェクトを使用して複合現実エクスペリエンスを作成できます。 完了すると、空間アンカーを保存して再呼び出しできる HoloLens アプリが作成されます。

学習内容は次のとおりです。

> [!div class="checklist"]
> * Spatial Anchors アカウントを作成する
> * Spatial Anchors アカウント識別子とアカウント キーを構成する
> * HoloLens デバイスにデプロイして実行する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートを実行するには、以下が必要です。
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>、**ユニバーサル Windows プラットフォーム開発**ワークロード、および **Windows 10 SDK (10.0.18362.0 以降)** コンポーネントがインストールされた Windows マシン。 <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a> および <a href="https://git-lfs.github.com/">Git LFS</a> もインストールする必要があります。
- [Visual Studio Marketplace](https://marketplace.visualstudio.com/) から Visual Studio 用の [C++/WinRT Visual Studio 拡張機能 (VSIX)](https://aka.ms/cppwinrt/vsix) をインストールする必要があります。
- [開発者モード](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio)が有効になっている HoloLens デバイス。 この記事では、[Windows 10 October 2018 Update](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (別名 RS5) が適用された HoloLens デバイスが必要です。 HoloLens を最新のリリースに更新するには、 **[設定]** アプリを開き、 **[更新とセキュリティ]** を選択し、 **[更新プログラムの確認]** ボタンをクリックします。
- アプリにより、その AppX マニフェストに **spatialPerception** 機能を設定する必要があります。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>サンプル プロジェクトを開く

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Visual Studio で `HoloLens\DirectX\SampleHoloLens.sln` を開きます。

## <a name="configure-account-identifier-and-key"></a>アカウント識別子とキーを構成する

次に、自分のアカウント識別子とアカウント キーを使用するようにアプリを構成します。 これらの情報は、[Spatial Anchors リソースを設定](#create-a-spatial-anchors-resource)するときにテキスト エディターにコピーしました。

`HoloLens\DirectX\SampleHoloLens\ViewController.cpp`を開きます。

`SpatialAnchorsAccountKey` フィールドを見つけ、`Set me` をアカウント キーに置き換えます。

`SpatialAnchorsAccountId` フィールドを見つけ、`Set me` をアカウント識別子に置き換えます。

`SpatialAnchorsAccountDomain` フィールドを見つけ、`Set me` をアカウント ドメインに置き換えます。

## <a name="deploy-the-app-to-your-hololens"></a>アプリを HoloLens にデプロイする

**[ソリューション構成]** を **[リリース]** に変更し、 **[ソリューション プラットフォーム]** を **[x86]** に変更し、デプロイ ターゲット オプションから **[デバイス]** を選択します。

HoloLens 2 を使用している場合は、**x86** の代わりに **ARM64** を **[ソリューション プラットフォーム]** として使用します。

![Visual Studio の構成](./media/get-started-hololens/visual-studio-configuration.png)

HoloLens デバイスの電源をオンにしてサインインし、USB ケーブルを使用して PC に接続します。

**[デバッグ]**  >  **[デバッグの開始]** を選択して、アプリのデプロイとデバッグを開始します。

アプリの指示に従って、アンカーを配置し、呼び戻します。

Visual Studio で、 **[デバッグの停止]** を選択するか、**Shift + F5** キーを押して、アプリを停止します。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [チュートリアル:デバイス間で Spatial Anchors を共有する](../tutorials/tutorial-share-anchors-across-devices.md)
