---
title: ステージ空間
description: ステージ空間の設定とユース ケースについて説明します
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 5809494fb8b619569316a24816a2e5d943dee6b4
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013131"
---
# <a name="stage-space"></a>ステージ空間

HoloLens 2 のようなヘッド トラッキング データを提供するデバイスで ARR を実行すると、頭部姿勢がユーザー アプリケーションとサーバーの両方に送信されます。 この頭部の変化が定義されている空間は、*ステージ空間*と呼ばれます。

ローカルとリモートのコンテンツを配置するため、クライアントとサーバーの両方で、ステージ空間とワールド空間が同じであることを前提としています。 ユーザーがカメラの上にさらに変化を加えることにした場合は、ローカルとリモートのコンテンツを正しく配置するために、サーバーにも送信する必要があります。

ステージ空間を移動させる一般的な理由としては、[ワールド ロック ツール](https://microsoft.github.io/MixedReality-WorldLockingTools-Unity/README.html)などの現実世界でのアンカー技術や、VR で仮想キャラクターを動かすことなどが挙げられます。

## <a name="stage-space-settings"></a>ステージ空間の設定

> [!CAUTION]
> 試験段階:この機能は試験段階にあり、時間の経過と共に変化します。 したがって、新しいバージョンのクライアント SDK に更新するには、コードをアップグレードするための追加の作業が必要になる場合があります。 現在の実装では、ステージ空間の原点を変更したときに、ローカルおよびリモートのコンテンツの配置が少しだけ中断されます。
そのため、現時点では、時間の経過と共にわずかな変化しか示さないアンカーのような、ワールド ロックの目的でのみ使用されることを意図しています。

ステージ空間に追加の変化が適用されたことをサーバーに通知するには、ワールド空間における位置と回転で定義される原点を送信する必要があります。 この設定には、「*ステージ空間の設定*」からアクセスできます。

> [!IMPORTANT]
> [デスクトップ シミュレーション](../../concepts/graphics-bindings.md)では、カメラのワールド空間における位置はユーザー アプリケーションによって提供されます。 この場合、ステージ空間の原点の設定は既にカメラの変化に乗算されているため、スキップする必要があります。

```cs
void ChangeStageSpace(AzureSession session)
{
    StageSpaceSettings settings = session.Actions.StageSpaceSettings;

    // Set position and rotation to the world-space transform of the stage space.
    settings.Position = new Double3(0, 0, 0);
    settings.Rotation = new Quaternion(0, 0, 0, 1);
}
```

```cpp
void ChangeStageSpace(ApiHandle<AzureSession> session)
{
    ApiHandle<StageSpaceSettings> settings = session->Actions()->GetStageSpaceSettings();

    // Set position and rotation to the world-space transform of the stage space.
    settings->SetPosition({0, 0, 0});
    settings->SetRotation({0, 0, 0, 1});
}
```

## <a name="unity-stage-space-script"></a>Unity のステージ空間スクリプト

Unity 統合には、カメラの親 GameObject に追加できる **StageSpace** と呼ばれるスクリプトが用意されています。 導入後、このスクリプトを使用してステージ空間の原点の設定を行えるようになります。

## <a name="next-steps"></a>次のステップ

* [グラフィックスのバインド](../../concepts/graphics-bindings.md)
* [Late Stage Reprojection](late-stage-reprojection.md)
