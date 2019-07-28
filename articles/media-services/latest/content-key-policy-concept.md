---
title: Media Services のコンテンツ キー ポリシー - Azure | Microsoft Docs
description: この記事では、コンテンツ キー ポリシーとは何か、および Azure Media Services でそれらを使用する方法について説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/28/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: a597ab3519f4ba1696e111622541bcab89488558
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/31/2019
ms.locfileid: "66425422"
---
# <a name="content-key-policies"></a>コンテンツ キー ポリシー

Media Services では、Advanced Encryption Standard (AES-128) または主要な 3 つのデジタル著作権管理 (DRM) システム (Microsoft PlayReady、Google Widevine、および Apple FairPlay) によって動的に暗号化されたライブまたはオンデマンドのコンテンツを配信できます。 Media Services では、承認されたクライアントに AES キーと DRM (PlayReady、Widevine、FairPlay) ライセンスを配信するためのサービスも提供しています。 

ストリームで暗号化オプションを指定するには、[ストリーミング ポリシー](streaming-policy-concept.md)を作成し、それを[ストリーミング ロケーター](streaming-locators-concept.md)に関連付ける必要があります。 コンテンツ キー ([資産](assets-concept.md)へのアクセスをセキュリティで保護する) をエンド クライアントに届ける方法を構成するには、[コンテンツ キー ポリシー](https://docs.microsoft.com/rest/api/media/contentkeypolicies)を作成します。 構成が指定されたキーをクライアントに配信するために満たす必要がある要件 (制限) をコンテンツ キー ポリシーに設定する必要があります。 コンテンツ キー ポリシーは、クリアなストリーミングまたはダウンロードには必要ありません。 

通常、**コンテンツ キー ポリシー**は、[ストリーミング ロケーター](streaming-locators-concept.md)に関連付けます。 または、[ストリーミング ポリシー](streaming-policy-concept.md)内部でコンテンツ キー ポリシーを指定できます (高度なシナリオ用にカスタム ストリーミング ポリシーを作成する場合)。 

Media Services にコンテンツ キーを自動生成させることをお勧めします。 通常、存続期間の長いキーを使用し、**Get** でポリシーの存在を確認します。 キーを取得するには、別のアクション メソッドを呼び出してシークレットまたは資格情報を取得する必要があります。次の例を参照してください。

**コンテンツ キー ポリシー**は更新可能です。 キー配信キャッシュでポリシーが更新されて、その更新されたポリシーが取得されるまでには、最大 15 分かかる場合があります。 

> [!IMPORTANT]
> * Datetime 型である**コンテンツ キー ポリシー**のプロパティは、常に UTC 形式です。
> * お使いの Media Service アカウント用にポリシーの限られたセットを設計し、同じオプションが必要な場合は常に、ストリーミング ロケーターに対して同じセットを再利用してください。 詳細については、「[クォータと制限](limits-quotas-constraints.md)」をご覧ください。

### <a name="example"></a>例

キーを取得するには、「[既存のポリシーから署名キーを取得する](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets)」の例に示すように **GetPolicyPropertiesWithSecretsAsync** を使用します。

## <a name="filtering-ordering-paging"></a>フィルター処理、順序付け、ページング

「[Media Services エンティティのフィルター処理、順序付け、ページング](entities-overview.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* [AES-128 動的暗号化とキー配信サービスの使用](protect-with-aes128.md)
* [DRM 動的暗号化とライセンス配信サービスの使用](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
