---
title: Azure Media Services の概要 | Microsoft Docs
description: Microsoft Azure Media Services は拡張可能なクラウド ベースのプラットフォームです。これにより、開発者はスケーラブルなメディア管理の構築、アプリケーションの配信を実行できます。 この記事では、Azure Media Services の概要を説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6e68e53387aa50b99bab8ed4cdba7f1e97fc48c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008236"
---
# <a name="azure-media-services-overview"></a>Azure Media Services の概要

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="使用している Media Services のバージョンを選択してください:"]
> * [Version 3](../latest/media-services-overview.md)
> * [Version 2](media-services-overview.md)

> [!NOTE]
> Media Services v2 に新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](../latest/index.yml) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-v-2-v-3-migration-introduction.md)を参照してください。

Microsoft Azure Media Services (AMS) は拡張可能なクラウド ベースのプラットフォームです。これにより、開発者はスケーラブルなメディア管理の構築、アプリケーションの配信を実行できます。 Media Services は、各種クライアント (TV、PC、モバイル デバイスなど) へのオンデマンドとライブ ストリーミングでの配信でビデオやオーディオのコンテンツの安全なアップロード、格納、エンコード、パッケージ化を可能にする REST API に基づいています。

Media Services を使いこなして、エンド ツー エンドのワークフローを構築できます。 ワークフローの一部にサード パーティのコンポーネントを使用することもできます。 たとえば、サード パーティのエンコーダーを使用してエンコードしてから、 Media Services を使用してアップロード、保護、パッケージ化、配信などを行うことができます。 コンテンツをライブ ストリーム配信したり、オンデマンドで配信したりできます。 


## <a name="compliance-privacy-and-security"></a>コンプライアンス、プライバシー、セキュリティ

重要な注意事項として、Azure Media Services を使用する際は、適用されるすべての法律に従う必要があります。また、他者の権利を侵害したり、他者に害を及ぼしたりする可能性のある方法で Media Services またはその他の Azure サービスを使用することはできません。

Media Services にビデオまたは画像をアップロードする前に、ビデオまたは画像を使用するための適切な権限をすべて取得している必要があります。これには、法律で義務づけられている場合において、ビデオまたは画像に含まれる人物から、Media Services および Azure でのデータの使用、処理、および保存について、必要なすべての同意を得ることが含まれます (このような人物が存在する場合)。 一部の法的管轄区域では、生体認証データなどの特定のカテゴリのデータの収集、オンライン処理、および保管に関して特別な法的要件が課せられる場合があります。 特別な法的要件の対象となるデータの処理と保管のために Media Services と Azure を使用する前に、適用される可能性のあるこのような法的要件に準拠していることを確認する必要があります。

Media Services のコンプライアンス、プライバシー、セキュリティについては、Microsoft [セキュリティ センター](https://www.microsoft.com/trust-center/?rtc=1)にアクセスしてください。 Microsoft のプライバシー義務、データの取り扱いと保持の慣行 (データの削除方法など) については、Microsoft の[プライバシー ステートメント](https://privacy.microsoft.com/PrivacyStatement)、[オンライン サービス条項](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST")、および[データ処理の補遺](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA") に関するページをご確認ください。 Media Services を使用することによって、OST、DPA およびプライバシー ステートメントに従うことに同意したものと見なされます。
 
## <a name="prerequisites"></a>前提条件

Azure Media Services を使用するには、次が必要です。

* Azure アカウント。 アカウントがない場合は、無料試用アカウントを数分で作成することができます。 詳細については、「[Azure の無料試用版サイト](https://azure.microsoft.com)」を参照してください。
* Azure Media Services アカウントを作成します。 詳細については、[アカウントの作成](media-services-portal-create-account.md)に関するページを参照してください。
* (省略可能) 開発環境をセットアップします。 開発環境の .NET または REST API を選択します。 詳細については、「 [環境を設定する](media-services-dotnet-how-to-use.md)」を参照してください。

    また、[プログラムによる AMS API への接続](media-services-use-aad-auth-to-access-ams-api.md)方法についても確認してください。
* 開始状態の Standard または Premium ストリーミング エンドポイント。  詳細については、[ストリーミング エンドポイントの管理](media-services-portal-manage-streaming-endpoints.md)に関するページを参照してください。

## <a name="sdks-and-tools"></a>SDK とツール

Media Services ソリューションを構築するために、以下を使用できます。

* [Media Services REST API](/rest/api/media/operations/azure-media-services-rest-api-reference)
* 使用可能な次のいずれかの SDK:
    * Azure Media Services SDK for .NET
    
        * [NuGet パッケージ](https://www.nuget.org/packages/windowsazure.mediaservices/)
        * [GitHub ソース コード](https://github.com/Azure/azure-sdk-for-media-services)
    * [Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java)
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)
    * [Azure Media Services for Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (これは、Node.js SDK の Microsoft 以外のバージョンです。 コミュニティによって管理されており、現在 AMS API を 100% カバーしてはいません。)
* 既存のツール:
    * [Azure Portal](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer (AMSE) は Windows 用の Winforms/C# アプリケーションです)

> [!NOTE]
> 最新バージョンの Java SDK を入手し、Java での開発を始めるには、「[Azure Media Services 用 Java クライアント SDK の概要](./media-services-java-how-to-use.md)」を参照してください。 <br/>
> Media Services 用の最新の PHP SDK をダウンロードするには、[Packagist リポジトリ](https://packagist.org/packages/microsoft/windowsazure#v0.5.7)でバージョン 0.5.7 の Microsoft/WindowAzure パッケージを検索してください。  

## <a name="code-samples"></a>コード サンプル

**Azure のコード サンプル** ギャラリーで、[Azure Media Services のコード サンプル](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0)を検索すると、複数のコード サンプルが見つかります。

## <a name="concepts"></a>概念

Azure Media Services の概念については、「 [概念](media-services-concepts.md)」を参照してください。

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Media Services のサポートされているシナリオとデータ センター全体における可用性

Azure の一般的なシナリオの詳細については、[AMS のシナリオ](scenarios-and-availability.md)に関するページを参照してください。
リージョン別の提供状況については、[メディア サービスの提供状況](availability-regions-v-2.md)に関するページを参照してください。

## <a name="service-level-agreement-sla"></a>サービス レベル アグリーメント (SLA)

詳細については、「 [サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)」参照してください。

## <a name="support"></a>サポート

[Azure サポート](https://azure.microsoft.com/support/options/) では、Media Services を含む Azure のオプションについてサポートを提供しています。

## <a name="provide-feedback"></a>フィードバックの提供

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
