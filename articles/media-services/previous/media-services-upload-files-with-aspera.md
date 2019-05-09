---
title: Aspera を使用した Azure Media Services アカウントへのファイルのアップロード | Microsoft Docs
description: このチュートリアルでは、Azure 上の **Aspera Server On Demand** サービスを使って、Media Services アカウントに関連付けられているストレージ アカウントにファイルをアップロードする手順について説明します。
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.assetid: 8812623a-b425-4a0f-9e05-0ee6c839b6f9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: e522883da7fddad44741599107f2dbc4c99aace6
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316373"
---
# <a name="upload-files-into-a-media-services-account-using-the-aspera-server-on-demand-service-on-azure"></a>Azure 上の Aspera Server On Demand サービスを使用して Media Services アカウントにファイルをアップロードする 

## <a name="overview"></a>概要

**Aspera** は高速ファイル転送ソフトウェアです。 Azure 用 **Aspera Server On Demand** を使用すると、大きいファイルを直接 Azure BLOB オブジェクト ストレージに高速でアップロードおよびダウンロードすることができます。 **Aspera On Demand** については、[Aspera Cloud](http://cloud.asperasoft.com/) サイトを参照してください。 
  
Azure 用 **Aspera Server On Demand** は、[Azure Marketplace](https://azure.microsoft.com/marketplace/) で購入できます。 Azure 用 **Aspera Server On Demand** の購入を完了するには、Windows Live ID を使って Azure Marketplace にログインしてください。

このチュートリアルでは、Azure 上の **Aspera Server On Demand** サービスを使って、Media Services アカウントに関連付けられているストレージ アカウントにファイルをアップロードする手順について説明します。 

Azure 関数を Aspera および Media Services と共に使用する方法を示す例は、[ここ](https://github.com/Azure-Samples/media-services-dotnet-functions-integration/tree/master/103-aspera-ingest)にあります。

>[!NOTE]
>Azure Media Services のメディア プロセッサ (MP) での処理についてサポートされる最大ファイル サイズには制限があります。 ファイル サイズの制限の詳細については、[こちら](media-services-quotas-and-limitations.md)の記事を参照してください。
>

## <a name="prerequisites"></a>前提条件 

このチュートリアルを完了するには、次のものが必要です。

* Windows Live ID。
* [Azure アカウント](https://azure.microsoft.com)。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。 
* [Azure Media Services アカウント](media-services-portal-create-account.md)。

## <a name="purchase-aspera-on-demand-for-azure"></a>Aspera On Demand for Azure の購入

Azure Marketplace にログインしたら、次の基本的な手順に従って、Aspera On Demand for Azure の購入を完了します。

1. Aspera を検索し、"Server On Demand" を選択します。

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera001.png)

2. サブスクリプション プランを確認し、[Sign Up (サインアップ)] をクリックします。

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera002.png)

3. Server On Demand サブスクリプションの詳細を入力します。

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera003.png)

4. **[価格レベル]** をクリックし、サブパネルで希望の月間ボリュームを選択します。 **[Plan details (プランの詳細)]** パネルで、**[OK]** を選択します。 その後、**[価格レベルの選択]** パネルで、**[選択]** をクリックします。

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera004.png)

5. **[法律条項]** をクリックし、サブパネルで法律条項を確認して同意します。 法律条項を確認したら、**[購入]** をクリックします。

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera005.png)

6. **[作成]** をクリックして購入を完了します。

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera006.png)

7. Azure ダッシュボードでは、サービスをプロビジョニングしていることが通知されます。  サービスのプロビジョニングが完了すると、リソース内でサービスの名前を検索して、新しいサブスクリプションを見つけることができます。 サービスが見つかったら、それをダブルクリックして、サービス管理ポータルを起動します。

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera007.png)

8. Aspera の管理ポータルを起動します。 新しい Aspera サービスが見つかったら、そのサービスをクリックすることで、管理ポータルにアクセスできるようになります。  新しいパネルが起動します。 新しいパネル内から、新しいサービスの**リソース名**をクリックする必要があります。  次のスクリーンショットでは、リソース名は "AsperaTransferDemo" です。 リソース名をクリックすると、別のパネルが起動します。 その新しく起動したパネルには、[管理] リンクが表示されます。 この [管理] リンクをクリックして、Aspera の管理ポータルを起動します。

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera008.png)

9. [管理] リンクをクリックすると、登録ページが表示されます。これは、サービスにアクセスするために必要です。

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera009.png)

10. この時点で、Aspera のサービス管理ポータルにアクセスできます。このポータルでは、アクセス キーの作成、Aspera のクライアントとライセンスのダウンロード、使用状況の表示、API の詳細確認を実行できます。

    次のスクリーンショットは、アクセスの作成を示します。 

    ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera010.png)

    次のスクリーンショットは、ポータルの使用状況レポート インターフェイスを示しています。 

    ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera011.png)

## <a name="upload-files-with-aspera"></a>Aspera を使用したファイルのアップロード

1. Aspera クライアント ソフトウェアをダウンロードしてインストールします。
    
    * [ブラウザー プラグイン](https://downloads.asperasoft.com/connect2/)
    * [リッチ クライアント](https://downloads.asperasoft.com/en/downloads/2)

2. 最初の転送を行います。 Aspera クライアントを使って Aspera 転送サービスで転送するには、次の手順を完了する必要があります。 

   1. Aspera ポータルを使用して、アクセス キーを作成します。  
   2. Aspera クライアントをダウンロード、インストール、ライセンスします (ソフトウェアは Aspera ポータルで見つかります)。  

      >[!NOTE]
      >構成情報については、Aspera クライアント ガイドをお読みください。
    
   3. Azure Media アカウントに関連付けられているストレージ アカウントの情報を、[Azure Portal](https://portal.azure.com/) を使用して取得します。 具体的には、名前とキーのほか、コンテンツの配置先となるストレージ BLOB コンテナー名です。 

       * ポータルからストレージ情報を取得するには、ストレージ アカウントを見つけて、[アクセス キー] をクリックし、アカウントの名前とキーをコピーします。
       * コンテナー名を取得するには、ストレージ アカウントを見つけて、**[BLOB]** を選択し、コンテンツのアップロード先にするコンテナーの名前を選択します。 

      Aspera クライアントの **Connection Manager** のスクリーンショットを次に示します。ここでは、ストレージの種類 "Azure" と資格情報に加え、BLOB コンテナーを指定する必要があります。

      ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera012.png)

## <a name="resources"></a>リソース

この記事では、次のリソースについて説明しました。 

* [Connect ブラウザー プラグイン](https://downloads.asperasoft.com/connect2/)
* [Connect ガイド](https://downloads.asperasoft.com/en/documentation/8)
* [Aspera クライアント](https://downloads.asperasoft.com/en/downloads/2)
* [クライアント ガイド](https://downloads.asperasoft.com/en/documentation/2)

## <a name="next-steps"></a>次の手順

[ストレージ アカウントから AMS アカウントに BLOB をコピー](media-services-copying-existing-blob.md#copy-blobs-from-a-storage-account-into-an-ams-account)できます。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

