---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: aee6e6d8ca505bfdcfd4a51e4693779f44b2b0c0
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88226491"
---
このクイックスタートでは、Speech Devices SDK for Linux を使用して音声対応製品を構築するか、またはそれを[会話の文字起こし](../conversation-transcription-service.md)デバイスとして使用する方法について説明します。 現在、[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) のみがサポートされています。

アプリケーションは、Speech SDK パッケージと、64 ビット Linux (Ubuntu 16.04、Ubuntu 18.04、Debian 9、RHEL 7 または 8、CentOS 7 または 8) 上の Eclipse Java IDE (v4) によって構築されます。 これは、64 ビットの Java 8 のランタイム環境 (JRE) で実行されます。

このガイドでは、Speech サービス リソースがある [Azure Cognitive Services](../get-started.md) アカウントが必要になります。 

[サンプル アプリケーション](https://aka.ms/sdsdk-download-JRE)のソース コードは Speech Devices SDK に付属しています。 [GitHub で入手する](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)こともできます。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* オペレーティング システム:64 ビット Linux (Ubuntu 16.04、Ubuntu 18.04、Debian 9、RHEL 7 または 8、CentOS 7 または 8)
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) または [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) のみ。
* Speech サービス用の Azure サブスクリプション キー。 [無料で 1 つ取得します](../get-started.md)。
* Java 用の [Speech Devices SDK](https://aka.ms/sdsdk-download-JRE) の最新バージョンをダウンロードし、.zip を作業ディレクトリに解凍します。
   > [!NOTE]
   > このクイックスタートでは、アプリが /home/wcaltest/JRE-Sample-Release に抽出されることを前提としています

Eclipse を開始する前に、これらの依存関係がインストールされていることを確認してください。

* Ubuntu の場合:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* Debian 9 の場合:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

* RHEL または CentOS の場合:
  
  ```sh
  sudo yum update
  sudo yum install alsa-lib openssl
  ```

  > [!NOTE]
  > - RHEL または CentOS 7 の場合、「[Speech SDK 用に RHEL/CentOS 7 を構成する](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)」の手順に従います。
> - RHEL または CentOS 8 の場合、「[Linux 用 OpenSSL の構成](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)」の手順に従います。

会話の文字起こしは、現時点では、"centralus" および "eastasia" リージョンで "en-US" と "zh-CN" 言語のみに対応しています。 会話の文字起こしを使用するには、それらのいずれかのリージョンの Speech キーが必要です。

意図の使用を計画している場合は、[Language Understanding サービス (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) サブスクリプションが必要になります。 LUIS と意図認識の詳細については、 「[LUIS、C# を使って音声から意図を認識する](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)」 を参照してください。 [サンプル LUIS モデル](https://aka.ms/sdsdk-luis)は、このアプリで使用できます。

## <a name="create-and-configure-the-project"></a>プロジェクトの作成と構成

1. Eclipse を起動します。

1. **Eclipse IDE Launcher** の **[Workspace]\(ワークスペース\)** フィールドに、新しいワークスペース ディレクトリの名前を入力します。 次に **[Launch]** を選択します。

   ![[Eclipse Launcher] のスクリーンショット](../media/speech-devices-sdk/eclipse-launcher-linux.png)

1. しばらくすると、Eclipse IDE のメイン ウィンドウが表示されます。 ウェルカム画面が表示される場合は画面を閉じます。

1. Eclipse メニュー バーから、 **[File]\(ファイル\)**  >  **[New]\(新規\)**  >  **[Java Project]\(Java プロジェクト\)** を選択して新しいプロジェクトを作成します。 使用できない場合は、 **[Project]\(プロジェクト\)** 、 **[Java Project]\(Java プロジェクト\)** の順に選択します。

1. **新規 Java プロジェクト** ウィザードが開始されます。 サンプル プロジェクトの場所を**参照**します。 **[完了]** を選択します。

   ![[新規 Java プロジェクト] ウィザードのスクリーンショット](../media/speech-devices-sdk/eclipse-new-java-project-linux.png)

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

1. **パッケージ エクスプローラー**で、自分のプロジェクトを右クリックします。 **[Properties]\(プロパティ\)** を選択してから、 **[Run/Debug Settings]\(実行/デバッグ設定\)**  >  **[New…]\(新規…\)** >  **[Java Application]\(Java アプリケーション\)** の順に選択します。 

1. **[Edit Configuration]\(構成の編集\)** ウィンドウが表示されます。 **[Name]\(名前\)** フィールドに「**Main**」と入力し、 **[Search]\(検索\)** を使用して **Main クラス** を探して **[com.microsoft.cognitiveservices.speech.samples.FunctionsList]** を選択します。

   ![起動構成の編集のスクリーンショット](../media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. ターゲット アーキテクチャのオーディオ バイナリを **Linux-arm** または **Linux-x64** から Java プロジェクトの場所 ( **/home/wcaltest/JRE-Sample-Release** など) にコピーします。

1. **[Edit Configuration]\(構成の編集\)** ウィンドウから、 **[Environment]\(環境\)** ページと **[New]\(新規\)** も選択します。 **[New Environment Variable]\(新しい環境変数\)** ウィンドウが表示されます。 **[Name]\(名前\)** フィールドに「**LD_LIBRARY_PATH**」を入力し、 **[value]\(値\)** フィールドに *.so ファイルが含まれているフォルダー (例: **/home/wcaltest/JRE-Sample-Release**) を入力します。

1. `kws.table` と `participants.properties` をプロジェクト フォルダー **target/classes** にコピーします。


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

1. 既定のキーワード (keyword) は "Computer" です。 用意されている別のキーワード ("Machine"、"Assistant" など) を試すこともできます。 これらの代替キーワード用のリソース ファイルは、Speech Devices SDK の keyword フォルダーにあります。 たとえば、`/home/wcaltest/JRE-Sample-Release/keyword/Computer` には、キーワード "Computer" で使用されるファイルが含まれています。

   > [!TIP]
   > [カスタム キーワードを作成する](../speech-devices-sdk-create-kws.md)こともできます。

    新しいキーワードを使用するには、`FunctionsList.java` 内の次の行を更新し、そのキーワードをアプリにコピーします。 たとえば、キーワード パッケージ `machine.zip` からキーワード "Machine" を使用するには:

   * zip パッケージの `kws.table` ファイルをプロジェクト フォルダー **target/classes** にコピーします。

   * `FunctionsList.java` のキーワード名を更新します。

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Eclipse からサンプル アプリケーションを実行する

1. Eclipse のメニュー バーから、 **[Run]\(実行\)**  >  **[Run]\(実行\)** の順に選択します。 

1. Speech Devices SDK のサンプル アプリケーションが起動し、次のオプションが表示されます。

   ![Speech Devices SDK のサンプル アプリケーションとオプション](../media/speech-devices-sdk/java-sample-app-linux.png)

1. 新しい**会話の文字起こし**のデモをお試しください。 **[Session]\(セッション\)**  >  **[Start]\(開始\)** で文字起こしを開始します。 既定では、すべてのユーザーがゲストになります。 ただし、参加者の声紋がある場合は、プロジェクト フォルダー **target/classes** 内の `participants.properties` に入れることができます。 声紋を生成するには、[会話の文字起こし (SDK)](../how-to-use-conversation-transcription-service.md) に関するページを参照してください。

   ![会話の文字起こしアプリケーションのデモ](../media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>スタンドアロン アプリケーションの作成と実行

1. **パッケージ エクスプローラー**で、自分のプロジェクトを右クリックします。 **[Export]\(エクスポート\)** を選択します。 
1. **[Export]\(エクスポート\)** ウィンドウが表示されます。 **[Java]** を展開して、 **[Runnable JAR file]\(実行可能な JAR ファイル\)** を選択し、 **[Next]\(次へ\)** を選択します。

   ![[Export]\(エクスポート\) ウィンドウのスクリーンショット](../media/speech-devices-sdk/eclipse-export-linux.png) 

1. **[Runnable JAR File Export]\(実行可能な JAR ファイルのエクスポート\)** ウィンドウが表示されます。 アプリケーションの **[Export destination]\(エクスポート先\)** を選択してから、 **[Finish]\(完了\)** を選択します。
 
   ![[Runnable JAR File Export]\(実行可能な JAR ファイルのエクスポート\) のスクリーンショット](../media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. `kws.table` と `participants.properties` のファイルはアプリケーションで必要なため、上記で選択したエクスポート先フォルダー内に配置してください。

1. *.so ファイルが含まれているフォルダーに、LD_LIBRARY_LIB を設定します。

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. スタンドアロン アプリケーションを実行するには

     ```bash
     java -jar SpeechDemo.jar
     ```
