---
title: モデル
description: Azure Remote Rendering におけるモデルの概要について説明します
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 9fd18195c0276999f445e0990838e293a5081131
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021886"
---
# <a name="models"></a>モデル

Azure Remote Rendering の*モデル*とは、[エンティティ](entities.md)と[コンポーネント](components.md)で構成される完全なオブジェクト表現を指します。 モデルは、リモート レンダリング サービスにカスタム データを取得するための主な方法です。

## <a name="model-structure"></a>モデルの構造

モデルには、そのルート ノードとして[エンティティ](entities.md)が 1 つだけあります。 その下に、任意の子エンティティの階層を含めることができます。 モデルを読み込むときに、このルート エンティティへの参照が返されます。

各エンティティには、[コンポーネント](components.md)がアタッチされている場合があります。 最も一般的なケースでは、エンティティには[メッシュ リソース](meshes.md)を参照する *MeshComponents* があります。

## <a name="creating-models"></a>モデルの作成

ランタイム用のモデルの作成は、FBX や GLTF などのファイル形式から[入力モデルを変換する](../how-tos/conversion/model-conversion.md)ことによって行われます。 変換プロセスでは、テクスチャ、素材、メッシュなどのすべてのリソースが抽出され、最適化されたランタイム形式に変換されます。 また、構造情報が抽出され、それが ARR のエンティティまたはコンポーネント グラフ構造に変換されます。

> [!IMPORTANT]
>
> [モデルの変換](../how-tos/conversion/model-conversion.md)は、[メッシュ](meshes.md)を作成する唯一の方法です。 メッシュは実行時にエンティティ間で共有できますが、ランタイムにメッシュを取得する方法は、モデルを読み込む以外にありません。

## <a name="loading-models"></a>モデルの読み込み

変換されたモデルは、Azure Blob Storage からランタイムに読み込むことができます。

BLOB ストレージでのアセットのアドレス指定方法が異なる、2 つの別個の読み込み関数があります。

* モデルは、その SAS URI でアドレス指定できます。 関連する読み込み関数は、`LoadModelFromSASAsync` とパラメーター `LoadModelFromSASParams` です。 [組み込みのモデル](../samples/sample-model.md)を読み込む場合にもこのバリアントを使用します。
* [BLOB ストレージがアカウントにリンクされている](../how-tos/create-an-account.md#link-storage-accounts)場合、BLOB ストレージ パラメーターによってモデルを直接アドレス指定することができます。 この場合、関連する読み込み関数は、`LoadModelAsync` とパラメーター `LoadModelParams` です。

次のコード スニペットは、いずれかの関数を使用してモデルを読み込む方法を示しています。 SAS URI を使用してモデルを読み込むには、次のようなコードを使用します。

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelFromSASParams(modelUri, modelParent);

    var loadOp = session.Actions.LoadModelFromSASAsync(modelParams);

    loadOp.ProgressUpdated += (float progress) =>
    {
        Debug.Log($"Loading: {progress * 100.0f}%");
    };

    await loadOp.AsTask();
}
```

```cpp
ApiHandle<LoadModelAsync> LoadModel(ApiHandle<AzureSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSASParams modelParams;
    modelParams.ModelUrl = modelUri;
    modelParams.Parent = modelParent;

    ApiHandle<LoadModelAsync> loadOp = *session->Actions()->LoadModelFromSASAsync(modelParams);

    loadOp->Completed([](const ApiHandle<LoadModelAsync>& async)
    {
        printf("Loading: finished.");
    });
    loadOp->ProgressUpdated([](float progress)
    {
        printf("Loading: %.1f%%", progress*100.f);
    });

    return loadOp;
}
```

BLOB ストレージ パラメーターを直接使用してモデルを読み込む場合は、次のスニペットのようなコードを使用します。

```csharp
async void LoadModel(AzureSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelParams = new LoadModelParams(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Actions.LoadModelAsync(modelParams);

    // ... (identical to the SAS URI snippet above)
}
```

```cpp
ApiHandle<LoadModelAsync> LoadModel(ApiHandle<AzureSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelParams modelParams;
    modelParams.Parent = modelParent;
    modelParams.Blob.StorageAccountName = std::move(storageAccount);
    modelParams.Blob.BlobContainerName = std::move(containerName);
    modelParams.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelAsync> loadOp = *session->Actions()->LoadModelAsync(modelParams);
    // ... (identical to the SAS URI snippet above)
}
```

その後、エンティティ階層を走査し、エンティティとコンポーネントを変更することができます。 同じモデルを複数回読み込むと、複数のインスタンスが作成され、それぞれにエンティティまたはコンポーネント構造のコピーが作成されます。 一方、メッシュ、素材、テクスチャは[共有リソース](../concepts/lifetime.md)であるため、それらのデータは再度読み込まれません。 したがって、モデルを複数回インスタンス化すると、比較的小さいメモリのオーバーヘッドが発生します。

> [!CAUTION]
> ARR ではすべての *Async* 関数が非同期操作オブジェクトを返します。 操作が完了するまで、これらのオブジェクトへの参照を保存する必要があります。 そうしないと、C# ガベージ コレクターによって操作が早期に削除されて、完了できなくなる場合があります。 上のサンプル コードでは、*await* を使用して、モデルの読み込みが完了するまでローカル変数 'loadOp' で参照が保持されることを保証しています。 ただし、代わりに *Completed* イベントを使用する場合は、非同期操作をメンバー変数に格納する必要があります。

## <a name="next-steps"></a>次のステップ

* [エンティティ](entities.md)
* [メッシュ](meshes.md)
