---
title: Media Services のコンテンツ キー ポリシー - Azure | Microsoft Docs
description: この記事では、コンテンツ キー ポリシーとは何か、および Azure Media Services でそれらを使用する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: dd19d0730b25685b008af16d1c1eac6537750612
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297271"
---
# <a name="content-key-policies"></a>コンテンツ キー ポリシー

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services では、Advanced Encryption Standard (AES-128) または主要な 3 つのデジタル著作権管理 (DRM) システム コンテンツを配信できます。 Media Services では、承認されたクライアントに AES キーと DRM (PlayReady、Widevine、FairPlay) ライセンスを配信するためのサービスも提供しています。 

ストリームで暗号化オプションを指定するには、[ストリーミング ポリシー](streaming-policy-concept.md)を作成し、それを[ストリーミング ロケーター](streaming-locators-concept.md)に関連付ける必要があります。 コンテンツ キー ([資産](assets-concept.md)へのアクセスをセキュリティで保護する) をエンド クライアントに届ける方法を構成するには、[コンテンツ キー ポリシー](/rest/api/media/contentkeypolicies)を作成します。 構成が指定されたキーをクライアントに配信するために満たす必要がある要件 (制限) をコンテンツ キー ポリシーに設定する必要があります。 コンテンツ キー ポリシーは、クリアなストリーミングまたはダウンロードには必要ありません。 

通常、コンテンツ キー ポリシーは、[ストリーミング ロケーター](streaming-locators-concept.md)に関連付けます。 または、[ストリーミング ポリシー](streaming-policy-concept.md)内部でコンテンツ キー ポリシーを指定できます (高度なシナリオ用にカスタム ストリーミング ポリシーを作成する場合)。 

## <a name="best-practices-and-considerations"></a>ベスト プラクティスと考慮事項

> [!IMPORTANT]
> 次の推奨事項を確認してください。

* お使いの Media Service アカウント用にポリシーの限られたセットを設計し、同じオプションが必要な場合は常に、ストリーミング ロケーターに対して同じセットを再利用してください。 詳しくは、[クォータと制限](limits-quotas-constraints.md)に関するトピックを参照してください。
* コンテンツ キー ポリシーは更新可能です。 キー配信キャッシュでポリシーが更新されて、その更新されたポリシーが取得されるまでには、最大 15 分かかる場合があります。 

   ポリシーを更新することで、既存の CDN キャッシュが上書きされ、キャッシュされたコンテンツを使用している顧客に対して、再生の問題が発生する可能性があります。  
* 資産ごとに新しいコンテンツ キー ポリシーを作成しないことをお勧めします。 同じポリシー オプションを必要とする資産間で同じコンテンツ キー ポリシーを共有することの主な利点は次のとおりです。
   
   * 少数のポリシーを簡単に管理できます。
   * コンテンツ キー ポリシーを更新する必要がある場合、変更はほとんどすぐにすべての新しいライセンス要求に反映されます。
* 新しいポリシーを作成する必要がある場合は、資産に対して新しいストリーミング ロケーターを作成する必要があります。
* Media Services にコンテンツ キーを自動生成させることをお勧めします。 

   通常、存続期間の長いキーを使用し、[Get](/rest/api/media/contentkeypolicies/get) でコンテンツ キー ポリシーの存在を確認します。 キーを取得するには、別のアクション メソッドを呼び出してシークレットまたは資格情報を取得する必要があります。次の例を参照してください。

## <a name="example"></a>例

キーを取得するには、「[既存のポリシーから署名キーを取得する](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets)」の例で示すされているように `GetPolicyPropertiesWithSecretsAsync` を使用します。

## <a name="filtering-ordering-paging"></a>フィルター処理、順序付け、ページング

「[Media Services エンティティのフィルター処理、順序付け、ページング](entities-overview.md)」を参照してください。

## <a name="additional-notes"></a>その他のメモ

* `Datetime` 型であるコンテンツ キー ポリシーのプロパティは、常に UTC 形式です。
* Widevine は Google Inc. によって提供されるサービスであり、Google Inc. の利用規約とプライバシー ポリシーが適用されます。

## <a name="next-steps"></a>次のステップ

* [AES-128 動的暗号化とキー配信サービスの使用](protect-with-aes128.md)
* [DRM 動的暗号化とライセンス配信サービスの使用](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
