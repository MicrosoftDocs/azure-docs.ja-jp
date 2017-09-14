---
title: "Azure Portal での Media Services アカウントへのファイルのアップロード | Microsoft Docs"
description: "このチュートリアルでは、Azure Portal で Media Services アカウントにファイルをアップロードする手順について説明します。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: 3eb68cba15e89c455d7d33be1ec0bf596df5f3b7
ms.openlocfilehash: 7ddfe44918b358a1749640d1c93dba490855cc5a
ms.contentlocale: ja-jp
ms.lasthandoff: 09/01/2017

---
# <a name="upload-files-to-a-media-services-account-in-the-azure-portal"></a>Azure Portal での Media Services アカウントへのファイルのアップロード
> [!div class="op_single_selector"]
> * [ポータル](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST ()](media-services-rest-upload-files.md)
> 
> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。 詳細については、[Azure の無料評価版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。 
> 

Azure Media Services で、デジタル ファイルを資産にアップロードします。 資産には、ビデオ、オーディオ、画像、サムネイル コレクション、テキスト トラック、クローズド キャプション ファイル (および各ファイルのメタデータ) を含めることができます。 ファイルをアップロードすると、クラウドにコンテンツが安全に保存され、処理したりストリーミングしたりできるようになります。

> [!NOTE]
> Media Services では、処理するファイルに対して、最大ファイル サイズの制限があります。 ファイル サイズの制限の詳細については、[Media Services のクォータと制限](media-services-quotas-and-limitations.md)に関する記事を参照してください。
>

## <a name="upload-files"></a>ファイルのアップロード
1. [Azure Portal](https://portal.azure.com/) で Azure Media Services アカウントを選択します。
2. **[設定]** > **[資産]**を参照してください。 次に、**[アップロード]** ボタンを選択します。
   
    ![ファイルのアップロード](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    **[Upload a video asset (ビデオ資産をアップロード)]** ウィンドウが表示されます。
   
   > [!NOTE]
   > Media Services には、アップロードするビデオ ファイルのサイズに関する制限はありません。
 
3. コンピューター上で、アップロードするビデオの場所に移動します。 ビデオを選択し、**[OK]** を選択します。  
   
    アップロードが開始されます。 ファイル名の下に進行状況が表示されます。  

アップロードが完了すると、**[資産]** ウィンドウの一覧に新しい資産が表示されます。 

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>次のステップ
* [アップロードした資産のエンコード](media-services-portal-encode.md)方法を学ぶ。

* また、Azure Functions を使用すると、構成したコンテナーにファイルが到着したときにエンコード ジョブをトリガーすることもできます。 詳細については、「[Media Services: Integrating Azure Media Services with Azure Functions and Logic Apps (Media Services: Azure Media Services と Azure Functions および Logic Apps の統合)](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/)」のサンプルを参照してください。



