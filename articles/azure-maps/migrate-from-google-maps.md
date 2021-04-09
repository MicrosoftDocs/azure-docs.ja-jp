---
title: チュートリアル - Google Maps から Azure Maps に移行する | Microsoft Azure Maps
description: Google Maps から Microsoft Azure Maps に移行する方法に関するチュートリアルです。 ガイダンスでは、Azure Maps の API と SDK への切り替え方法について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6241f6156b01c3c90f00578ae5416e4e77270930
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386800"
---
# <a name="tutorial-migrate-from-google-maps-to-azure-maps"></a>チュートリアル:Google マップから Azure Maps に移行する

この記事では、Web アプリケーション、モバイル アプリケーション、サーバーベース アプリケーションを Google Maps から Microsoft Azure Maps プラットフォームに移行する方法についての分析情報を提供します。 このチュートリアルには、Azure Maps に移行するための比較コード サンプル、移行に関する提案、ベスト プラクティスが含まれています。 このチュートリアルでは、次の事項について説明します。

> [!div class="checklist"]
> * Azure Maps で使用できる機能と同等の Google Maps の機能との大まかな比較。
> * 考慮すべきライセンスの相違点。
> * 移行を計画する方法。
> * テクニカル リソースとサポートの場所

## <a name="prerequisites"></a>前提条件

