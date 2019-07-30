---
title: チュートリアル - HTTPS 経由で Azure CDN のカスタム ドメインを使用してストレージ BLOB にアクセスする | Microsoft Docs
description: ''
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: e3b10760b95662570c8a6e802a57e1073e2faa0a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593382"
---
# <a name="tutorial-access-storage-blobs-using-an-azure-cdn-custom-domain-over-https"></a>チュートリアル:HTTPS 経由で Azure CDN のカスタム ドメインを使用してストレージ BLOB にアクセスする

ご自分の Azure ストレージ アカウントを Azure Content Delivery Network (CDN) と統合したら、カスタム ドメインを追加し、ご利用のカスタム BLOB ストレージのエンドポイントに対してそのドメインで HTTPS を有効にすることができます。 

## <a name="prerequisites"></a>前提条件

このチュートリアルの手順を完了するには、ご利用の Azure ストレージ アカウントを Azure CDN と統合する必要があります。 詳細については、「[クイック スタート: Azure ストレージ アカウントと Azure CDN との統合](cdn-create-a-storage-account-with-cdn.md)」を参照してください。

## <a name="add-a-custom-domain"></a>カスタム ドメインの追加
プロファイルに CDN エンドポイントを作成すると、azureedge.net のサブドメインであるエンドポイント名が、CDN コンテンツを配信するための URL に既定で含まれます。 カスタム ドメインを CDN エンドポイントと関連付けるオプションも用意されています。 このオプションを使用すると、URL にエンドポイント名の代わりにカスタム ドメインを使用してコンテンツを配信できます。 ご利用のエンドポイントにカスタム ドメインを追加するには、次のチュートリアルの手順に従ってください:「[カスタム ドメインを Azure CDN エンドポイントに追加する](cdn-map-content-to-custom-domain.md)」。

## <a name="configure-https"></a>HTTPS の構成
ご利用のカスタム ドメインで HTTPS プロトコルを使用することにより、TLS/SSL 暗号化を使用してインターネット上でデータを確実にセキュリティで保護して配信できます。 Web ブラウザーが HTTPS 経由で Web サイトに接続しているときに、Web サイトのセキュリティ証明書を検証し、正当な証明機関によって発行されていることを確認します。 ご利用のカスタム ドメインで HTTP を構成するには、次のチュートリアルの手順に従ってください:「[Azure CDN カスタム ドメインで HTTPS を構成する](cdn-custom-ssl.md)」。

## <a name="shared-access-signatures"></a>Shared Access Signature
匿名の読み取りアクセスを許可しないよう BLOB ストレージのエンドポイントを設定している場合は、ご利用のカスタム ドメインに対するそれぞれの要求で [Shared Access Signature (SAS)](cdn-sas-storage-support.md) トークンを提供する必要があります｡ 既定では､BLOB ストレージ エンドポイントは匿名の読み取りアクセスを許可しません｡ SAS に関する詳細については、「[コンテナーと BLOB への匿名読み取りアクセスを管理する](../storage/blobs/storage-manage-access-to-resources.md)」を参照してください。

Azure CDN では、SAS トークンに追加された制限がすべて無視されます。 たとえば、SAS トークンにはすべて有効期限がありまる。つまり、コンテンツが CDN のポイント オブ プレゼンス (POP) サーバーから削除されるまで、期限切れの SAS でそのコンテンツにアクセスし続けることができるということです。 Azure CDN 上でデータがキャッシュされる期間は、キャッシュ応答ヘッダーを設定することで制御できます｡ 詳細については、｢[Azure CDN で Azure Blob Storage の有効期限を管理する](cdn-manage-expiration-of-blob-content.md)｣を参照してください｡

同じ BLOB エンドポイントに複数の SAS URL を作成する場合は、クエリ文字列のキャッシュを有効にすることを検討します。 そうすることで、各 URL が一意のエンティティとして処理されるようになります｡ 詳細については、「[クエリ文字列による Azure CDN キャッシュ動作の制御 - Standard レベル](cdn-query-string.md)」を参照してください。

## <a name="http-to-https-redirection"></a>HTTP から HTTPS へのリダイレクト
[Azure CDN ルール エンジン](cdn-verizon-premium-rules-engine.md)を使用して [URL リダイレクト規則](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)を作成することで、HTTP トラフィックから HTTPS にリダイレクトするように選択できます。 このオプションには、**Azure CDN Premium from Verizon** プロファイルが必要です。

![URL リダイレクト規則](./media/cdn-storage-custom-domain-https/cdn-url-redirect-rule.png)

この規則では、*Cdn-endpoint-name* では、ご利用の CDN エンドポイントに対して構成した名前を参照します。これは、ドロップダウン リストから選択できます。 *origin-path* の値では、ご自分の静的コンテンツが存在する配信元ストレージ アカウント内のパスを参照します。 静的コンテンツをすべて 1 つのコンテナーでホストしている場合は、*origin-path* をそのコンテナーの名前に置き換えます。

## <a name="pricing-and-billing"></a>価格と課金
Azure CDN 経由で BLOB にアクセスする場合、POP サーバーと配信元 (BLOB ストレージ) 間のトラフィックに対する [BLOB ストレージ料金](https://azure.microsoft.com/pricing/details/storage/blobs/)と、POP サーバーからアクセスするデータに対する [Azure CDN 価格](https://azure.microsoft.com/pricing/details/cdn/)がかかります｡

たとえば、Azure CDN を使用してアクセスされている米国でストレージ アカウントを所有し、ヨーロッパにいる任意のユーザーが Azure CDN 経由でそのストレージ アカウントの BLOB のいずれかにアクセスしようとしている場合、Azure CDN ではまずその BLOB 向けにヨーロッパに最も近い POP が確認されます。 見つかった場合は、Azure CDN でその BLOB のコピーにアクセスします｡これは Azure CDN からアクセスされているので、CDN 価格になります｡ 見つからなかった場合は、Azure CDN で POP サーバーに BLOB をコピーし (BLOB ストレージ価格で指定されたようにエグレス料金とトランザクション料金が発生)、その POP サーバー上のファイルにアクセスします (Azure CDN 課金が発生)｡

## <a name="next-steps"></a>次の手順
[チュートリアル:Azure CDN キャッシュ規則の設定](cdn-caching-rules-tutorial.md)




