---
title: Unity のゲーム オブジェクトとコンポーネント
description: Remote Rendering のエンティティおよびコンポーネントを操作するための Unity 固有のメソッドについて説明します。
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 15822c357db63db81e6c1efda2467279a98d7c34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594148"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Unity のゲーム オブジェクトとコンポーネントを操作する

Azure Remote Rendering (ARR) は、膨大な数のオブジェクト向けに最適化されています (「[制限事項](../../reference/limits.md)」を参照)。 ホスト上で大規模で複雑な階層を管理することは可能ですが、すべてを低電力デバイス上の Unity でレプリケートすることは不可能です。

そのため、モデルがホストに読み込まれると、Azure Remote Rendering によってモデル構造に関する情報がクライアント デバイス上にミラー化されますが (これによりネットワーク トラフィックが発生します)、Unity のオブジェクトとコンポーネントはレプリケートされません。 代わりに、必要な Unity のゲーム オブジェクトとコンポーネントを手動で要求することを想定しています。これにより、オーバーヘッドを最低限必要な量に制限できます。 このようにすることで、クライアント側のパフォーマンスをより細かく制御できます。

そのため、Azure Remote Rendering の Unity 統合には、必要に応じて Remote Rendering 構造をレプリケートするための追加機能が用意されています。

## <a name="load-a-model-in-unity"></a>Unity でモデルを読み込む

モデルを読み込むと、読み込まれたモデルのルート オブジェクトへの参照が取得されます。 この参照は Unity のゲーム オブジェクトではありませんが、拡張メソッド `Entity.GetOrCreateGameObject()` を使用してこれに変換することができます。 この関数には `UnityCreationMode` 型の引数が必要です。 `CreateUnityComponents` を渡すと、新しく作成された Unity のゲーム オブジェクトに、ホスト上に存在するすべての Remote Rendering コンポーネントのプロキシ コンポーネントが追加されます。 ただし、`DoNotCreateUnityComponents` を優先する場合は、オーバーヘッドを最小限に抑えることをお勧めします。

### <a name="load-model-with-unity-coroutines"></a>Unity コルーチンを使用したロード モデル

```cs
IEnumerator LoadModelWithCoroutine(RenderingSession session)
{
    float currentProgress = 0.0f;
    var task = session.Connection.LoadModelFromSasAsync(new LoadModelFromSasOptions("builtin://Engine"),
        (float progress) =>
        {
            currentProgress = progress;
        });

    while (!task.IsCompleted && !task.IsFaulted)
    {
        int percentage = (int)(currentProgress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }
}
```

### <a name="load-model-with-await-pattern"></a>await パターンを使用したロード モデル

```cs
async void LoadModelWithAwait(RenderingSession session)
{
    var result = await session.Connection.LoadModelFromSasAsync(new LoadModelFromSasOptions("builtin://Engine"), null);
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

上記のコード サンプルでは、組み込みモデルが読み込まれるため、SAS 経由でモデル読み込みパスを使用しました。 BLOB コンテナーを介してモデルにアドレスを指定 (`LoadModelAsync` と `LoadModelOptions`を使用) しても、同様に機能します。

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

Unity のゲーム オブジェクトを作成すると、`RemoteEntitySyncObject` コンポーネントが暗黙的にゲーム オブジェクトに追加されます。 このコンポーネントは、エンティティ変換をサーバーに同期するために使用されます。 既定では `RemoteEntitySyncObject` は、ローカルの Unity 状態をサーバーと同期するために、ユーザーが `SyncToRemote()` を明示的に呼び出す必要があります。 `SyncEveryFrame` を有効にすると、オブジェクトが自動的に同期されます。

`RemoteEntitySyncObject` を持つオブジェクトでは、リモートの子をインスタンス化し、Unity エディターで **:::no-loc text="Show children":::** ボタンを使用して表示できます。

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>ラッパー コンポーネント

Remote Rendering エンティティにアタッチされている[コンポーネント](../../concepts/components.md)は、プロキシの `MonoBehavior` を介して Unity に公開されます。 これらのプロキシは Unity のリモート コンポーネントを表し、すべての変更をホストに転送します。

プロキシの Remote Rendering コンポーネントを作成するには、拡張メソッド `GetOrCreateArrComponent` を使用します。

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>結合された有効期間

リモート [エンティティ](../../concepts/entities.md) と Unity のゲーム オブジェクトの有効期間は、`RemoteEntitySyncObject` を通じてバインドされている間に結合されます。 このようなゲーム オブジェクトを使用して `UnityEngine.Object.Destroy(...)` を呼び出すと、リモート エンティティも削除されます。

リモート エンティティに影響を与えずに Unity のゲーム オブジェクトを破棄するには、最初に `RemoteEntitySyncObject` で `Unbind()` を呼び出す必要があります。

すべてのプロキシ コンポーネントにも同じことが当てはまります。 クライアント側の表現のみを破棄するには、まずプロキシ コンポーネントで `Unbind()` を呼び出す必要があります。

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>次のステップ

* [Unity 向けに Remote Rendering を設定する](unity-setup.md)
* [チュートリアル:Unity でリモート エンティティを操作する](../../tutorials/unity/manipulate-models/manipulate-models.md)
