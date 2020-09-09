---
title: Unity 用の Remote Rendering パッケージをインストールする
description: Unity 用の Remote Rendering クライアント DLL をインストールする方法について説明します
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 3704d1a418baeec18c3303b8203a0185790cbcc7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564299"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Unity 用の Remote Rendering パッケージをインストールする

Azure Remote Rendering では、Unity パッケージを使用して Unity への統合をカプセル化します。

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Unity での Remote Rendering パッケージの管理

Unity パッケージは、Unity の[パッケージ マネージャー](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)を介して管理できるコンテナーです。
このパッケージには、C# API 全体に加えて、Unity で Azure Remote Rendering を使用するために必要なすべてのプラグイン バイナリが含まれています。
Unity のパッケージの命名規則に従い、パッケージは **com.microsoft.azure.remote-rendering** と呼ばれます。

このパッケージは [ARR サンプル リポジトリ](https://github.com/Azure/azure-remote-rendering)の一部ではなく、Unity の内部パッケージ レジストリからは使用できません。 プロジェクトに追加するには、プロジェクトの `manifest.md` ファイルを手動で編集して、次のものを追加する必要があります。

```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.31",
    ...existing dependencies...
  }
}
```

これが追加されたら、Unity のパッケージ マネージャーを使用して、最新バージョンがあることを確認できます。
より包括的な手順については、次をご覧ください: 「[チュートリアル: リモート モデルの表示](../../tutorials/unity/view-remote-models/view-remote-models.md)」。

## <a name="unity-render-pipelines"></a>Unity のレンダリング パイプライン

Remote Rendering は、 **:::no-loc text="Universal render pipeline":::** と **:::no-loc text="Standard render pipeline":::** の両方で機能します。 パフォーマンス上の理由から、ユニバーサル レンダリング パイプラインを使用することをお勧めします。

**:::no-loc text="Universal render pipeline":::** を使用するには、Unity にそのパッケージをインストールする必要があります。 これを行うには、Unity の **パッケージ マネージャー** UI (パッケージ名 **Universal RP**、バージョン 7.3.1 以降)、または[Unity プロジェクトのセットアップのチュートリアル](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package)で説明されているように、`Packages/manifest.json` ファイルを使用します。

## <a name="next-steps"></a>次のステップ

* [Unity のゲーム オブジェクトとコンポーネント](objects-components.md)
* [チュートリアル:リモートモデルの表示](../../tutorials/unity/view-remote-models/view-remote-models.md)
