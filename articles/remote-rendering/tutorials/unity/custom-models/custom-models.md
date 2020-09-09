---
title: インターフェイスとカスタム モデル
description: ビュー コントローラーを追加し、Azure Remote Rendering でレンダリングするカスタム モデルを取り込みます
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: ebadaf51a7dfbb286dac0bbdb0c3c8437ae2356f
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022226"
---
# <a name="tutorial-interfaces-and-custom-models"></a>チュートリアル:インターフェイスとカスタム モデル

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * プロジェクトに Mixed Reality Toolkit を追加する
> * モデルの状態を管理する
> * モデルを取り込むために Azure Blob Storage を構成する
> * レンダリングするモデルをアップロードして処理する

## <a name="prerequisites"></a>前提条件

* このチュートリアルは、「[チュートリアル: リモートでレンダリングされたモデルの表示](../view-remote-models/view-remote-models.md)」に基づいて作成されています。

## <a name="get-started-with-the-mixed-reality-toolkit-mrtk"></a>Mixed Reality Toolkit (MRTK) の概要

Mixed Reality Toolkit (MRTK) は、Mixed Reality エクスペリエンスを構築するためのクロスプラットフォームのツールキットです。 ここでは、そのインタラクションとビジュアル化の機能に MRTK 2.3 を使用します。

