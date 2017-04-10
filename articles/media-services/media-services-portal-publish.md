---
title: "  Azure Portal を使用したコンテンツの発行 | Microsoft Docs"
description: "このチュートリアルでは、Azure ポータルを使用してコンテンツを発行する手順について説明します。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 92c364eb-5a5f-4f4e-8816-b162c031bb40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: 72b0b7feef583799613b1cb2c18b2020c1401b36


---
# <a name="publish-content-with-the-azure-portal"></a>Azure ポータルを使用したコンテンツの発行
> [!div class="op_single_selector"]
> * [ポータル](media-services-portal-publish.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST ()](media-services-rest-deliver-streaming-content.md)
> 
> 

## <a name="overview"></a>概要
> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。 
> 
> 

ストリーミングかダウンロードに使用できる URL を提供するには、まず、ロケーターを作成して資産を "発行" する必要があります。 資産に含まれているファイルには、ロケーターを通じてアクセスできます。 Media Services では、2 種類のロケーターがサポートされています。 

* ストリーミング (OnDemandOrigin) ロケーター。アダプティブ ストリーミング (MPEG DASH、HLS、スムーズ ストリーミングなどでのストリーミング) に使用します。 ストリーミング ロケーターを作成する場合、資産に .ism ファイルが含まれている必要があります。 
* プログレッシブ (SAS) ロケーター。プログレッシブ ダウンロードを使用してビデオを配信する場合に使用します。

ストリーミング URL には次の形式があり、スムーズ ストリーミング資産の再生に使用できます。

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS ストリーミング URL を作成するには、(format=m3u8-aapl) を URL に追加します。

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH ストリーミング URL を作成するには、(format=mpd-time-csf) を URL に追加します。

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

SAS URL には次の形式があります。

    {blob container name}/{asset name}/{file name}/{SAS signature}

詳細については、 [コンテンツ配信の概要](media-services-deliver-content-overview.md)に関する記事を参照してください。

> [!NOTE]
> 2015 年 3 月より前にポータルを使用してロケーターを作成した場合、有効期限が 2 年のロケーターが作成されています。  
> 
> 

ロケーターの有効期限を更新するには、[REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) API または [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API を使用します。 SAS ロケーターの有効期限を更新すると、URL が変更されることにご注意ください。

### <a name="to-use-the-portal-to-publish-an-asset"></a>ポータルを使用して資産を発行するには
ポータルを使用して資産を発行するには、次の操作を行います。

1. [Azure Portal](https://portal.azure.com/) で Azure Media Services アカウントを選択します。
2. **[設定]** > **[資産]**を参照してください。
3. 発行する資産を選択します。
4. **[発行]** ボタンをクリックします。
5. ロケーターの種類を選択します。
6. **[追加]**をクリックします。
   
    ![[発行]](./media/media-services-portal-vod-get-started/media-services-publish1.png)

URL が **[発行された URL]**の一覧に追加されます。

## <a name="play-content-from-the-portal"></a>ポータルでコンテンツを再生する
ビデオは、Azure ポータルにあるコンテンツ プレーヤーを使用してテストできます。

目的のビデオをクリックし、 **[再生]** ボタンをクリックします。

![[発行]](./media/media-services-portal-vod-get-started/media-services-play.png)

いくつかの考慮事項が適用されます。

* ビデオが発行されたことを確認します。
* この **メディア プレイヤー** は既定のストリーミング エンドポイントから再生を行います。 既定以外のストリーミング エンドポイントから再生する場合は、URL をクリックしてコピーし、別のプレーヤーを使用します。 ( [Azure Media Services プレーヤーなど](http://amsplayer.azurewebsites.net/azuremediaplayer.html))。
* ストリーミング元となるストリーミング エンドポイントが実行されている必要があります。  

## <a name="next-steps"></a>次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO2-->