1. [Azure portal](https://portal.azure.com) にサインインします。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
2. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
3. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 Azure Maps での認証の詳細については、「[Azure Maps での認証の管理](how-to-manage-authentication.md)」を参照してください。

## <a name="azure-maps-platform-overview"></a>Azure Maps プラットフォームの概要

Azure Maps は、あらゆる業界の開発者に強力な地理空間機能を提供します。 それらの機能には、Web およびモバイル アプリケーションに地理的コンテキストを提供するために、定期的に更新されるマップ データが搭載されています。 Azure Maps は、Azure One API に準拠した REST API のセットです。 REST API は、Maps のレンダリング、検索、ルート指定、トラフィック、タイム ゾーン、位置情報、ジオフェンシング、マップ データ、天気、モビリティ、空間演算を提供します。 演算は、Web と Android の両方の SDK によって実現されているため、開発が簡単になり、柔軟性が高く、複数のプラットフォーム間で移植性が確保されます。

## <a name="high-level-platform-comparison"></a>プラットフォームのおおまかな比較

次の表は、Google マップの機能とそれに対応する Azure Maps の機能の大まかな一覧です。 この一覧に Azure Maps のすべての機能が網羅されているわけではありません。 Azure Maps のその他の機能には、アクセシビリティ、ジオフェンシング、等時線、空間演算、直接マップ タイル アクセス、バッチ サービス、データ カバレッジの比較 (画像のカバレッジ) などがあります。

| Google Maps 機能         | Azure Maps のサポート                     |
|-----------------------------|:--------------------------------------:|
| Web SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | 対応予定                                |
| REST サービス API           | ✓                                      |
| Directions (ルーティング)        | ✓                                      |
| Distance Matrix             | ✓                                      |
| Elevation                   | ✓ (プレビュー)                            |
| Geocoding (Forward/Reverse) | ✓                                      |
| 地理的位置情報                 | 該当なし                                    |
| Nearest Roads               | ✓                                      |
| Places Search               | ✓                                      |
| Places Details              | 該当なし – Web サイトと電話番号を利用可能 |
| Places Photos               | 該当なし                                    |
| Place Autocomplete          | ✓                                      |
| Snap to Road                | ✓                                      |
| Speed Limits                | ✓                                      |
| Static Maps                 | ✓                                      |
| Static Street View          | 該当なし                                    |
| タイム ゾーン                   | ✓                                      |
| Maps Embedded API           | 該当なし                                    |
| Map URL                    | 該当なし                                    |

Google Maps では、基本的なキーベースの認証が提供されます。 Azure Maps では、基本的なキーベースの認証と Azure Active Directory 認証の両方が提供されます。 Azure Active Directory 認証には、基本的なキーベースの認証と比較して、多くのセキュリティ機能があります。

## <a name="licensing-considerations"></a>ライセンスに関する考慮事項

Google マップから Azure Maps に移行する際は、ライセンスに関して次の点を考慮してください。

* Azure Maps では、対話型マップの使用量に対して料金が請求され、その使用量は、読み込まれたマップ タイルの数が基準となります。 一方、Google マップでは、マップ コントロールの読み込みに対して料金が請求されます。 対話型 Azure Maps SDK では、開発コストを削減するために、マップ タイルが自動的にキャッシュされます。 マップ タイルが 15 個読み込まれるごとに、1 個の Azure Maps トランザクションが生成されます。 対話型 Azure Maps SDK では、512 ピクセルのタイルが使用され、平均でページ ビューあたり 1 個またはそれより少ないトランザクションが生成されます。
* 多くの場合、Google Maps Web サービスの静的マップ画像を Azure Maps Web SDK に置き換えるとよりコスト効果が高くなります。 Azure Maps Web SDK ではマップ タイルが使用されます。 ユーザーがマップをパンしたりズームしたりしない限り、多くの場合、マップの読み込みごとにトランザクションのごく一部のみが生成されます。 Azure Maps Web SDK には、必要に応じてパンとズームを無効にするオプションがあります。 また、Azure Maps Web SDK には、静的なマップ Web サービスよりも非常に多くの視覚化オプションが用意されています。
* Azure Maps を使用すると、データをそのプラットフォームから Azure に格納できます。 また、[利用規約](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)に従って、最大 6 か月間データをキャッシュすることもできます。

Azure Maps のいくつかの関連リソースを次に示します。

* [Azure Maps の価格のページ](https://azure.microsoft.com/pricing/details/azure-maps/)
* [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
* [Azure Maps 使用条件](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (Microsoft オンライン サービスの使用条件に含まれています)
* [Azure Maps での適切な価格レベルの選択](./choose-pricing-tier.md)

## <a name="suggested-migration-plan"></a>おすすめの移行プラン

移行プランの概要を次に示します。

1. アプリケーションで使用されている Google マップ SDK とサービスの一覧を作成します。 代わりとなる SDK とサービスが Azure Maps にあることを確認します。
2. [https://azure.com](https://azure.com) で Azure サブスクリプションを作成します (まだお持ちでない場合)。
3. Azure Maps アカウント ([ドキュメント](./how-to-manage-account-keys.md)) と認証キーまたは Azure Active Directory ([ドキュメント](./how-to-manage-authentication.md)) を作成します。
4. アプリケーション コードを移行します。
5. 移行したアプリケーションをテストします。
6. 移行したアプリケーションを運用環境にデプロイします。

## <a name="create-an-azure-maps-account"></a>Azure Maps アカウントを作成する

Azure Maps アカウントを作成し、Azure Maps プラットフォームにアクセスできるようにするには、次の手順を行います。

1. Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
2. [Azure portal](https://portal.azure.com/) にサインインします。
3. [Azure Maps アカウント](./how-to-manage-account-keys.md)を作成します。 
4. [Azure Maps サブスクリプション キー](./how-to-manage-authentication.md#view-authentication-details)を取得するか、Azure Active Directory 認証を設定し、セキュリティを強化します。

## <a name="azure-maps-technical-resources"></a>Azure Maps の技術リソース

Azure Maps に関する役に立つ技術リソースの一覧を次に示します。

- 概要: [https://azure.com/maps](https://azure.com/maps)
- ドキュメント: [https://aka.ms/AzureMapsDocs](./index.yml)
- Web SDK コード サンプル: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- 開発者フォーラム: [https://aka.ms/AzureMapsForums](/answers/topics/azure-maps.html)
- ビデオ: [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- ブログ: [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- 技術ブログ: [https://aka.ms/AzureMapsTechBlog](https://aka.ms/AzureMapsTechBlog)
- Azure Maps のフィードバック (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)
- [Azure Maps Jupyter Notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)

## <a name="migration-support"></a>移行サポート

開発者は、[フォーラム](/answers/topics/azure-maps.html) または多くの Azure サポート オプション ([https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)) のいずれかを使用して、移行サポートを探すことができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップすべきリソースはありません。

## <a name="next-steps"></a>次のステップ

Google マップ アプリケーションを移行する詳しい方法について次の記事でご確認ください。

> [!div class="nextstepaction"]
> [Web アプリを移行する](migrate-from-google-maps-web-app.md)
