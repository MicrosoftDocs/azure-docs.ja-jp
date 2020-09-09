---
title: Unity によるモデルのレンダリング
description: モデルをレンダリングする手順をユーザーに案内するクイックスタート
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: b5865f2fd76c1159f7f72633362a96335af8a059
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509367"
---
# <a name="quickstart-render-a-model-with-unity"></a>クイック スタート:Unity によるモデルのレンダリング

このクイックスタートでは、Azure Remote Rendering (ARR) サービスを使用して、組み込みモデルをリモートでレンダリングする Unity サンプルを実行する方法について説明します。

ARR API 自体、または新しい Unity プロジェクトの設定方法については詳しく説明しません。 これらのトピックについては、次のページで説明しています: 「[チュートリアル: リモートでレンダリングされたモデルの表示](../tutorials/unity/view-remote-models/view-remote-models.md)」。

このクイックスタートでは、次の方法について説明します。
> [!div class="checklist"]
>
>* ローカルの開発環境を設定する
>* Unity 用の ARR クイックスタート サンプル アプリを入手して構築する
>* ARR クイックスタート サンプル アプリでモデルをレンダリングする

## <a name="prerequisites"></a>前提条件

Azure Remote Rendering サービスにアクセスするには、まず[アカウントを作成する](../how-tos/create-an-account.md)必要があります。

次のソフトウェアがインストールされている必要があります。

* Windows SDK 10.0.18362.0 [(ダウンロード)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* 最新バージョンの Visual Studio 2019 [(ダウンロード)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio tools for Mixed Reality](https://docs.microsoft.com/windows/mixed-reality/install-the-tools)。 特に、次の*ワークロード*のインストールは必須です。
  * **C++ によるデスクトップ開発**
  * **ユニバーサル Windows プラットフォーム (UWP) の開発**
* Git ([ダウンロード](https://git-scm.com/downloads))
* Unity 2019.3.1 [(ダウンロード)](https://unity3d.com/get-unity/download)
  * 次のモジュールを Unity にインストールします。
    * **UWP** - ユニバーサル Windows プラットフォーム Build Support
    * **IL2CPP** - Windows Build Support (IL2CPP)

## <a name="clone-the-sample-app"></a>サンプル アプリの複製

コマンド プロンプトを開き (Windows の [スタート] メニューで「`cmd`」と入力します)、ARR サンプル プロジェクトを格納するディレクトリに移動します。

次のコマンドを実行します。

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

最後のコマンドでは、Azure Remote Rendering 用のさまざまなサンプル プロジェクトを含む ARR ディレクトリにサブディレクトリを作成します。

Unity 用のクイックスタート サンプル アプリは、*Unity/Quickstart* サブディレクトリにあります。

## <a name="rendering-a-model-with-the-unity-sample-project"></a>Unity サンプル プロジェクトを使用したモデルのレンダリング

Unity Hub を開き、*ARR\azure-remote-rendering\Unity\Quickstart* フォルダーにあるサンプル プロジェクトを追加します。
プロジェクトを開きます。 必要に応じて、Unity がプロジェクトをお使いのインストール済みのバージョンにアップグレードすることを許可します。

ここでレンダリングする既定のモデルは、[組み込みのサンプル モデル](../samples/sample-model.md)です。 ARR 変換サービスを使用してカスタム モデルを変換する方法については、[次のクイックスタート](convert-model.md)で説明します。

### <a name="enter-your-account-info"></a>アカウント情報を入力する

1. Unity アセット ブラウザーで、 *[Scenes]\(シーン\)* フォルダーに移動し、 **[Quickstart]\(クイックスタート\)** シーンを開きます。
1. *[Hierarchy]\(階層\)* で、**RemoteRendering** ゲーム オブジェクトを選択します。
1. *[Inspector]\(インスペクター\)* に、自分の[アカウント資格情報](../how-tos/create-an-account.md)を入力します。 アカウントをお持ちでない場合は、[1 つ作成します](../how-tos/create-an-account.md)。

![ARR アカウント情報](./media/arr-sample-account-info.png)

> [!IMPORTANT]
> Azure portal では、お使いのアカウントのドメインは単に *mixedreality.azure.com* と表示されます。 これは、正常に接続するには不十分です。
> **AccountDomain** を `<region>.mixedreality.azure.com` に設定します。ここで、`<region>` は、[お近くの利用可能なリージョンのいずれか](../reference/regions.md)です。

後でこのプロジェクトを HoloLens にデプロイし、そのデバイスから Remote Rendering サービスに接続します。 デバイスに資格情報を入力する簡単な方法がないため、クイックスタート サンプルでは **Unity シーンに資格情報を保存**します。

> [!WARNING]
> 保存した資格情報を含むプロジェクトは、秘密のログイン情報が漏えいする可能性のあるリポジトリにチェックインしないようにしてください。

### <a name="create-a-session-and-view-the-default-model"></a>セッションを作成し、既定のモデルを表示する

Unity の **[Play]\(再生\)** ボタンを押してセッションを開始します。 *[Game]\(ゲーム\)* パネルのビューポートの下部に、状態テキストを含むオーバーレイが表示されます。 セッションでは、一連の状態遷移が行われます。 **[Starting]\(開始\)** 状態では、サーバーが起動されます。これには数分かかります。 成功すると、 **[Ready]\(準備完了\)** 状態に移行します。 次に、セッションは **[Connecting]\(接続中\)** 状態になります。このとき、セッションは、そのサーバーのレンダリング ランタイムへの接続を試みます。 成功すると、サンプルは **[Connected]\(接続済み\)** 状態に移行します。 この時点で、レンダリング用のモデルのダウンロードが開始されます。 モデルのサイズが大きいため、ダウンロードにさらに数分かかることがあります。 次に、リモートでレンダリングされたモデルが表示されます。

![サンプルからの出力](media/arr-sample-output.png)

お疲れさまでした。 これで、リモートでレンダリングされたモデルを表示できました。

## <a name="inspecting-the-scene"></a>シーンの検査

リモート レンダリング接続が実行されると、[Inspector]\(インスペクター\) パネルが更新され、追加のステータス情報が表示されます。

![Unity のサンプルの再生](./media/arr-sample-configure-session-running.png)

この段階で、新しいノードを選択し、インスペクターで **[Show children]\(子を表示\)** をクリックして、シーン グラフを探索できます。

![Unity 階層](./media/unity-hierarchy.png)

シーンには[切断面](../overview/features/cut-planes.md)オブジェクトがあります。 プロパティでこれを有効にし、動かしてみてください。

![切断面の変更](media/arr-sample-unity-cutplane.png)

変換を同期するには、 **[Sync now]\(今すぐ同期\)** をクリックするか、 **[Sync every frame]\(すべてのフレームを同期\)** オプションをオンにします。 コンポーネントのプロパティは変更するだけで十分です。

## <a name="next-steps"></a>次のステップ

次のクイックスタートでは、このサンプルを HoloLens にデプロイして、リモートでレンダリングされたモデルを元のサイズで表示します。

> [!div class="nextstepaction"]
> [クイック スタート: Unity のサンプルを HoloLens にデプロイする](deploy-to-hololens.md)

または、サンプルをデスクトップ PC にデプロイすることもできます。

> [!div class="nextstepaction"]
> [クイック スタート: Unity のサンプルをデスクトップにデプロイする](deploy-to-desktop.md)
