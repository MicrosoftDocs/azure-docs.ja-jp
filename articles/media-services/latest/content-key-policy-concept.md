---
title: Media Services のコンテンツ キー ポリシー - Azure | Microsoft Docs
description: この記事では、コンテンツ キー ポリシーとは何か、および Azure Media Services でそれらを使用する方法について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: d9e86c45d535862e0c3d02b3f331bc40ebb7f6c7
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745123"
---
# <a name="content-key-policies"></a>コンテンツ キー ポリシー

Media Services では、Advanced Encryption Standard (AES-128) または主要な 3 つのデジタル著作権管理 (DRM) システム (Microsoft PlayReady、Google Widevine、および Apple FairPlay) によって動的に暗号化されたライブまたはオンデマンドのコンテンツを配信できます。 Media Services では、承認されたクライアントに AES キーと DRM (PlayReady、Widevine、FairPlay) ライセンスを配信するためのサービスも提供しています。

ストリームで暗号化オプションを指定するには、[コンテンツ キー ポリシー](https://docs.microsoft.com/rest/api/media/contentkeypolicies)を作成し、それを**ストリーミング ロケーター**に関連付ける必要があります。 **コンテンツ キー ポリシー**によって、Media Services の Key Delivery コンポーネントを介してコンテンツ キーがエンド クライアントに配信される方法が構成されます。 Media Services でコンテンツ キーを自動生成させることができます。 通常、存続期間の長いキーを使用して、Get でポリシーの存在を確認します。 キーを取得するには、別のアクション メソッドを呼び出してシークレットまたは資格情報を取得する必要があります。次の例を参照してください。

**コンテンツ キー ポリシー**は更新可能です。 たとえば、キーのローテーションを行う必要がある場合には、ポリシーを更新できます。 既存のポリシーにあるプライマリ検証キーと代替検証キーのリストを更新できます。 キー配信キャッシュでポリシーが更新されて、その更新されたポリシーが取得されるまでには、最大 15 分かかる場合があります。 

> [!IMPORTANT]
> * Datetime 型である**コンテンツ キー ポリシー**のプロパティは、常に UTC 形式です。
> * お使いの Media Service アカウント用にポリシーの限られたセットを設計し、同じオプションが必要な場合は常に、ストリーミング ロケーターに対して同じセットを再利用してください。 

## <a name="example"></a>例

キーを取得するには、次の例に示すように **GetPolicyPropertiesWithSecretsAsync** を使用します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="filtering-ordering-paging"></a>フィルター処理、順序付け、ページング

「[Media Services エンティティのフィルター処理、順序付け、ページング](entities-overview.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* [AES-128 動的暗号化とキー配信サービスの使用](protect-with-aes128.md)
* [DRM 動的暗号化とライセンス配信サービスの使用](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
