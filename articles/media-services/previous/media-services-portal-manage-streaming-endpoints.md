---
title: Azure Portal を使用したストリーミング エンドポイントの管理 | Microsoft Docs
description: この記事では、Azure portal を使ってストリーミング エンドポイントを管理する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: bda244267e81d959abbc4ae0a8532ddce68d5726
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017394"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Azure ポータルを使用したストリーミング エンドポイントの管理

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

この記事では、Azure Portal を使用してストリーミング エンドポイントを管理する方法について説明します。 

>[!NOTE]
>必ず、[概要](media-services-streaming-endpoints-overview.md)記事をお読みください。 

ストリーミング エンドポイントのスケールを設定する方法については、[こちら](media-services-portal-scale-streaming-endpoints.md)の記事をご覧ください。

## <a name="start-managing-streaming-endpoints"></a>ストリーミング エンドポイントの管理を開始する 

アカウントのストリーミング エンドポイントの管理を開始するには、次の操作を行います。

1. [Azure Portal](https://portal.azure.com/) で Azure Media Services アカウントを選択します。
2. **[設定]** ブレードで **[ストリーミング エンドポイント]** を選択します。
   
    ![[Media services] ページを示すスクリーンショット。[設定] ブレードで [ストリーミング エンドポイント] が選択されています。](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> ストリーミング エンドポイントが実行状態の場合のみ課金されます。

## <a name="adddelete-a-streaming-endpoint"></a>ストリーミング エンドポイントの追加/削除

>[!NOTE]
>既定のストリーミング エンドポイントは削除できません。

Azure ポータルを使用してストリーミング エンドポイントを追加または削除するには、次の操作を行います。

1. ストリーミング エンドポイントを追加するには、ページの上部にある **[+ エンドポイント]** をクリックします。 

    複数の CDN を使用する場合や、CDN と直接アクセスを使用する場合には、複数のストリーミング エンドポイントが必要になる可能性があります。

2. ストリーミング エンドポイントを削除するには、 **[削除]** ボタンをクリックします。      
3. **[開始]** をクリックしてストリーミング エンドポイントを開始します。
   
    ![[エンドポイント] アクションが選択され、[ストリーミング エンドポイントの詳細] ウィンドウが表示されていることを示すスクリーンショット。](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a name="configuring-the-streaming-endpoint"></a><a id="configure_streaming_endpoints"></a>ストリーミング エンドポイントの構成
ストリーミング エンドポイントでは、次のプロパティを構成できます。

* アクセス制御
* キャッシュ制御
* Cross site access policies

これらのプロパティの詳細については、「 [StreamingEndpoint](/rest/api/media/operations/streamingendpoint)」をご覧ください。

>[!NOTE]
>CDN が有効になっている場合、IP アクセスにアクセスすることはできません。 IP アクセスは、CDN を指定していない場合にのみ適用されます。

ストリーミング エンドポイントを構成するには、以下の操作を行います。

1. 構成するストリーミング エンドポイントを選択します。
2. **[設定]** をクリックします。

以下で、その各フィールドについて簡単に説明します。

![ストリーミング エンドポイントに対して [設定] アクション選択されていることを示すスクリーンショット。](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. [Maximum cache policy (最大キャッシュ ポリシー)]\: このストリーミング エンドポイントから提供されるアセットのキャッシュの有効期間を構成するために使用します。 値を設定しない場合は、既定値が使用されます。 既定値は Azure ストレージで直接定義することもできます。 ストリーミング エンドポイントで Azure CDN を有効にする場合、キャッシュ ポリシーの値は 600 秒以上に設定してください。  
2. [Allowed IP addresses \(使用できる IP アドレス)]: 公開されているストリーミング エンドポイントへの接続を許可する IP アドレスを指定するために使用します。 IP アドレスが指定されていない場合、どの IP アドレスでも接続できます。 IP アドレスは、1 つの IP アドレス (例: ‘10.0.0.1’)、IP アドレスと CIDR サブネット マスクを使用した IP 範囲 (例: ‘10.0.0.1/22’)、IP アドレスとピリオドで区切られた 10 進数のサブネット マスクを使用した IP 範囲 (例: ‘10.0.0.1(255.255.255.0)’) のいずれかの形で指定できます。
3. [Configuration for Akamai signature header authentication (Akamai 署名ヘッダー認証の構成)\]\: Akamai サーバーからの署名ヘッダー認証要求を構成する方法 を指定するために使用します。 有効期限は UTC 時間で指定します。

## <a name="scale-your-premium-streaming-endpoint"></a>Premium ストリーミング エンドポイントを拡張する

詳細については、[こちらの記事](media-services-portal-scale-streaming-endpoints.md)を参照してください。

## <a name="enable-azure-cdn-integration"></a><a id="enable_cdn"></a>Azure CDN 統合を有効にする

新しいアカウントを作成すると、既定のストリーミング エンドポイント Azure CDN 統合が既定で有効になります。

後で CDN を有効/無効にする場合は、ストリーミング エンドポイントを **停止** 状態にする必要があります。 Azure CDN 統合が有効になり、すべての CDN POP で変更がアクティブになるまでに、最大で 2 時間かかる場合があります。 ただし、ストリーミング エンドポイントの起動は可能で、ストリーミング エンドポイントから中断なくストリーミングを行うことができます。統合が完了すると、CDN からストリーミングが提供されます。 プロビジョニング期間中はストリーミング エンドポイントが **開始中** の状態になり、パフォーマンスが低下します。

CDN 統合は、中国および連邦政府地域を除くすべての Azure データ センターで有効になります。

これが有効になると、**Access Control**、**カスタム ホスト名、および **Akamai 署名認証** 構成は無効になります。
 
> [!IMPORTANT]
> Azure CDN との Azure Media Services 統合は、Standard ストリーミング エンドポイント用の **Azure CDN from Verizon** で実装されます。 Premium ストリーミング エンドポイントは、すべての **Azure CDN 価格レベルとプロバイダー** を使用して構成できます。 Azure CDN 機能の詳細については、「 [CDN の概要](../../cdn/cdn-overview.md)」を参照してください。
 
### <a name="additional-considerations"></a>その他の注意点

* CDN がストリーミング エンドポイントで有効になっている場合、クライアントは配信元から直接コンテンツを要求することはできません。 CDN の有無にかかわらずコンテンツをテストする必要がある場合は、CDN が有効になっていない別のストリーミング エンドポイントを作成できます。
* ストリーミング エンドポイントのホスト名は、CDN を有効にした後も変化しません。 CDN を有効にした後、Media Services のワークフローを変更する必要はありません。 たとえば、ストリーミング エンドポイントのホスト名が strasbourg.streaming.mediaservices.windows.net の場合、CDN を有効にした後も同じホスト名が使用されます。
* 新しいストリーミング エンドポイントの場合は、新しいエンドポイントを作成するだけで CDN を有効にできます。既存のストリーミング エンドポイントの場合は、最初にエンドポイントを停止してから CDN を有効/無効にする必要があります。
* Standard ストリーミング エンドポイントは、Azure クラシック ポータルを使用して、**Verizon Standard CDN プロバイダー** でのみ構成できます。 ただし、REST API を使用して、他の Azure CDN プロバイダーを有効にすることは可能です。

## <a name="configure-cdn-profile"></a>CDN プロファイルを構成する

CDN プロファイルは、上部にある **[CDN の管理]** ボタンを選択することで構成できます。

![ストリーミング エンドポイント](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
