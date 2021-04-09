---
title: Z ファイティングの軽減
description: Z ファイティング アーティファクトを軽減する方法について説明します
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: a399565d62b20f62b72257bcb9f3beb2c910ac98
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594063"
---
# <a name="z-fighting-mitigation"></a>Z ファイティングの軽減

2 つの面が重なっている場合、どちらをもう一方にレンダリングする必要があるかはっきりしません。 結果はピクセル単位でも変化し、カメラ ビューに依存するアーティファクトが生成されることになります。 その結果、カメラまたはメッシュが移動すると、これらのパターンが著しくちらつくことになります。 このアーティファクトは "*Z ファイティング*" と呼ばれます。 AR アプリケーションと VR アプリケーションの場合、ヘッドマウント デバイスは当然ながら常に動くため、問題はさらに悪化します。 視聴者に不快感を与えないため、Azure Remote Rendering では Z ファイティングの軽減機能が提供されています。

## <a name="z-fighting-mitigation-modes"></a>Z ファイティングの軽減モード

|状況                        | 結果                               |
|---------------------------------|:-------------------------------------|
|通常の Z ファイティング               |![赤と緑のクアッドの間に決定的な優先順位はありません](./media/zfighting-0.png)|
|Z ファイティングの軽減が有効    |![赤いクアッドが優先されます](./media/zfighting-1.png)|
|チェッカーボードの強調表示が有効|![チェッカーボード パターンでの赤と緑のクアッドの切り替え設定](./media/zfighting-2.png)|

次のコードでは、Z ファイティングの軽減を有効にしています。

```cs
void EnableZFightingMitigation(RenderingSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Connection.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

```cpp
void EnableZFightingMitigation(ApiHandle<RenderingSession> session, bool highlight)
{
    ApiHandle<ZFightingMitigationSettings> settings = session->Connection()->GetZFightingMitigationSettings();

    // enabling z-fighting mitigation
    settings->SetEnabled(true);

    // enabling checkerboard highlighting of z-fighting potential
    settings->SetHighlighting(highlight);
}
```

> [!NOTE]
> Z ファイティングの軽減は、レンダリングされるすべてのメッシュに影響を与えるグローバル設定です。

## <a name="reasons-for-z-fighting"></a>Z ファイティングの理由

Z ファイティングは、主に 2 つの理由によって発生します。

1. 面がカメラから遠く離れており、深度値の精度が低下して、値が区別できなくなる場合
1. メッシュ内の面が物理的に重なり合う場合

最初の問題は常に発生する可能性があり、排除するのは困難です。 これがアプリケーションで発生する場合は、"*前方面*" の距離と "*後方面*" の距離の比率ができる限り低くなるようにしてください。 たとえば、前方面の距離が 0.01 で後方面の距離が 1000 の場合、前方面が 0.1 で後方面が 20 の場合に比べて、この問題がかなり早い段階で発生します。

2 つ目の問題は、不適切に作成されたコンテンツを示すインジケーターです。 実世界では、2 つのオブジェクトを同時に同じ場所に置くことはできません。 アプリケーションによっては、重なっている面が存在するかどうか、そしてその場所はどこなのかを、ユーザーが把握する必要がある場合があります。 たとえば、実際の建設の基礎となるビルの CAD シーンには、物理的には不可能な面の交差が含まれていてはなりません。 視覚的な検査を可能にするために、強調表示モードを使用できます。これにより、潜在的な Z ファイティングがアニメーション化されたチェッカーボード パターンとして表示されます。

## <a name="limitations"></a>制限事項

Z ファイティングの軽減は、最大限可能な対策として提供されています。 すべての Z ファイティングが排除される保証はありません。 また、一方の面がもう一方よりも自動的に優先されます。 したがって、互いに密接している面があると、"間違った" 面が上になることがあります。 一般的な問題のケースは、テキストやその他のデカールが面に適用される場合です。 Z ファイティングの軽減が有効になっていると、これらの詳細は消えてしまいがちです。

## <a name="performance-considerations"></a>パフォーマンスに関する考慮事項

* Z ファイティングの軽減を有効にした場合、パフォーマンスのオーバーヘッドはほとんど発生しません。
* さらに Z ファイティングのオーバーレイを有効にすると、かなりのパフォーマンスのオーバーヘッドが発生します。ただし、これはシーンによって異なる場合があります。

## <a name="api-documentation"></a>API のドキュメント

* [C# RenderingConnection.ZFightingMitigationSettings プロパティ](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.zfightingmitigationsettings)
* [C++ RenderingConnection::ZFightingMitigationSettings()](/cpp/api/remote-rendering/renderingconnection#zfightingmitigationsettings)

## <a name="next-steps"></a>次のステップ

* [レンダリング モード](../../concepts/rendering-modes.md)
* [Late Stage Reprojection](late-stage-reprojection.md)