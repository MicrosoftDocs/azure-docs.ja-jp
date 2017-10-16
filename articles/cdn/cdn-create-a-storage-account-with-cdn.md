---
title: "Azure ストレージ アカウントと Azure CDN との統合 | Microsoft Docs"
description: "Azure Content Delivery Network (CDN) を使用して、Azure Storage から BLOB をキャッシュすることにより、高帯域幅コンテンツを配信する方法について説明します。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 511076935d06ed0908341044e37069e74530be49
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-an-azure-storage-account-with-azure-cdn"></a>Azure ストレージ アカウントと Azure CDN との統合
CDN を使用して、Azure ストレージのコンテンツをキャッシュできます。 CDN は、米国、ヨーロッパ、アジア、オーストラリア、および南米にある物理ノードで、コンピューティング インスタンスの BLOB と静的コンテンツをキャッシュすることで、高帯域幅コンテンツを配信するグローバル ソリューションを開発者に提供します。

## <a name="step-1-create-a-storage-account"></a>ステップ 1: ストレージ アカウントを作成する
Azure サブスクリプションの新しいストレージ アカウントを作成するには、次の手順に従います。 ストレージ アカウントを使用すると、Azure のストレージ サービスにアクセスできます。 ストレージ アカウントは、Blob service、Queue サービス、および Table service という Azure の各ストレージ サービス コンポーネントへのアクセスに使用する最高レベルの名前空間です。 詳しくは、「 [Microsoft Azure Storage の概要](../storage/common/storage-introduction.md)」をご覧ください。

ストレージ アカウントを作成するには、関連付けられているサブスクリプションのサービス管理者または共同管理者である必要があります。

> [!NOTE]
> Azure Portal や Powershell など、ストレージ アカウントの作成に使用できる方法はいくつかあります。  このチュートリアルでは Azure Portal を使用します。  
> 
> 

**Azure サブスクリプションのストレージ アカウントを作成するには**

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左上隅にある **[新規]**を選択します。 **[新規]** ダイアログ ボックスで、**[データ + ストレージ]** を選択し、**[ストレージ アカウント]** をクリックします。
    
    **[ストレージ アカウントを作成]** ブレードが表示されます。   

    ![[ストレージ アカウントを作成]][create-new-storage-account]  

3. **[名前]** フィールドにサブドメイン名を入力します。 文字数は 3 ～ 24 文字とし、アルファベット小文字と数字を使用できます。
   
    この名前は、対応するサブスクリプションの BLOB リソース、キュー リソース、またはテーブル リソースのアドレス指定に使用される URI のホスト名になります。 Blob service でコンテナー リソースをアドレス指定するには、次の形式の URI を使用します。*&lt;StorageAccountLabel&gt;* は、**[URL を入力してください]** に入力した値を表します。
   
    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*
   
    **重要:** この URL は、ストレージ アカウントの URI のサブドメインとなるため、Azure のすべてのホストされるサービスで一意である必要があります。
   
    この値は、このストレージ アカウントの名前として、ポータルやプログラムでこのアカウントにアクセスするときにも使用されます。
4. **[デプロイ モデル]**、**[アカウントの種類]**、**[パフォーマンス]**、**[レプリケーション]** は既定値のままにします。 
5. ストレージ アカウントを使用する **サブスクリプション** を選択します。
6. **リソース グループ**を選択または作成します。  リソース グループの詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md#resource-groups)」を参照してください。
7. ストレージ アカウントの場所を選択します。
8. **Create** をクリックしてください。 ストレージ アカウントを作成するプロセスは、完了までに数分かかる場合があります。

## <a name="step-2-enable-cdn-for-the-storage-account"></a>手順 2: ストレージ アカウントの CDN を有効にする

最新の統合により、ストレージ ポータル拡張機能を終了しなくても、ストレージ アカウントの CDN を有効にすることができるようになりました。 

1. ストレージ アカウントを選択し、"CDN" を検索するか左側のナビゲーション メニューから下にスクロールして、[Azure CDN] をクリックします。
    
    **[Azure CDN]** ブレードが表示されます。

    ![CDN を有効にするナビゲーション][cdn-enable-navigation]
    
