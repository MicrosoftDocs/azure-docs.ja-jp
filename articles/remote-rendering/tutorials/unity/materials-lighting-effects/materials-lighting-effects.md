---
title: 素材、ライト、および効果の調整
description: モデルの素材とライトを変更します。 アウトラインや切断面など、その他の効果を追加します。
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 400f5de5326ce7700b627999ade312484055a4e7
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574443"
---
# <a name="tutorial-refining-materials-lighting-and-effects"></a>チュートリアル:素材、ライト、および効果の調整

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * モデルとモデル コンポーネントを強調表示してアウトラインを描画する
> * さまざまな素材をモデルに適用する
> * 切断面を使用してモデルをスライスする
> * リモートでレンダリングされたオブジェクトに簡単なアニメーションを追加する

## <a name="prerequisites"></a>前提条件

* このチュートリアルは、「[チュートリアル: モデルの操作](..\manipulate-models\manipulate-models.md)」に基づいて作成されています。

## <a name="highlighting-and-outlining"></a>強調表示とアウトライン

視覚的なフィードバックをユーザーに提供することは、どのようなアプリケーションにおいてもユーザー エクスペリエンスの重要な点です。 Azure Remote Rendering では、[階層状態のオーバーライド](../../../overview/features/override-hierarchical-state.md)を通じて視覚的なフィードバック メカニズムを実現します。 階層状態のオーバーライドは、モデルのローカル インスタンスにアタッチされたコンポーネントを使用して実装されます。 これらのローカル インスタンスの作成方法については、「[Unity 階層へのリモート オブジェクト グラフの同期](../manipulate-models/manipulate-models.md#synchronizing-the-remote-object-graph-into-the-unity-hierarchy)」で学習しました。

まず、[**HierarchicalStateOverrideComponent**](/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent) コンポーネントのラッパーを作成します。 **HierarchicalStateOverrideComponent** は、リモート エンティティのオーバーライドを制御するローカル スクリプトです。 [**チュートリアル アセット**](../custom-models/custom-models.md#import-assets-used-by-this-tutorial)には、**BaseEntityOverrideController** と呼ばれる抽象基底クラスが含まれており、これを拡張してラッパーを作成します。

1. **EntityOverrideController** という名前の新しいスクリプトを作成し、その内容を次のコードで置き換えます。

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    public class EntityOverrideController : BaseEntityOverrideController
    {
        public override event Action<HierarchicalStates> FeatureOverrideChange;

        private ARRHierarchicalStateOverrideComponent localOverride;
        public override ARRHierarchicalStateOverrideComponent LocalOverride
        {
            get
            {
                if (localOverride == null)
                {
                    localOverride = gameObject.GetComponent<ARRHierarchicalStateOverrideComponent>();
                    if (localOverride == null)
                    {
                        localOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
                    }

                    var remoteStateOverride = TargetEntity.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                    if (remoteStateOverride == null)
                    {
                        // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                        localOverride.Create(RemoteManagerUnity.CurrentSession);
                    }
                    else
                    {
                        // otherwise, bind our local stateOverride component to the remote component
                        localOverride.Bind(remoteStateOverride);

                    }
                }
                return localOverride;
            }
        }

        private RemoteEntitySyncObject targetEntity;
        public override RemoteEntitySyncObject TargetEntity
        {
            get
            {
                if (targetEntity == null)
                    targetEntity = gameObject.GetComponent<RemoteEntitySyncObject>();
                return targetEntity;
            }
        }

        private HierarchicalEnableState ToggleState(HierarchicalStates feature)
        {
            HierarchicalEnableState setToState = HierarchicalEnableState.InheritFromParent;
            switch (LocalOverride.RemoteComponent.GetState(feature))
            {
                case HierarchicalEnableState.ForceOff:
                case HierarchicalEnableState.InheritFromParent:
                    setToState = HierarchicalEnableState.ForceOn;
                    break;
                case HierarchicalEnableState.ForceOn:
                    setToState = HierarchicalEnableState.InheritFromParent;
                    break;
            }

            return SetState(feature, setToState);
        }

        private HierarchicalEnableState SetState(HierarchicalStates feature, HierarchicalEnableState enableState)
        {
            if (GetState(feature) != enableState) //if this is actually different from the current state, act on it
            {
                LocalOverride.RemoteComponent.SetState(feature, enableState);
                FeatureOverrideChange?.Invoke(feature);
            }

            return enableState;
        }

        public override HierarchicalEnableState GetState(HierarchicalStates feature) => LocalOverride.RemoteComponent.GetState(feature);

        public override void ToggleHidden() => ToggleState(HierarchicalStates.Hidden);

        public override void ToggleSelect() => ToggleState(HierarchicalStates.Selected);

        public override void ToggleSeeThrough() => ToggleState(HierarchicalStates.SeeThrough);

        public override void ToggleTint(Color tintColor = default)
        {
            if (tintColor != default) LocalOverride.RemoteComponent.TintColor = tintColor.toRemote();
            ToggleState(HierarchicalStates.UseTintColor);
        }

        public override void ToggleDisabledCollision() => ToggleState(HierarchicalStates.DisableCollision);

        public override void RemoveOverride()
        {
            var remoteStateOverride = TargetEntity.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();
            if (remoteStateOverride != null)
            {
                remoteStateOverride.Destroy();
            }

            if (localOverride == null)
                localOverride = gameObject.GetComponent<ARRHierarchicalStateOverrideComponent>();

            if (localOverride != null)
            {
                Destroy(localOverride);
            }
        }
    }
    ```

**LocalOverride** の主なジョブは、自身とその `RemoteComponent` との間にリンクを作成することです。 **LocalOverride** を使用すると、ローカル コンポーネントに状態フラグを設定して、それをリモート エンティティにバインドできます。 オーバーライドとその状態については、「[階層状態のオーバーライド](../../../overview/features/override-hierarchical-state.md)」ページを参照してください。 

この実装によって、一度に 1 つだけ状態が切り替えられます。 ただし、1 つのエンティティで複数のオーバーライドを組み合わせたり、階層内のさまざまなレベルで組み合わせを作成したりすることもまったく可能です。 たとえば、1 つのコンポーネントで `Selected` と `SeeThrough` を組み合わせると、コンポーネントが透明になると共にアウトラインが描画されます。 また、ルート エンティティの `Hidden` オーバーライドを `ForceOn` に設定し、ある子エンティティの `Hidden` オーバーライドを `ForceOff` に設定すると、そのオーバーライドが設定された子以外はすべてが非表示になります。

エンティティに状態を適用するには、前に作成した **RemoteEntityHelper** を変更します。

1. **RemoteEntityHelper** クラスを変更して、**BaseRemoteEntityHelper** 抽象クラスを実装します。 この変更により、**チュートリアル アセット** で提供されているビュー コントローラーを使用できるようになります。 変更すると、次のようになります。

    ```cs
    public class RemoteEntityHelper : BaseRemoteEntityHelper
    ```

2. 次のコードを使用して、抽象メソッドをオーバーライドします。

    ```cs
    public override BaseEntityOverrideController EnsureOverrideComponent(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var overrideComponent = entityGameObject.GetComponent<EntityOverrideController>();
        if (overrideComponent == null)
            overrideComponent = entityGameObject.AddComponent<EntityOverrideController>();
        return overrideComponent;
    }

    public override HierarchicalEnableState GetState(Entity entity, HierarchicalStates feature)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        return overrideComponent.GetState(feature);
    }

    public override void ToggleHidden(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleHidden();
    }

    public override void ToggleSelect(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleSelect();
    }

    public override void ToggleSeeThrough(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleSeeThrough();
    }

    public Color TintColor = new Color(0.0f, 1.0f, 0.0f, 0.1f);
    public override void ToggleTint(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleTint(TintColor);
    }

    public override void ToggleDisableCollision(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleHidden();
    }

    public override void RemoveOverrides(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var overrideComponent = entityGameObject.GetComponent<EntityOverrideController>();
        if (overrideComponent != null)
        {
            overrideComponent.RemoveOverride();
            Destroy(overrideComponent);
        }
    }
    ```

このコードにより、**EntityOverrideController** コンポーネントがターゲット エンティティに追加され、いずれかのトグル メソッドが呼び出されます。 必要に応じて、**TestModel** GameObject で、**RemoteEntityHelper** を `OnRemoteEntityClicked` イベントのコールバックとして **RemoteRayCastPointerHandler** コンポーネントに追加することにより、これらのヘルパー メソッドを呼び出すことができます。

![ポインター コールバック](./media/pointer-event-callbacks.png)

これらのスクリプトをモデルに追加したので、ランタイムに接続すると、**AppMenu** ビュー コントローラーで **EntityOverrideController** スクリプトとやり取りするための追加のインターフェイスが有効になります。 **[Model Tools]\(モデル ツール\)** メニューで、ロックが解除されているビュー コントローラーを確認します。

この時点で、**TestModel** GameObject のコンポーネントはこのようになります。

![追加のスクリプトが設定されたテスト モデル](./media/test-model-updated.png)

次に示すのは、単一のエンティティでオーバーライドが重なっている例です。 `Select` と `Tint` を使用して、アウトラインと色分けの両方を指定しています。

![テスト モデルでの淡色の選択](./media/select-tint-test-model.png)

## <a name="cut-planes"></a>切断面

[切断面](../../../overview/features/cut-planes.md)は、任意のリモート エンティティに追加できる機能です。 通常は、切断面コンポーネントを保持するために、どのメッシュ データにも関連付けられていない新しいリモート エンティティを作成します。 切断面の位置と向きは、切断面がアタッチされるリモート エンティティの位置と向きによって決まります。

ここでは、リモート エンティティを自動的に作成して、切断面コンポーネントを追加し、ローカル オブジェクトの変換を切断面エンティティと同期するスクリプトを作成します。 次に、**CutPlaneViewController** を使用して、切断面を操作できるようにするインターフェイスで切断面をラップします。

1. **RemoteCutPlane** という名前の新しいスクリプトを作成し、そのコードを次のコードで置き換えます。

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    public class RemoteCutPlane : BaseRemoteCutPlane
    {
        public Color SliceColor = new Color(0.5f, 0f, 0f, .5f);
        public float FadeLength = 0.01f;
        public Axis SliceNormal = Axis.NegativeY;

        public bool AutomaticallyCreate = true;

        private CutPlaneComponent remoteCutPlaneComponent;
        private bool cutPlaneReady = false;

        public override bool CutPlaneReady 
        { 
            get => cutPlaneReady;
            set 
            { 
                cutPlaneReady = value;
                CutPlaneReadyChanged?.Invoke(cutPlaneReady);
            }
        }

        public override event Action<bool> CutPlaneReadyChanged;

        public UnityBoolEvent OnCutPlaneReadyChanged = new UnityBoolEvent();

        public void Start()
        {
            // Hook up the event to the Unity event
            CutPlaneReadyChanged += (ready) => OnCutPlaneReadyChanged?.Invoke(ready);

            RemoteRenderingCoordinator.CoordinatorStateChange += RemoteRenderingCoordinator_CoordinatorStateChange;
            RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        private void RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    if (AutomaticallyCreate)
                        CreateCutPlane();
                    break;
                default:
                    DestroyCutPlane();
                    break;
            }
        }

        public override void CreateCutPlane()
        {
            //Implement me
        }

        public override void DestroyCutPlane()
        {
            //Implement me
        }
    }
    ```

    このコードによって、**チュートリアル アセット** に含まれる **BaseRemoteCutPlane** クラスが拡張されます。 リモートでレンダリングされるモデルと同様に、このスクリプトはリモート コーディネーターにアタッチし、そこから送られてくる `RemoteRenderingState` の変更をリッスンします。 コーディネーターが `RuntimeConnected` の状態になると、そのように設定されていれば自動的に接続を試みます。 ここには `CutPlaneComponent` 変数もあり、追跡する必要があります。 これは、リモート セッションの切断面と同期する Azure Remote Rendering コンポーネントです。 では、切断面を作成するために必要な作業を見ていきましょう。

2. `CreateCutPlane()` メソッドを次の完成版で置き換えます。

    ```cs
    public override void CreateCutPlane()
    {
        if (remoteCutPlaneComponent != null)
            return; //Nothing to do!

        //Create a root object for the cut plane
        var cutEntity = RemoteRenderingCoordinator.CurrentSession.Connection.CreateEntity();

        //Bind the remote entity to this game object
        cutEntity.BindToUnityGameObject(this.gameObject);

        //Sync the transform of this object so we can move the cut plane
        var syncComponent = this.gameObject.GetComponent<RemoteEntitySyncObject>();
        syncComponent.SyncEveryFrame = true;

        //Add a cut plane to the entity
        remoteCutPlaneComponent = RemoteRenderingCoordinator.CurrentSession.Connection.CreateComponent(ObjectType.CutPlaneComponent, cutEntity) as CutPlaneComponent;

        //Configure the cut plane
        remoteCutPlaneComponent.Normal = SliceNormal;
        remoteCutPlaneComponent.FadeColor = SliceColor.toRemote();
        remoteCutPlaneComponent.FadeLength = FadeLength;
        CutPlaneReady = true;
    }
    ```

    ここでは、リモート エンティティを作成し、ローカルの GameObject にバインドしています。 `SyncEveryFrame` を `true` に設定することによって、リモート エンティティの変換がローカルの変換に同期されるようにします。 次に、`CreateComponent` の呼び出しを使用して、リモート オブジェクトに `CutPlaneComponent` を追加します。 最後に、MonoBehaviour の最初の部分で定義されている設定を使用して、切断面を構成します。 `DestroyCutPlane()` メソッドを実装して切断面をクリーンアップするために必要なことを見てみましょう。

3. `DestroyCutPlane()` メソッドを次の完成版で置き換えます。

    ```cs
    public override void DestroyCutPlane()
    {
        if (remoteCutPlaneComponent == null)
            return; //Nothing to do!

        remoteCutPlaneComponent.Owner.Destroy();
        remoteCutPlaneComponent = null;
        CutPlaneReady = false;
    }
    ```

リモート オブジェクトは非常に単純であり、リモート エンドをクリーンアップするだけなので (ローカル オブジェクトは保持します)、リモート オブジェクトで `Destroy` を呼び出して、そこへの参照をクリアするのは簡単です。

**AppMenu** には、切断面に自動的にアタッチし、それとやり取りできるようにするビュー コントローラーが含まれています。 **AppMenu** またはビュー コントローラーのいずれかを使用することは必須ではありませんが、使用するとエクスペリエンスが向上します。 では、切断面とそのビュー コントローラーをテストしましょう。

1. 新しい空の GameObject をシーンに作成し、**CutPlane** という名前を付けます。
1. **RemoteCutPlane** コンポーネントを **CutPlane** GameObject に追加します。

   ![切断面コンポーネントの構成](./media/cut-plane-config.png)

1. Unity エディターで [Play]\(再生\) を押し、リモート セッションを読み込んで接続します。
1. MRTK の手のシミュレーションを使用して切断面をつかみ、(Ctrl キーを押しながら) 回転させて、シーン内で動かします。 それが **TestModel** へとスライスされ、内部のコンポーネントが表示されるのを確認します。

![切断面の例](./media/cut-plane-example-engine.png)

## <a name="configuring-the-remote-lighting"></a>リモートのライティングの構成

リモート レンダリング セッションでは、[ライティングのオプション](../../../overview/features/lights.md)がすべてサポートされています。 リモート レンダリングで使用する、[空のテクスチャ](../../../overview/features/sky.md)用のスクリプトと、2 種類の Unity ライトの簡単なマップを作成します。

### <a name="sky-texture"></a>空のテクスチャ

空のテクスチャを変更するときに選択できる組み込みのキューブマップがいくつかあります。 これらはセッションに読み込まれて、空のテクスチャに適用されます。 また、[独自のテクスチャを読み込んで](../../../concepts/textures.md)、スカイ ライトとして使用することもできます。

パラメーターを読み込む形で使用できる、組み込みのキューブマップの一覧を備えた **RemoteSky** スクリプトを作成します。 これにより、ユーザーがいずれかのオプションを選択して読み込むことができるようになります。

1. **RemoteSky** という新しいスクリプトを作成し、その内容全体を次のコードで置き換えます。

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using System;
    using System.Collections.Generic;
    using UnityEngine;

    public class RemoteSky : BaseRemoteSky
    {
        public override Dictionary<string, LoadTextureFromSasOptions> AvailableCubemaps => builtInTextures;

        private bool canSetSky;
        public override bool CanSetSky
        {
            get => canSetSky;
            set
            {
                canSetSky = value;
                CanSetSkyChanged?.Invoke(canSetSky);
            }
        }

        private string currentSky = "DefaultSky";
        public override string CurrentSky
        {
            get => currentSky;
            protected set
            {
                currentSky = value;
                SkyChanged?.Invoke(value);
            }
        }

        private Dictionary<string, LoadTextureFromSasOptions> builtInTextures = new Dictionary<string, LoadTextureFromSasOptions>()
        {
            {"Autoshop",new LoadTextureFromSasOptions("builtin://Autoshop", TextureType.CubeMap)},
            {"BoilerRoom",new LoadTextureFromSasOptions("builtin://BoilerRoom", TextureType.CubeMap)},
            {"ColorfulStudio",new LoadTextureFromSasOptions("builtin://ColorfulStudio", TextureType.CubeMap)},
            {"Hangar",new LoadTextureFromSasOptions("builtin://Hangar", TextureType.CubeMap)},
            {"IndustrialPipeAndValve",new LoadTextureFromSasOptions("builtin://IndustrialPipeAndValve", TextureType.CubeMap)},
            {"Lebombo",new LoadTextureFromSasOptions("builtin://Lebombo", TextureType.CubeMap)},
            {"SataraNight",new LoadTextureFromSasOptions("builtin://SataraNight", TextureType.CubeMap)},
            {"SunnyVondelpark",new LoadTextureFromSasOptions("builtin://SunnyVondelpark", TextureType.CubeMap)},
            {"Syferfontein",new LoadTextureFromSasOptions("builtin://Syferfontein", TextureType.CubeMap)},
            {"TearsOfSteelBridge",new LoadTextureFromSasOptions("builtin://TearsOfSteelBridge", TextureType.CubeMap)},
            {"VeniceSunset",new LoadTextureFromSasOptions("builtin://VeniceSunset", TextureType.CubeMap)},
            {"WhippleCreekRegionalPark",new LoadTextureFromSasOptions("builtin://WhippleCreekRegionalPark", TextureType.CubeMap)},
            {"WinterRiver",new LoadTextureFromSasOptions("builtin://WinterRiver", TextureType.CubeMap)},
            {"DefaultSky",new LoadTextureFromSasOptions("builtin://DefaultSky", TextureType.CubeMap)}
        };

        public UnityBoolEvent OnCanSetSkyChanged;
        public override event Action<bool> CanSetSkyChanged;

        public UnityStringEvent OnSkyChanged;
        public override event Action<string> SkyChanged;

        public void Start()
        {
            // Hook up the event to the Unity event
            CanSetSkyChanged += (canSet) => OnCanSetSkyChanged?.Invoke(canSet);
            SkyChanged += (key) => OnSkyChanged?.Invoke(key);

            RemoteRenderingCoordinator.CoordinatorStateChange += ApplyStateToView;
            ApplyStateToView(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        private void ApplyStateToView(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CanSetSky = true;
                    break;
                default:
                    CanSetSky = false;
                    break;
            }
        }

        public override async void SetSky(string skyKey)
        {
            if (!CanSetSky)
            {
                Debug.Log("Unable to set sky right now");
                return;
            }

            if (AvailableCubemaps.ContainsKey(skyKey))
            {
                Debug.Log("Setting sky to " + skyKey);
                //Load the texture into the session
                var texture = await RemoteRenderingCoordinator.CurrentSession.Connection.LoadTextureFromSasAsync(AvailableCubemaps[skyKey]);

                //Apply the texture to the SkyReflectionSettings
                RemoteRenderingCoordinator.CurrentSession.Connection.SkyReflectionSettings.SkyReflectionTexture = texture;
                SkyChanged?.Invoke(skyKey);
            }
            else
            {
                Debug.Log("Invalid sky key");
            }
        }
    }
    ```

    このコードの最も重要な部分は、わずか数行です。

    ```cs
    //Load the texture into the session
    var texture = await RemoteRenderingCoordinator.CurrentSession.Connection.LoadTextureFromSasAsync(AvailableCubemaps[skyKey]);

    //Apply the texture to the SkyReflectionSettings
    RemoteRenderingCoordinator.CurrentSession.Connection.SkyReflectionSettings.SkyReflectionTexture = texture;
    ```

    ここでは、組み込みの BLOB ストレージからセッションに読み込むことによって、使用するテクスチャへの参照を取得します。 次は、そのテクスチャをセッションの `SkyReflectionTexture` に割り当てて、適用するだけでかまいません。

1. シーンに空の GameObject を作成し、**SkyLight** という名前を付けます。

1. **RemoteSky** スクリプトを **SkyLight** GameObject に追加します。

    スカイ ライト間の切り替えは、`AvailableCubemaps` で定義されている文字列キーのいずれかを使用して、`SetSky` を呼び出すことによって実行できます。 **AppMenu** に組み込まれているビュー コントローラーによってボタンが自動的に作成され、それぞれのキーを使用して `SetSky` を呼び出すようイベントにフックされます。
1. Unity エディターで [Play]\(再生\) を押し、接続を承認します。
1. ローカル ランタイムをリモート セッションに接続したら、 **[AppMenu]、[Session Tools]\(セッション ツール\)、[Remote Sky]\(リモート スカイ\)** の順に移動して、さまざまな空のオプションを調べ、それらが **TestModel** にどのように影響するかを確認します。

### <a name="scene-lights"></a>シーン ライト

リモートのシーン ライトには、ポイント、スポット、指向性などがあります。 先ほど作成した切断面と同様に、これらのシーン ライトはコンポーネントがアタッチされたリモート エンティティです。 リモート シーンをライティングする場合の重要な考慮事項としては、ローカル シーンのライティングと一致させることが挙げられます。 HoloLens 2 の多くの Unity アプリケーションでは、ローカルにレンダリングされるオブジェクトに対して物理学に基づいたレンダリングが使用されないため、この戦略は常に可能であるとは限りません。 ただし、一定のレベルまでは、Unity の単純な既定のライティングをシミュレートできます。

1. **RemoteLight** という名前の新しいスクリプトを作成し、そのコードを次のコードで置き換えます。

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(Light))]
    public class RemoteLight : BaseRemoteLight
    {
        public bool AutomaticallyCreate = true;

        private bool lightReady = false;
        public override bool LightReady 
        {
            get => lightReady;
            set
            {
                lightReady = value;
                LightReadyChanged?.Invoke(lightReady);
            }
        }

        private ObjectType remoteLightType = ObjectType.Invalid;
        public override ObjectType RemoteLightType => remoteLightType;

        public UnityBoolEvent OnLightReadyChanged;

        public override event Action<bool> LightReadyChanged;

        private Light localLight; //Unity Light

        private Entity lightEntity;
        private LightComponentBase remoteLightComponent; //Remote Rendering Light

        private void Awake()
        {
            localLight = GetComponent<Light>();
            switch (localLight.type)
            {
                case LightType.Directional:
                    remoteLightType = ObjectType.DirectionalLightComponent;
                    break;
                case LightType.Point:
                    remoteLightType = ObjectType.PointLightComponent;
                    break;
                case LightType.Spot:
                case LightType.Area:
                    //Not supported in tutorial
                case LightType.Disc:
                    // No direct analog in remote rendering
                    remoteLightType = ObjectType.Invalid;
                    break;
            }
        }

        public void Start()
        {
            // Hook up the event to the Unity event
            LightReadyChanged += (ready) => OnLightReadyChanged?.Invoke(ready);

            RemoteRenderingCoordinator.CoordinatorStateChange += RemoteRenderingCoordinator_CoordinatorStateChange;
            RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        public void OnDestroy()
        {
            lightEntity?.Destroy();
        }

        private void RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    if (AutomaticallyCreate)
                        CreateLight();
                    break;
                default:
                    DestroyLight();
                    break;
            }
        }

        public override void CreateLight()
        {
            if (remoteLightComponent != null)
                return; //Nothing to do!

            //Create a root object for the light
            if(lightEntity == null)
                lightEntity = RemoteRenderingCoordinator.CurrentSession.Connection.CreateEntity();

            //Bind the remote entity to this game object
            lightEntity.BindToUnityGameObject(this.gameObject);

            //Sync the transform of this object so we can move the light
            var syncComponent = this.gameObject.GetComponent<RemoteEntitySyncObject>();
            syncComponent.SyncEveryFrame = true;

            //Add a light to the entity
            switch (RemoteLightType)
            {
                case ObjectType.DirectionalLightComponent:
                    var remoteDirectional = RemoteRenderingCoordinator.CurrentSession.Connection.CreateComponent(ObjectType.DirectionalLightComponent, lightEntity) as DirectionalLightComponent;
                    //No additional properties
                    remoteLightComponent = remoteDirectional;
                    break;

                case ObjectType.PointLightComponent:
                    var remotePoint = RemoteRenderingCoordinator.CurrentSession.Connection.CreateComponent(ObjectType.PointLightComponent, lightEntity) as PointLightComponent;
                    remotePoint.Radius = 0;
                    remotePoint.Length = localLight.range;
                    //remotePoint.AttenuationCutoff = //No direct analog in Unity legacy lights
                    //remotePoint.ProjectedCubeMap = //No direct analog in Unity legacy lights

                    remoteLightComponent = remotePoint;
                    break;
                default:
                    LightReady = false;
                    return;
            }

            // Set the common values for all light types
            UpdateRemoteLightSettings();

            LightReady = true;
        }

        public override void UpdateRemoteLightSettings()
        {
            remoteLightComponent.Color = localLight.color.toRemote();
            remoteLightComponent.Intensity = localLight.intensity;
        }

        public override void DestroyLight()
        {
            if (remoteLightComponent == null)
                return; //Nothing to do!

            remoteLightComponent.Destroy();
            remoteLightComponent = null;
            LightReady = false;
        }

        [ContextMenu("Sync Remote Light Configuration")]
        public override void RecreateLight()
        {
            DestroyLight();
            CreateLight();
        }

        public override void SetIntensity(float intensity)
        {
            localLight.intensity = Mathf.Clamp(intensity, 0, 1);
            UpdateRemoteLightSettings();
        }

        public override void SetColor(Color color)
        {
            localLight.color = color;
            UpdateRemoteLightSettings();
        }
    }
    ```

    このスクリプトを使用すると、スクリプトがアタッチされているローカルの Unity ライトの種類に応じて、さまざまな種類のリモート ライトを作成できます。 リモート ライトによって、ローカル ライトの位置、回転、色、および強さが複製されます。 可能であれば、リモート ライトに追加の構成も設定します。 Unity ライトは PBR ライトではないため、これは完全には一致しません。

1. シーン内で **DirectionalLight** GameObject を見つけます。 シーンから既定の **DirectionalLight** を削除した場合は、上部のメニュー バーから *[GameObject]、[Light]\(ライト\)、[DirectionalLight]* の順に選択して、シーンに新しいライトを作成します。

1. **[DirectionalLight]** GameObject を選択し、 **[コンポーネントの追加]** ボタンを使用して、**RemoteLight** スクリプトを追加します。

1. このスクリプトによって `BaseRemoteLight` 基底クラスが実装されるため、提供されている **AppMenu** ビュー コントローラーを使用してリモート ライトとやり取りできるようにします。 **[AppMenu]、[Session Tools]\(セッション ツール\)、[指向性ライト]** の順に移動します。

    > [!NOTE]
    > **AppMenu** の UI は、わかりやすくするために 1 つの指向性ライトに制限されています。 ただし、ポイント ライトを追加して **RemoteLight** スクリプトをそれらにアタッチできるので、そうすることをお勧めします。 これらの追加のライトは、エディターで Unity ライトのプロパティを編集することによって変更できます。 インスペクターの **RemoteLight** コンテキスト メニューを使用して、ローカルの変更をリモート ライトに手動で同期する必要があります。
    >
    > ![手動によるリモート ライトの同期](./media/sync-remote-light.png)

1. Unity エディターで [Play]\(再生\) を押し、接続を承認します。

1. ランタイムをリモート セッションに接続したら、カメラを配置して向きを定めます (WASD キーおよび右クリック + マウスの移動を使用します)。これにより、ビューに指向性ライトのビュー コントローラーが表示されます。 
1. ライトのプロパティを変更するには、リモート ライトのビュー コントローラーを使用します。 MRTK の手のシミュレーションを使用して、指向性ライトをつかみ、(Ctrl キーを押しながら) 回転させて、シーンのライティングの効果を確認します。

    ![指向性ライト](./media/directional-light-test.png)

## <a name="editing-materials"></a>素材の編集

リモートでレンダリングされる[素材](../../../concepts/materials.md)を変更することで、視覚効果を追加したり、レンダリングされたモデルのビジュアルを微調整したり、ユーザーに追加のフィードバックを提供したりできます。 素材を変更する方法と理由はさまざまです。 ここでは、素材のアルベド カラーを変更し、PBR 素材の粗さと金属感を変更する方法について説明します。

> [!NOTE]
> 多くの場合、**HierarchicalStateOverrideComponent** を使用して機能または効果を実装できるのであれば、素材を変更するのではなく、そちらを使用することをお勧めします。

ターゲット エンティティを受け入れ、いくつかの `OverrideMaterialProperty` オブジェクトを構成してターゲット エンティティの素材のプロパティを変更するスクリプトを作成します。 まず、ターゲット エンティティの [**MeshComponent**](../../../concepts/meshes.md#meshcomponent) を取得します。これには、メッシュで使用される素材の一覧が含まれています。 わかりやすくするために、最初に見つかった素材のみを使用します。 この単純な戦略は、コンテンツの作成方法によっては非常に簡単に失敗する可能性があるため、より複雑なアプローチを使用して適切な素材を選択することをお勧めします。

素材から、アルベドなどの一般的な値にアクセスできます。 **GetMaterialColor** メソッドで見たように、値を取得するには、まず素材を適切な型 (`PbrMaterial` または `ColorMaterial`) にキャストする必要があります。 目的の素材への参照を取得したら、値を設定するだけで、ARR によってローカルの素材プロパティとリモートの素材の間の同期が処理されます。

1. **EntityMaterialController** という名前のスクリプトを作成し、その内容を次のコードで置き換えます。

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using System.Linq;
    using UnityEngine;
    // to prevent namespace conflicts
    using ARRMaterial = Microsoft.Azure.RemoteRendering.Material;

    public class EntityMaterialController : BaseEntityMaterialController
    {
        public override bool RevertOnEntityChange { get; set; } = true;

        public override OverrideMaterialProperty<Color> ColorOverride { get; set; }
        public override OverrideMaterialProperty<float> RoughnessOverride { get; set; }
        public override OverrideMaterialProperty<float> MetalnessOverride { get; set; }

        private Entity targetEntity;
        public override Entity TargetEntity
        {
            get => targetEntity;
            set
            {
                if (targetEntity != value)
                {
                    if (targetEntity != null && RevertOnEntityChange)
                    {
                        Revert();
                    }

                    targetEntity = value;
                    ConfigureTargetEntity();
                    TargetEntityChanged?.Invoke(value);
                }
            }
        }

        private ARRMaterial targetMaterial;
        private ARRMeshComponent meshComponent;

        public override event Action<Entity> TargetEntityChanged;
        public UnityRemoteEntityEvent OnTargetEntityChanged;

        public void Start()
        {
            // Forward events to Unity events
            TargetEntityChanged += (entity) => OnTargetEntityChanged?.Invoke(entity);

            // If there happens to be a remote RayCaster on this object, assume we should listen for events from it
            if (GetComponent<BaseRemoteRayCastPointerHandler>() != null)
                GetComponent<BaseRemoteRayCastPointerHandler>().RemoteEntityClicked += (entity) => TargetEntity = entity;
        }

        protected override void ConfigureTargetEntity()
        {
            //Get the Unity object, to get the sync object, to get the mesh component, to get the material.
            var targetEntityGameObject = TargetEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

            var localSyncObject = targetEntityGameObject.GetComponent<RemoteEntitySyncObject>();
            meshComponent = targetEntityGameObject.GetComponent<ARRMeshComponent>();
            if (meshComponent == null)
            {
                var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
                if (mesh != null)
                {
                    targetEntityGameObject.BindArrComponent<ARRMeshComponent>(mesh);
                    meshComponent = targetEntityGameObject.GetComponent<ARRMeshComponent>();
                }
            }

            meshComponent.enabled = true;

            targetMaterial = meshComponent.RemoteComponent.Mesh.Materials.FirstOrDefault();
            if (targetMaterial == default)
            {
                return;
            }

            ColorOverride = new OverrideMaterialProperty<Color>(
                GetMaterialColor(targetMaterial), //The original value
                targetMaterial, //The target material
                ApplyMaterialColor); //The action to take to apply the override

            //If the material is a PBR material, we can override some additional values
            if (targetMaterial.MaterialSubType == MaterialType.Pbr)
            {
                var firstPBRMaterial = (PbrMaterial)targetMaterial;

                RoughnessOverride = new OverrideMaterialProperty<float>(
                    firstPBRMaterial.Roughness, //The original value
                    targetMaterial, //The target material
                    ApplyRoughnessValue); //The action to take to apply the override

                MetalnessOverride = new OverrideMaterialProperty<float>(
                    firstPBRMaterial.Metalness, //The original value
                    targetMaterial, //The target material
                    ApplyMetalnessValue); //The action to take to apply the override
            }
            else //otherwise, ensure the overrides are cleared out from any previous entity
            {
                RoughnessOverride = null;
                MetalnessOverride = null;
            }
        }

        public override void Revert()
        {
            if (ColorOverride != null)
                ColorOverride.OverrideActive = false;

            if (RoughnessOverride != null)
                RoughnessOverride.OverrideActive = false;

            if (MetalnessOverride != null)
                MetalnessOverride.OverrideActive = false;
        }

        private Color GetMaterialColor(ARRMaterial material)
        {
            if (material == null)
                return default;

            if (material.MaterialSubType == MaterialType.Color)
                return ((ColorMaterial)material).AlbedoColor.toUnity();
            else
                return ((PbrMaterial)material).AlbedoColor.toUnity();
        }

        private void ApplyMaterialColor(ARRMaterial material, Color color)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Color)
                ((ColorMaterial)material).AlbedoColor = color.toRemoteColor4();
            else
                ((PbrMaterial)material).AlbedoColor = color.toRemoteColor4();
        }

        private void ApplyRoughnessValue(ARRMaterial material, float value)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Pbr) //Only PBR has Roughness
                ((PbrMaterial)material).Roughness = value;
        }

        private void ApplyMetalnessValue(ARRMaterial material, float value)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Pbr) //Only PBR has Metalness
                ((PbrMaterial)material).Metalness = value;
        }
    }
    ```

`OverrideMaterialProperty` 型は、必要に応じて他のいくつかの素材の値を変更できるくらい十分に柔軟である必要があります。 `OverrideMaterialProperty` 型は、オーバーライドの状態を追跡し、古い値と新しい値を保持し、委任を使用してオーバーライドを設定します。 例として、`ColorOverride` を見てみましょう。

```cs
ColorOverride = new OverrideMaterialProperty<Color>(
    GetMaterialColor(targetMaterial), //The original value
    targetMaterial, //The target material
    ApplyMaterialColor); //The action to take to apply the override
