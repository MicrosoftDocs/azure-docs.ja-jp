---
title: モデルの操作
description: リモートでレンダリングされたモデルに対し、移動、回転、拡大縮小などの操作を実行します
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 301d6eb0867604a6e780eb1d059eef0c153f246f
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574511"
---
# <a name="tutorial-manipulating-models"></a>チュートリアル:モデルの操作

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * リモートでレンダリングされたモデルの周りに視覚および操作の境界を追加する
> * 移動、回転、拡大縮小を行う
> * 空間クエリを使用してレイキャストを実行する
> * リモートでレンダリングされたオブジェクトに簡単なアニメーションを追加する

## <a name="prerequisites"></a>前提条件

* このチュートリアルは、「[チュートリアル: インターフェイスとカスタム モデル](../custom-models/custom-models.md)」に基づいて作成されています。

## <a name="query-remote-object-bounds-and-apply-to-local-bounds"></a>リモート オブジェクトの境界を照会してローカルの境界に適用する

リモート オブジェクトを操作するには、まず、その対象となるローカルな表現が必要となります。 [オブジェクトの境界](../../../concepts/object-bounds.md)は、リモート オブジェクトを簡単に操作するのに役立ちます。 リモートの境界は、ARR から、ローカル エンティティを参照に使用して照会することができます。 境界のクエリは、リモート セッションにモデルが読み込まれた後で実行します。