MRTK を追加するには、「[MRTK の概要](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html)」に記載されている[必須の手順](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#required)に従います。

次のような手順です。
 - [最新の MRTK Unity パッケージを取得する](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#get-the-latest-mrtk-unity-packages)
     - "最新" と書かれていますが、バージョン 2.3 のことです。
     - このチュートリアルで使用するのは *Foundation* パッケージのみです。 *Extensions*、*Tools*、*Examples* の各パッケージは必要ありません。
 - [MRTK パッケージを Unity プロジェクトにインポートする](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#import-mrtk-packages-into-your-unity-project)
 - [Unity プロジェクトをターゲット プラットフォームに切り替える](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#switch-your-unity-project-to-the-target-platform)
     - この手順は既に最初の章で完了しているはずですが、ここでもう一度確認するとよいでしょう。
 - [新しいシーンまたは新しいプロジェクトに MRTK を追加する](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#add-mrtk-to-a-new-scene-or-new-project)
     - 新しいシーンに MRTK を追加してコーディネーターとモデル オブジェクトまたはスクリプトを再度追加することも、 *[Mixed Reality Toolkit] -> [Add to Scene and Configure]\(シーンに追加して構成\)* メニュー コマンドを使用して既存のシーンに MRTK を追加することもできます。

## <a name="import-assets-used-by-this-tutorial"></a>このチュートリアルで使用するアセットをインポートする

この章からは、取り上げられる素材の多くに簡単な [Model-View-Controller パターン](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)を実装します。 このパターンの *Model* 部分は、Azure Remote Rendering 固有のコードと、Azure Remote Rendering に関連した状態管理です。 パターンの *View* 部分と *Controller* 部分は、MRTK アセットといくつかのカスタム スクリプトを使用して実装します。 このチュートリアルの *Model* は、ここで実装する *View-Controller* がなくても使用できます。 このように分離されているので、このチュートリアルにあるコードを独自のアプリケーションに容易に統合することができます。このアプリケーションは、設計パターンの *View-Controller* 部分を引き継ぐことになります。

MRTK の導入により、インタラクションとビジュアル フィードバックをサポートするためにプロジェクトに追加できるようになったスクリプト、プレハブ、アセットが多数存在します。 これらのアセット (**チュートリアル アセット**といいます) は、[Azure Remote Rendering GitHub](https://github.com/Azure/azure-remote-rendering) の "\Unity\TutorialAssets\TutorialAssets.unitypackage" にある [Unity アセット パッケージ](https://docs.unity3d.com/Manual/AssetPackages.html)にバンドルされています。

1. Git リポジトリ [Azure Remote Rendering](https://github.com/Azure/azure-remote-rendering) をクローンまたはダウンロードします。ダウンロードした場合は、zip を既知の場所に展開します。
1. Unity プロジェクトで、 *[Assets]\(アセット\) -> [Import Package]\(パッケージのインポート\) -> [Custom Package]\(カスタム パッケージ\)* を選択します。
1. エクスプローラーで、Azure Remote Rendering リポジトリをクローンまたは解凍したディレクトリに移動し、**Unity -> TutorialAssets -> TutorialAssets.unitypackage** にある .unitypackage を選択します。
1. **[インポート]** ボタンを選択して、パッケージの内容をプロジェクトにインポートします。
1. Unity エディターで、上部のメニュー バーから *[Mixed Reality Toolkit] -> [Utilities]\(ユーティリティ\) -> [Upgrade MRTK Standard Shader for Lightweight Render Pipeline]\(MRTK Standard Shader を Lightweight Render Pipeline 用にアップグレード\)* を選択し、画面の指示に従ってシェーダーをアップグレードします。

MRTK とチュートリアル アセットがプロジェクトに追加されたら、MRTK プロファイルを、このチュートリアルにより適したものに切り替えます。

1. シーン階層の **MixedRealityToolkit** GameObject を選択します。
1. インスペクターの **MixedRealityToolkit** コンポーネントで、構成プロファイルを *ARRMixedRealityToolkitConfigurationProfile* に切り替えます。
1. *Ctrl + S* キーを押して、変更内容を保存します。

これで、主に既定の HoloLens 2 プロファイルで MRTK が構成されます。 提供されるプロファイルは、あらかじめ次のように構成されています。
 - プロファイラーをオフにする。オンとオフを切り替えるには 9 を押すか、デバイスで "Show/Hide Profiler (プロファイラーを表示/非表示にしてください)" と言います。
 - 視線カーソルをオフにする。
 - Unity のマウス クリックを有効する。シミュレートされた手ではなくマウスで、MRTK の UI 要素をクリックできるようになります。

## <a name="add-the-app-menu"></a>アプリ メニューを追加する

このチュートリアルのほとんどのビュー コントローラーは、具象クラスではなく抽象基本クラスに対して動作します。 このパターンにより、柔軟性が向上し、ビュー コントローラーが与えられるだけでなく、自ら Azure Remote Rendering のコードを習得できます。 簡潔にするために、**RemoteRenderingCoordinator** クラスには抽象クラスは用意されておらず、そのビュー コントローラーは直接、具象クラスに対して動作します。

これで、プレハブの **AppMenu** をシーンに追加し、現在のセッション状態のビジュアル フィードバックを得ることができます。 ARR のより多くの機能をシーンに実装、統合する際は、このビュー コントローラーが、他のサブメニューのビュー コントローラーへの "扉" となります。 現時点では、ARR の状態を視覚的に示す機能と、アプリケーションに ARR への接続を承認する際にユーザーが使用するモーダル パネルを表示する機能が **AppMenu** には備わっています。

1. *Assets/RemoteRenderingTutorial/Prefabs/AppMenu* で **AppMenu** プレハブを見つけます。
1. **AppMenu** プレハブをシーンにドラッグします。
1. このシーンに *Text Mesh Pro* アセットを追加するのは今回が初めてなので、おそらく、**TMP Importer** のダイアログが表示されます。 画面の指示に従って **[Import TMP Essentials]\(TMP の必須パッケージをインポート\)** を選択します。 その後、インポーターのダイアログを閉じてください。サンプルや追加パッケージは必要ありません。
1. **AppMenu** は、セッションへの接続に同意するためのモーダルに自動的に接続して表示するよう構成されているため、前に設定しておいたバイパスは削除してかまいません。 **RemoteRenderingCoordinator** GameObject で、 **[On Requesting Authorization]\(承認要求時\)** イベントの [-] ボタンをクリックして、以前に実装した承認のバイパスを削除してください。
 ![バイパスを削除する](./media/remove-bypass-event.png)
1. Unity エディターの **[Play]\(再生\)** を押して、ビュー コントローラーをテストします。
1. エディターで MRTK が構成されたら、WASD キーを使用してビューの位置を変更したり、マウスの右ボタンを押しながらマウスを移動することでビューの方向を変更したりすることができます。 シーン内を少し動き回ってみて、コントロールの感触を確かめてください。
1. デバイスで、手のひらを上げることで、**AppMenu** を呼び出すことができます。Unity エディターでは、ホットキーの "M" を使用します。
1. メニューが見えなくなった場合は、"M" キーを押してメニューを呼び出します。 操作しやすいよう、このメニューはカメラの近くに配置されます。
1. これで、承認は **AppMenu** の右側に要求として表示されるようになります。それ以降は、これを使用して、リモート レンダリング セッションの管理をアプリに承認します。
 ![承認の UI](./media/authorize-request-ui.png)
1. Unity の再生を停止して、チュートリアルを続行します。

## <a name="manage-model-state"></a>モデルの状態を管理する

次に、状態の追跡、イベントへの応答、イベントの生成、構成を行うための新しいスクリプト、**RemoteRenderedModel** を実装します。 基本的に、**RemoteRenderedModel** はモデル データのリモート パスを `modelPath` に格納します。 **RemoteRenderingCoordinator** で状態の変化をリッスンし、定義されているモデルを自動的に読み込む (またはアンロードする) 必要があるかどうかを判断します。 **RemoteRenderedModel** がアタッチされている GameObject が、リモート コンテンツのローカルにおける親になります。

**RemoteRenderedModel** スクリプトには、**チュートリアル アセット**にある **BaseRemoteRenderedModel** が実装されていることに注目してください。 これによって、リモート モデル ビュー コントローラーをスクリプトにバインドすることができます。

1. **RemoteRenderingCoordinator** と同じフォルダーに、**RemoteRenderedModel** という名前の新しいスクリプトを作成します。 内容全体を次のコードに置き換えます。

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;
    using UnityEngine.Events;

    public class RemoteRenderedModel : BaseRemoteRenderedModel
    {
        public bool AutomaticallyLoad = true;

        private ModelState currentModelState = ModelState.NotReady;

        [SerializeField]
        [Tooltip("The friendly name for this model")]
        private string modelDisplayName;
        public override string ModelDisplayName { get => modelDisplayName; set => modelDisplayName = value; }

        [SerializeField]
        [Tooltip("The URI for this model")]
        private string modelPath;
        public override string ModelPath
        {
            get => modelPath.Trim();
            set => modelPath = value;
        }

        public override ModelState CurrentModelState
        {
            get => currentModelState;
            protected set
            {
                if (currentModelState != value)
                {
                    currentModelState = value;
                    ModelStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<ModelState> ModelStateChange;
        public override event Action<float> LoadProgress;
        public override Entity ModelEntity { get; protected set; }

        public UnityEvent OnModelNotReady = new UnityEvent();
        public UnityEvent OnModelReady = new UnityEvent();
        public UnityEvent OnStartLoading = new UnityEvent();
        public UnityEvent OnModelLoaded = new UnityEvent();
        public UnityEvent OnModelUnloading = new UnityEvent();

        public UnityFloatEvent OnLoadProgress = new UnityFloatEvent();

        public void Awake()
        {
            // Hook up the event to the Unity event
            LoadProgress += (progress) => OnLoadProgress?.Invoke(progress);

            ModelStateChange += HandleUnityStateEvents;
        }

        private void HandleUnityStateEvents(ModelState modelState)
        {
            switch (modelState)
            {
                case ModelState.NotReady:  OnModelNotReady?.Invoke();  break;
                case ModelState.Ready:     OnModelReady?.Invoke();     break;
                case ModelState.Loading:   OnStartLoading?.Invoke();   break;
                case ModelState.Loaded:    OnModelLoaded?.Invoke();    break;
                case ModelState.Unloading: OnModelUnloading?.Invoke(); break;
            }
        }

        private void Start()
        {
            //Attach to and initialize current state (in case we're attaching late)
            RemoteRenderingCoordinator.CoordinatorStateChange += Instance_CoordinatorStateChange;
            Instance_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        /// <summary>
        /// Listen for state changes on the coordinator, clean up this model's remote objects if we're no longer connected.
        /// Automatically load if required
        /// </summary>
        private void Instance_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CurrentModelState = ModelState.Ready;
                    if (AutomaticallyLoad)
                        LoadModel();
                    break;
                default:
                    UnloadModel();
                    break;
            }
        }

        private void OnDestroy()
        {
            RemoteRenderingCoordinator.CoordinatorStateChange -= Instance_CoordinatorStateChange;
            UnloadModel();
        }

        /// <summary>
        /// Asks the coordinator to create a model entity and listens for coordinator state changes
        /// </summary>
        [ContextMenu("Load Model")]
        public override async void LoadModel()
        {
            if (CurrentModelState != ModelState.Ready)
                return; //We're already loaded, currently loading, or not ready to load

            CurrentModelState = ModelState.Loading;

            ModelEntity = await RemoteRenderingCoordinator.instance?.LoadModel(ModelPath, this.transform, SetLoadingProgress);

            if (ModelEntity != null)
                CurrentModelState = ModelState.Loaded;
            else
                CurrentModelState = ModelState.Error;
        }

        /// <summary>
        /// Clean up the local model instances
        /// </summary>
        [ContextMenu("Unload Model")]
        public override void UnloadModel()
        {
            CurrentModelState = ModelState.Unloading;

            if (ModelEntity != null)
            {
                var modelGameObject = ModelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
                Destroy(modelGameObject);
                ModelEntity.Destroy();
                ModelEntity = null;
            }

            if (RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
                CurrentModelState = ModelState.Ready;
            else
                CurrentModelState = ModelState.NotReady;
        }

        /// <summary>
        /// Update the Unity progress event
        /// </summary>
        /// <param name="progressValue"></param>
        public override void SetLoadingProgress(float progressValue)
        {
            LoadProgress?.Invoke(progressValue);
        }
    }
    ```

**RemoteRenderedModel** の役割は、一言で言えば、モデルの読み込みに必要なデータ (この場合は、SAS または *builtin://* URI) を保持し、リモート モデルの状態を追跡することです。 読み込み時に、**RemoteRenderingCoordinator** の `LoadModel` メソッドが呼び出され、モデルを含むエンティティが参照とアンロード用に返されます。

## <a name="load-the-test-model"></a>テスト モデルを読み込む

もう一度テスト モデルを読み込んで、新しいスクリプトをテストしてみましょう。 スクリプトを含み、テスト モデルの親となるゲーム オブジェクトを作成します。

1. シーンに新しい空のゲーム オブジェクトを作成し、**TestModel** という名前を付けます。
1. *RemoteRenderedModel* スクリプトを **TestModel** に追加します。
![RemoteRenderedModel コンポーネントを追加する](./media/add-remote-rendered-model-script.png)
1. [`Model Display Name`] と [`Model Path`] に、それぞれ「*TestModel*」と「*builtin://Engine*」を入力します。
![モデルの詳細を指定する](./media/add-model-script.png)
1. カメラの前の位置 **x = 0、y = 0、z = 3** に、**TestModel** オブジェクトを配置します。
![オブジェクトを配置する](./media/test-model-position.png)
1. **AutomaticallyLoad** がオンになっていることを確認します。
1. Unity エディターで **[Play]\(再生\)** を押してアプリケーションをテストします。
1. アプリがセッションを作成できるように *[Connect]\(接続\)* ボタンをクリックして承認することで、セッションに接続し、自動的にモデルを読み込むようになります。

コンソールでアプリケーションの状態を監視します。 状態によっては完了までに時間がかかる場合があり、進行状況が表示されないことに注意してください。 最終的には、モデルの読み込みからのログが表示され、テスト モデルがシーンにレンダリングされます。

インスペクターの [Transform]\(トランスフォーム\)、または [Scene]\(シーン\) ビューで、**TestModel** GameObject の移動や回転を試します。 [Game]\(ゲーム\) ビューでモデルが移動したり回転したりすることがわかります。

![Unity ログ](./media/unity-loading-log.png)

## <a name="provision-blob-storage-in-azure-and-custom-model-ingestion"></a>Azure に Blob Storage をプロビジョニングしてカスタム モデルを取り込む

ここで、自分のモデルを読み込んでみましょう。 そのためには、Blob Storage を構成し、Azure でモデルをアップロードして変換する必要があります。その後、**RemoteRenderedModel** スクリプトを使用してそのモデルを読み込みます。 この時点で読み込む独自のモデルがなければ、カスタム モデルの読み込みステップは省略してかまいません。

「[クイックスタート: モデルをレンダリング用に変換する](../../../quickstarts/convert-model.md)」に記載の手順に従います。 このチュートリアルの目的上、「**クイックスタート サンプル アプリに新しいモデルを挿入する**」セクションはスキップしてください。 取り込んだモデルの *Shared Access Signature (SAS)* URI が得られたら、以降の手順に進みます。

## <a name="load-and-rendering-a-custom-model"></a>カスタム モデルを読み込んでレンダリングする

1. 新しい空の GameObject をシーンに作成し、カスタム モデルと同様の名前を付けます。
1. 新しく作成した GameObject に *RemoteRenderedModel* スクリプトを追加します。
 ![RemoteRenderedModel コンポーネントを追加する](./media/add-remote-rendered-model-script.png)
1. `Model Display Name` に、カスタム モデルの適切な名前を入力します。
1. 前の取り込み手順で作成した、モデルの *Shared Access Signature (SAS)* URI を `Model Path` に入力します。
1. カメラの前の **x = 0, y = 0, z = 3** の位置に GameObject を配置します。
1. **AutomaticallyLoad** がオンになっていることを確認します。
1. Unity エディターで **[Play]\(再生\)** を押してアプリケーションをテストします。

    コンソールには、まず現在の状態が表示され、その後、モデル読み込みの進行状況を示すメッセージが表示されます。 続けてカスタム モデルがシーンに読み込まれます。

1. カスタム モデル オブジェクトをシーンから削除します。 このチュートリアルは、テスト モデルを使用したときに最良のエクスペリエンスが得られます。 ARR では確かに複数のモデルがサポートされますが、このチュートリアルは、一度に 1 つのリモート モデルをサポートするよう最適化して作成されています。

## <a name="next-steps"></a>次のステップ

これで、独自のモデルを Azure Remote Rendering に読み込んで、自分のアプリケーションで表示できるようになりました。 次に、モデルの操作方法について説明します。

> [!div class="nextstepaction"]
> [次:モデルの操作](../manipulate-models/manipulate-models.md)
