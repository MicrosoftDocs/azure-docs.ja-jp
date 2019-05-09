---
title: チュートリアル - Azure Spatial Anchors を使用して新しい Android アプリを作成する手順 | Microsoft Docs
description: このチュートリアルでは、Azure Spatial Anchors を使用して新しい Android アプリを作成する方法について説明します。
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 04/03/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 64f78b04d433c81302499addf15c3d19621bbf9f
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/04/2019
ms.locfileid: "58919875"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>チュートリアル:Azure Spatial Anchors を使用して新しい Android アプリを作成する手順

このチュートリアルでは、ARCore 機能を Azure Spatial Anchors と統合する新しい Android アプリの作成方法を示します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のものが必要です。

- <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3 以降</a>がインストールされている Windows または macOS コンピューター。
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">開発者向け</a>の <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore 対応</a> Android デバイス。

## <a name="getting-started"></a>使用の開始

Android Studio を起動します。 **[Android Studio へようこそ]** ウィンドウで、**[新規 Android Studio プロジェクトの開始]** を選択します。 または、既に開かれているプロジェクトがある場合は、**[ファイル]**->**[新規プロジェクト]** を選択します。

**[新規プロジェクトの作成]** ウィンドウの **[スマホおよびタブレット]** セクションで、**[空のアクティビティ]** を選択し、**[次へ]** をクリックします。 次に、**[Minimum API level]\(最小 API レベル\)** で `API 26: Android 8.0 (Oreo)` を選択し、**[Language]\(言語\)** が `Java` に設定されていることを確認します。 プロジェクトの名前と場所、およびパッケージ名の変更が必要な場合があります。 他のオプションはそのままにします。 **[完了]** をクリックします。 **コンポーネント インストーラー**が実行されます。 完了したら、**[完了]** をクリックします。 いくつかの処理の後、Android Studio によって IDE が開かれます。

## <a name="trying-it-out"></a>試してみる

新しいアプリをテストするには、USB ケーブルを使用して開発用マシンに開発者向けのデバイスを接続します。 **[実行]**->**[Run 'app']\('アプリ' を実行\)** をクリックします。 **[Select Deployment Target]\(配置ターゲットの選択\)** ウィンドウで、お使いのデバイスを選択し、**[OK]** をクリックします。 Android Studio によって、接続されているデバイスにアプリがインストールされて起動されます。 "Hello World!" が、 お使いのデバイスで実行されているアプリに表示されます。 **[実行]**->**[Stop 'app']\('app' を停止\)** をクリックします。

## <a name="integrating-arcore"></a>_ARCore_ との統合

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> は、Augmented Reality エクスペリエンスを構築するための Google のプラットフォームであり、お使いのデバイスが移動するときにその位置を追跡し、現実世界の独自の認識を構築できるようにします。

ルート `<manifest>` ノード内に次のエントリを含むように `app\manifests\AndroidManifest.xml` を変更します。 このコード スニペットではいくつかのことを行います。

- アプリにデバイスのカメラへのアクセスを許可します。
- Google Play ストア内で、ARCore をサポートするデバイスに対してのみアプリが表示されるようにもします。
- アプリのインストール時に ARCore がまだインストールされていない場合は ARCore をダウンロードしてインストールするように Google Play ストアを構成します。

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

