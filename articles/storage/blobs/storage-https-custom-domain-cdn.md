---
title: "カスタム ドメインを用いた BLOB にAzure CDN から HTTPS 経由でアクセスする"
description: "Azure CDN と BLOB ストレージを統合することで､カスタム ドメインを用いた BLOB に HTTPS 経由でアクセスする方法を学びます｡"
services: storage
documentationcenter: 
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mihauss
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 6bad04df324a374f6e8473890345cf516322abd6
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="using-the-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>カスタム ドメインを用いた BLOB にAzure CDN から HTTPS 経由でアクセスする

Azure CDN (Content Delivery Network) が､HTTPS でカスタム ドメイン名をサポートするようになりました｡
この機能を利用することで､カスタム ドメインを用いたストレージ BLOB に HTTPS 経由でアクセスできます｡ このためには､先ず､BLOB のエンドポイントで Azure CDN を有効にし､CDN をカスタム ドメイン名にマッピングする必要があります｡ これら措置をとることで､カスタム ドメインに対する HTTPS の有効化が簡略化されます｡ワンクリックでの有効化や完全な証明書管理などのすべてが､通常の CDN 価格に対する追加コストなしで実現されます｡

この機能は重要です｡この機能により､機密性の高い Web アプリケーションデータのプライバシーとデータ整合性を転送中も保護することができます｡ SSL プロトコルを使った HTTPS 経由での通信により､インターネット送信時に確実にデータが暗号化されるようになります｡ HTTPS は信頼と認証を提供し、Web アプリケーションを攻撃から保護します。

> [!NOTE]
> カスタム ドメイン名に対する SSL サポートに加え､Azure CDN はアプリケーションをスケーリングして世界中に高帯域幅のコンテンツを配信するのにも役立てることができます｡
> 詳細は､[Overview of the Azure CDN](../../cdn/cdn-overview.md) でご確認ください｡
>
>

## <a name="quick-start"></a>クイック スタート

ここでは､カスタム BLOB ストレージエンドポイントに対して HTTPS を有効にする手順を説明します｡

1.  [Azure ストレージ アカウントと Azure CDN を統合します](../../cdn/cdn-create-a-storage-account-with-cdn.md)｡
    この記事では､Azure Portal でストレージ アカウントを作成する手順を順を追って説明しています｡まだ､この手順を行っていない場合は､この手順で行ってください｡
2.  [Azure CDN コンテンツをカスタム ドメインにマッピングします](../../cdn/cdn-map-content-to-custom-domain.md)｡
3.  [Azure CDN カスタム ドメインで HTTPS を有効にします](../../cdn/cdn-custom-ssl.md)｡

## <a name="shared-access-signatures"></a>Shared Access Signature

匿名の読み取りアクセスを許可しないよう BLOB ストレージのエンドポイントを設定している場合は､カスタム ドメインに対する要求の 1 つ 1 つで [Shared Access Signature (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) トークンを提供する必要があります｡ 既定では､BLOB ストレージ エンドポイントは匿名の読み取りアクセスを許可しません｡ Shared Access Signatures (SAS) についての詳細は､｢[Managing anonymous read access to containers and blobs](storage-manage-access-to-resources.md)｣を参照してください｡

Azure CDN は､SAS トークンに追加されたいかなる制限事項に対応していません｡ たとえば､すべての SAS トークンには有効期限があります｡ このことは､CDN のエッジノードからコンテンツが削除されるまで､有効期限切れの SAS を使ってコンテンツにアクセスし続けられることを意味します｡ CDN 上でデータがキャッシュされる期間は､キャッシュ応答ヘッダーを設定することで制御できます｡ この方法については､｢[Managing expiration of Azure Storage blobs in Azure CDN](../../cdn/cdn-manage-expiration-of-blob-content.md)｣を参照してください｡

1 つの BLOB エンドポイントに対して複数の SAS URL を作成する場合は､Azure CDN に対してクエリ文字列のキャッシュを有効にすることをお勧めします｡ そうすることで､それら URL が個別の一意のエンティティとして処理されるようになります｡ 詳細は、「[クエリ文字列による Azure CDN キャッシュ動作の制御](../../cdn/cdn-query-string.md)」を参照してください。

## <a name="http-to-https-redirection"></a>HTTP から HTTPS へのリダイレクト

HTTP 通信を HTTPS にリダイレクトするよう設定することができます｡ このためには､Verizon の Azure CDN Premium を使用する必要があります｡ 次のルールに従って､[Azure CDN ルールエンジンを使った HTTP 動作をオーバーライドする](../../cdn/cdn-rules-engine.md)必要があります｡

![](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

｢Cdn-endpoint-name｣は､CDN エンドポイントに設定した名前を表します この値は、ドロップダウン リストから選択できます。 ｢Origin-path｣は､静的コンテンツが存在する配信元ストレージ コンテンツ内のパスを表します｡
静的コンテンツを 1 つのコンテナーでホストしている場合は､｢Origin-path｣の部分をそのコンテナーの名前に置き換えください｡

ルールについての詳細は､｢[Azure CDN ルール エンジンの機能](../../cdn/cdn-rules-engine-reference-features.md)｣を参照してください｡

## <a name="pricing-and-billing"></a>価格と課金

Azure CDN からの BLOB アクセスでは､各エッジ ノードと配信元 (BLOB ストレージ) 間の通信に対する [BLOB ストレージ料金](https://azure.microsoft.com/pricing/details/storage/blobs/)と､エッジノードからアクセスするデータに対する[ CDN 料金](https://azure.microsoft.com/pricing/details/cdn/)がかかります｡

たとえば､米国西部に Azure CDN からアクセスするストレージアカウントをお持ちであると仮定しましょう｡ イギリスにいる従業員が､CDN からストレージ アカウント内の BLOB の 1 つにアクセスしようとした場合､Azure は先ず､イギリスに最も近いエッジ ノードにその BLOB があるか調べます｡ 見つかった場合は､その BLOB のコピーにアクセスします｡これは CDN からのアクセスですから､CDN 価格になります｡ 見つからなかった場合は､エッジ ノードに BLOB をコピーし(BLOB ストレージ価格の規定に従って egress 料金とトランザクション料金が発生)､ そのエッジ ノード上のファイルにアクセスします (CDN 課金が発生)｡

[CDN 価格のページ](https://azure.microsoft.com/pricing/details/cdn/)を参照したときは､カスタム ドメイン名に対する HTTPS サポートが､Verizon 製品 (Standard と Premium) の Azure CDN についてのみ提供されていることに注意してください｡

## <a name="next-steps"></a>次のステップ

[BLOB ストレージ エンドポイントのカスタム ドメイン名の構成](storage-custom-domain-name.md)

