---
title: Unity 向けの Azure Spatial Anchors をインストールする
description: Azure Spatial Anchors を使用するように Unity プロジェクトを構成する
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 2/3/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 56532c17c91e6c703a6acd7990bbae47cd248165
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576567"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Unity プロジェクトでの Azure Spatial Anchors の構成

このガイドでは、Unity プロジェクトで Azure Spatial Anchors SDK の使用を開始する方法について説明します。

## <a name="project-requirements"></a>プロジェクトの要件

[!INCLUDE [Unity Project Requirements](../../../includes/spatial-anchors-unity-project-requirements.md)]

## <a name="configuring-a-project"></a>プロジェクトの構成

Unity プロジェクトに Azure Spatial Anchors SDK を含める前に、必ず Unity パッケージ マネージャーを使用して[必要な](#project-requirements)パッケージをインストールしてください。

### <a name="download-asa-packages"></a>ASA パッケージのダウンロード
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-asa-packages"></a>ASA パッケージのインポート
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="android-only-configure-the-maintemplategradle-file"></a>Android のみ:mainTemplate.gradle ファイルを構成する

1. **[Edit]\(編集\)**  >  **[Project Settings]\(プロジェクト設定\)**  >  **[Player]\(プレーヤー\)** の順に移動します。
2. **[プレーヤー設定]** の **[インスペクター] パネル** で、**Android** アイコンを選択します。
3. **[ビルド]** セクションで、 **[カスタム メイン Gradle テンプレート]** チェックボックスをオンにして、`Assets\Plugins\Android\mainTemplate.gradle` でカスタム Gradle テンプレートを生成します。
4. `mainTemplate.gradle` ファイルをテキスト エディターで開きます。
5. `dependencies` セクションに、次の依存関係を貼り付けます。

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

すべて完了すると、`dependencies` セクションは次のようになります。

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [方法: Unity でアンカーを作成して配置する](./create-locate-anchors-unity.md)
