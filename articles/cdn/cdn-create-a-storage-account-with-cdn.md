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
translationtype: Human Translation
ms.sourcegitcommit: 06bd0112eab46f3347dfb039a99641a37c2b0197
ms.openlocfilehash: 3a0db75612531ebf947c011e9e4545f7d9590e20


---
# <a name="integrate-an-azure-storage-account-with-azure-cdn"></a>Azure ストレージ アカウントと Azure CDN との統合
CDN を使用して、Azure ストレージのコンテンツをキャッシュできます。 CDN は、米国、ヨーロッパ、アジア、オーストラリア、および南米にある物理ノードで、コンピューティング インスタンスの BLOB と静的コンテンツをキャッシュすることで、高帯域幅コンテンツを配信するグローバル ソリューションを開発者に提供します。

## <a name="step-1-create-a-storage-account"></a>ステップ 1: ストレージ アカウントを作成する
Azure サブスクリプションの新しいストレージ アカウントを作成するには、次の手順に従います。 ストレージ アカウントを使用すると、Azure のストレージ サービスにアクセスできます。 ストレージ アカウントは、BLOB サービス、Queue サービス、および Table サービスという Azure の各ストレージ サービス コンポーネントへのアクセスに使用する最高レベルの名前空間です。 詳しくは、「 [Microsoft Azure Storage の概要](../storage/storage-introduction.md)」をご覧ください。

ストレージ アカウントを作成するには、関連付けられているサブスクリプションのサービス管理者または共同管理者である必要があります。

> [!NOTE]
> Azure ポータルや Powershell など、ストレージ アカウントの作成に使用できる方法はいくつかあります。  このチュートリアルでは Azure ポータルを使用します。  
> 
> 

**Azure サブスクリプションのストレージ アカウントを作成するには**

1. [Azure ポータル](https://portal.azure.com)にサインインします。
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
8. **[作成]**をクリックします。 ストレージ アカウントを作成するプロセスは、完了までに数分かかる場合があります。

## <a name="step-2-create-a-new-cdn-profile"></a>ステップ 2: 新しい CDN プロファイルを作成する
CDN プロファイルは、CDN エンドポイントのコレクションです。  各プロファイルには、1 つ以上の CDN エンドポイントが含まれます。  複数のプロファイルを使って、インターネット ドメイン、Web アプリケーション、またはその他の一部の基準別に CDN エンドポイントを整理する必要が生じる場合があります。

> [!TIP]
> このチュートリアルで使用する CDN プロファイルが既にある場合は [手順 3](#step-3-create-a-new-cdn-endpoint)に進みます。
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="step-3-create-a-new-cdn-endpoint"></a>ステップ 3: 新しい CDN エンドポイントを作成する
**ストレージ アカウントに対する新しい CDN エンドポイントを作成するには**

1. [Microsoft Azure 管理ポータル](https://portal.azure.com)で CDN プロファイルに移動します。  これは、前の手順でダッシュボードにピン留めしている可能性があります。  ピン留めしていない場合は、**[参照]**、**[CDN プロファイル]** の順にクリックし、エンドポイントの追加先のプロファイルをクリックします。
   
    CDN プロファイル ブレードが表示されます。
   
    ![CDN プロファイル][cdn-profile-settings]
2. **[エンドポイントの追加]** ボタンをクリックします。
   
    ![[エンドポイントの追加] ボタン][cdn-new-endpoint-button]
   
    **[エンドポイントの追加]** ブレードが表示されます。
   
    ![[エンドポイントの追加] ブレード][cdn-add-endpoint]
3. この CDN エンドポイントの **名前** を入力します。  この名前は、ドメイン `<endpointname>.azureedge.net`でキャッシュされたリソースにアクセスする際に使用します。
4. **[配信元の種類]** ドロップダウンで、 *[ストレージ]*を選択します。  
5. **[配信元ホスト名]** ドロップダウンで、ストレージ アカウントを選択します。
6. **[元のパス]**、**[配信元のホスト ヘッダー]**、および **[プロトコル/配信元ポート]** の既定値はそのまま使用します。  少なくとも&1; つのプロトコル (HTTP または HTTPS) を指定する必要があります。
   
   > [!NOTE]
   > この構成により、ストレージ アカウントで公開されているすべてのコンテナーの CDN でのキャッシュが可能になります。  範囲を&1; つのコンテナーに限定する場合は、 **配信元パス**を使用します。  コンテナーの表示設定は公開に指定されている必要があります。
   > 
   > 
7. **[追加]** ボタンをクリックして、新しいエンドポイントを作成します。
8. エンドポイントが作成されると、プロファイルのエンドポイントの一覧に表示されます。 一覧には、キャッシュされたコンテンツへのアクセスに使用する URL と元のドメインが表示されます。
   
    ![CDN エンドポイント][cdn-endpoint-success]
   
   > [!NOTE]
   > エンドポイントはすぐには使用できません。  登録が CDN ネットワークに反映されるまでに最大で 90 分かかる場合があります。 CDN ドメイン名を直ちに使用しようとするユーザーは、CDN を経由してコンテンツを取得できるようになるまでは状態コード 404 を受け取る場合があります。
   > 
   > 

## <a name="step-4-access-cdn-content"></a>ステップ 4: CDN コンテンツにアクセスする
CDN にキャッシュされたコンテンツにアクセスするには、ポータルで提供される CDN URL を使用します。 キャッシュされた BLOB のアドレスは、次のようになります。

http://<*EndpointName*\>.azureedge.net/<*myPublicContainer*\>/<*BlobName*\>

> [!NOTE]
> ストレージ アカウントまたはホストされるサービスへの CDN アクセスを有効にすると、パブリックにアクセスできるオブジェクトは CDN エッジ キャッシュの対象になります。 CDN で現在キャッシュされているオブジェクトを変更する場合、キャッシュされたコンテンツの有効期限終了時に CDN によりそのコンテンツが更新されるまでは、CDN を通じて新しいコンテンツを使用することはできません。
> 
> 

## <a name="step-5-remove-content-from-the-cdn"></a>ステップ 5: CDN からコンテンツを削除する
Azure Content Delivery Network (CDN) にオブジェクトをキャッシュする必要がなくなった場合は、次のいずれかの手順を実行できます。

* コンテナーをパブリックではなくプライベートに設定できます。 詳細については、「 [コンテナーと BLOB への匿名読み取りアクセスを管理する](../storage/storage-manage-access-to-resources.md) 」を参照してください。
* 管理ポータルを使用して CDN エンドポイントを無効にしたり削除したりできます。
* オブジェクトの要求に応答しなくなるようにホストされるサービスを変更できます。

CDN に既にキャッシュされているオブジェクトは、オブジェクトの有効期限が切れるまで、またはエンドポイントが消去されるまでキャッシュに残ったままとなります。 有効期限が切れると、CDN エンドポイントがまだ有効で、オブジェクトがまだ匿名アクセス可能かどうかが CDN によって確認されます。 CDN エンドポイントが無効になり、オブジェクトに匿名でアクセスできなくなった場合、オブジェクトはキャッシュされなくなります。

## <a name="additional-resources"></a>その他のリソース
* [CDN コンテンツをカスタム ドメインにマッピングする方法](cdn-map-content-to-custom-domain.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

[cdn-profile-settings]: ./media/cdn-create-a-storage-account-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-a-storage-account-with-cdn/cdn-endpoint-success.png



<!--HONumber=Jan17_HO4-->


