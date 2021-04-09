---
title: Azure Media Services REST API 呼び出し用の Postman の構成
description: この記事では、Media Services REST API 呼び出し用の Postman を構成する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: ef92e772085b1b89388c3f85fb3fdb91df0f6a75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012209"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Media Services v2 REST API 呼び出し用に Postman を構成する

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](../latest/index.yml) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-v-2-v-3-migration-introduction.md)を参照してください。

このチュートリアルでは、Azure Media Services (AMS) REST API を呼び出すために使用できるように **Postman** を構成する方法を示します。 チュートリアルでは、環境およびコレクション ファイルを **Postman** にインポートする方法を示しています。 コレクションには、Azure Media Services (AMS) REST API を呼び出す HTTP 要求のグループ化された定義が含まれます。 環境ファイルには、コレクションによって使用される変数が含まれています。

この環境とコレクションは、Azure Media Services REST API を使用してさまざまなタスクを実現する方法を示す記事で使用されます。

## <a name="prerequisites"></a>前提条件

- [Postman](https://www.getpostman.com/) REST クライアントをインストールして、AMS REST チュートリアルの一部で示されている REST API を実行します。 

    ここでは **Postman** を使用しますが、任意の REST ツールを使用できます。 その他の選択肢は、REST プラグインを使用する **Visual Studio Code** や **Telerik Fiddler** です。 

## <a name="configure-the-environment"></a>環境の構成 

1. AMS チュートリアルで使用される環境変数を含む .json ファイルを作成します。 ファイルに名前を付けます (例: **AzureMediaServices.postman_environment.json**)。 ファイルを開き、Postman 環境を定義するコードを[このコード リスト](postman-environment.md)から貼り付けます。 
2. **Postman** を開きます。
3. 画面の右側で、 **[Manage environment]/(環境の管理/)** オプションを選択します。

    ![スクリーンショットには、選択されている [Manage environment]/(環境の管理/) オプションが示されています。](./media/media-services-rest-upload-files/postman-create-env.png)
4. **[Manage environment]/(環境の管理/)** ダイアログで、 **[インポート]** をクリックします。
5. **AzureMediaServices.postman_environment.json** ファイルを参照し、選択します。
6. **AzureMedia** 環境が追加されます。
7. ダイアログを閉じます。
8. **AzureMedia** 環境を選択します。

    ![スクリーンショットには、選択された AzureMedia 環境が示されています。](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>コレクションの構成

1. Media Services にファイルをアップロードするために必要なすべての操作を含む **Postman** コレクションが格納された .json ファイルを作成します。 ファイルに名前を付けます (例: **AzureMediaServicesOperations.postman_collection.json**)。 ファイルを開き、**Postman** コレクションを定義するコードを [このコード リスト](postman-collection.md)から貼り付けます。
2. **[インポート]** をクリックしてコレクション ファイルをインポートします。
3. **AzureMediaServicesOperations.postman_collection.json** ファイルを選択します。

    ![スクリーンショットには、[ファイルの選択] が選択されている [インポート] ダイアログ ボックスが示されています。](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>次のステップ

[資産のアップロード](media-services-rest-upload-files.md)に関する記事を確認します。  
