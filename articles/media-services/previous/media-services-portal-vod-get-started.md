---
title: Azure Portal を使用したビデオ オン デマンド配信の概要 | Microsoft Docs
description: このチュートリアルでは、Azure Portal での Azure Media Services アプリケーションによる基本的なビデオ オン デマンド コンテンツ配信サービスの実装手順を紹介します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: fc908f909cdb64e968db78b2c98b8f0edf1c61dd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009693"
---
# <a name="get-started-with-delivering-content-on-demand-by-using-the-azure-portal"></a>Azure Portal を使用したオン デマンド コンテンツ配信の概要

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](../latest/index.yml) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-v-2-v-3-migration-introduction.md)を参照してください。

このチュートリアルでは、Azure Portal での Azure Media Services アプリケーションによる基本的なビデオ オン デマンド コンテンツ配信サービスの実装手順を紹介します。

## <a name="prerequisites"></a>前提条件
チュートリアルを完了するには以下が必要です。

* Azure アカウント。 詳細については、[Azure の無料評価版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。 
* Media Services アカウント。 Media Services アカウントを作成するには、[Media Services アカウントを作成する方法](media-services-portal-create-account.md)に関するページを参照してください。

このチュートリアルに含まれるタスクは次のとおりです。

1. ストリーミング エンドポイントを開始する。
2. ビデオ ファイルをアップロードする
3. 一連のアダプティブ ビットレート MP4 ファイルにソース ファイルをエンコードします。
4. アセットを発行してストリーミング URL とプログレッシブ ダウンロード URL を取得する。  
5. コンテンツの再生

## <a name="start-the-streaming-endpoint"></a>ストリーミング エンドポイントを開始する

アダプティブ ビットレート ストリーミングでのビデオ配信は、Azure Media Services の代表的な用途の 1 つです。 Media Services では、ダイナミック パッケージが使用できます。 ダイナミック パッケージでは、アダプティブ ビットレート MP4 エンコード コンテンツを、Media Services でサポートされている Just-In-Time ストリーミング形式で配信できます。 例としては、Apple HTTP ライブ ストリーミング (HLS)、Microsoft Smooth Streaming、Dynamic Adaptive Streaming over HTTP (DASH。MPEG-DASH とも呼ばれます) などがあります。 Media Services アダプティブ ビットレート ストリーミングを使用すると、これらの各ストリーミング形式の事前にパッケージ化されたバージョンを格納しなくても、ビデオを配信できます。

> [!NOTE]
> Media Services アカウントの作成時に、既定のストリーミング エンドポイントが自分のアカウントに追加され、**停止** 状態になっています。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが **実行中** 状態である必要があります。 

ストリーミング エンドポイントを開始するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[設定]**  >  **[ストリーミング エンドポイント]** の順に選択します。 
3. 既定のストリーミング エンドポイントを選択します。 **[DEFAULT STREAMING ENDPOINT DETAILS (既定のストリーミング エンドポイントの詳細)]** ウィンドウが表示されます。
4. **[開始]** アイコンを選択します。
5. **[保存]** を選択します。

## <a name="upload-files"></a>ファイルをアップロードする
Media Services を使用してビデオをストリーミングするには、ソース ビデオをアップロードし、複数のビットレートにエンコードして発行します。 このセクションで最初の手順を説明します。 

1. [Azure Portal](https://portal.azure.com/) で Azure Media Services アカウントを選択します。
2. **[設定]**  >  **[アセット]** を参照してください。 次に、 **[アップロード]** ボタンを選択します。
   
    ![ファイルをアップロードする](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    **[Upload a video asset (ビデオ アセットのアップロード)]** ウィンドウが表示されます。
   
   > [!NOTE]
   > Media Services には、アップロードするビデオ ファイルのサイズに関する制限はありません。
   > 
   > 
3. コンピューター上で、アップロードするビデオの場所に移動します。 ビデオを選択し、 **[OK]** を選択します。  
   
    アップロードが開始されます。 ファイル名の下に進行状況が表示されます。  

アップロードが完了すると、 **[アセット]** ウィンドウの一覧に新しいアセットが表示されます。 

## <a name="encode-assets"></a>Encode assets
ダイナミック パッケージを利用するには、ソース ファイルを一連のマルチビットレート MP4 ファイルにエンコードする必要があります。 このセクションでは、エンコードの手順が示されています。

### <a name="encode-assets-in-the-portal"></a>ポータルでのアセットのエンコード
Azure Portal で Media Encoder Standard を使用してコンテンツをエンコードするには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com/) で Azure Media Services アカウントを選択します。
2. **[設定]**  >  **[アセット]** を参照してください。 エンコードするアセットを選択します。
3. **[エンコード]** を選択します。
4. **[アセットのエンコード]** ウィンドウで、**Media Encoder Standard** プロセッサとプリセットを選択します。 プリセットについては、[ビットレート ラダーの自動生成](media-services-autogen-bitrate-ladder-with-mes.md)に関するページと [Media Encoder Standard 用のタスク プリセット](media-services-mes-presets-overview.md)に関するページを参照してください。 入力ビデオに最適なプリセットを選択することが重要です。 たとえば、入力ビデオの解像度が 1920 &#215; 1080 ピクセルであるとわかっている場合は、**H264 Multiple Bitrate 1080p** のプリセットを使用します。 低解像度 (640 &#215; 360) のビデオの場合は、**H264 Multiple Bitrate 1080p** プリセットを使用しないでください。
   
   リソースを管理しやすくするために、出力アセットの名前とジョブの名前を編集することができます。
   
   ![Encode assets](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. **［作成］** を選択します

### <a name="monitor-encoding-job-progress"></a>エンコード ジョブの進行状況の監視
エンコード ジョブの進行状況を監視するには、ページの上部にある **[設定]** を選択し、 **[ジョブ]** を選択します。

![ジョブ](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>コンテンツを発行する
コンテンツのストリーミングまたはダウンロードに使用できる URL をユーザーに提供するには、まず、ロケーターを作成してアセットを発行する必要があります。 アセット内のファイルには、ロケーターを通じてアクセスできます。 Azure Media Services では、次の 2 種類のロケーターがサポートされています。 

* **ストリーミング (OnDemandOrigin) ロケーター**。 ストリーミング ロケーターは、アダプティブ ストリーミングに使用されます。 アダプティブ ストリーミングの例として、HLS、Smooth Streaming、MPEG-DASH などが挙げられます。 ストリーミング ロケーターを作成するには、アセットに .ism ファイルが含まれている必要があります。 
* **プログレッシブ (Shared Access Signature) ロケーター**。 プログレッシブ ロケーターは、プログレッシブ ダウンロードを通じてビデオを配信する場合に使用されます。

HLS ストリーミング URL を作成するには、次のように、 *(format=m3u8-aapl)* を URL に追加します。

`{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)`

Smooth Streaming アセットを再生するためのストリーミング URL を作成するには、次の URL 形式を使用します。

`{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest`

MPEG-DASH ストリーミング URL を作成するには、 *(format=mpd-time-csf)* を URL に追加します。

`{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)`

Shared Access Signature URL の形式は、次のとおりです。

`{blob container name}/{asset name}/{file name}/{shared access signature}`

> [!NOTE]
> 2015 年 3 月より前に Azure Portal で作成されたロケーターには、2 年間の有効期限があります。  
> 
> 

ロケーターの有効期限を更新するには、[REST API](/rest/api/media/operations/locator#update_a_locator) または [.NET API](/dotnet/api/microsoft.windowsazure.mediaservices.client.ilocator) を使用することができます。 

> [!NOTE]
> Shared Access Signature ロケーターの有効期限を更新すると、URL が変更されます。

### <a name="to-use-the-portal-to-publish-an-asset"></a>ポータルを使用してアセットを発行するには
1. [Azure Portal](https://portal.azure.com/) で Azure Media Services アカウントを選択します。
2. **[設定]**  >  **[アセット]** を参照してください。 発行する資産を選択します。
3. **[発行]** を選択します。
4. ロケーターの種類を選択します。
5. **[追加]** を選択します。
   
    ![ビデオを発行する](./media/media-services-portal-vod-get-started/media-services-publish1.png)

URL が **[発行された URL]** の一覧に追加されます。

## <a name="play-content-from-the-portal"></a>ポータルでコンテンツを再生する
Azure Portal のコンテンツ プレーヤーでビデオをテストできます。

ビデオを選択し、 **[再生]** を選択します。

![Azure Portal でビデオを再生する](./media/media-services-portal-vod-get-started/media-services-play.png)

いくつかの考慮事項が適用されます。

* ストリーミングを開始するには、既定のストリーミング エンドポイントの実行を開始してください。
* ビデオが発行されたことを確認します。
* Azure Portal のメディア プレーヤーは、既定のストリーミング エンドポイントから再生を行います。 既定以外のストリーミング エンドポイントから再生する場合は、URL を選択してコピーし、別のプレーヤーに貼り付けます。 たとえば、[Azure Media Player](https://aka.ms/azuremediaplayer) でビデオをテストすることができます。

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>次のステップ
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]