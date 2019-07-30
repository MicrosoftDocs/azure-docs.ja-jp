---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: ebefe878393b66e5097ebbda5ef6159a8413833f
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298443"
---
1. Eclipse を起動します。

1. [Eclipse Launcher] で、新しいディレクトリの名前を **[Workspace]** フィールドに入力します。 次に **[Launch]** を選択します。

   ![[Eclipse Launcher] のスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. しばらくすると、Eclipse IDE のメイン ウィンドウが表示されます。 ウェルカム画面が表示される場合は画面を閉じます。

1. Eclipse メニュー バーから、 **[File]**  >  **[New]**  >  **[Project]** を選択して新しいプロジェクトを作成します。

1. **[新しいプロジェクト]** ダイアログ ボックスが表示されます。 **[Java Project]** を選択し、 **[Next]** を選択します。

   ![Java プロジェクトが強調表示されている [新しいプロジェクト] ダイアログ ボックスのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-02-select-wizard.png)

1. [New Java Project] ウィザードが開始されます。 **[Project name]** フィールドに「**quickstaart**」と入力し、実行環境として **[JavaSE-1.8]** を選択します。 **[完了]** を選択します。

   ![[新規 Java プロジェクト] ウィザードのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-03-create-java-project.png)

1. **[Open Associated Perspective?]\(パースペクティブを開きますか?)** というウィンドウが表示される場合は、 **[Open Perspective]\(パースペクティブを開く)** を選択します。

1. **Package explorer** で、**quickstart** プロジェクトを右クリックします。 コンテキスト メニューから、 **[Configure]**  >  **[Convert to Maven Project]** を選択します。

   ![パッケージ エクスプローラーのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. **[Create new POM]** ウィンドウが表示されます。 **[Group Id]** フィールドに、 **com.microsoft.cognitiveservices.speech.samples** と入力し、 **[Artifact Id]** フィールドに、**quickstart** と入力します。 続けて、 **[完了]** を選択します。

   ![[Create new POM] ウィンドウのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. **pom.xml** ファイルを開いて編集します。

   * ファイルの最後にある、終了タグ `</project>` の前に、次に示すように Speech SDK 用の Maven リポジトリへの参照を含む `repositories` 要素を作成します。

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

   * 依存関係として Speech SDK バージョン 1.6.0 を指定した `dependencies` 要素も追加します。

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

   * 変更を保存します。
