---
title: クイックスタート - Azure portal を使用して BLOB を作成する
titleSuffix: Azure Storage
description: このクイック スタートでは、オブジェクト (BLOB) ストレージで Azure portal を使用します。 その後、Azure Portal を使用して、Azure Storage への BLOB のアップロード、BLOB のダウンロード、およびコンテナー内の BLOB の一覧表示を行います。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 10/19/2020
ms.author: tamram
ms.openlocfilehash: edadcc4025913052e048ea94d47cac253e4bcd1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95523333"
---
# <a name="quickstart-upload-download-and-list-blobs-with-the-azure-portal"></a>クイック スタート:Azure portal を使用して BLOB をアップロード、ダウンロード、および一覧表示する

このクイックスタートでは、[Azure Portal](https://portal.azure.com/) を使用して、Azure Storage 内にコンテナーを作成したり、そのコンテナー内のブロック BLOB をアップロードおよびダウンロードしたりする方法を説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-container"></a>コンテナーを作成する

Azure Portal でコンテナーを作成するには、次の手順に従います。

1. Azure Portal で新しいストレージ アカウントに移動します。
2. ストレージ アカウントの左側のメニューで、 **[Blob service]** セクションまでスクロールしてから、 **[コンテナー]** を選択します。
3. **[+ コンテナー]** ボタンを選択します。
4. 新しいコンテナーの名前を入力します。 コンテナー名は小文字である必要があり、英文字または数字で始まる必要があり、英文字、数字、ダッシュ (-) 文字のみを含めることができます。 コンテナーと BLOB の名前の詳細については、「[Naming and referencing containers, blobs, and metadata (コンテナー、BLOB、およびメタデータの名前付けと参照)](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」を参照してください。
5. コンテナーにパブリック アクセスのレベルを設定します。 既定のレベルは **[ プライベート (匿名アクセスなし)]** です。
6. **[OK]** を選択してコンテナーを作成します。

    :::image type="content" source="media/storage-quickstart-blobs-portal/create-container.png" alt-text="Azure Portal でコンテナーを作成する方法を示したスクリーンショット":::

## <a name="upload-a-block-blob"></a>ブロック BLOB をアップロードする

ブロック BLOB は、BLOB を作成するためにまとめられたデータのブロックで構成されます。 Blob ストレージを使用するほとんどのシナリオでは、ブロック BLOB を使用します。 ブロック BLOB は、クラウドでファイル、画像、およびビデオのようなテキストやバイナリ データを格納するために最適です。 このクイックスタートでは、ブロック BLOB を使用する方法を示します。

Azure Portal で新しいコンテナーにブロック BLOB をアップロードするには、次の手順に従います。

1. Azure Portal で、前のセクションで作成したコンテナーに移動します。
1. コンテナーを選択して、それに含まれている BLOB の一覧を表示します。 このコンテナーは新しいため、BLOB はまだ含まれていません。
1. **[アップロード]** ボタンを選択してアップロード ブレードを開き、ローカル ファイル システムを参照して、ブロック BLOB としてアップロードするファイルを見つけます。 必要に応じて **詳細** セクションを展開し、アップロード操作に関する他の設定を構成することができます。

    :::image type="content" source="media/storage-quickstart-blobs-portal/upload-blob.png" alt-text="Azure portal を使用してローカル ドライブから BLOB をアップロードする方法を示したスクリーン ショット":::

1. **[アップロード]** ボタンを選択して、BLOB をアップロードします。
1. この方法で、希望する数の BLOB をアップロードします。 これで新しい BLOB がコンテナー内で一覧表示されていることを確認できます。

## <a name="download-a-block-blob"></a>ブロック BLOB をダウンロードする

ブロック BLOB をダウンロードして、ブラウザーで表示したり、ローカル ファイル システムに保存したりできます。 ブロック BLOB をダウンロードするには、次の手順に従います。

1. 前のセクションでアップロードした BLOB の一覧に移動します。
1. ダウンロードする BLOB を右クリックし、 **[ダウンロード]** を選択します。

    :::image type="content" source="media/storage-quickstart-blobs-portal/download-blob.png" alt-text="Azure Portal で BLOB をダウンロードする方法を示したスクリーンショット":::

## <a name="delete-a-block-blob"></a>ブロック BLOB を削除する

Azure portal で 1 つ以上の BLOB を削除するには、次の手順に従います。

1. Azure portal でコンテナーに移動します。
1. コンテナー内の BLOB の一覧を表示します。
1. チェック ボックスを使用して、一覧から 1 つ以上の BLOB を選択します。
1. 選択した BLOB を削除するには、 **[削除]** ボタンを選択します。
1. ダイアログで、削除を確認し、BLOB スナップショットも削除するかどうかを指定します。

:::image type="content" source="media/storage-quickstart-blobs-portal/delete-blobs.png" alt-text="Azure portal から BLOB を削除する方法を示したスクリーンショット":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートで作成したすべてのリソースは、単にコンテナーを削除するだけで削除できます。 コンテナー内のすべての BLOB も削除されます。

コンテナーを削除するには:

1. Azure Portal でストレージ アカウント内のコンテナーの一覧に移動します。
1. 削除するコンテナーを選択します。
1. **[詳細]** ボタン ( **[...]** ) を選択し、 **[削除]** を選択します。
1. コンテナーを削除することを確認します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure portal を使用して、コンテナーを作成して BLOB をアップロードする方法を学習しました。 Web アプリから BLOB ストレージを操作する方法の詳細については、ストレージ アカウントへのイメージのアップロード方法を示すチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル:Azure Storage を使用してクラウドに画像データをアップロードする](storage-upload-process-images.md)