---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: e8daaaf5b6b15eb3095f11e94c707a33b4b18e28
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180862"
---
**[Build]\(ビルド\)** を選択します。 開かれたダイアログ ボックスで、Xcode プロジェクトをエクスポートするフォルダーを選択します。

エクスポートが完了すると、エクスポートされた Xcode プロジェクトを含むフォルダーが表示されます。

> [!NOTE]
> 置き換えるか追加するかを尋ねるウィンドウが表示される場合は、 **[Append]\(追加\)** を選択することをお勧めします。 シーンのアセットを変更している場合は、 **[Replace]\(置換\)** を選択するだけで済みます (たとえば、親子関係を追加、削除、または変更する場合や、プロパティを追加、削除、または変更する場合)。ソース コードを変更するだけの場合は、 **[Append]\(追加\)** で十分です。

### <a name="open-the-xcode-project"></a>Xcode プロジェクトを開く

エクスポートされた Xcode プロジェクトのフォルダーで、ターミナルで次のコマンドを実行して、プロジェクトに必要な CocoaPods をインストールします。

```bash
pod install --repo-update
```

これで、`Unity-iPhone.xcworkspace` を開き、Xcode でプロジェクトを開くことができるようになります。

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> `library not found for -lPods-Unity-iPhone` エラーが表示される場合は、`.xcworkspace` ファイルではなく `.xcodeproj` ファイルを開いた可能性があります。 

ルートの **Unity-iPhone** ノードを選択してプロジェクトの設定を表示し、 **[General]\(全般\)** タブを選択します。

**[Signing]\(署名\)** で、 **[Automatically manage signing]\(署名を自動的に管理\)** を有効にします。 有効でない場合は有効にして、表示されるダイアログ ボックスで **[Enable Automatic]\(自動を有効にする\)** を選択して、ビルドの設定をリセットします。

**[Deployment Info]\(展開情報\)** で、 **[Deployment Target]\(展開ターゲット\)** が `11.0` に設定されていることを確認します。

### <a name="deploy-the-app-to-your-ios-device"></a>アプリを iOS デバイスに展開する

iOS デバイスを Mac に接続し、**アクティブ スキーム**を iOS デバイスに設定します。

![デバイスを選択する](./media/spatial-anchors-unity/select-device.png)

**[Build and then run the current scheme]\(ビルドしてから現在のスキームを実行する\)** を選択します。

![展開して実行する](./media/spatial-anchors-unity/deploy-run.png)