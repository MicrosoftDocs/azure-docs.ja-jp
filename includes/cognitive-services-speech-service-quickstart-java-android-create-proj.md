---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: travisw
ms.openlocfilehash: 84c9f61783d2fea425b83c214deede295c4ed581
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374748"
---
1. Android Studio を起動し、**ウェルカム** ウィンドウの **[Start a new Android Studio project]\(新しい Android Studio プロジェクトを開始する\)** を選択します。

    ![Android Studio のウェルカム ウィンドウのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. **[Choose your project]\(プロジェクトの選択\)** ウィザードが表示されます。 アクティビティの選択ボックスで **[Phone and Tablet]\(電話およびタブレット\)** と **[Empty Activity]\(空のアクティビティ\)** を選択します。 **[次へ]** を選択します。

   ![[Choose your project]\(プロジェクトの選択\) ウィザードのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. **[Configure your project]\(プロジェクトの構成\)** 画面で、 **[Name]\(名前\)** として「*Quickstart*」を、 **[Package name]\(パッケージ名\)** として「*samples.speech.cognitiveservices.microsoft.com*」を入力します。 次に、プロジェクト ディレクトリを選択します。 **[Minimum API level]\(最小 API レベル\)** で **[API 23: Android 6.0 Marshmallow)]** を選択します。 他のすべてのチェック ボックスをオフのままにして、 **[Finish]\(完了\)** を選択します。

   ![[Configure your project]\(プロジェクトの構成\) ウィザードのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Android Studio が新しい Android プロジェクトを準備するまでしばらく時間がかかります。 次に、Azure Cognitive Services Speech SDK への理解を深め、Java 8 を使用するためのプロジェクトを構成します。

[!INCLUDE [License notice](cognitive-services-speech-service-license-notice.md)]

Cognitive Services Speech SDK の最新バージョンは 1.13.0 です。

Android 用 Speech SDK は、必要なライブラリと必要な Android アクセス許可を含む [AAR (Android ライブラリ)](https://developer.android.com/studio/projects/android-library) としてパッケージ化されます。
Maven リポジトリ (https:\//csspeechstorage.blob.core.windows.net/maven/) でホストされています。

Speech SDK を使用するためにプロジェクトを設定します。 Android Studio のメニュー バーの **[File]\(ファイル\)**  >  **[Project Structure]\(プロジェクトの構造\)** を選択して **[Project Structure]\(プロジェクトの構造\)** ウィンドウを開きます。 **[Project Structure]\(プロジェクトの構造\)** ウィンドウで、次の変更を行います。

1. ウィンドウの左側にあるリストから、 **[プロジェクト]** を選択します。 **[Default Library Repository]\(既定のライブラリ リポジトリ\)** 設定を編集して、コンマと、一重引用符で囲んだ Maven リポジトリの URL を追加します ('https:\//csspeechstorage.blob.core.windows.net/maven/')。

   ![[Project Structure] (プロジェクトの構造) ウィンドウのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. 同じ画面の左側で、 **[app]\(アプリ\)** を選択します。 次に、ウィンドウの上部にある **[Dependencies]\(依存関係\)** タブを選択します。 緑色のプラス記号 ( **+** ) を選択して、ドロップダウン メニューから **[Library dependency]\(ライブラリ依存関係\)** を選択します。

   ![[Library dependency]\(ライブラリ依存関係\) のスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. 表示されたウィンドウで、Android 用 Speech SDK の名前とバージョンを入力します (*com.microsoft.cognitiveservices.speech:client-sdk:1.13.0*)。 **[OK]** をクリックします。
   次に示すように、依存関係の一覧に Speech SDK が追加されます。

   ![Speech SDK が含まれた依存関係の一覧のスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. **[プロパティ]** タブを選択します。 **[Source Compability]\(ソース互換性\)** と **[Target Compatibility]\(ターゲット互換性\)** はどちらも **[1.9]** を選択します。

   ![[Source Compability]\(ソース互換性\) と [Target Compatibility]\(ターゲット互換性\) のスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. **[OK]** を選択して **[Project Structure]\(プロジェクトの構造\)** ウィンドウを閉じ、プロジェクトに変更を適用します。