2. 必要な情報を入力して新しいエンドポイントを作成します。
    - **CDN のプロファイル**: 新しいプロファイルを作成するか、既存のプロファイルを使用することができます。
    - **価格レベル**: 新しい CDN プロファイルを作成する場合にのみ、価格レベルを選択する必要があります。
    - **CDN endpoint name (CDN エンドポイント名)**: 任意のエンドポイント名を入力します。

    > [!TIP]
    > 作成した CDN エンドポイントは、既定の送信元としてストレージ アカウントのホスト名を使用します。

    ![cdn new endpoint creation][cdn-new-endpoint-creation]

3. 作成後は、上記のエンドポイント一覧に新しいエンドポイントが表示されます。

    ![CDN ストレージの新しいエンドポイント][cdn-storage-new-endpoint]

> [!NOTE]
> Azure CDN 拡張機能に移動して CDN を有効にすることもできます。詳細については[チュートリアル](#Tutorial-cdn-create-profile)を参照してください。
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]  

## <a name="step-3-enable-additional-cdn-features"></a>手順 3: その他の CDN 機能を有効にする

ストレージ アカウント [Azure CDN] ブレードから、一覧の CDN エンドポイントをクリックして CDN の構成ブレードを開きます。 圧縮、クエリ文字列、geo フィルタリングなど、配布用にその他の CDN 機能を有効にすることができます。 また、カスタム ドメイン マッピングを CDN エンドポイントに追加して、カスタム ドメインの HTTPS を有効にすることもできます。
    
![CDN ストレージの CDN の構成][cdn-storage-cdn-configuration]

## <a name="step-4-access-cdn-content"></a>ステップ 4: CDN コンテンツにアクセスする
CDN にキャッシュされたコンテンツにアクセスするには、ポータルで提供される CDN URL を使用します。 キャッシュされた BLOB のアドレスは、次のようになります。

http://<*EndpointName*\>.azureedge.net/<*myPublicContainer*\>/<*BlobName*\>

> [!NOTE]
> ストレージ アカウントへの CDN アクセスを有効にすると、パブリックにアクセスできるオブジェクトは CDN エッジ キャッシュの対象になります。 CDN で現在キャッシュされているオブジェクトを変更する場合、キャッシュされたコンテンツの有効期限終了時に CDN によりそのコンテンツが更新されるまでは、CDN を通じて新しいコンテンツを使用することはできません。
> 
> 

## <a name="step-5-remove-content-from-the-cdn"></a>ステップ 5: CDN からコンテンツを削除する
Azure Content Delivery Network (CDN) にオブジェクトをキャッシュする必要がなくなった場合は、次のいずれかの手順を実行できます。

* コンテナーをパブリックではなくプライベートに設定できます。 詳細については、「 [コンテナーと BLOB への匿名読み取りアクセスを管理する](../storage/blobs/storage-manage-access-to-resources.md) 」を参照してください。
* 管理ポータルを使用して CDN エンドポイントを無効にしたり削除したりできます。
* オブジェクトの要求に応答しなくなるようにホストされるサービスを変更できます。

CDN に既にキャッシュされているオブジェクトは、オブジェクトの有効期限が切れるまで、またはエンドポイントが消去されるまでキャッシュに残ったままとなります。 有効期限が切れると、CDN エンドポイントがまだ有効で、オブジェクトがまだ匿名アクセス可能かどうかが CDN によって確認されます。 CDN エンドポイントが無効になり、オブジェクトに匿名でアクセスできなくなった場合、オブジェクトはキャッシュされなくなります。

## <a name="additional-resources"></a>その他のリソース
* [CDN コンテンツをカスタム ドメインにマッピングする方法](cdn-map-content-to-custom-domain.md)
* [Azure CDN カスタム ドメインの HTTPS の有効化](cdn-custom-ssl.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png
[cdn-enable-navigation]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png
[cdn-storage-new-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png
[cdn-storage-cdn-configuration]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png 
