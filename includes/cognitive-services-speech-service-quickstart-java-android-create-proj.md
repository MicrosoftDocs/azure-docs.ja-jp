---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 5/23/2019
ms.author: travisw
ms.openlocfilehash: a8118d80e85d562fa4137ed1f1844e6bf9f1793e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485028"
---
1. Android Studio を起動し、ウェルカム ウィンドウの **[Start a new Android Studio project]\(新しい Android Studio プロジェクトを開始する\)** を選択します。

    ![Android Studio のウェルカム ウィンドウのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. **[Choose your project]\(プロジェクトの選択\)** ウィザードが表示されたら、アクティビティの選択ボックスで **[Phone and Tablet]\(電話およびタブレット\)** と **[Empty Activity]\(空のアクティビティ\)** を選択します。 **[次へ]** を選択します。

   ![[Choose your project]\(プロジェクトの選択\) ウィザードのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. **[Configure your project]\(プロジェクトの構成\)** 画面で、 **[Name]\(名前\)** として「**Quickstart**」を、 **[Package name]\(パッケージ名\)** として「**samples.speech.cognitiveservices.microsoft.com**」を入力して、プロジェクト ディレクトリを選択します。 **[Minimum API level]\(最小 API レベル\)** で **[API 23: Android 6.0 (Marshmallow)]** を選択し、他のすべてのチェック ボックスはオフのまま、 **[Finish]\(完了\)** を選択します。

   ![[Configure your project]\(プロジェクトの構成\) ウィザードのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Android Studio が新しい Android プロジェクトを準備するまでしばらく時間がかかります。 次に、Speech SDK への理解を深め、Java 8 を使用するためのプロジェクトを構成します。

[!INCLUDE [License Notice](cognitive-services-speech-service-license-notice.md)]

Cognitive Services Speech SDK の現在のバージョンは `1.6.0` です。

Android 用 Speech SDK は、必要なライブラリと必要な Android アクセス許可を含む [AAR (Android ライブラリ)](https://developer.android.com/studio/projects/android-library) としてパッケージ化されます。
Maven リポジトリ (https:\//csspeechstorage.blob.core.windows.net/maven/) でホストされています。

Speech SDK を使用するためにプロジェクトを設定します。 Android Studio のメニュー バーから **[ファイル]**  >  **[Project Structure]** (プロジェクトの構造) を選択してプロジェクトの構造のウィンドウ開きます。 [Project Structure] (プロジェクトの構造) ウィンドウで、次の変更を行います。

1. ウィンドウの左側にあるリストから、 **[プロジェクト]** を選択します。 **[Default Library Repository]\(既定のライブラリ リポジトリ\)** 設定を編集して、コンマと、一重引用符で囲んだ Maven リポジトリの URL を追加します。 'https:\//csspeechstorage.blob.core.windows.net/maven/'

   ![[Project Structure] (プロジェクトの構造) ウィンドウのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. 同じ画面の左側で、 **[app]\(アプリ\)** を選択します。 次に、ウィンドウの上部にある **[Dependencies]\(依存関係\)** タブを選択します。 緑色のプラス記号 (+) を選択して、ドロップダウン メニューから **[Library dependency]\(ライブラリ依存関係\)** を選択します。

   ![[Project Structure] (プロジェクトの構造) ウィンドウのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. 表示されたウィンドウで、Android 用 Speech SDK の名前とバージョン (`com.microsoft.cognitiveservices.speech:client-sdk:1.6.0`) を入力します。 **[OK]** をクリックします。
   次に示すように、依存関係のリストに Speech SDK が追加されます。

   ![[Project Structure] (プロジェクトの構造) ウィンドウのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. **[プロパティ]** タブを選択します。 **[Source Compability]\(ソース互換性\)** と **[Target Compatibility]\(ターゲット互換性\)** はどちらも **[1.8]** を選択します。

   ![](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. **[OK]** を選択してプロジェクトの構造 ウィンドウを閉じ、プロジェクトに変更を適用します。