```

ここでは、オーバーライドによって `Color` 型がラップされる新しい `OverrideMaterialProperty` を作成しています。 オーバーライドの作成時に、現在または元の色を指定します。 また、操作する ARR 素材も指定します。 最終的に、オーバーライドを適用する委任が提供されます。 委任は、ARR 素材と、オーバーライドによってラップされる型を受け入れるメソッドです。 このメソッドは、ARR によって素材の値がどのように調整されるかを理解するうえで最も重要な部分です。

`ColorOverride` では、`ApplyMaterialColor` メソッドを使用して処理を実行します。

```cs
private void ApplyMaterialColor(ARRMaterial material, Color color)
{
    if (material.MaterialSubType == MaterialType.Color)
        ((ColorMaterial)material).AlbedoColor = color.toRemoteColor4();
    else
        ((PbrMaterial)material).AlbedoColor = color.toRemoteColor4();
}
```

このコードは、素材と色を受け取ります。 素材の種類が確認されてから、色を適用する素材のキャストが実行されます。

`RoughnessOverride` と `MetalnessOverride` は同様に機能します。つまり、`ApplyRoughnessValue` および `ApplyMetalnessValue` メソッドを使用して処理を実行します。

次に、素材コントローラーをテストしてみましょう。

1. **EntityMaterialController** スクリプトを **TestModel** GameObject に追加します。
1. [Play in Unity]\(Unity で再生\) を押してシーンを開始し、ARR に接続します。
1. ランタイムをリモート セッションに接続してモデルを読み込んだら、 **[AppMenu]、[Model Tools]\(モデル ツール\)、[Edit Material]\(素材の編集\)** の順に移動します
1. シミュレートされた手を使用してモデルからエンティティを選択し、 **[TestModel]** をクリックします。
1. 素材ビュー コントローラー ( **[AppMenu]、[Model Tools]\(モデル ツール\)、[Edit Material]\(素材の編集\)** の順に選択) がターゲット エンティティに更新されていることを確認します。
1. 素材ビュー コントローラーを使用して、ターゲット エンティティの素材を調整します。

メッシュの最初の素材のみが変更されているため、素材の変更を確認できない場合があります。 変更対象の素材がメッシュ内部に存在するかどうかを確認するには、階層オーバーライド **SeeThrough** を使用します。

![素材の編集の例](./media/material-edit-example.png)

## <a name="next-steps"></a>次のステップ

お疲れさまでした。 これで、Azure Remote Rendering の主要な機能をすべて実装できました。 次の章では、Azure Remote Rendering と BLOB ストレージのセキュリティ保護について説明します。 これは、Azure Remote Rendering を使用した商用アプリケーションをリリースするための最初の手順となります。

> [!div class="nextstepaction"]
> [次:Azure Remote Rendering とモデル ストレージのセキュリティ保護](../security/security.md)