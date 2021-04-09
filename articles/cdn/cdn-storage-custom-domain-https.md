---
title: チュートリアル - HTTPS 経由で Azure CDN のカスタム ドメインを使用してストレージ BLOB にアクセスする
description: Azure CDN のカスタム ドメインを追加し、ご利用のカスタム Blob Storage のエンドポイントに対してそのドメインで HTTPS を有効にする方法について説明します。
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 296a51edf024551a03330f4843690e97c6abd47d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564885"
---
# <a name="tutorial-access-storage-blobs-using-an-azure-cdn-custom-domain-over-https"></a>チュートリアル - HTTPS 経由で Azure CDN のカスタム ドメインを使用してストレージ BLOB にアクセスする

ご自分の Azure ストレージ アカウントを Azure Content Delivery Network (CDN) と統合したら、カスタム ドメインを追加し、ご利用のカスタム BLOB ストレージのエンドポイントに対してそのドメインで HTTPS を有効にすることができます。 

## <a name="prerequisites"></a>前提条件

このチュートリアルの手順を完了するには、ご利用の Azure ストレージ アカウントを Azure CDN と統合する必要があります。 詳細については、「[クイック スタート: Azure ストレージ アカウントと Azure CDN との統合](cdn-create-a-storage-account-with-cdn.md)」を参照してください。

## <a name="add-a-custom-domain"></a>カスタム ドメインの追加
プロファイルに CDN エンドポイントを作成すると、azureedge.net のサブドメインであるエンドポイント名が、CDN コンテンツを配信するための URL に既定で含まれます。 カスタム ドメインを CDN エンドポイントと関連付けるオプションも用意されています。 このオプションを使用すると、URL にエンドポイント名の代わりにカスタム ドメインを使用してコンテンツを配信できます。 ご利用のエンドポイントにカスタム ドメインを追加するには、「[チュートリアル: カスタム ドメインを Azure CDN エンドポイントに追加する](cdn-map-content-to-custom-domain.md)」の手順に従います。

## <a name="configure-https"></a>HTTPS の構成
ご利用のカスタム ドメインで HTTPS プロトコルを使用することにより、TLS/SSL 暗号化を使用してインターネット上でデータを確実にセキュリティで保護して配信できます。 Web ブラウザーが HTTPS 経由で Web サイトに接続しているときに、Web サイトのセキュリティ証明書を検証し、正当な証明機関によって発行されていることを確認します。 ご利用のカスタム ドメインで HTTP を構成するには、「[チュートリアル: Azure CDN カスタム ドメインで HTTPS を構成する](cdn-custom-ssl.md)」の手順に従います。

## <a name="shared-access-signatures"></a>Shared Access Signature
匿名の読み取りアクセスを許可しないよう BLOB ストレージのエンドポイントを設定している場合は、ご利用のカスタム ドメインに対するそれぞれの要求で [Shared Access Signature (SAS)](cdn-sas-storage-support.md) トークンを提供する必要があります｡ 既定では､BLOB ストレージ エンドポイントは匿名の読み取りアクセスを許可しません｡ SAS に関する詳細については、「[コンテナーと BLOB への匿名読み取りアクセスを管理する](../storage/blobs/anonymous-read-access-configure.md)」を参照してください。

Azure CDN では、SAS トークンに追加された制限がすべて無視されます。 たとえば、SAS トークンにはすべて有効期限がありまる。つまり、コンテンツが CDN のポイント オブ プレゼンス (POP) サーバーから削除されるまで、期限切れの SAS でそのコンテンツにアクセスし続けることができるということです。 Azure CDN 上でデータがキャッシュされる期間は、キャッシュ応答ヘッダーを設定することで制御できます｡ 詳細については、｢[Azure CDN で Azure Blob Storage の有効期限を管理する](cdn-manage-expiration-of-blob-content.md)｣を参照してください｡

同じ BLOB エンドポイントに複数の SAS URL を作成する場合は、クエリ文字列のキャッシュを有効にすることを検討します。 そうすることで、各 URL が一意のエンティティとして処理されるようになります｡ 詳細については、「[クエリ文字列による Azure CDN キャッシュ動作の制御 - Standard レベル](cdn-query-string.md)」を参照してください。

## <a name="http-to-https-redirection"></a>HTTP から HTTPS へのリダイレクト
[Standard ルール エンジン](cdn-standard-rules-engine.md)または [Verizon Premium ルール エンジン](cdn-verizon-premium-rules-engine.md)を使用して URL リダイレクト規則を作成することで、HTTP トラフィックを HTTPS にリダイレクトすることを選択できます。 Standard ルール エンジンは、Microsoft プロファイルの Azure CDN に対してのみ使用できます。Verizon Premium ルール エンジンは、Verizon プロファイルの Azure CDN Premium からのみ使用できます。

![Microsoft のリダイレクト規則](./media/cdn-storage-custom-domain-https/cdn-standard-redirect-rule.png)

上記の規則では、[ホスト名]、[パス]、[クエリ文字列]、[フラグメント] をそのままにすると、受信値がリダイレクトで使用されることになります。 

![Verizon のリダイレクト規則](./media/cdn-storage-custom-domain-https/cdn-url-redirect-rule.png)

上記の規則では、*Cdn-endpoint-name* は、ご利用の CDN エンドポイントに対して構成した名前を参照します。これは、ドロップダウン リストから選択できます。 *origin-path* の値では、ご自分の静的コンテンツが存在する配信元ストレージ アカウント内のパスを参照します。 静的コンテンツをすべて 1 つのコンテナーでホストしている場合は、*origin-path* をそのコンテナーの名前に置き換えます。

## <a name="pricing-and-billing"></a>価格と課金
Azure CDN 経由で BLOB にアクセスする場合、POP サーバーと配信元 (BLOB ストレージ) 間のトラフィックに対する [BLOB ストレージ料金](https://azure.microsoft.com/pricing/details/storage/blobs/)と、POP サーバーからアクセスするデータに対する [Azure CDN 価格](https://azure.microsoft.com/pricing/details/cdn/)がかかります｡

たとえば、Azure CDN を使用してアクセスされている米国でストレージ アカウントを所有し、ヨーロッパにいる任意のユーザーが Azure CDN 経由でそのストレージ アカウントの BLOB のいずれかにアクセスしようとしている場合、Azure CDN ではまずその BLOB 向けにヨーロッパに最も近い POP が確認されます。 見つかった場合は、Azure CDN でその BLOB のコピーにアクセスします｡これは Azure CDN からアクセスされているので、CDN 価格になります｡ 見つからなかった場合は、Azure CDN で POP サーバーに BLOB をコピーし (BLOB ストレージ価格で指定されたようにエグレス料金とトランザクション料金が発生)、その POP サーバー上のファイルにアクセスします (Azure CDN 課金が発生)｡

## <a name="next-steps"></a>次のステップ
[チュートリアル:Azure CDN キャッシュ規則の設定](cdn-caching-rules-tutorial.md)