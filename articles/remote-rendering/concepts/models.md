---
title: モデル
description: Azure Remote Rendering におけるモデルの概要について説明します
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 0d1e66d09db3e3934871ed15493feb685d1cbe6a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593876"
---
# <a name="models"></a>モデル

Azure Remote Rendering の *モデル* とは、[エンティティ](entities.md)と [コンポーネント](components.md)で構成される完全なオブジェクト表現を指します。 モデルは、リモート レンダリング サービスにカスタム データを取得するための主な方法です。

## <a name="model-structure"></a>モデルの構造

モデルには、そのルート ノードとして[エンティティ](entities.md)が 1 つだけあります。 その下に、任意の子エンティティの階層を含めることができます。 モデルを読み込むときに、このルート エンティティへの参照が返されます。

各エンティティには、[コンポーネント](components.md)がアタッチされている場合があります。 最も一般的なケースでは、エンティティには [メッシュ リソース](meshes.md)を参照する *MeshComponents* があります。

## <a name="creating-models"></a>モデルの作成

ランタイム用のモデルの作成は、FBX や GLTF などのファイル形式から[入力モデルを変換する](../how-tos/conversion/model-conversion.md)ことによって行われます。 変換プロセスでは、テクスチャ、素材、メッシュなどのすべてのリソースが抽出され、最適化されたランタイム形式に変換されます。 また、構造情報が抽出され、それが ARR のエンティティまたはコンポーネント グラフ構造に変換されます。

> [!IMPORTANT]
> [モデルの変換](../how-tos/conversion/model-conversion.md)は、[メッシュ](meshes.md)を作成する唯一の方法です。 メッシュは実行時にエンティティ間で共有できますが、ランタイムにメッシュを取得する方法は、モデルを読み込む以外にありません。

## <a name="loading-models"></a>モデルの読み込み

変換されたモデルは、Azure Blob Storage からランタイムに読み込むことができます。

BLOB ストレージでのアセットのアドレス指定方法が異なる、2 つの別個の読み込み関数があります。

* [BLOB ストレージがアカウントにリンクされている](../how-tos/create-an-account.md#link-storage-accounts)場合、BLOB ストレージ パラメーターによってモデルを直接アドレス指定することができます。 この場合、関連する読み込み関数は、`LoadModelAsync` とパラメーター `LoadModelOptions` です。
* モデルは、その SAS URI でアドレス指定できます。 関連する読み込み関数は、`LoadModelFromSasAsync` とパラメーター `LoadModelFromSasOptions` です。 [組み込みのモデル](../samples/sample-model.md)を読み込む場合にもこのバリアントを使用します。

次のコード スニペットは、いずれかの関数を使用してモデルを読み込む方法を示しています。 Blob Storage パラメーターを使用してモデルを読み込むには、次のようなコードを使用します。


```cs
async void LoadModel(RenderingSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelOptions(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Connection.LoadModelAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelOptions modelOptions;
    modelOptions.Parent = modelParent;
    modelOptions.Blob.StorageAccountName = std::move(storageAccount);
    modelOptions.Blob.BlobContainerName = std::move(containerName);
    modelOptions.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

SAS トークンを使用してモデルを読み込む場合は、次のスニペットのようなコードを使用します。

```cs
async void LoadModel(RenderingSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelFromSasOptions(modelUri, modelParent);

    var loadOp = session.Connection.LoadModelFromSasAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSasOptions modelOptions;
    modelOptions.ModelUri = modelUri;
    modelOptions.Parent = modelParent;

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelFromSasAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

その後、エンティティ階層を走査し、エンティティとコンポーネントを変更することができます。 同じモデルを複数回読み込むと、複数のインスタンスが作成され、それぞれにエンティティまたはコンポーネント構造のコピーが作成されます。 一方、メッシュ、素材、テクスチャは[共有リソース](../concepts/lifetime.md)であるため、それらのデータは再度読み込まれません。 したがって、モデルを複数回インスタンス化すると、比較的小さいメモリのオーバーヘッドが発生します。

## <a name="api-documentation"></a>API のドキュメント

* [C# RenderingConnection.LoadModelAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelasync)
* [C# RenderingConnection.LoadModelFromSasAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelfromsasasync)
* [C++ RenderingConnection::LoadModelAsync()](/cpp/api/remote-rendering/renderingconnection#loadmodelasync)
* [C++ RenderingConnection::LoadModelFromSasAsync()](/cpp/api/remote-rendering/renderingconnection#loadmodelfromsasasync)

## <a name="next-steps"></a>次のステップ

* [エンティティ](entities.md)
* [メッシュ](meshes.md)