---
title: Cognitive Services コンテナーについてよくあるご質問 (FAQ)
titleSuffix: Azure Cognitive Services
description: よくあるご質問と回答。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: bf30fc5e6ccfc0f59c1769245e58177428472156
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701812"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Azure Cognitive Services コンテナーについてよくあるご質問 (FAQ)

## <a name="general-questions"></a>一般的な質問

**Q:何を利用できますか。**

**A:** Azure Cognitive Services コンテナーを使用すると、開発者は Azure で利用できるものと同じインテリジェントな API を使用できますが、それに加えてコンテナー化の[利点](../cognitive-services-container-support.md#features-and-benefits)があります。 一部のコンテナーは、ゲート プレビューとして使用できます。この場合、アプリケーションへのアクセスが必要になることがあります。 他のコンテナーは、ゲートなしプレビューとして公開されているか、一般提供されています。 コンテナーの詳細な一覧と使用可能性については、「[Azure Cognitive Services でのコンテナーの使用可能性](../cognitive-services-container-support.md#container-availability-in-azure-cognitive-services)」を参照してください。 

**Q:Cognitive Services のクラウドとコンテナーには違いがありますか。**

**A:** Cognitive Services コンテナーは Cognitive Services クラウドに代わるものです。 コンテナーからは、それに対応するクラウド サービスと同じ機能が提供されます。 お客様はオンプレミスまたは Azure でコンテナーをデプロイできます。 中心となる AI テクノロジ、価格レベル、API キー、API 署名は、コンテナーとそれに対応するクラウドサービスの間で同じです。 それに対応するクラウド サービスではなく、コンテナーを選択すると、次のような[機能と利点](../cognitive-services-container-support.md#features-and-benefits)が与えられます。

**Q:コンテナーはすべての Cognitive Services で利用できますか。また、次に期待できるコンテナー セットは何ですか。**

**A:** Microsoft では、コンテナー サービスとして利用できる Cognitive Services を増やしたいと考えております。 新しいコンテナーのリリースに関する最新情報や Cognitive Services 関連のその他の告知については、お住まいの地域の Microsoft アカウント マネージャーにお問い合わせください。

**Q:Cognitive Services コンテナーのサービス レベル アグリーメント (SLA) はどのようなものになりますか。**

**A:** Cognitive Services コンテナーには SLA がありません。

Cognitive Services コンテナーのリソース構成はお客様が管理します。そのため、Microsoft は一般公開 (GA) のための SLA は提供しません。 お客様はオンプレミスでコンテナーを自由にデプロイできます。そのため、ホスト環境はお客様が定義します。

> [!IMPORTANT]
> Cognitive Services サービス レベル アグリーメントの詳細については、[Microsoft の SLA ページ](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)にアクセスしてください。

**Q:これらのコンテナーはソブリン クラウドで利用できますか。**

**A:** "ソブリン クラウド" という用語になじみのない人もいるので、その定義から始めましょう。

> "ソブリン クラウド" は [Azure Government](../../azure-government/documentation-government-welcome.md) クラウド、[Azure Germany](../../germany/germany-welcome.md) クラウド、[Azure China 21Vianet](https://docs.microsoft.com/azure/china/overview-operations) クラウドからなります。

残念ながら、Cognitive Services コンテナーはソブリン クラウドでネイティブ サポートされて*いません*。 それらのクラウドでコンテナーを実行することはできますが、パブリック クラウドからプルされます。パブリック エンドポイントに使用状況データを送信する必要があります。

### <a name="versioning"></a>バージョン管理

**Q:コンテナーはどのような方法で最新バージョンに更新されますか。**

**A:** お客様は、自分がデプロイしたコンテナーを更新するタイミングを選択できます。 最新のバージョンを示すために、コンテナーには `latest` のような標準 [Docker タグ](https://docs.docker.com/engine/reference/commandline/tag/)のマークが付けられます。 お客様にはコンテナーの最新版がリリースされたタイミングでプルすることをお勧めしています。画像が更新されたときに通知を受ける方法については、[Azure Container Registry Webhook](../../container-registry/container-registry-webhook.md) をご覧ください。
 
**Q:サポートされるバージョンは何ですか。**

**A:** コンテナーの現行の最新メジャー バージョンがサポートされます。 お客様には、常に最新のテクノロジを入手することをお勧めしています。
 
**Q:更新プログラムはどのようにバージョン管理されますか?**

**A:** メジャー バージョンの変更は、API 署名に破壊的変更があることを示します。 これは通常、それに対応する Cognitive Service クラウド サービスのメジャー バージョンの変更に一致するものと、Microsoft では予測しています。 マイナー バージョンの変更は、バグ修正、モデル更新、または API 署名に破壊的変更を加えない新機能を示します。

## <a name="technical-questions"></a>技術的なご質問

**Q:Cognitive Services コンテナーを IoT デバイスで実行するにはどうすればよいでしょうか?**

**A:** 信頼できるインターネット接続がありませんか。それとも、帯域幅コストで節約したいですか。 あるいは、短い待ち時間が求められていますか。オンサイトで分析しなければならない機密データを扱っていますか。[Azure IoT Edge と Cognitive Services コンテナー](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/)を使用すれば、クラウドによる一貫性が与えられます。

**Q:これらのコンテナーは OpenShift と互換性がありますか。** 

OpenShift を使用してコンテナーをテストすることはありませんが、通常、Cognitive Services コンテナーは Docker イメージをサポートするすべてのプラットフォームで実行できるはずです。 OpenShift を使用している場合は、`root-user` としてコンテナーを実行することをお勧めします。

**Q:製品フィードバックや機能推奨はどのような方法で提供しますか?**

**A:** お客様には公の場で[懸念を表明する](https://cognitive.uservoice.com/)ことと、同じ問題を投稿した他のユーザーに賛成票を投じることをお勧めしています。 User Voice ツールは、製品フィードバックと機能推奨の両方にご利用いただけます。

**Q:サポートが必要なとき、どこに連絡すればよいですか?**

**A:** カスタマー サポート チャネルは、Cognitive Services クラウド サービスと同じです。 すべての Cognitive Services コンテナーにログ記録機能があります。Microsoft やコミュニティがお客様をサポートする際、その機能が役に立ちます。 その他のサポートについては、次の選択肢をご覧ください。

### <a name="customer-support-plan"></a>カスタマー サポート プラン

お客様はご利用の [Azure サポート プラン](https://azure.microsoft.com/support/plans/)で連絡先をご確認ください。

### <a name="azure-knowledge-center"></a>Azure Knowledge Center

疑問に答え、問題解決に役立てるための [Azure Knowledge Center](https://azure.microsoft.com/resources/knowledge-center/) をお客様は自由に閲覧できます。

### <a name="stack-overflow"></a>Stack Overflow

> [スタック オーバーフロー](https://en.wikipedia.org/wiki/Stack_Overflow)は、プログラミングのプロフェッショナルや愛好家のための質疑応答サイトです。

次のタグを調べることで、自分が必要とする質問/回答を得られることがあります。

* [Azure Cognitive Services](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft Cognitive](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**Q:請求のしくみを教えてください。**

**A:** Cognitive Services クラウドと同様に、お客様の使用量に基づいて料金が請求されます。 コンテナーは、Azure に測定データを送信するように構成する必要があります。構成後、トランザクションに対して適宜課金されます。 ホステッド サービスとオンプレミス サービスの間で使用されるリソースは、料金が階層化された単一クォータに追加されます。集計は両方の使用量に対して行われます。 詳細については、対応するサービスの料金ページを参照してください。

* [Anomaly Detector][ad-containers-billing]
* [Computer Vision][cv-containers-billing]
* [Face][fa-containers-billing]
* [Form Recognizer][fr-containers-billing]
* [Language Understanding (LUIS)][lu-containers-billing]
* [Speech Service API][sp-containers-billing]
* [Text Analytics][ta-containers-billing]

> [!IMPORTANT]
> Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 Cognitive Services のコンテナーから Microsoft に顧客データが送信されることはありません。
 
**Q:コンテナーの現在のサポート保証はどのようになっていますか?**

**A:** プレビューには保証がありません。 Microsoft が企業向けソフトウェアに与える標準保証は、コンテナーが一般公開 (GA) として正式に告知されたときに適用されます。
 
**Q:インターネット接続が失われると、Cognitive Services コンテナーはどうなりますか?**

**A:** Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行の*ライセンスが許可されません*。 お客様は、コンテナーが計測サービスと常時通信するようにする必要があります。

**Q:Azure に接続されていない状態でどのくらいの時間、コンテナーを運用できますか?**

**A:** Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行の*ライセンスが許可されません*。 お客様は、コンテナーが計測サービスと常時通信するようにする必要があります。
 
**Q:これらのコンテナーを実行するために現在必要なハードウェアは何ですか?**

**A:** Cognitive Services コンテナーは x64 ベースのコンテナーであり、x64 Linux Docker コンテナーに対応している、互換性があるあらゆる Linux ノード、VM、エッジ デバイスで実行できます。 それらすべてで CPU プロセッサを必要とします。 コンテナー サービス別の最小構成と推奨構成は以下のようになります。

* [Anomaly Detector][ad-containers-recommendations]
* [Computer Vision][cv-containers-recommendations]
* [Face][fa-containers-recommendations]
* [Form Recognizer][fr-containers-recommendations]
* [Language Understanding (LUIS)][lu-containers-recommendations]
* [Speech Service API][sp-containers-recommendations]
* [Text Analytics][ta-containers-recommendations]
 
**Q:これらのコンテナーは Windows で現在サポートされていますか?**

**A:** Cognitive Services コンテナーは Linux コンテナーですが、Windows で Linux コンテナーが一部サポートされています。 Windows で Linux コンテナーを実行する方法については、[Docker ドキュメント](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/)を参照してください。
 
**Q:コンテナーはどのような方法で見つけますか?**

**A:** Cognitive Services コンテナーは、Azure portal、Docker ハブ、Azure コンテナー レジストリなど、さまざまな場所で利用できます。 コンテナーの場所の最新情報については、「[コンテナーのリポジトリとイメージ](../cognitive-services-container-support.md#container-repositories-and-images)」を参照してください。

**Q:Cognitive Services コンテナーは、AWS や Google のサービスと比べてどうですか?**

**A:** Microsoft は、事前にトレーニングした AI モデルをコンテナーに導入した最初のクラウド プロバイダーです。トランザクション単位の簡単な請求機能を採用し、まるで 1 つのクラウド サービスを利用しているような印象をお客様に与えます。 Microsoft は、ハイブリッド クラウドはお客様に与える選択肢を増やすものと考えています。

**Q:コンテナーにはどのようなコンプライアンス認定がありますか?**

**A:** Cognitive Services コンテナーにはコンプライアンス認定がありません。

**Q:Cognitive Services コンテナーはどのリージョンで利用できますか?**

**A:** コンテナーはあらゆるリージョンのあらゆる場所で実行できますが、キーを必要とし、また、測定のために Azure にコールバックする必要があります。 クラウド サービスでサポートされているリージョンはすべて、コンテナーの測定呼び出しでサポートされています。

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations
