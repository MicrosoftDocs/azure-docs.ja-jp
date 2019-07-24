---
title: 'チュートリアル: Azure CDN を使用し、静的な Web サイト上で SSL を使ってカスタム ドメインを有効にする - Azure Storage'
description: 静的な Web サイト ホスティングのためにカスタム ドメインを構成する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: tamram
ms.custom: seodec18
ms.openlocfilehash: 6ccd33805fe4b62d3456121321edc4eec3bff2e5
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53110378"
---
# <a name="tutorial-use-azure-cdn-to-enable-a-custom-domain-with-ssl-for-a-static-website"></a>チュートリアル: Azure CDN を使用し、静的な Web サイトに対して SSL を使ってカスタム ドメインを有効にする

このチュートリアルは、シリーズの第 2 部です。 ここでは、静的な Web サイトに対して SSL を使ってカスタム ドメイン エンドポイントを有効にする方法について説明します。 

このチュートリアルでは、[Azure CDN](../../cdn/cdn-overview.md) を使用して、静的な Web サイト用のカスタム ドメイン エンドポイントを構成する方法を示します。 Azure CDN では、カスタム SSL 証明書をプロビジョニングし、カスタム ドメインを使用して、同時にすべてのカスタム書き換えルールを構成することができます。 Azure CDN を構成すると追加料金が発生しますが、世界中のあらゆる場所から Web サイトへの一貫した低待機時間が提供されます。 また、Azure CDN では、独自の証明書での SSL 暗号化が提供されます。 Azure CDN の価格については、[Azure CDN の価格](https://azure.microsoft.com/pricing/details/cdn/)に関するページを参照してください。

シリーズの第 2 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * 静的な Web サイト エンドポイントで CDN エンドポイントを作成する
> * カスタム ドメインと SSL を有効にする

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、第 1 部の「[Tutorial: Host a static website on Blob Storage](storage-blob-static-website-host.md)」 (チュートリアル: Blob Storage で静的な Web サイトをホストする) を完了します。 

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[Azure ポータル](https://portal.azure.com/)にサインインして、作業を開始します。

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>静的な Web サイト エンドポイントで CDN エンドポイントを作成する

1. Web ブラウザーで [Azure ポータル](https://portal.azure.com/)を開きます。 
1. ストレージ アカウントを見つけて、アカウントの概要を表示します。
1. **[Blob service]** メニューで **[Azure CDN]** を選択して、Azure CDN を構成します。
1. **[新しいエンドポイント]** セクションで、フィールドに入力して新しい CDN エンドポイントを作成します。
1. エンドポイント名 (*mystaticwebsiteCDN* など) を入力します。
1. CDN エンドポイントのホスト名として、Web サイトのドメインを入力します。
1. 配信元のホスト名については、静的な Web サイト エンドポイントを入力します。 静的な Web サイト エンドポイントを見つけるには、ストレージ アカウントの **[静的な Web サイト]** セクションに移動して、エンドポイントをコピーします。 
1. ブラウザーで *mywebsitecdn.azureedge.net* に移動して、CDN エンドポイントをテストします。

## <a name="enable-custom-domain-and-ssl"></a>カスタム ドメインと SSL を有効にする

1. ドメイン名プロバイダーで CNAME レコードを作成し、カスタム ドメインを CDN エンドポイントにリダイレクトします。 *www* サブドメインの CNAME レコードは、次のようになります。

    ![www サブドメインの CNAME レコードを指定する](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. Azure ポータルで、新しく作成されたエンドポイントをクリックし、カスタム ドメインと SSL 証明書を構成します。
1. **[カスタム ドメインの追加]** を選択し、ドメイン名を入力してから **[追加]** をクリックします。
1. 新しく作成されたカスタム ドメインのマッピングを選択し、SSL 証明書をプロビジョニングします。
1. **[カスタム ドメイン HTTPS]** を **[オン]** に設定します。 **[CDN マネージド]** を選択し、Azure CDN で SSL 証明書を管理するようにします。 **[Save]** をクリックします。
1. Web サイトの URL にアクセスして、Web サイトをテストします。

## <a name="next-steps"></a>次の手順

このチュートリアルの第 2 部では、静的な Web サイト用に Azure CDN で SSL を使用してカスタム ドメインを構成する方法について説明しました。

次は以下のリンクに従って、Azure Storage での静的な Web サイト ホスティングの詳細を確認します。

> [!div class="nextstepaction"]
> [静的な Web サイトに関する詳細](storage-blob-static-website.md)