次のエントリを含むように `Gradle Scripts\build.gradle (Module: app)` を変更します。 このコードにより、アプリは確実に ARCore バージョン 1.5 を対象とするようになります (Azure Spatial Anchors 上での ARCore 1.6 以上のサポートは近日対応予定です)。 この変更の後に、Gradle から同期を求める通知を受け取ることがあります。**[Sync now]\(今すぐ同期\)** をクリックします。

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.5.0'
    ...
}
```

## <a name="integrating-sceneform"></a>_Sceneform_ の統合

<a href="https://developers.google.com/ar/develop/java/sceneform/" target="_blank">_Sceneform_</a> により、OpenGL を習得しなくても、Augmented Reality アプリ内でリアルな 3D シーンを簡単にレンダリングできます。

次のエントリを含むように `Gradle Scripts\build.gradle (Module: app)` を変更します。 このコードにより、アプリは `Sceneform` に必要な Java 8 の言語コンストラクトの使用を許可されます。 また、アプリは確実に `Sceneform` バージョン 1.5 を対象とするようになります。これはアプリで使用している ARCore のバージョンと一致する必要があるためです。 この変更の後に、Gradle から同期を求める通知を受け取ることがあります。**[Sync now]\(今すぐ同期\)** をクリックします。

```
android {
    ...

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.5.0'
    ...
}
```

`app\res\layout\activity_main.xml` を開き、既存の Hello World `<TextView>` 要素を次の ArFragment に置き換えます。 このコードにより、カメラ フィードが画面に表示され、お使いのデバイスが移動するときにその位置を ARCore で追跡できます。

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

お使いのデバイスにアプリを[再デプロイ](#trying-it-out)して、もう一度検証します。 今回は、カメラのアクセス許可を求められます。 承認されると、画面上にカメラ フィードのレンダリングが表示されます。

## <a name="place-an-object-in-the-real-world"></a>現実世界でのオブジェクトの配置

アプリを使用してオブジェクトを作成し、配置しましょう。 まず、以下の import を `app\java\<PackageName>\MainActivity` に追加します。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

続いて、以下のメンバー変数を `MainActivity` クラスに追加します。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

次に、以下のコードを `app\java\<PackageName>\MainActivity` `onCreate()` メソッドに追加します。 このコードでは、ユーザーがデバイスの画面をタップしたときにそれを検出する `handleTap()` というリスナーをフックします。 ARCore の追跡によって既に認識されている現実世界のサーフェス上でタップが行われると、リスナーが実行されます。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

最後に、すべてをまとめる次の `handleTap()` メソッド追加します。 これにより球体が作成されて、タップされた位置に配置されます。 `this.recommendedSessionProgress` は現在 0 に設定されているので、最初は球体が黒くなります。 この値は後で調整されます。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-171,174-182,198-199)]

お使いのデバイスにアプリを[再デプロイ](#trying-it-out)して、もう一度検証します。 今回は、ARCore が環境の認識を開始するようにお使いのデバイスを移動できます。 次に、画面をタップして黒い球体を作成し、任意のサーフェス上に配置します。

## <a name="attach-a-local-azure-spatial-anchor"></a>ローカル Azure Spatial Anchor のアタッチ

次のエントリを含むように `Gradle Scripts\build.gradle (Module: app)` を変更します。 このコードにより、アプリは確実に Azure Spatial Anchors バージョン 1.0.2 を対象とするようになります。 ただし、Azure Spatial Anchors の任意の最新バージョンの参照が機能します。

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[1.0.2]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[1.0.2]"
    ...
}
```

`app\java\<PackageName>`->**[新規]**->**[Java Class]\(Java クラス\)** を右クリックします。 **[名前]** を _MyFirstApp_ に設定し、**[スーパークラス]** を _android.app.Application_ に設定します。 他のオプションはそのままにします。 Click **OK**. `MyFirstApp.java` というファイルが作成されます。 そこに次の import を追加します。

```java
import com.microsoft.CloudServices;
```

次に、新しい `MyFirstApp` クラス内に次のコードを追加します。このコードにより、Azure Spatial Anchors がアプリケーションのコンテキストで初期化されます。

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

ここで、ルート `<application>` ノード内に次のエントリを含むように `app\manifests\AndroidManifest.xml` を変更します。 このコードでは、作成したアプリケーション クラスがアプリにフックされます。

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

`app\java\<PackageName>\MainActivity` に戻り、そこに以下の import を追加します。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

続いて、以下のメンバー変数を `MainActivity` クラスに追加します。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

次に、以下の `initializeSession()` メソッドを `mainActivity` クラス内に追加しましょう。 これが呼び出されると、Azure Spatial Anchors セッションが作成され、アプリの起動時に適切に初期化されます。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,146)]

次に、`initializeSession()` メソッドを `onCreate()` メソッドにフックしましょう。 また、カメラ フィードからのフレームが処理のために Azure Spatial Anchors SDK に送信されるようにします。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

最後に、以下のコードを `handleTap()` メソッドに追加します。 これにより、現実世界に配置している黒い球体にローカルの Azure 空間アンカーがアタッチされます。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-182,198-199&highlight=12-13)]

