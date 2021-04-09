---
title: 数日間の記録の再生 - Azure
description: このチュートリアルでは、Azure Media Services API を使用して数日間の継続的なビデオ記録を再生する方法について説明します。
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 81a778b40649c1318b3738a289f0db37fd35376a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492791"
---
# <a name="tutorial-playback-of-multi-day-recordings"></a>チュートリアル:数日間の記録の再生  

このチュートリアルは、[継続的なビデオ記録](continuous-video-recording-concept.md) (CVR) のチュートリアルを基礎にしています。 このチュートリアルでは、Azure Media Services API を使用してクラウドから数日間の継続的なビデオ記録を再生する方法について説明します。 

これは、公共安全などのシナリオで、数日間 (または数週間) にわたってカメラから撮影した映像を維持する必要があり、不定期にその映像の特定の部分を見る必要がある場合に便利です。

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * 数日間の記録の内容を閲覧する方法を示すサンプル アプリを実行する
> * その記録の選択した部分を表示する

## <a name="suggested-pre-reading"></a>先に読んでおくことが推奨される記事  

次のドキュメントのページに目を通すことをお勧めします。

* [Live Video Analytics on IoT Edge の概要](overview.md)
* [Live Video Analytics on IoT Edge の用語](terminology.md)
* [メディア グラフの概念](media-graph-concept.md)
* [継続的なビデオ記録](continuous-video-recording-concept.md) 
* [攻略ガイド: 記録の再生](playback-recordings-how-to.md)
* [チュートリアル:継続的なビデオ記録](continuous-video-recording-tutorial.md)

## <a name="prerequisites"></a>前提条件

* [CVR のチュートリアル](continuous-video-recording-tutorial.md)を完了します。 このチュートリアルと関連 API ([チュートリアル: 継続的なビデオ記録](continuous-video-recording-tutorial.md)に関するページで説明) は、5 分以上の録画に適用されますが、少なくとも 5 時間分のビデオを記録することをお勧めします。 記録の閲覧に使われる API の例示には、長い記録が適しています。
* このチュートリアルは、[チュートリアル: 継続的なビデオ記録](continuous-video-recording-tutorial.md)をまだ実行している間、つまり、まだクラウドにビデオを記録している間に実行することをお勧めします。

## <a name="run-the-sample"></a>サンプルを実行する 

[CVR チュートリアル](continuous-video-recording-tutorial.md)の一部として、メディア サービス アカウントを作成しました。 このチュートリアルでは、そのアカウントに対する完全な API アクセス権を持っている必要があります。 「[Media Services API にアクセスするための資格情報を取得する](../latest/access-api-howto.md?tabs=portal)」にある手順を使用して、サービス プリンシパルを作成できます。 Azure portal から次のような JSON ブロックを取得できます。

```
{
    "AadClientId": "<<INSERT_AZURE_AD_APP_ID_HERE>>",
    "AadSecret": "<<INSERT_AZURE_AD_APP_SECRET_HERE>>",
    "AadTenantDomain": "<<YOUR_TENANT_DOMAIN>>",
    "AadTenantId": "<<YOUR_TENANT_ID>>",
    "AccountName": "<<YOUR_ACCOUNT_NAME>>",
    "Location": "<<YOUR_REGION_NAME>>",
    "ResourceGroup": "<<YOUR_RESOURCE_GROUP>>",
    "SubscriptionId": "<<YOUR_SUBSCRIPTION_ID>>",
    "ArmAadAudience": "https://management.core.windows.net",
    "ArmEndpoint": "https://management.azure.com"
}
```

次に、Visual Studio Code で src/ams-asset-player を開きます。 このフォルダーには、このチュートリアルで必要なファイルが含まれています。 appsettings.json ファイルを開き、その内容を appsettings.development.json という新しいファイルにコピーします。 新しく作成した appsettings.development.json に対して、次の編集を行います。

```
  "AMS" : {
    "subscriptionId" : "Use value of SubscriptionId above",
    "resourceGroup" : "Use value of ResourceGroup above",
    "accountId" : "Use value of AccountName above",
    "aadTenantId" : "Use value of AadTenantId above",
    "aadClientId" : "Use value of AadClientId above",
    "aadSecret" : "Use value of AadSecret above"
} 
```

