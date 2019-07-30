---
title: Azure Spatial Anchors についてよく寄せられる質問 | Microsoft Docs
description: Azure Spatial Anchors はマネージド クラウド サービスの開発者プラットフォームであり、HoloLens、iOS、Android デバイスにおいてクロスデバイスでマルチユーザーの複合現実エクスペリエンスを可能にします。 これらの FAQ では、サービスに関する技術的な観点からの質問にお答えします。
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 435ad986a9f0b96d42c88d450f40e5900ad33b7c
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653246"
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

**A:** 標準の Azure サービスと同様に、99.9% を超える可用性を目標にしています。 Azure Spatial Anchors は現在プレビュー段階にあるため、「[プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」が適用されることに注意してください。

**Q:Azure Spatial Anchors を使用している自分のアプリをアプリ ストアに公開することができますですか? ミッション クリティカルな運用シナリオに Azure Spatial Anchors を使用できますか?**

**A:** Azure Spatial Anchors は現在プレビュー段階であり、この期間中は、開発者の皆さんに、アプリの開発、製品についての[フィードバック](https://feedback.azure.com/forums/919252-azure-spatial-anchors)、および運用デプロイの計画をお勧めしています。

一般提供 (GA) の期日はまもなく発表されます。

**Q:何らかのスロットリング制限が適用されますか?**

**A**: はい、スロットリング制限があります。  一般的なアプリケーションの開発とテストでは、それに達することはないものと思われます。 運用デプロイの場合は、お客様の高スケール要件をサポートする準備を整えています。 詳しくは[お問い合わせください](mailto:azuremrs@microsoft.com)。 このプレビュー フェーズでは、レベル設定と価格体系をまだ公開していませんが、まもなく公開する予定です。

**Q:Azure Spatial Anchors はどのリージョンで利用できますか?**

**A:** 現在、Azure Spatial Anchors アカウントを作成できるのは米国東部 2 リージョンです。 つまり、このサービスのコンピューティングとストレージの能力はどちらもこのリージョン内にあります。 ただし、クライアントの場所に制限はありません。 今後は、すべてのプライマリ Azure リージョンでサービスを利用できるように拡張する予定です。

**Q:Azure Spatial Anchors には課金されますか? いつ課金されるようになりますか?**

**A:** プレビュー期間中の料金について詳しくは、[価格に関するページ](https://azure.microsoft.com/pricing/details/spatial-anchors/)をご覧ください。

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
- Swift または Objective-C (iOS)
- Java または Android NDK (Android)
- C++/WinRT (HoloLens)

[こちらで開発](index.yml)についての概要をご覧ください。

**Q:Unreal で動作しますか?**

**A:** 今後、Unreal での対応を検討予定です。

**Q:Xamarin で動作しますか?**

**A:** 今後、Xamarin での対応を検討予定です。

**Q:Azure Spatial Anchors ではどのポートとプロトコルが使用されますか?**

**A:** Azure Spatial Anchors は、暗号化プロトコルを使用して TCP ポート 443 を介して通信します。 認証には、ポート 443 経由で HTTPS を使用して通信する [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) が使用されます。