アプリをもう一度[再デプロイ](#trying-it-out)します。 お使いのデバイスを移動し、画面をタップして黒い球体を配置します。 ただし今回は、コードによりローカルの Azure 空間アンカーが作成されて球体にアタッチされます。

先に進む前に、Azure Spatial Anchors アカウント識別子とキーがまだない場合はこれらを作成します。 次のセクションに従ってこれらを取得します。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>クラウドへのローカル アンカーのアップロード

自分の Azure Spatial Anchors アカウント識別子とキーを作成したら、`app\java\<PackageName>\MainActivity` に戻り、そこに以下の import を追加できます。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

続いて、以下のメンバー変数を `MainActivity` クラスに追加します。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

次に、以下のコードを `initializeSession()` メソッドに追加します。 まず、このコードにより、Azure Spatial Anchors SDK によってカメラ フィードからフレームが収集される際の進行状況をアプリで監視できます。 その際、球体の色が当初の黒から灰色に変化し始めます。 さらに、アンカーをクラウドに送信するための十分なフレームが収集されると、白に変わります。 次に、このコードでは、クラウド バックエンドとの通信に必要な資格情報が提供されます。 ここでは、アカウント識別子とキーを使用するようにアプリを構成します。 これらの情報は、[Spatial Anchors リソースを設定](#create-a-spatial-anchors-resource)するときにテキスト エディターにコピーしました。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-146&highlight=11-36)]

次に、以下の `uploadCloudAnchorAsync()` メソッドを `mainActivity` クラスに追加します。 呼び出されると、このメソッドは、お使いのデバイスから十分なフレームが収集されるまで非同期的に待機します。 この状況が発生するとすぐに、球体の色が黄色に切り替わってから、クラウドへのローカル Azure 空間アンカーのアップロードが開始されます。 アップロードが完了すると、コードによってアンカー識別子が返されます。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

最後に、すべてをつなげましょう。 `handleTap()` メソッドに次のコードを追加します。 これにより、球体が作成されるとすぐに `uploadCloudAnchorAsync()` メソッドが呼び出されます。 メソッドから戻ると、次のコードによって球体への最終的な更新が実行され、色が青色に変わります。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-199&highlight=24-37)]

アプリをもう一度[再デプロイ](#trying-it-out)します。 お使いのデバイスを移動し、画面をタップして球体を配置します。 ただし今回は、カメラ フレームの収集時に球体の色が黒から白に向かって変化します。 十分なフレームを取得すると、球体が黄色に変わり、クラウドのアップロードが開始されます。 アップロードの完了後に、球体は青色になります。 必要に応じて、Android Studio 内の `Logcat` ウィンドウを使用して、アプリによって送信されているログ メッセージを監視することもできます。 たとえば、フレーム キャプチャ中のセッションの進行状況や、アップロードが完了した後にクラウドから返されるアンカー識別子などです。

## <a name="locate-your-cloud-spatial-anchor"></a>クラウド空間アンカーの配置

アンカーがクラウドにアップロードされると、それを配置する準備ができています。 まず、以下の import をコードに追加しましょう。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

次に、以下のコードを `handleTap()` メソッドに追加しましょう。 このコードでは、次のことが行われます。

- 既存の青い球体を画面から削除します。
- Azure Spatial Anchors セッションをもう一度初期化します。 このアクションにより、配置しようとしているアンカーは、作成したローカル アンカーではなくクラウドから取得されます。
- クラウドにアップロードしたアンカーに対するクエリを発行します。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

ここで、クエリの実行対象のアンカーが配置されたときに呼び出されるコードをフックしましょう。 `initializeSession()` メソッド内に次のコードを追加します。 このスニペットでは、クラウド空間アンカーが配置された後に緑色の球体が作成され、配置されます。 これにより、画面をもう一度タップできるようになるので、シナリオ全体をもう一度繰り返すことができます。別のローカル アンカーを作成し、アップロードして、もう一度配置します。

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

これで完了です。 最後にアプリを[再デプロイ](#trying-it-out)し、シナリオ全体をエンド ツー エンドで試します。 お使いのデバイスを移動し、黒い球体を配置します。 次に、球体が黄色に変わるまでデバイスを移動し続けてカメラ フレームをキャプチャします。 ローカル アンカーがアップロードされ、球体が青色に変わります。 最後に、ローカル アンカーが削除されるように画面をもう一度タップしてから、対応するクラウド アンカーに対してクエリを実行します。 クラウド空間アンカーが配置されるまで、デバイスの移動を続けます。 正しい場所に緑色の球体が表示され、シナリオ全体をもう一度繰り返すことができます。

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
