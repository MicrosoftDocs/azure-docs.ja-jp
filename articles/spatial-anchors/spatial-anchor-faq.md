---
title: よく寄せられる質問
description: Azure Spatial Anchors サービスに関する FAQ。
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 05/18/2020
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9457836a3048ff08b228bf9d5b84400af15d6cc5
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854944"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Azure Spatial Anchors についてよく寄せられる質問

Azure Spatial Anchors はマネージド クラウド サービスの開発者プラットフォームであり、HoloLens、iOS、Android デバイスにおいてマルチユーザー対応の空間認識機能を備えた複合現実エクスペリエンスを可能にします。

詳しくは、「[Azure Spatial Anchors overview (Azure Spatial Anchors の概要)](overview.md)」をご覧ください。

## <a name="azure-spatial-anchors-product-faqs"></a>Azure Spatial Anchors 製品に関する FAQ

**Q:Azure Spatial Anchors では、どのデバイスがサポートされていますか?**

**A:** 開発者は、HoloLens、ARKit をサポートする iOS デバイス、ARCore をサポートする Android デバイスで、Azure Spatial Anchors を使用するアプリを構築できます。iOS と Android については、スマートフォンとタブレットの両方が含まれます。

**Q:Azure Spatial Anchors を使用するにはクラウドに接続する必要がありますか?**

