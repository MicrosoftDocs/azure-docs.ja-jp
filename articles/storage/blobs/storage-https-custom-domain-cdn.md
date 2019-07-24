---
title: Azure CDN を使用して HTTPS 経由でカスタム ドメインを使用した BLOB にアクセスする
description: Azure CDN と Blob ストレージを統合することで､カスタム ドメインを使用した BLOB に HTTPS 経由でアクセスする方法について説明します
services: storage
author: michaelhauss
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: mihauss
ms.subservice: blobs
ms.openlocfilehash: 90ddb58f3499180e17df559a98ac8a46b53fb824
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579014"
---
# <a name="use-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Azure CDN を使用して HTTPS 経由でカスタム ドメインを使用した BLOB にアクセスする

Azure CDN (Azure Content Delivery Network) でカスタム ドメイン名に HTTPS をサポートするようになりました｡ Azure CDN では、HTTPS 経由でカスタム ドメイン名を使用して、BLOB にアクセスできます。 そうするには､まず BLOB または Web エンドポイントで Azure CDN を有効にし､Azure CDN をカスタム ドメイン名にマッピングします。 完了したら、Azure では、ワンクリック アクセスと完全な証明書管理によって、カスタム ドメインの HTTPS を簡単に有効にできます。 通常の Azure CDN の料金の追加はありません。

Azure CDN は、転送中の Web アプリケーション データのプライバシーおよびデータ整合性の保護に役立ちます。 HTTPS 経由でのトラフィックの処理に SSL プロトコルを使うことにより､Azure CDN ではインターネットでのデータの送信時にデータが暗号化されます｡ Azure CDN で HTTPS を使用することは、Web アプリケーションを攻撃から保護するのに役立ちます。

> [!NOTE]  
> カスタム ドメイン名に対する SSL サポートに加え､Azure CDN はアプリケーションをスケーリングして世界中に高帯域幅のコンテンツを配信するのにも役立てることができます｡ 詳細については、[Azure CDN の概要](../../cdn/cdn-overview.md)に関するページをご覧ください。

## <a name="quickstart"></a>クイック スタート

カスタム Blob ストレージ エンドポイントに対して HTTPS を有効にするには、次の操作を実行します。

1.  [Azure ストレージ アカウントと Azure CDN を統合します](../../cdn/cdn-create-a-storage-account-with-cdn.md)｡  
    この記事では､まだ Azure portal でストレージ アカウントを作成していない場合に、その手順を順を追って説明します｡

    > [!NOTE]  
    > Azure Storage での静的 Web サイトのサポートのプレビュー中にストレージ Web エンドポイントを追加するには、**[配信元の種類]** ドロップダウン リストから **[カスタムの配信元]** を選択します。 Azure portal では、ストレージ アカウント内で直接これを行うのではなく、Azure CDN プロファイルから行う必要があります。

2.  [Azure CDN コンテンツをカスタム ドメインにマッピングします](../../cdn/cdn-map-content-to-custom-domain.md)｡

3.  [Azure CDN カスタム ドメインで HTTPS を有効にします](../../cdn/cdn-custom-ssl.md)｡

## <a name="shared-access-signatures"></a>共有アクセス署名

既定では､Blob ストレージ エンドポイントは匿名の読み取りアクセスを許可しません｡ 匿名の読み取りアクセスを許可しないよう Blob ストレージ エンドポイントを設定している場合は、ご利用のカスタム ドメインに対するそれぞれの要求で [Shared Access Signature](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) トークンを提供する必要があります｡ 詳細については、「 [コンテナーと BLOB への匿名読み取りアクセスを管理する](storage-manage-access-to-resources.md)」を参照してください。

Azure CDN では、Shared Access Signature トークンに追加されたすべての制限が考慮されません。 たとえば、すべての Shared Access Signature トークンが期限切れになります。 それでも期限切れの Shared Access Signature 付きのコンテンツには、Azure CDN エッジ ノードからそのコンテンツが消去されるまで引き続きアクセスできます。 Azure CDN 上でデータがキャッシュされる期間は、キャッシュ応答ヘッダーを設定することで制御できます｡ この方法については､[Azure CDN での Azure Storage BLOB の有効期限の管理](../../cdn/cdn-manage-expiration-of-blob-content.md)に関するページを参照してください｡

1 つの BLOB エンドポイントに対して複数の Shared Access Signature URL を作成する場合は､Azure CDN に対してクエリ文字列のキャッシュを有効にすることをお勧めします｡ このアクションにより、Azure で各 URL が一意のエンティティとして扱われます。 詳細については、「[クエリ文字列による Azure CDN キャッシュ動作の制御](../../cdn/cdn-query-string.md)」を参照してください。

## <a name="http-to-https-redirection"></a>HTTP から HTTPS へのリダイレクト

HTTP トラフィックを HTTPS にリダイレクトできます｡ そうするには､Verizon の Azure CDN Premium 製品を使用する必要があります｡ 次のルールを適用して､[Azure CDN ルール エンジンで HTTP 動作をオーバーライドする](../../cdn/cdn-rules-engine.md)必要があります｡

![HTTP から HTTPS へのリダイレクト ルール](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

*Cdn-endpoint-name* はドロップダウン リストから選択し、ご利用の Azure CDN エンドポイントに対して構成した名前を表します。 *Origin-path* は､静的コンテンツが格納される配信元ストレージ アカウント内のパスを表します。 静的コンテンツをすべて 1 つのコンテナーでホストしている場合は、*origin-path* をそのコンテナーの名前に置き換えます。

ルールについての詳細は､｢[Azure CDN ルール エンジンの機能](../../cdn/cdn-rules-engine-reference-features.md)｣を参照してください｡

## <a name="pricing-and-billing"></a>価格と課金

Azure CDN から BLOB にアクセスする場合､各エッジ ノードと配信元 (Blob ストレージ) 間のトラフィックに対して、[Blob ストレージ料金](https://azure.microsoft.com/pricing/details/storage/blobs/)がかかります｡ エッジ ノードからアクセスされるデータに対しては、[Azure CDN 料金](https://azure.microsoft.com/pricing/details/cdn/)がかかります。

たとえば､米国西部に Azure CDN 経由でアクセスするストレージアカウントがあるとします｡ 英国にいる従業員が､Azure CDN 経由でそのストレージ アカウント内の BLOB にアクセスしようとした場合､Azure によってまず､英国に最も近いエッジ ノードにその BLOB があるかチェックされます｡ Azure が BLOB を見つけた場合、コピーにアクセスし、Azure CDN 料金を使用します。Azure CDN がそれにアクセスしているためです。 Azure が BLOB を見つけられない場合、BLOB をエッジ ノードにコピーします。 このアクションにより、Blob ストレージ料金で指定されているように、エグレスとトランザクションの料金が発生します。 次に、Azure がエッジ ノード上のファイルにアクセスするため、それにより Azure CDN の課金が発生します。

[Azure CDN の価格のページ](https://azure.microsoft.com/pricing/details/cdn/)では、カスタム ドメイン名に対する HTTPS サポートは､Verizon Standard および Premium 製品の Azure CDN についてのみ提供されています｡

## <a name="next-steps"></a>次の手順

* [BLOB ストレージ エンドポイントのカスタム ドメイン名の構成](storage-custom-domain-name.md)
* [Azure Storage での静的な Web サイトのホスティング](storage-blob-static-website.md)
