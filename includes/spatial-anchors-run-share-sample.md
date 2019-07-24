---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 397a8a9b07b4d7a88d0345399ac4abcc3e738a82
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631177"
---
## <a name="set-up-your-device"></a>デバイスのセットアップ

Unity で、`Unity` フォルダー内のプロジェクトを開きます。

![Unity ウィンドウ](./media/spatial-anchors-unity/unity-window.png)

### <a name="set-up-an-android-device"></a>Android デバイスのセットアップ

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>iOS デバイスのセットアップ

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>アカウント識別子とキーの構成

**[Project]\(プロジェクト\)** ウィンドウで `Assets/AzureSpatialAnchorsPlugin/Examples` に移動し、シーン ファイル `AzureSpatialAnchorsLocalSharedDemo.unity` を開きます。

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

**[Inspector]\(インスペクター\)** ウィンドウで、`Base Sharing Url` の値として (ASP.NET Web アプリの Azure デプロイにある) `Sharing Anchors Service url` を入力し、`index.html` を `api/anchors` に置き換えます。 `https://<app_name>.azurewebsites.net/api/anchors` のようになります。

**[File]\(ファイル\)** > **[Save]\(保存\)** の順に選択してシーンを保存します。

## <a name="deploy-to-your-device"></a>デバイスにデプロイする

### <a name="deploy-to-android-device"></a>Android デバイスにデプロイする

Android デバイスにサインインし、USB ケーブルを使用してコンピューターに接続します。

**[ファイル]** > **[ビルド設定]** を選択して、**[ビルド設定]** を開きます。

**[Scenes In Build]\(ビルド中のシーン\)** で、`AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` シーンの横にチェック マークを付けて、他のすべてのシーンのチェック マークをクリアします。

**[Export Project]\(プロジェクトのエクスポート\)** がオフであることを確認します。 **[Build And Run]\(ビルドして実行\)** を選択します。 `.apk` ファイルを保存するように求められます。 任意の名前を選択できます。

アプリ内の手順に従います。 **[Create & Share Anchor]\(アンカーの作成と共有\)** または **[Locate Shared Anchor]\(共有されたアンカーの配置\)** を選択できます。 最初のシナリオでは、後で同じデバイスまたは別のデバイス上で探知できるアンカーを作成できます。 2 つ目のシナリオでは、アプリを同じデバイスまたは別のデバイス上で既に実行している場合、前もって共有されているアンカーを探知できます。 シナリオを選択すると、実行する操作がアプリによって案内されます。 たとえば、環境情報を収集するためにデバイスを移動させるように求められます。 後で、環境内にアンカーを配置し、アップロードが完了するのを待機します。

### <a name="deploy-to-an-ios-device"></a>iOS デバイスにデプロイする

**[ファイル]** > **[ビルド設定]** を選択して、**[ビルド設定]** を開きます。

**[Scenes In Build]\(ビルド中のシーン\)** で、`AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` シーンの横にチェック マークを付けて、他のすべてのシーンのチェック マークをクリアします。

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

アプリ内の手順に従います。 **[Create & Share Anchor]\(アンカーの作成と共有\)** または **[Locate Shared Anchor]\(共有されたアンカーの配置\)** を選択できます。 最初のシナリオでは、後で同じデバイスまたは別のデバイス上で探知できるアンカーを作成できます。 2 つ目のシナリオでは、アプリを同じデバイスまたは別のデバイス上で既に実行している場合、前もって共有されているアンカーを探知できます。 シナリオを選択すると、実行する操作がアプリによって案内されます。 たとえば、環境情報を収集するためにデバイスを移動させるように求められます。 後で、環境内にアンカーを配置し、アップロードが完了するのを待機します。

Xcode で、**[Stop]\(停止\)** を選択してアプリを停止します。