1. Visual Studio Code で **[拡張機能]** タブを開き (または Ctrl + Shift + X キーを押し)、Azure IoT Hub を検索します。
1. マウスの右ボタンをクリックし、 **[拡張機能の設定]** を選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="拡張機能の設定":::
1. [Show Verbose Message]\(詳細メッセージの表示\) を検索して有効にします。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="詳細メッセージの表示":::
1. <!--In Visual Studio Code, you can click-->左側の [実行] アイコンをクリックして (または Ctrl + Shift + D キーを押して)、実行可能なアプリケーションを表示します。

    ![[実行] 項目が選択された Visual Studio Code メニューのスクリーンショット。](./media/playback-multi-day-recordings-tutorial/run.png)
1. 次に示すように、ドロップダウン ボックスから AMS Asset Player アプリケーションを選択し、F5 キーを押してデバッグを開始します。

    ![AMS Asset Player が選択された Visual Studio Code メニューのスクリーンショット。](./media/playback-multi-day-recordings-tutorial/debug.png)

このサンプル アプリケーションでは、既定のブラウザー アプリをビルドして起動し、AMS Asset Player ページを開きます。

> [!NOTE]
> 使用しているブラウザーのセキュリティ設定によっては、警告メッセージが表示される場合があります。 Web ページはローカルで実行されるため、警告を無視することを選択できます。

AMS Asset Player で、メディア サービス資産の名前を入力するように求められます。 使用する資産の名前は、[チュートリアル: 継続的なビデオ記録](continuous-video-recording-tutorial.md)に関するページでビデオを記録するために使用したものである必要があります。

資産の名前を入力して [送信] を押すと、プレーヤー コードによってストリーミング URL が読み込まれます。 詳細については、「[攻略ガイド: 記録の再生](playback-recordings-how-to.md)」を参照してください。 推奨されているように、まだ資産に記録中である場合は、プレーヤーでそれが検出され、記録されたビデオの最新部分に再生のキューを送ることが試みられます。 プレーヤーの左上にタイムスタンプ (UTC) が表示されていることがわかります。 次のスクリーンショットでは、[ライブ] ボタンがどのように選択されているかに注意してください。

![ストリーム](./media/playback-multi-day-recordings-tutorial/assetplayer1.png)
 
プレーヤーの右側には、アーカイブを閲覧するためのコントロールが表示されます。 このコントロールの年、月、日付は、「[攻略ガイド: 記録の再生](playback-recordings-how-to.md)」で説明されている availableMedia API を使用して設定されます。
日付を展開したとき、CVR チュートリアルを数時間実行している場合は、次のような結果が表示されます。

![アーカイブの閲覧](./media/playback-multi-day-recordings-tutorial/results.png)

チュートリアルのビデオ フィードのソースは、MKV ファイルです。 RSTP シミュレーター (「[Live555 Media Server](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)」を参照) がファイルの最後に到達すると、ストリームが終了します。 メディア グラフの RTSP ソース ノードでこれが検出され、接続が再確立されて、ビデオが再開されます。 このような個々のファイルの終わりと再接続の間には、記録されたアーカイブにギャップがあり、availableMedia の結果に新しいエントリとして表示されます。

![結果](./media/playback-multi-day-recordings-tutorial/assetplayer2.png)
 
一覧のいずれか 1 つのエントリをクリックすると、アプリケーションで適切なフィルターを適用したストリーミング URL が作成されます (https://{hostname}/{locatorId}/content.ism/manifest(format=mpd-time-csf,startTime=YYYY-MM-DDTHH:MM:SS) など)。 この URL がプレーヤーに読み込まれ、目的の startTime から再生が開始されます。

または、ページの下部にあるコントロールを使用して、特定の開始時刻と終了時刻を使用することもできます。 右側に示されているように、availableMedia 呼び出しの結果を開始時刻と終了時刻の許容値のガイドとして使用できます。 startTime および endTime フィルターに関する詳しい制約については、「[攻略ガイド: 記録の再生](playback-recordings-how-to.md)」で説明されています。 時刻の値を選択した後に [送信] をクリックすると、アプリケーションで次のようなストリーミング URL がプレーヤーに読み込まれます: https://{hostname}/{locatorId}/content.ism/manifest(format=mpd-time-csf,startTime=YYYY-MM-DDTHH:MM:SS,endTime= YYYY-MM-DDTHH:MM:SS)。目的の startTime で再生が開始されます。

## <a name="next-steps"></a>次のステップ

[クラウドへのイベントベースのビデオ記録とクラウドからの再生](event-based-video-recording-tutorial.md)
