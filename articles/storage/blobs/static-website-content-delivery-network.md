---
title: 静的な Web サイトを Azure CDN と統合する - Azure Storage
description: Azure Content Delivery Network (CDN) を使用して、Azure Storage アカウントから静的な Web サイトのコンテンツをキャッシュする方法について説明します。
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.author: normesta
ms.date: 04/07/2020
ms.openlocfilehash: e458f98d82c910ec845ebf951b7f6470b6aba10d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95527328"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>静的な Web サイトを Azure CDN と統合する

[Azure Content Delivery Network (CDN)](../../cdn/cdn-overview.md) を有効にして、Azure ストレージアカウントでホストされている[静的な Web サイト](storage-blob-static-website.md)のコンテンツをキャッシュすることができます。 Azure CDN を使用すると、静的な Web サイトのカスタム ドメイン エンドポイントの構成、カスタム TLS/SSL 証明書のプロビジョニング、およびカスタム書き換え規則の構成を行うことができます。 Azure CDN を構成すると追加料金が発生しますが、世界中のあらゆる場所から Web サイトへの一貫した低待機時間が提供されます。 また、Azure CDN では、独自の証明書での TLS 暗号化が提供されます。 

Azure CDN の価格については、[Azure CDN の価格](https://azure.microsoft.com/pricing/details/cdn/)に関するページを参照してください。

## <a name="enable-azure-cdn-for-your-static-website"></a>静的な Web サイトで Azure CDN を有効にする

ストレージ アカウントから直接、静的な Web サイトの Azure CDN を有効にできます。 CDN エンドポイントの詳細な構成設定 ([大きなファイル ダウンロードの最適化](../../cdn/cdn-optimization-overview.md#large-file-download)など) を指定する場合は、代わりに [Azure CDN 拡張機能](../../cdn/cdn-create-new-endpoint.md)を使用して CDN プロファイルとエンドポイントを作成できます。

1. Azure portal で自分のストレージ アカウントを探して、アカウントの概要を表示します。

1. **[Blob Service]** メニューで **[Azure CDN]** を選択して、 **[Azure CDN]** ページを開きます。

    ![CDN エンドポイントを作成する](media/storage-blob-static-website-custom-domain/cdn-storage-new.png)

1. **[CDN プロファイル]** セクションで、新しい CDN プロファイルを作成するか、既存の CDN プロファイルを使用するかを指定します。 CDN プロファイルは、価格レベルとプロバイダーを共有する CDN エンドポイントをまとめたものです。 次に、サブスクリプション内で一意の CDN の名前を入力します。

1. CDN エンドポイントの価格レベルを指定します。 価格の詳細については、「[Content Delivery Network の価格](https://azure.microsoft.com/pricing/details/cdn/)」をご覧ください。 各レベルで使用可能な機能については、「[Azure CDN 製品の機能を比較する](../../cdn/cdn-features.md)」をご覧ください。

1. **[CDN エンドポイント名]** フィールドに、CDN エンドポイントの名前を指定します。 CDN エンドポイントは、Azure 全体で一意である必要があり、エンドポイント URL の最初の部分となります。 フォームは、エンドポイント名が一意であることを検証します。

1. **[配信元のホスト名]** フィールドに、静的な Web サイト エンドポイントを指定します。 

   静的な Web サイト エンドポイントを見つけるには、ストレージ アカウントの **[静的な Web サイト]** 設定に移動します。  プライマリ エンドポイントをコピーし、CDN 構成に貼り付けます。

   > [!IMPORTANT]
   > URL からプロトコル識別子 (*例:* HTTPS など) と 末尾のスラッシュを必ず削除してください。 たとえば、静的な Web サイトのエンドポイントが `https://mystorageaccount.z5.web.core.windows.net/` の場合、 **[配信元のホスト名]** フィールドで `mystorageaccount.z5.web.core.windows.net` を指定します。

   次の図に、エンドポイント構成の例を示します。

   ![サンプルの CDN エンドポイント構成を示すスクリーンショット](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. **[作成]** を選択し、CDN がプロビジョニングされるまで待機します。 作成されたエンドポイントが、エンドポイントの一覧に表示されます。 (フォームにエラーがある場合は、そのフィールドの横に感嘆符が表示されます)。

1. CDN エンドポイントが正しく構成されていることを確認するには、エンドポイントをクリックして設定に移動します。 ストレージ アカウントの CDN の概要からエンドポイントのホスト名を探し、次の図に示すように、エンドポイントに移動します。 CDN エンドポイントの形式は、`https://staticwebsitesamples.azureedge.net` のようになります。

    ![CDN エンドポイントの概要を示すスクリーンショット](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

1. CDN エンドポイントのプロビジョニングが完了した後、CDN エンドポイントに移動すると、静的な Web サイトに以前アップロードした index.html ファイルの内容が表示されます。

1. CDN エンドポイントの配信元の設定を確認するには、CDN エンドポイントの **[設定]** セクションにある **[配信元]** に移動します。 **[配信元の種類]** フィールドが *[カスタムの配信元]* に設定され、 **[配信元のホスト名]** フィールドに静的な Web サイト エンドポイントが表示されていることを確認できます。

    ![CDN エンドポイントの配信元の設定を示すスクリーンショット](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Azure CDN からコンテンツを削除する

Azure CDN にオブジェクトをキャッシュする必要がなくなった場合は、次のいずれかの手順を実行できます。

* コンテナーをパブリックではなくプライベートにします。 詳細については、「 [コンテナーと BLOB への匿名読み取りアクセスを管理する](./anonymous-read-access-configure.md)」を参照してください。
* Azure Portal を使って、CDN エンドポイントを無効にするか削除します。
* オブジェクトの要求に応答しなくなるように、ホストされるサービスを変更します。

Azure CDN に既にキャッシュされているオブジェクトは、オブジェクトの有効期限が切れるまで、またはエンドポイントが[消去](../../cdn/cdn-purge-endpoint.md)されるまで、キャッシュに残ったままとなります。 有効期限が切れると、Azure CDN は、CDN エンドポイントがまだ有効で、オブジェクトがまだ匿名アクセス可能かどうかを確認します。 そうではない場合、オブジェクトはキャッシュされなくなります。

## <a name="next-steps"></a>次のステップ

(省略可能) カスタム ドメインを Azure CDN エンドポイントに追加します。 「[チュートリアル:カスタム ドメインを Azure CDN エンドポイントに追加します](../../cdn/cdn-map-content-to-custom-domain.md)。