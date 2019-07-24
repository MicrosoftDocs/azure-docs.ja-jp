---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 33c932c36cd6de730d3768d596a214c442d74ae1
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632897"
---
**[ファイル]** > **[ビルド設定]** を選択して、**[ビルド設定]** を開きます。

**[プラットフォーム]** セクションで、**[Android]** を選択します。 **[ビルド システム]** を **[Gradle]** に変更し、**[プロジェクトのエクスポート]** を選択します。

**[Switch Platform]\(プラットフォームの切り替え\)** を選択して、プラットフォームを **[Android]** に変更します。 Android をサポートするコンポーネントが不足している場合は、Unity によってそれらをインストールすることを要求される場合があります。

![Unity の [Build Settings]\(ビルド設定\) ウィンドウ](./media/spatial-anchors-unity/unity-android-build-settings.png)

**[ビルド設定]** ウィンドウを閉じます。

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Unity 用 ARCore SDK をダウンロードしてインポートする

[Unity 用 ARCore SDK 1.5 リリース](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0)から `unitypackage` ファイルをダウンロードします。 Unity プロジェクトに戻り、**[資産]** > **[パッケージのインポート]** > **[カスタム パッケージ]** を選択した後、先ほどダウンロードした `unitypackage` ファイルを選択します。 **[Unity パッケージのインポート]** ダイアログ ボックスで、すべてのファイルが選択されていることを確認し、**[インポート]** を選択します。
