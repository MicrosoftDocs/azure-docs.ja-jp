---
title: Azure Portal での Media Services アカウントへのファイルのアップロード | Microsoft Docs
description: このチュートリアルでは、Azure Portal で Media Services アカウントにファイルをアップロードする手順について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 3ebeb3c601dd3f734265d49d60728056561928be
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803218"
---
# <a name="upload-files-to-a-media-services-account-in-the-azure-portal"></a>Azure Portal での Media Services アカウントへのファイルのアップロード 

> [!div class="op_single_selector"]
> * [ポータル](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-from-v2-to-v3.md)を参照してください。

Azure Media Services で、デジタル ファイルをアセットにアップロードします。 資産には、ビデオ、オーディオ、画像、サムネイル コレクション、テキスト トラック、クローズド キャプション ファイル (および各ファイルのメタデータ) を含めることができます。 ファイルをアップロードすると、クラウドにコンテンツが安全に保存され、処理したりストリーミングしたりできるようになります。

Media Services では、処理するファイルに対して、最大ファイル サイズの制限があります。 ファイル サイズの制限の詳細については、[Media Services のクォータと制限](media-services-quotas-and-limitations.md)に関する記事を参照してください。

このチュートリアルを完了するには、Azure アカウントが必要です。 詳細については、[Azure の無料評価版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。 

## <a name="upload-files"></a>ファイルのアップロード
1. [Azure Portal](https://portal.azure.com/) で Azure Media Services アカウントを選択します。
2. **[設定]** > **[資産]** を参照してください。 次に、**[アップロード]** ボタンを選択します。
   
    ![ファイルのアップロード](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    **[Upload a video asset (ビデオ アセットのアップロード)]** ウィンドウが表示されます。
   
   > [!NOTE]
   > Media Services には、アップロードするビデオ ファイルのサイズに関する制限はありません。
 
3. コンピューター上で、アップロードするビデオの場所に移動します。 ビデオを選択し、**[OK]** を選択します。  
   
    アップロードが開始されます。 ファイル名の下に進行状況が表示されます。  

アップロードが完了すると、**[アセット]** ウィンドウの一覧に新しいアセットが表示されます。 

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>次の手順
* [アップロードしたアセットのエンコード](media-services-portal-encode.md)方法を学ぶ。

* また、Azure Functions を使用すると、構成したコンテナーにファイルが到着したときにエンコード ジョブをトリガーすることもできます。 詳しくは、「[Media Services:Integrating Azure Media Services with Azure Functions and Logic Apps (Media Services: Azure Media Services と Azure Functions および Logic Apps の統合)](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/)」のサンプルをご覧ください。


