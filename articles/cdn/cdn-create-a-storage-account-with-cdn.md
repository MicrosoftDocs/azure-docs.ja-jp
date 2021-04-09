---
title: クイック スタート:Azure Storage アカウントと Azure CDN との統合
description: このクイックスタートでは、Azure Content Delivery Network (CDN) を使用して、Azure Storage から BLOB をキャッシュすることにより、高帯域幅コンテンツを配信する方法について説明します。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 444639d9e36b67f25ed9b82bd68102273c66ae00
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951234"
---
# <a name="quickstart-integrate-an-azure-storage-account-with-azure-cdn"></a>クイック スタート:Azure Storage アカウントと Azure CDN との統合

このクイックスタートでは、[Azure Content Delivery Network (CDN)](cdn-overview.md) を有効にして、Azure Storage からのコンテンツをキャッシュします。 Azure CDN では、高帯域幅コンテンツ配信用のグローバル ソリューションが開発者に提供されています。 Azure CDN は、コンピューティング インスタンスの BLOB と静的コンテンツを、米国、ヨーロッパ、アジア、オーストラリア、南アメリカの物理ノードにキャッシュできます。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

Azure アカウントで [Azure Portal](https://portal.azure.com) にサインインします。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

ストレージ アカウントを使うと、Azure Storage サービスにアクセスできます。 ストレージ アカウントは、Azure Storage サービスの各コンポーネント (Azure Blob Storage、Queue Storage、Table Storage) にアクセスするための最高レベルの名前空間を表します。 詳細については、「[Microsoft Azure Storage の概要](../storage/common/storage-introduction.md)」をご覧ください。

ストレージ アカウントを作成するには、サービス管理者または関連付けられているサブスクリプションの共同管理者である必要があります。

1. Azure portal の左上にある **[リソースの作成]** を選択します。 **[新規]** ウィンドウが表示されます。

1. **[ストレージ アカウント]** を探し、ドロップダウン リストから **[ストレージ アカウント - Blob、File、Table、Queue]** を選択します。 次に、 **[作成]** を選択します。
    
    ![ストレージ リソースの選択](./media/cdn-create-a-storage-account-with-cdn/cdn-select-new-storage-account.png)

1. **[ストレージ アカウントの作成] ペイン** で、次の情報を入力します。

    | 設定 | 値 | 
    | --- | --- |
    | [プロジェクトの詳細] > [リソース グループ] | **[新規作成]** を選択し、*CDNQuickstart-rg* という名前を使用します。 既存のリソース グループを使用してもかまいません。 |
    | [インスタンスの詳細] > [ストレージ アカウント名] | 3 から 24 文字の小文字と数値のみを使用してアカウントの名前を入力します。 この名前は、Azure 全体で一意であることが必要です。対応するサブスクリプションの BLOB リソース、キュー リソース、またはテーブル リソースのアドレス指定に使用される URL のホスト名になります。 Blob Storage 内のコンテナー リソースをアドレス指定するには、 http:// *&lt;storageaccountname&gt;* .blob.core.windows.net/ *&lt;container-name&gt;* 形式の URI を使います。
    | [インスタンスの詳細] > [Location]\(場所\) | ドロップダウン リストから最寄りの Azure リージョンを選択します。 |
    
    その他の情報はすべて既定値のままにして、 **[確認と作成]** を選択します。

1. ストレージ アカウントの作成は、完了までに数分かかる場合があります。 作成後、 **[リソースに移動]** を選択して、ストレージ アカウントのページを開き、次の手順に備えます。

## <a name="enable-azure-cdn-for-the-storage-account"></a>ストレージ アカウントの Azure CDN を有効にする

1. ストレージ アカウントのページで、左側のメニューから **[Blob service]**  >  **[Azure CDN]** を選択します。 **[Azure CDN]** ページが表示されます。

    ![CDN エンドポイントを作成する](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png)
    
1. **[新しいエンドポイント]** セクションで、次の情報を入力します。

    | 設定  | 値 |
    | -------- | ----- |
    | **[CDN プロファイル]** | **[新規作成]** を選択し、プロファイル名を入力します (例: *cdn-profile-123*)。 プロファイルは、エンドポイントのコレクションです。 |
    | **価格レベル** | いずれかの **Standard** オプション (**Standard Microsoft** など) を選択します。 |
    | **[CDN エンドポイント名]** | エンドポイントのホスト名を入力します (例: *cdn-endpoint-123*)。 この名前は、 _&lt;エンドポイント名&gt;_ .azureedge.net という URL でキャッシュされたリソースにアクセスする際に使用されるため、Azure 全体でグローバルに一意である必要があります。 |
    | **配信元のホスト名** | 既定では、新しい CDN エンドポイントは、ストレージ アカウントのホスト名を配信元サーバーとして使います。 |

1. **［作成］** を選択します 作成されたエンドポイントが、エンドポイントの一覧に表示されます。

    ![ストレージの新しい CDN エンドポイント](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png)

> [!TIP]
> CDN エンドポイントの詳細な構成設定 ([大きなファイル ダウンロードの最適化](cdn-optimization-overview.md#large-file-download)など) を指定する場合は、代わりに [Azure CDN 拡張機能](cdn-create-new-endpoint.md)を使用して CDN プロファイルとエンドポイントを作成できます。


## <a name="enable-additional-cdn-features"></a>その他の CDN 機能を有効にする

ストレージ アカウントの **[Azure CDN]** ページで、一覧から CDN エンドポイントを選んで、CDN エンドポイント構成ページを開きます。

このページから、[圧縮](cdn-improve-performance.md)、[クエリ文字列キャッシュ](cdn-query-string.md)、[geo フィルタリング](cdn-restrict-access-by-country.md)など、配信のその他の CDN 機能を有効にすることができます。 
    
## <a name="enable-sas"></a>SAS を有効にする

プライベート ストレージ コンテナーへの制限付きアクセス権限を付与するには、Azure Storage アカウントの Shared Access Signature (SAS) 機能を使用できます。 SAS は、アカウント キーを公開することなく Azure Storage リソースへの制限付きアクセス権を付与する URI です。 詳細については、「[SAS を利用した Azure CDN の使用](cdn-sas-storage-support.md)」を参照してください。

## <a name="access-cdn-content"></a>CDN コンテンツにアクセスする

CDN にキャッシュされたコンテンツにアクセスするには、ポータルで提供される CDN URL を使用します。 キャッシュされた BLOB のアドレスの形式は次のようになります。

http://<*endpoint-name*\>.azureedge.net/<*myPublicContainer*\>/<*BlobName*\>

> [!NOTE]
> ストレージ アカウントへの Azure CDN アクセスを有効にすると、パブリックにアクセスできるオブジェクトが CDN POP キャッシュの対象になります。 CDN で現在キャッシュされているオブジェクトを変更する場合、キャッシュされたコンテンツの有効期限が終了した後で Azure CDN がそのコンテンツを更新するまで、Azure CDN を通して新しいコンテンツを使うことはできません。

## <a name="remove-content-from-azure-cdn"></a>Azure CDN からコンテンツを削除する

Azure CDN にオブジェクトをキャッシュする必要がなくなった場合は、次のいずれかの手順を実行できます。

- コンテナーをパブリックではなくプライベートにします。 詳細については、「 [コンテナーと BLOB への匿名読み取りアクセスを管理する](../storage/blobs/anonymous-read-access-configure.md)」を参照してください。
- Azure Portal を使って、CDN エンドポイントを無効にするか削除します。
- オブジェクトの要求に応答しなくなるように、ホストされるサービスを変更します。

Azure CDN に既にキャッシュされているオブジェクトは、オブジェクトの有効期限が切れるまで、またはエンドポイントが[消去](cdn-purge-endpoint.md)されるまで、キャッシュに残ったままとなります。 有効期限が切れると、Azure CDN は、CDN エンドポイントがまだ有効で、オブジェクトがまだ匿名アクセス可能かどうかを確認します。 そうではない場合、オブジェクトはキャッシュされなくなります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

前の手順では、リソース グループ内に CDN プロファイルとエンドポイントを作成しました。 [次の手順](#next-steps)に進んでエンドポイントにカスタム ドメインを追加する方法について学習するには、これらのリソースを保存してください。 ただし、将来これらのリソースを使用する予定がない場合は、次の手順に従ってリソース グループを削除してリソースを削除することで、追加の料金が発生するのを避けることができます。

1. Azure portal の左側のメニューで、 **[リソース グループ]** 、[*CDNQuickstart-rg*\*] の順に選択します。

2. **[リソース グループ]** ページで、 **[リソース グループの削除]** を選択し、テキスト ボックスに「*CDNQuickstart-rg*」と入力して、 **[削除]** を選択します。

    このアクションにより、このクイック スタートで作成したリソース グループ、プロファイル、およびエンドポイントが削除されます。

3. ストレージ アカウントを削除するには、ダッシュボードからそのアカウントを選択し、上部のメニューから **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル: サーバーの静的コンテンツへの CDN を Web アプリから使用する](cdn-add-to-web-app.md)
