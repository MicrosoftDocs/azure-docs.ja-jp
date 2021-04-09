---
title: Unity 用の Remote Rendering パッケージをインストールする
description: Unity 用の Remote Rendering クライアント DLL をインストールする方法について説明します
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: bfb383a7079e98db1db1f9b5077558c187bcea96
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047730"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Unity 用の Remote Rendering パッケージをインストールする

Azure Remote Rendering では、Unity パッケージを使用して Unity への統合をカプセル化します。
このパッケージには、C# API 全体に加えて、Unity で Azure Remote Rendering を使用するために必要なすべてのプラグイン バイナリが含まれています。
Unity のパッケージの命名規則に従い、パッケージは **com.microsoft.azure.remote-rendering** と呼ばれます。

次のいずれかのオプションを選択して、Unity パッケージをインストールできます。

## <a name="install-remote-rendering-package-using-the-mixed-reality-feature-tool"></a>Mixed Reality 機能ツールを使用して Remote Rendering パッケージをインストールする

[Mixed Reality 機能ツール](/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool) ([ダウンロード](https://aka.ms/mrfeaturetool)) は、Mixed Reality 機能パッケージを Unity プロジェクトに統合するために使用するツールです。 このパッケージは [ARR サンプル リポジトリ](https://github.com/Azure/azure-remote-rendering)の一部ではなく、Unity の内部パッケージ レジストリからは使用できません。

このパッケージをプロジェクトに追加するには、次を行う必要があります。
1. [Mixed Reality 機能ツールをダウンロードします](https://aka.ms/mrfeaturetool)
1. このツールの使用方法に関する[詳細な手順](/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool)に従います。
1. **[Discover Features]\(機能の検出\)** ページで **Microsoft Azure Remote Rendering** パッケージのボックスをオンにして、プロジェクトに追加するパッケージのバージョンを選択します。

![Mixed_Reality_feature_tool_package](media/mixed-reality-feature-tool-package.png)

ローカル パッケージを更新する場合は、Mixed Reality 機能ツールから新しいバージョンを選択してインストールするだけです。 パッケージを更新すると、コンソール エラーが発生することがあります。 その場合は、プロジェクトを閉じてから再度開いてみてください。

## <a name="install-remote-rendering-package-manually"></a>Remote Rendering パッケージを手動でインストールする

Remote Rendering パッケージを手動でインストールするには、次を行う必要があります。

1. `https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry` の Mixed Reality パッケージの NPM フィードからパッケージをダウンロードします。
    * [NPM](https://www.npmjs.com/get-npm) を使用し、次のコマンドを実行して現在のフォルダーにパッケージをダウンロードできます。
      ```
      npm pack com.microsoft.azure.remote-rendering --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry
      ```

    * または、[azure-remote-rendering GitHub リポジトリ](https://github.com/Azure/azure-remote-rendering)から `Scripts/DownloadUnityPackages.ps1` の PowerShell スクリプトを使用することもできます。
        * `Scripts/unity_sample_dependencies.json` の内容を次のように編集します。
          ```json
          {
            "packages": [
              {
                "name": "com.microsoft.azure.remote-rendering", 
                "version": "latest", 
                "registry": "https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry"
              }
            ]
          }
          ```

        * PowerShell で次のコマンドを実行し、指定された宛先ディレクトリにパッケージをダウンロードします。
          ```
          DownloadUnityPackages.ps1 -DownloadDestDir <destination directory>
          ```

1. Unity のパッケージ マネージャーを使用して、[ダウンロードしたパッケージをインストール](https://docs.unity3d.com/Manual/upm-ui-tarball.html)します。

ローカル パッケージを更新する場合は、それぞれのコマンドを再実行してパッケージを再インポートするだけです。 パッケージを更新すると、コンソール エラーが発生することがあります。 その場合は、プロジェクトを閉じてから再度開いてみてください。

## <a name="unity-render-pipelines"></a>Unity のレンダリング パイプライン

Remote Rendering は、 **:::no-loc text="Universal render pipeline":::** と **:::no-loc text="Standard render pipeline":::** の両方で機能します。 パフォーマンス上の理由から、ユニバーサル レンダリング パイプラインを使用することをお勧めします。

**:::no-loc text="Universal render pipeline":::** を使用するには、Unity にそのパッケージをインストールする必要があります。 これを行うには、Unity の **パッケージ マネージャー** UI (パッケージ名 **Universal RP**、バージョン 7.3.1 以降)、または [Unity プロジェクトのセットアップのチュートリアル](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package)で説明されているように、`Packages/manifest.json` ファイルを使用します。

## <a name="next-steps"></a>次のステップ

* [Unity のゲーム オブジェクトとコンポーネント](objects-components.md)
* [チュートリアル:リモートモデルの表示](../../tutorials/unity/view-remote-models/view-remote-models.md)