モデルの境界は、モデル全体を含んだボックスで定義されます。x、y、z 軸に対して中心とサイズが定義されている、Unity の [**BoxCollider**](https://docs.unity3d.com/Manual/class-BoxCollider.html) に似ています。 実際に Unity の **BoxCollider** を使用して、リモート モデルの境界を表現しましょう。

1. **RemoteRenderedModel** と同じディレクトリに新しいスクリプトを作成し、**RemoteBounds** という名前を付けます。
1. スクリプトの内容を、次のコードに置き換えます。

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(BaseRemoteRenderedModel))]
    public class RemoteBounds : BaseRemoteBounds
    {
        //Remote bounds works with a specific remotely rendered model
        private BaseRemoteRenderedModel targetModel = null;

        private RemoteBoundsState currentBoundsState = RemoteBoundsState.NotReady;

        public override RemoteBoundsState CurrentBoundsState
        {
            get => currentBoundsState;
            protected set
            {
                if (currentBoundsState != value)
                {
                    currentBoundsState = value;
                    BoundsStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<RemoteBoundsState> BoundsStateChange;

        public void Awake()
        {
            BoundsStateChange += HandleUnityEvents;
            targetModel = GetComponent<BaseRemoteRenderedModel>();

            targetModel.ModelStateChange += TargetModel_OnModelStateChange;
            TargetModel_OnModelStateChange(targetModel.CurrentModelState);
        }

        private void TargetModel_OnModelStateChange(ModelState state)
        {
            switch (state)
            {
                case ModelState.Loaded:
                    QueryBounds();
                    break;
                default:
                    BoundsBoxCollider.enabled = false;
                    CurrentBoundsState = RemoteBoundsState.NotReady;
                    break;
            }
        }

        // Create an async query using the model entity
        async private void QueryBounds()
        {
            //Implement me
        }
    }
    ```

    > [!NOTE]
    > Visual Studio で "*機能 'X' は C# 6 では使用できません。7.0 以上の言語バージョンを使用してください*" というエラーが表示される場合、このようなエラーは無視してかまいません。 これは Unity のソリューションとプロジェクトの生成に関連しています。

    このスクリプトは、**BaseRemoteRenderedModel** を実装するスクリプトと同じ GameObject に追加する必要があります。 つまり、この場合は、**RemoteRenderedModel** です。 前のスクリプトと同様に、リモート境界に関連するすべての状態変化、イベント、データは、この初期コードによって処理されます。

    別途実装すべきメソッドは 1 つだけです。**QueryBounds** です。 **QueryBounds** が非同期的に境界を取得し、クエリの結果を受け取って、それをローカルの **BoxCollider** に適用します。

    **QueryBounds** メソッドは、リモート レンダリング セッションにクエリを送信して、結果を待つだけの簡単なものです。

1. **QueryBounds** メソッドを次の完成したメソッドに置き換えます。

    ```cs
    // Create a query using the model entity
    async private void QueryBounds()
    {
        var remoteBounds = targetModel.ModelEntity.QueryLocalBoundsAsync();
        CurrentBoundsState = RemoteBoundsState.Updating;
        await remoteBounds;

        if (remoteBounds.IsCompleted)
        {
            var newBounds = remoteBounds.Result.toUnity();
            BoundsBoxCollider.center = newBounds.center;
            BoundsBoxCollider.size = newBounds.size;
            BoundsBoxCollider.enabled = true;
            CurrentBoundsState = RemoteBoundsState.Ready;
        }
        else
        {
            CurrentBoundsState = RemoteBoundsState.Error;
        }
    }
    ```

    クエリの結果をチェックして、成功したかどうかを確認します。 成功した場合は、返された境界を、**BoxCollider** で使用できる形式に変換して適用します。

ここで、**RemoteBounds** スクリプトを **RemoteRenderedModel** と同じゲーム オブジェクトに追加すると、必要に応じて **BoxCollider** が追加され、モデルが `Loaded` 状態になったときに、境界が自動的に照会されて **BoxCollider** に適用されます。

1. 前に作成した **TestModel** GameObject を使用して、**RemoteBounds** コンポーネントを追加します。
1. スクリプトが追加されていることを確認します。

     ![RemoteBounds コンポーネントを追加する](./media/remote-bounds-script.png)

1. アプリケーションをもう一度実行する モデルが読み込まれるとすぐに、リモート オブジェクトの境界が表示されます。 以下のような値が表示されます。

     ![リモート オブジェクトの境界の例を示すスクリーンショット。](./media/updated-bounds.png)

Unity オブジェクトで境界を厳密に指定して、ローカル **BoxCollider** を構成しました。 この境界を利用すれば、ローカルにレンダリングされたオブジェクトと同じ方法を使用して、ビジュアル化とインタラクションを実行することができます。 たとえば、トランスフォームや物理演算などを変更するスクリプトです。

## <a name="move-rotate-and-scale"></a>移動、回転、拡大縮小を行う  

リモートでレンダリングされたオブジェクトの移動、回転、拡大縮小のしくみは、他の Unity オブジェクトと同じです。 **RemoteRenderingCoordinator** は、その `LateUpdate` メソッドの中で、現在アクティブなセッションに対して `Update` を呼び出します。 `Update` の役割は、ローカル モデル エンティティのトランスフォームをそれに対応するリモート モデルと同期させることです。 リモートでレンダリングされたモデルの移動、回転、拡大縮小を行うには、リモート モデルを表す GameObject のトランスフォームを移動、回転、拡大縮小するだけです。 ここでは、**RemoteRenderedModel** スクリプトがアタッチされている親 GameObject のトランスフォームに変更を加えます。

このチュートリアルでは、オブジェクトのインタラクションに MRTK を使用しています。 オブジェクトの移動、回転、拡大縮小に使用される MRTK 固有の実装は、その多くがこのチュートリアルの範囲を超えています。 **AppMenu** 内の **[Model Tools]\(モデル ツール\)** メニューには、事前構成済みの Model View Controller が用意されています。

1. 前に作成した **TestModel** GameObject がシーンに存在することを確認します。
1. **AppMenu** プレハブがシーンに存在することを確認します。
1. Unity の [Play]\(再生\) ボタンを押してシーンを再生し、**AppMenu** 内の **[Model Tools]\(モデル ツール\)** メニューを開きます。
![ビュー コントローラー](./media/model-with-view-controller.png)

**AppMenu** には、モデルとのバインドに使用されるビュー コントローラーを実装した **[Model Tools]\(モデル ツール\)** サブメニューがあります。 この GameObject に **RemoteBounds** コンポーネントが含まれている場合、[**BoundingBox**](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_BoundingBox.html) コンポーネント (**BoxCollider** を使用してオブジェクトの周囲に境界ボックスをレンダリングする MRTK コンポーネント) がビュー コントローラーによって追加されます。 ハンド インタラクションは、[**ObjectManipulator**](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/api/Microsoft.MixedReality.Toolkit.UI.ObjectManipulator.html) が担います。 これらのスクリプトを組み合わせることで、リモートでレンダリングされたモデルの移動、回転、拡大縮小を行うことができます。

1. ゲーム パネルにマウスを移動し、その中でクリックしてフォーカスを設定します。
1. [MRTK のハンド シミュレーション](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/InputSimulation/InputSimulationService.html#hand-simulation)を使用して、左 Shift キーを長押しします。
1. シミュレートされた手を操作して、ハンド レイがテスト モデルを指すようにします。

    ![ハンド レイによるポインティング](./media/handray-engine.png)

1. マウスの左ボタンでモデルをクリックしたままドラッグして移動します。

リモートでレンダリングされたコンテンツが境界ボックスと一緒に移動するようすを確認できます。 境界ボックスとリモート コンテンツとの間で多少の遅れ (時間差) が生じる場合があります。 この遅れは、インターネットの待ち時間や帯域幅によって異なります。

## <a name="ray-cast-and-spatial-queries-of-remote-models"></a>リモート モデルの空間クエリとレイ キャスト

モデルを囲むボックス コライダーは、モデル全体の操作には適していますが、モデルの各部分を操作できるほどの細密さは備えていません。 これを解決するために、ここでは[リモート レイ キャスト](../../../overview/features/spatial-queries.md#ray-casts)を使用します。 リモート レイ キャストは、Azure Remote Rendering が提供する API です。リモート シーンにレイ (光線) をキャストし、ヒット結果をローカルに返します。 この手法を使用して、大きなモデルの子エンティティを選択したり、ヒット結果の情報 (位置、表面法線、距離など) を取得したりすることができます。

このテスト モデルには、照会したり選択したりできるサブエンティティが多数存在します。 現時点では、エンティティを選択すると、その名前が Unity コンソールに出力されます。 選択したエンティティの強調表示については、[素材、ライト、効果](../materials-lighting-effects/materials-lighting-effects.md#highlighting-and-outlining)に関するセクションを参照してください。

まず、リモート レイ キャスト クエリの静的ラッパーを作成しましょう。 このスクリプトは、Unity 空間の位置と方向を受け取り、リモート レイ キャストで使用できるデータ型に変換して結果を返します。 このスクリプトでは、`RayCastQueryAsync` API が利用されます。

1. **RemoteRayCaster** という新しいスクリプトを作成し、その内容を次のコードに置き換えます。

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    /// <summary>
    /// Wraps the Azure Remote Rendering RayCast queries to easily send requests using Unity data types
    /// </summary>
    public class RemoteRayCaster
    {
        public static double maxDistance = 30.0;

        public static async Task<RayCastHit[]> RemoteRayCast(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            if(RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
            {
                var rayCast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), maxDistance, hitPolicy);
                var result = await RemoteRenderingCoordinator.CurrentSession.Connection.RayCastQueryAsync(rayCast);
                return result.Hits;
            }
            else
            {
                return new RayCastHit[0];
            }
        }

        public static async Task<Entity[]> RemoteRayCastEntities(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            var hits = await RemoteRayCast(origin, dir, hitPolicy);
            return hits.Select(hit => hit.HitEntity).Where(entity => entity != null).ToArray();
        }
    }
    ```

    > [!NOTE]
    > Unity には [**RaycastHit**](https://docs.unity3d.com/ScriptReference/RaycastHit.html) という名前のクラスがあり、また、Azure Remote Rendering には [**RayCastHit**](/dotnet/api/microsoft.azure.remoterendering.raycasthit) という名前のクラスがあります。 コンパイル エラーを防ぐうえで、**C** の大文字と小文字の区別が重要となります。

    **RemoteRayCaster** は、リモート レイを現在のセッションにキャストするための共通のアクセス ポイントとなります。 この後、実際に MRTK ポインター ハンドラーを実装します。 このスクリプトに `IMixedRealityPointerHandler` インターフェイスを実装することにより、[Mixed Reality Pointer](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/Input/Pointers.html) イベントをリッスンしたいという意図が MRTK に伝えられます。

1. **RemoteRayCastPointerHandler** という新しいスクリプトを作成し、そのコードを次のコードに置き換えます。

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.MixedReality.Toolkit.Input;
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    public class RemoteRayCastPointerHandler : BaseRemoteRayCastPointerHandler, IMixedRealityPointerHandler
    {
        public UnityRemoteEntityEvent OnRemoteEntityClicked = new UnityRemoteEntityEvent();

        public override event Action<Entity> RemoteEntityClicked;

        public void Awake()
        {
            // Forward events to Unity events
            RemoteEntityClicked += (entity) => OnRemoteEntityClicked?.Invoke(entity);
        }

        public async void OnPointerClicked(MixedRealityPointerEventData eventData)
        {
            if (RemoteEntityClicked != null) //Ensure someone is listening before we do the work
            {
                var firstHit = await PointerDataToRemoteRayCast(eventData.Pointer);
                if (firstHit.success)
                    RemoteEntityClicked.Invoke(firstHit.hit.HitEntity);
            }
        }

        public void OnPointerDown(MixedRealityPointerEventData eventData) { }

        public void OnPointerDragged(MixedRealityPointerEventData eventData) { }

        public void OnPointerUp(MixedRealityPointerEventData eventData) { }

        private async Task<(bool success, RayCastHit hit)> PointerDataToRemoteRayCast(IMixedRealityPointer pointer, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            RayCastHit hit;
            var result = pointer.Result;
            if (result != null)
            {
                var endPoint = result.Details.Point;
                var direction = pointer.Rays[pointer.Result.RayStepIndex].Direction;
                Debug.DrawRay(endPoint, direction, Color.green, 0);
                hit = (await RemoteRayCaster.RemoteRayCast(endPoint, direction, hitPolicy)).FirstOrDefault();
            }
            else
            {
                hit = new RayCastHit();
            }
            return (hit.HitEntity != null, hit);
        }
    }
    ```

**RemoteRayCastPointerHandler** の `OnPointerClicked` メソッドは、ポインターによってコライダー (ここで取り上げたボックス コライダーなど) が "クリック" されたときに MRTK によって呼び出されます。 その後、`PointerDataToRemoteRayCast` が呼び出されて、ポインターの結果が点と方向に変換されます。 さらに、その点と方向を使用して、リモート セッションでリモート レイがキャストされます。

![境界が更新されたところ](./media/raycast-local-remote.png)

クリック時にレイ キャスト要求を送信するのは、リモート オブジェクトにクエリを実行する効率的な方法です。 しかし理想的なユーザー エクスペリエンスではありません。なぜなら、カーソルが衝突するのはボックス コライダーであって、モデルそのものではないためです。

リモート セッションでもっと頻繁にレイをキャストする新しい MRTK ポインターを作成することもできます。 これはより複雑なアプローチですが、ユーザー エクスペリエンスは向上するでしょう。 この手法は、このチュートリアルの範囲外ですが、[ARR サンプル リポジトリ](https://github.com/Azure/azure-remote-rendering/tree/master/Unity/Showcase)にあるショーケース アプリで、このアプローチの例を確認できます。

**RemoteRayCastPointerHandler** でレイ キャストが正常に完了すると、ヒットした `Entity` が `OnRemoteEntityClicked` Unity イベントから生成されます。 そのイベントに応答するには、`Entity` を受け取って、それに対するアクションを実行するヘルパー スクリプトを作成します。 まず、スクリプトで `Entity` の名前をデバッグ ログに出力してみましょう。

1. **RemoteEntityHelper** という名前の新しいスクリプトを作成し、その内容を次のように置き換えます。

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.
    
    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using UnityEngine;
    
    public class RemoteEntityHelper : MonoBehaviour
    {
        public void EntityToDebugLog(Entity entity)
        {
            Debug.Log(entity.Name);
        }
    }
    ```

2. 前に作成した **TestModel** GameObject で、**RemoteRayCastPointerHandler** コンポーネントと **RemoteEntityHelper** コンポーネントの両方を追加します。
1. `OnRemoteEntityClicked` イベントに `EntityToDebugLog` メソッドを割り当てます。 イベントの出力の型とメソッドの入力の型が一致すると、Unity の動的イベント フックアップを使用できます。これにより、イベントの値が自動的にメソッドに渡されます。
    1. 新しいコールバック フィールドを作成します ![コールバックを追加する](./media/add-callback-remote-entity-clicked.png)
    1. 親 GameObject を参照するために、**Remote Entity Helper** コンポーネントを Object フィールドにドラッグします ![オブジェクトを割り当てる](./media/assign-object.png)
    1. `EntityToDebugLog` をコールバックとして割り当てます  ![コールバックを割り当てる](./media/remote-entity-event.png)
1. Unity エディターで [play]\(再生\) を押してシーンを開始し、リモート セッションに接続してテスト モデルを読み込みます。
1. MRTK のハンド シミュレーションを使用して、左 Shift キーを長押しします。
1. シミュレートされた手を操作して、ハンド レイがテスト モデルを指すようにします。
1. 長押ししてエアタップをシミュレートし、`OnPointerClicked` イベントを実行します。
1. 選択した子エンティティの名前が含まれたログ メッセージを Unity コンソールで確認します。 次に例を示します。![子エンティティの例](./media/child-entity-example.png)

## <a name="synchronizing-the-remote-object-graph-into-the-unity-hierarchy"></a>リモート オブジェクト グラフを Unity 階層に同期させる

ここまでは、モデル全体を表す単一のローカル GameObject だけを見てきました。 モデル全体のレンダリングと操作であれば、それでうまく機能します。 しかし、効果を適用したり、特定のサブエンティティを操作したりする場合は、それらのエンティティを表すローカル GameObject の作成が必要になります。 まず、テスト モデルで手動で検証してみましょう。

1. シーンを開始し、テスト モデルを読み込みます。
1. Unity の階層で **TestModel** GameObject の子を展開し、**TestModel_Entity** GameObject を選択します。
1. インスペクターで、 *[Show Children]\(子の表示\)* ボタンをクリックします。
![子の表示](./media/show-remote-children.png)
1. 多数の子の一覧が表示されるまで、引き続き *[Show Children]\(子の表示\)* をクリックしながら階層の子を展開していきます。
![すべての子](./media/test-model-children.png)

多数のエンティティの一覧が階層表示されます。 そのいずれかを選択すると、`Transform` コンポーネントと `RemoteEntitySyncObject` コンポーネントがインスペクターに表示されます。 既定では、各エンティティが自動的にはフレームごとに同期されないので、`Transform` に対するローカルな変更がサーバーとの間で同期されません。 *[Sync Every Frame]\(フレームごとに同期\)* チェック ボックスをオンにして、[Scene]\(シーン\) ビューでトランスフォームを移動、拡大縮小、回転することができます。[Scene]\(シーン\) ビューには、レンダリングされたモデルが表示されません。モデルの位置と回転が視覚的に更新されるようすを確認するには、[Game]\(ゲーム\) ビューを監視してください。

同じプロセスはプログラムから実行でき、それが特定のリモート エンティティを変更するうえでの最初のステップとなります。

1. **RemoteEntityHelper** スクリプトに次のメソッドを追加します。

    ```cs
    public void MakeSyncedGameObject(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var sync = entityGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;
    }
    ```

1. **RemoteRayCastPointerHandler** イベント `OnRemoteEntityClicked` にコールバックをもう 1 つ追加し、それを `MakeSyncedGameObject` に設定します。
![コールバックを追加する](./media/additional-callback.png)
1. MRTK のハンド シミュレーションを使用して、左 Shift キーを長押しします。
1. シミュレートされた手を操作して、ハンド レイがテスト モデルを指すようにします。
1. 長押ししてエアタップをシミュレートし、`OnPointerClicked` イベントを実行します。
1. 階層をチェックして展開し、クリックされたエンティティを表す新しい子オブジェクトを表示します。
![GameObject の表現](./media/gameobject-representing-entity.png)
1. テスト後、`MakeSyncedGameObject` のコールバックを削除します。このコールバックは、後で他の効果の一部として組み込みます。

> [!NOTE]
> すべてのフレームを同期する必要があるのは、トランスフォームのデータを同期する必要があるときだけです。 トランスフォームの同期は、ある程度のオーバーヘッドが伴うため、多用しないでください。

ローカル インスタンスを作成し、自動的に同期させるのが、サブエンティティを操作するうえでの最初のステップとなります。 モデル全体を操作するために使用した手法は、サブエンティティにも同じように使用できます。 たとえば、あるエンティティについて、同期されたローカル インスタンスを作成した後、その境界を照会し、操作ハンドラーを追加すれば、ユーザーのハンド レイで自由に移動することができます。

## <a name="next-steps"></a>次のステップ

ここでは、リモート レンダリングされたモデルを操作する方法について説明しました。 次のチュートリアルでは、リモート レンダリングされたモデルへの素材の変更、ライトの変更、効果の適用について取り上げます。

> [!div class="nextstepaction"]
> [次:素材、ライト、および効果の調整](../materials-lighting-effects/materials-lighting-effects.md)