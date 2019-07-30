---
title: REST を使用した Azure Media Services アカウントへのファイルのアップロード | Microsoft Docs
description: 資産を作成し、アップロードすることによって、Media Services にメディア コンテンツを取得する方法について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: a6f872880b61a5bd9510abda2f15e2edea16e940
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703871"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>REST を使用して Media Services アカウントにファイルをアップロードする

Media Services で、デジタル ファイルを資産に関連付けられている BLOB コンテナーにアップロードします。 [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) エンティティには、ビデオ、オーディオ、画像、サムネイル コレクション、テキスト トラック、クローズド キャプション ファイル (各ファイルのメタデータを含む) を格納できます。 ファイルを資産のコンテナーにアップロードすると、コンテンツがクラウドに安全に保存され、処理したりストリーミングしたりできるようになります。

この記事では、REST を使用してローカル ファイルをアップロードする方法を示します。

## <a name="prerequisites"></a>前提条件

このトピックで説明する手順を完了するには以下を行う必要があります。

- [資産の概念](assets-concept.md)を確認します。
- [Azure Media Services REST API を呼び出すように Postman を構成します](media-rest-apis-with-postman.md)。
    
    トピック「[Azure AD トークンを取得する](media-rest-apis-with-postman.md#get-azure-ad-token)」の最後の手順を必ず実行してください。 

## <a name="create-an-asset"></a>資産を作成する

このセクションでは、新しい資産を作成する方法を示します。

1. **[Assets]\(資産\)**  ->  **[Create or update an Asset]\(資産を作成または更新する\)** を選択します。
2. **[送信]** をクリックします。

    ![資産を作成する](./media/upload-files/postman-create-asset.png)

新しく作成された資産に関する情報を含む**応答**が表示されます。

## <a name="get-a-sas-url-with-read-write-permissions"></a>読み取り/書き込みアクセス許可を持つ SAS URL を取得する 

このセクションでは、作成された資産に対して生成された SAS URL を取得する方法を示します。 作成された SAS URL には読み取り/書き込みアクセス許可が設定されており、資産コンテナーにデジタル ファイルをアップロードするために使用できます。

1. **[Assets]\(資産\)**  ->  **[List the Asset URLs]\(資産 URL の一覧表示\)** を選択します。
2. **[送信]** をクリックします。

    ![ファイルをアップロードする](./media/upload-files/postman-create-sas-locator.png)

資産の URL に関する情報を含む**応答**が表示されます。 最初の URL をコピーし、それを使用してファイルをアップロードします。

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>アップロード URL を使用して BLOB ストレージにファイルをアップロードする

Azure Storage API または SDK (たとえば、[Storage REST API](../../storage/common/storage-rest-api-auth.md)、[JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md)、[.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) を使用します。

## <a name="next-steps"></a>次の手順

[チュートリアル:リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する - REST](stream-files-tutorial-with-rest.md)
