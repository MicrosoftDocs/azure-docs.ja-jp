---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: d2160a5cd38fcd24c3af5d32ccbbbee8ac723b2f
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88226517"
---
このクイックスタートでは、Speech Devices SDK for Windows を使用して音声対応製品を構築するか、またはそれを[会話の文字起こし](../conversation-transcription-service.md)デバイスとして使用する方法について説明します。 会話の文字起こしには [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) のみがサポートされています。 その他の音声の用途には、マイク配列ジオメトリを提供する直線的なマイク配列がサポートされています。

アプリケーションは、Speech SDK パッケージと、64 ビット Windows 上の Eclipse Java IDE (v4) で構築されます。 これは、64 ビットの Java 8 のランタイム環境 (JRE) で実行されます。

このガイドでは、Speech サービス リソースがある [Azure Cognitive Services](../get-started.md) アカウントが必要になります。

[サンプル アプリケーション](https://aka.ms/sdsdk-download-JRE)のソース コードは Speech Devices SDK に付属しています。 [GitHub で入手する](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)こともできます。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* オペレーティング システム:64 ビット Windows
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) などのマイク配列
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) または [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) のみ。
* [Microsoft Visual C++ 再頒布可能パッケージ](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* Speech サービス用の Azure サブスクリプション キー。 [無料で 1 つ取得します](../get-started.md)。
* Java 用の [Speech Devices SDK](https://aka.ms/sdsdk-download-JRE) の最新バージョンをダウンロードし、.zip を作業ディレクトリに解凍します。
   > [!NOTE]
   > このクイックスタートでは、アプリが C:\SDSDK\JRE-Sample-Release に抽出されることを前提としています

会話の文字起こしは、現時点では、"centralus" および "eastasia" リージョンで "en-US" と "zh-CN" 言語のみに対応しています。 会話の文字起こしを使用するには、それらのいずれかのリージョンの Speech キーが必要です。

意図の使用を計画している場合は、[Language Understanding サービス (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) サブスクリプションが必要になります。 LUIS と意図認識の詳細については、 「[LUIS、C# を使って音声から意図を認識する](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)」 を参照してください。 [サンプル LUIS モデル](https://aka.ms/sdsdk-luis)は、このアプリで使用できます。

## <a name="create-and-configure-the-project"></a>プロジェクトの作成と構成

1. Eclipse を起動します。

1. **Eclipse IDE Launcher** の **[Workspace]\(ワークスペース\)** フィールドに、新しいワークスペース ディレクトリの名前を入力します。 次に **[Launch]** を選択します。

   ![[Eclipse Launcher] のスクリーンショット](../media/speech-devices-sdk/eclipse-launcher.png)

1. しばらくすると、Eclipse IDE のメイン ウィンドウが表示されます。 ウェルカム画面が表示される場合は画面を閉じます。

1. Eclipse メニュー バーから、 **[File]\(ファイル\)**  >  **[New]\(新規\)**  >  **[Java Project]\(Java プロジェクト\)** を選択して新しいプロジェクトを作成します。 使用できない場合は、 **[Project]\(プロジェクト\)** 、 **[Java Project]\(Java プロジェクト\)** の順に選択します。

1. **新規 Java プロジェクト** ウィザードが開始されます。 サンプル プロジェクトの場所を**参照**します。 **[完了]** を選択します。

   ![[新規 Java プロジェクト] ウィザードのスクリーンショット](../media/speech-devices-sdk/eclipse-new-java-project.png)

1. **パッケージ エクスプローラー**で、自分のプロジェクトを右クリックします。 コンテキスト メニューから、 **[Configure]**  >  **[Convert to Maven Project]** を選択します。 **[完了]** を選択します。

   ![パッケージ エクスプローラーのスクリーンショット](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. pom.xml ファイルを開き、それを編集します。

    ファイルの最後でタグ `</project>` を閉じる前に、次に示すように `repositories` および `dependencies` 要素を作成し、`version` が現在のバージョンに一致していることを確認します。
    ```xml
    <repositories>
         <repository>
             <id>maven-cognitiveservices-speech</id>
             <name>Microsoft Cognitive Services Speech Maven Repository</name>
             <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
         </repository>
    </repositories>
 
    <dependencies>
        <dependency>
             <groupId>com.microsoft.cognitiveservices.speech</groupId>
             <artifactId>client-sdk</artifactId>
             <version>1.13.0</version>
        </dependency>
    </dependencies>
   ```

1. **Windows-x64** の内容を Java プロジェクトの場所 (**C:\SDSDK\JRE-Sample-Release** など) にコピーします。

1. `kws.table`、`participants.properties`、`Microsoft.CognitiveServices.Speech.extension.pma.dll` をプロジェクト フォルダー **target\classes** にコピーします。

## <a name="configure-the-sample-application"></a>サンプル アプリケーションを構成する

1. ソース コードに Speech サブスクリプション キーを追加します。 意図認識を試す場合は、[Language Understanding Service](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) のサブスクリプション キーとアプリケーション ID も追加します。

   Speech および LUIS の場合、ユーザーの情報が `FunctionsList.java` に配置されます。

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   会話の文字起こしを使用している場合は、`Cts.java` に Speech キーとリージョン情報も必要です。

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. 既定のキーワード (keyword) は "Computer" です。 用意されている別のキーワード ("Machine"、"Assistant" など) を試すこともできます。 これらの代替キーワード用のリソース ファイルは、Speech Devices SDK の keyword フォルダーにあります。 たとえば、`C:\SDSDK\JRE-Sample-Release\keyword\Computer` には、キーワード "Computer" で使用されるファイルが含まれています。

    > [!TIP]
    > [カスタム キーワードを作成する](../speech-devices-sdk-create-kws.md)こともできます。

    新しいキーワードを使用するには、`FunctionsList.java` 内の次の行を更新し、そのキーワードをアプリにコピーします。 たとえば、キーワード パッケージ `machine.zip` からキーワード "Machine" を使用するには:

   * zip パッケージの `kws.table` ファイルをプロジェクト フォルダー **target/classes** にコピーします。
   * `FunctionsList.java` のキーワード名を更新します。

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Eclipse からサンプル アプリケーションを実行する

1. Eclipse のメニュー バーから、 **[Run]\(実行\)**  >  **[Run As]\(プログラム名を指定して実行\)**  >  **[Java Application]\(Java アプリケーション\)** の順に選択します。 次に、 **[FunctionsList]** 、 **[OK]** の順に選択します。

   ![[Select Java Application]\(Java アプリケーションの選択\) のスクリーンショット](../media/speech-devices-sdk/eclipse-run-sample.png)

1. Speech Devices SDK のサンプル アプリケーションが起動し、次のオプションが表示されます。

   ![Speech Devices SDK のサンプル アプリケーションとオプション](../media/speech-devices-sdk/java-sample-app-windows.png)

1. 新しい**会話の文字起こし**のデモをお試しください。 **[Session]\(セッション\)**  >  **[Start]\(開始\)** で文字起こしを開始します。 既定では、すべてのユーザーがゲストになります。 ただし、参加者の声紋がある場合は、プロジェクト フォルダー **target/classes** 内のファイル `participants.properties` に入れることができます。 声紋を生成するには、[会話の文字起こし (SDK)](../how-to-use-conversation-transcription-service.md) に関するページを参照してください。

   ![会話の文字起こしアプリケーションのデモ](../media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>スタンドアロン アプリケーションの作成と実行

1. **パッケージ エクスプローラー**で、自分のプロジェクトを右クリックします。 **[Export]\(エクスポート\)** を選択します。

1. **[Export]\(エクスポート\)** ウィンドウが表示されます。 **[Java]** を展開して、 **[Runnable JAR file]\(実行可能な JAR ファイル\)** を選択し、 **[Next]\(次へ\)** を選択します。

   ![[Export]\(エクスポート\) ウィンドウのスクリーンショット](../media/speech-devices-sdk/eclipse-export-windows.png)

1. **[Runnable JAR File Export]\(実行可能な JAR ファイルのエクスポート\)** ウィンドウが表示されます。 アプリケーションの **[Export destination]\(エクスポート先\)** を選択してから、 **[Finish]\(完了\)** を選択します。

   ![[Runnable JAR File Export]\(実行可能な JAR ファイルのエクスポート\) のスクリーンショット](../media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. `kws.table`、`participants.properties`、`unimic_runtime.dll`、`pma.dll`、および `Microsoft.CognitiveServices.Speech.extension.pma.dll` はアプリケーションに必要なため、これらのファイルを上で選択したエクスポート先フォルダーに配置してください。

1. スタンドアロン アプリケーションを実行するには

   ```powershell
   java -jar SpeechDemo.jar
   ```
