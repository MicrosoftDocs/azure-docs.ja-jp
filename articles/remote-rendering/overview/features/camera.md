---
title: カメラ
description: カメラの設定とユース ケースについて説明します
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: dbe86313054706af974ccb324a39e942e9b5ca44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594131"
---
# <a name="camera"></a>カメラ

ローカルおよびリモートでレンダリングされるコンテンツをシームレスに合成できるようにするには、さまざまなカメラ プロパティがサーバーとクライアントの間で同期されたままになっている必要があります。 中でも注目すべきは、カメラの変換と投影です。 たとえば、ローカルでレンダリングされるコンテンツでは、最新のリモート フレームのレンダリングで使用されたのと同じカメラの変換と投影を使用する必要があります。

![ローカルとリモートのカメラ](./media/camera.png)

上の画像では、ローカルのカメラがサーバーから送信されたリモート フレームと比べて移動しています。 その結果、ローカル コンテンツはリモート フレームと同じパースペクティブからレンダリングされる必要があるため、最終的に、結果として生成される画像がローカルのカメラ空間に再投影されます。これについては、「[Late Stage Reprojection](late-stage-reprojection.md)」で詳しく説明しています。

リモート レンダリングとローカル レンダリングの間の遅延は、これらの設定の変更が遅れて適用されることを意味します。 そのため、新しい値を同じフレームですぐに使用することはできません。

カメラの設定では、前方面と後方面の距離を設定できます。 HoloLens 2 では、変換と投影の残りのデータはハードウェアによって定義されます。 [デスクトップのシミュレーション](../../concepts/graphics-bindings.md)を使用する場合は、`Update` 関数の入力から設定されます。

変更された値は、それらを使用してレンダリングされた最初のリモート フレームが到着するとすぐにローカルで使用できます。 HoloLens 2 では、レンダリングに使用されるデータは、他のホログラフィック アプリケーションと同様に、*HolographicFrame* によって提供されます。 [デスクトップのシミュレーション](../../concepts/graphics-bindings.md)では、`Update` 関数の出力から取得されます。

## <a name="camera-settings"></a>カメラの設定

カメラの設定では、次のプロパティを変更できます。

**前方および後方面:**

無効な範囲を設定できないようにするために、**NearPlane** と **FarPlane** の各プロパティは読み取り専用になっており、範囲の変更には別の関数 **SetNearAndFarPlane** が用意されています。 このデータは、フレームの終了時にサーバーに送信されます。 これらの値を設定するときは、**NearPlane** を **FarPlane** よりも小さくする必要があります。 そうしないと、エラーが発生します。

> [!IMPORTANT]
> Unity では、これはメイン カメラの前方面と後方面の変更時に自動的に処理されます。

**EnableDepth**:

デバッグのためにリモート画像の深度バッファーへの書き込みを無効にすると便利な場合があります。 深度を無効にすると、サーバーによる深度データの送信も停止されるため、帯域幅が減少します。

> [!TIP]
> Unity では、**EnableDepthComponent** と呼ばれるデバッグ コンポーネントが用意されており、これを使用してエディターの UI でこの機能を切り替えることができます。

**InverseDepth**:

> [!NOTE]
> この設定は、`EnableDepth` が `true` に設定されている場合にのみ重要です。 それ以外の場合、この設定の影響は何もありません。

通常、深度バッファーには [0;1] の浮動小数点の範囲で z 値が記録されます (この 0 は前方面の深さを示し、1 は後方面の深さを示します)。 この範囲を反転して、範囲 [1;0] の深度値を記録することもできます。つまり、前方面の深度が 1 になり、後方面の深度が 0 になります。 一般的に、後者の場合、非線形 z 範囲全体の浮動小数点精度の分布が向上します。

> [!WARNING]
> 一般的なアプローチでは、カメラ オブジェクトの前方面と後方面の値を反転します。 Azure Remote Rendering の場合、`CameraSettings` 上でこれを試行すると、エラーが発生して失敗します。

Azure Remote Rendering API は、リモート深度をローカル深度バッファーに正しく構成するために、ローカル レンダラーの深度バッファー規則を認識している必要があります。 深度バッファーの範囲が [0;1] の場合、このフラグは `false` のままにします。 [1;0] の範囲の反転深度バッファーを使用する場合は、`InverseDepth` フラグを `true` に設定します。

> [!NOTE]
> Unity の場合、正しい設定は `RenderingConnection` によって既に適用されているため、手動で介入する必要はありません。

カメラの設定の変更は、次のように行うことができます。

```cs
void ChangeCameraSetting(RenderingSession session)
{
    CameraSettings settings = session.Connection.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
    settings.InverseDepth = false;
}
```

```cpp
void ChangeCameraSetting(ApiHandle<RenderingSession> session)
{
    ApiHandle<CameraSettings> settings = session->Connection()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
    settings->SetInverseDepth(false);
}
```

## <a name="api-documentation"></a>API のドキュメント

* [C# CameraSettings](/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C++ CameraSettings](/cpp/api/remote-rendering/camerasettings)
* [C# GraphicsBindingSimD3d11.Update 関数](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C++ GraphicsBindingSimD3d11::Update 関数](/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>次のステップ

* [グラフィックスのバインド](../../concepts/graphics-bindings.md)
* [Late Stage Reprojection](late-stage-reprojection.md)