**A:** 現在、Azure Spatial Anchors を使用するにはインターネットへのネットワーク接続が必要です。 [フィードバック サイト](https://feedback.azure.com/forums/919252-azure-spatial-anchors)でご意見をお待ちしています。

**Q:Azure Spatial Anchors の接続に関してはどのような要件がありますか?**

**A:** Azure Spatial Anchors は、Wi-Fi 接続とモバイル ブロードバンド接続で動作します。

**Q:Azure Spatial Anchors はアンカーをどれくらいの正確さで検索できますか?**

**A:** 照明条件、環境内のオブジェクト、さらにはアンカーが配置されている表面など、アンカーの位置の特定の正確さには多くの要因が影響します。 正確さがニーズを満たすかどうかを判断するには、アンカーを使用する予定の代表的な環境でアンカーを試してみてください。 正確さがニーズを満たしていない環境がある場合は、「[Logging and diagnostics in Azure Spatial Anchors (Azure Spatial Anchors でのログ記録と診断)](./concepts/logging-diagnostics.md)」をご覧ください。

**Q:アンカーの作成と検索にはどれくらいの時間がかかりますか?**

**A:** アンカーの作成と検索に必要な時間は、ネットワーク接続、デバイスの処理と負荷、特定の環境など、多くの要因に依存します。 製造、小売り、ゲームなど、多くの業界のお客様がアプリケーションを構築されており、サービスはそれらのシナリオで優れたユーザー エクスペリエンスを提供できることが示されています。

## <a name="privacy-faq"></a>プライバシーに関する FAQ

**Q:アプリケーションで空間アンカーをどこかに配置すると、すべてのアプリがそれにアクセスできるのでしょうか?**

**A:** アンカーは、Azure アカウントによって分離されています。 お使いのアカウントへのアクセスを許可されているアプリだけが、アカウント内のアンカーにアクセスできます。

**Q:Azure Spatial Anchors ではどのようにデータが格納されますか?**

**A:** すべてのデータは、Microsoft によって管理されるデータ暗号化キーを使用して暗号化され、格納されます。また、すべてのデータは、リージョンごとにリソース単位で格納されます。

**Q:Azure Spatial Anchors を使用すると、環境に関するどのような情報が送信されて、サービスに格納されますか?環境の画像は送信されて格納されますか?**

**A**: アンカーが作成または特定されるときは、デバイス上で環境の画像が処理されて派生形式になります。 この派生形式が転送されて、サービスに格納されます。

透明性を提供するため、環境の画像と派生されたまばらな点の集まりを以下に示します。 点の集まりは、サービスに転送されて格納される環境の幾何学的表現を示します。 まばらな点の集まりの各点について、その点の視覚的特徴のハッシュが送信されて格納されます。 ハッシュは任意のピクセル データから派生されますが、そのピクセル データは含みません。

Azure Spatial Anchors は、[Azure サービス契約条件](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9)および [Microsoft のプライバシーに関する声明](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409)に従います。

![環境とそれから派生したまばらな点の集まり](./media/sparse-point-cloud.png)
*図 1: 環境とそれから派生したまばらな点の集まり*

**Q:診断情報を Microsoft に送信できる方法はありますか?**

**A**: はい。 Azure Spatial Anchors には診断モードがあり、開発者は Azure Spatial Anchors API を使用してオプトインすることができます。 これは、たとえば予測どおりにアンカーを作成したり検索したりできない環境が発生した場合などに便利です。 デバッグに役立つ情報を含む診断レポートを送信することができるかどうかお客様にお尋ねする場合があります。 詳しくは、「[Logging and diagnostics in Azure Spatial Anchors (Azure Spatial Anchors でのログ記録と診断)](./concepts/logging-diagnostics.md)」をご覧ください。

## <a name="availability-and-pricing-faqs"></a>可用性と料金に関する FAQ

**Q:SLA は提供されますか?**

**A:** 標準の Azure サービスと同様に、99.9% を超える可用性を目標にしています。 

**Q:Azure Spatial Anchors を使用している自分のアプリをアプリ ストアに公開することができますですか? ミッション クリティカルな運用シナリオに Azure Spatial Anchors を使用できますか?**

**A:** はい。Azure Spatial Anchors は一般提供段階にあり、標準の Azure サービス SLA が提供されます。 お使いの運用デプロイ用にアプリを開発し、製品に関する[フィードバックを Microsoft と共有](https://feedback.azure.com/forums/919252-azure-spatial-anchors)してください。

**Q:何らかのスロットリング制限が適用されますか?**

**A**: はい、スロットリング制限があります。  一般的なアプリケーションの開発とテストでは、それに達することはないものと思われます。 運用デプロイの場合は、お客様の高スケール要件をサポートする準備を整えています。 詳しくは[お問い合わせください](mailto:azuremrs@microsoft.com)。 

**Q:Azure Spatial Anchors はどのリージョンで利用できますか?**

**A:** Azure Spatial Anchors は、現在、米国西部 2、米国東部、米国東部 2、米国中南部、西ヨーロッパ、北ヨーロッパ、英国南部、およびオーストラリア東部で利用できます。 今後、さらに多くのリージョンで利用できるようになります。

つまり、このサービスを提供するコンピューティングとストレージの両方がこれらのリージョン内にあります。 ただし、クライアントの場所に制限はありません。 

**Q:Azure Spatial Anchors には課金されますか?**

**A:** 価格の詳細については、[価格に関するページ](https://azure.microsoft.com/pricing/details/spatial-anchors/)をご覧ください。

## <a name="technical-faqs"></a>技術的な FAQ

**Q:Azure Spatial Anchors はどのように動作しますか?**

**A:** Azure Spatial Anchors は、複合現実/拡張現実トラッカーに依存しています。 これらのトラッカーはカメラで環境を認識し、空間内を移動するデバイスを 6 自由度 (6DoF) で追跡します。

6DoF トラッカーで構成される Azure Spatial Anchors では、実際の環境内で関心のある特定のポイントを、"アンカー" ポイントとして指定できます。 たとえば、アンカーを使用して、現実世界の特定の位置にコンテンツをレンダリングすることができます。

アンカーを作成すると、そのポイントの周辺の環境情報がクライアント SDK によってキャプチャされて、サービスに送信されます。 別のデバイスがその同じ空間内でアンカーを検索した場合、同様のデータがサービスに送信されます。 そのデータは、以前に格納されている環境データと照合されます。 その後、デバイスを基準としたアンカーの位置が、アプリケーションで使用できるように返送されます。

**Q:iOS や Android では、Azure Spatial Anchors は ARKit および ARCore とどのように統合されますか?**

**A:** Azure Spatial Anchors では、ARKit および ARCore のネイティブ トラッキング機能が利用されます。 さらに、Microsoft の iOS および Android 用 SDK でも、マネージド クラウド サービスでのアンカーの保持などの機能が提供され、アプリではサービスに接続するだけで、それらのアンカーを再び検索することができます。

**Q:Azure Spatial Anchors は HoloLens とどのように統合されますか?**

**A:** Azure Spatial Anchors では、HoloLens のネイティブ トラッキング機能が利用されます。 HoloLens でアプリを構築するための Azure Spatial Anchors SDK が提供されています。 SDK は HoloLens のネイティブ機能と統合されて、追加の機能を提供します。 このような機能を利用して、アプリ開発者はマネージド クラウド サービスにアンカーを保持することができ、アプリでは、サービスに接続するだけで、それらのアンカーを再び検索することができます。

**Q:Azure Spatial Anchors ではどのようなプラットフォームと言語がサポートされていますか?**

**A:** 開発者は、デバイス用の次のような使い慣れたツールとフレームワークを使用して、Azure Spatial Anchors でアプリを構築できます。

- Unity (HoloLens、iOS、Android)
- Xamarin (iOS および Android)
- Swift または Objective-C (iOS)
- Java または Android NDK (Android)
- C++/WinRT (HoloLens)

[こちらで開発](index.yml)についての概要をご覧ください。

**Q:Unreal で動作しますか?**

**A:** 今後、Unreal での対応を検討予定です。

**Q:Azure Spatial Anchors ではどのポートとプロトコルが使用されますか?**

**A:** Azure Spatial Anchors は、暗号化プロトコルを使用して TCP ポート 443 を介して通信します。 認証には、ポート 443 経由で HTTPS を使用して通信する [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) が使用されます。
