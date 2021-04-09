---
title: チュートリアル:Bing 地図から Azure Maps に移行する | Microsoft Azure Maps
description: Bing 地図から Microsoft Azure Maps に移行する方法に関するチュートリアル。 ガイダンスでは、Azure Maps の API と SDK への切り替え方法について説明します。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 9bd0516889733a666bf15668cffd124dcc468f3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100388959"
---
# <a name="tutorial-migrate-from-bing-maps-to-azure-maps"></a>チュートリアル:Bing 地図から Azure Maps に移行する

このガイドでは、Web アプリケーション、モバイル アプリケーション、サーバーベース アプリケーションを Bing 地図から Azure Maps プラットフォームに移行する方法についての分析情報を提供します。 このガイドには、Azure Maps に移行するための比較コード サンプル、移行に関する提案、ベスト プラクティスが含まれています。 

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * Azure Maps で使用できる機能と同等の Bing 地図の機能との大まかな比較。
> * 考慮すべきライセンスの相違点。
> * 移行を計画する方法。
> * テクニカル リソースとサポートの場所

## <a name="prerequisites"></a>前提条件

1. [Azure portal](https://portal.azure.com) にサインインします。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
2. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
3. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 Azure Maps での認証の詳細については、「[Azure Maps での認証の管理](how-to-manage-authentication.md)」を参照してください。

## <a name="azure-maps-platform-overview"></a>Azure Maps プラットフォームの概要

Azure Maps は、Web アプリケーションやモバイル アプリケーションに地理的コンテキストを提供するために使用できる最新のマッピング データが搭載された強力な地理空間機能を、あらゆる業界の開発者に提供します。 Azure Maps は、Azure One API に準拠した一連の REST API であり、マップ、検索、経路指定、交通状況、タイム ゾーン、位置情報、マップ データ、気象データなど、さまざまなサービスに対応しています。Web SDK と Android SDK の両方が用意されており、開発を簡単かつ柔軟に行えるうえ、複数のプラットフォーム全体での移植が可能です。 [Azure Maps は Power BI でも利用できます](power-bi-visual-getting-started.md)。

## <a name="high-level-platform-comparison"></a>プラットフォームのおおまかな比較

次の表では、Bing 地図機能と、それらの機能に相当する Azure Maps でのサポートの大まかな一覧を示します。 この一覧には、その他の Azure Maps 機能 (アクセシビリティ、ジオフェンシング API、交通サービス、空間演算、直接マップ タイル アクセス、一括サービスなど) は含まれていません。

| Bing 地図の機能                     | Azure Maps のサポート |
|---------------------------------------|:------------------:|
| Web SDK                               | ✓                  |
| Android SDK                           | ✓                  |
| iOS SDK                               | 対応予定            |
| UWP SDK                               | 該当なし                 |
| WPF SDK                               | 該当なし                 |
| REST サービス API                     | ✓                  |
| 自動提案                           | ✓                  |
| ルート案内 (トラックを含む)          | ✓                  |
| Distance Matrix                       | ✓                  |
| 標高                            | ✓ (プレビュー)        |
| 映像 - 静的マップ                  | ✓                  |
| 映像メタデータ                      | ✓                  |
| 等時線                            | ✓                  |
| Local Insights                        | ✓                  |
| ローカル検索                          | ✓                  |
| 位置情報認識                  | ✓                  |
| 位置情報 (フォワード ジオコーディングと逆ジオコーディング) | ✓                  |
| 最適化旅程ルート            | 対応予定            |
| Snap to Road                         | ✓                  |
| Spatial Data Services (SDS)           | Partial            |
| タイム ゾーン                             | ✓                  |
| トラフィック インシデント                     | ✓                  |
| 構成ドリブン マップ             | 該当なし                |

Bing 地図では、基本的なキーベースの認証が提供されます。 Azure Maps には、基本的なキーベースの認証と、高度なセキュリティで保護された Azure Active Directory 認証の両方が用意されています。

## <a name="licensing-considerations"></a>ライセンスに関する考慮事項

Bing 地図から Azure Maps に移行する場合は、ライセンスに関して次の情報を考慮する必要があります。

* Azure Maps では、読み込まれたマップ タイルの数に基づいて対話型マップの使用量に対して料金が請求されます。一方、Bing 地図では、マップ コントロール (セッション) の読み込みに対して料金が請求されます。 Azure Maps では、開発者のコストを削減するためにマップ タイルが自動的にキャッシュされます。 マップ タイルが 15 個読み込まれるごとに、1 個の Azure Maps トランザクションが生成されます。 対話型 Azure Maps SDK では、512 ピクセルのタイルが使用され、平均してページ ビューあたり 1 個またはそれより少ないトランザクションが生成されます。

* Azure Maps を使用すると、データをそのプラットフォームから Azure に格納できます。 また、[使用条件](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)に従って、最大 6 か月間キャッシュすることもできます。

Azure Maps のいくつかのライセンス関連リソースを次に示します。

-   [Azure Maps の価格のページ](https://azure.microsoft.com/pricing/details/azure-maps/)
-   [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
-   [Azure Maps 使用条件](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (Microsoft オンライン サービスの使用条件に含まれています)
-   [Azure Maps での適切な価格レベルの選択](./choose-pricing-tier.md)

## <a name="suggested-migration-plan"></a>おすすめの移行プラン

大まかな移行プランの例を次に示します。

1.  アプリケーションで使用している Bing Maps SDK とサービスのインベントリを取得し、移行先となる代替の SDK とサービスが Azure Maps で用意されていることを確認します。
2.  <https://azure.com> で Azure サブスクリプションを作成します (まだお持ちでない場合)。
3.  Azure Maps アカウント ([ドキュメント](./how-to-manage-account-keys.md)) と認証キーまたは Azure Active Directory ([ドキュメント](./how-to-manage-authentication.md)) を作成します。
4.  アプリケーション コードを移行します。
5.  移行したアプリケーションをテストします。
6.  移行したアプリケーションを運用環境にデプロイします。

## <a name="create-an-azure-maps-account"></a>Azure Maps アカウントを作成する

Azure Maps アカウントを作成し、Azure Maps プラットフォームにアクセスできるようにするには、次の手順を行います。

1. Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
2. [Azure portal](https://portal.azure.com/) にサインインします。
3. [Azure Maps アカウント](./how-to-manage-account-keys.md)を作成します。
4. [Azure Maps サブスクリプション キー](./how-to-manage-authentication.md#view-authentication-details)を取得するか、Azure Active Directory 認証を設定し、セキュリティを強化します。

## <a name="azure-maps-technical-resources"></a>Azure Maps の技術リソース

Azure Maps に関する役に立つ技術リソースの一覧を次に示します。

* 概要: <https://azure.com/maps>
* ドキュメント: <https://aka.ms/AzureMapsDocs>
* Web SDK コード サンプル: <https://aka.ms/AzureMapsSamples>
* 開発者フォーラム: <https://aka.ms/AzureMapsForums>
* ビデオ: <https://aka.ms/AzureMapsVideos>
* ブログ: <https://aka.ms/AzureMapsBlog>
* Azure Maps のフィードバック (UserVoice): <https://aka.ms/AzureMapsFeedback>

## <a name="migration-support"></a>移行サポート

開発者は、[フォーラム](/answers/topics/azure-maps.html) または多くの Azure サポート オプション (<https://azure.microsoft.com/support/options/>) のいずれかを使用して、移行サポートを探すことができます。

## <a name="new-terminology"></a>新しい用語

以下に示したのは、Bing 地図でよく使われる用語と、それに対応する Azure Maps の用語を一覧にしたものです。

| Bing 地図の用語                    | Azure Maps の用語                                                |
|-----------------------------------|----------------------------------------------------------------|
| 航空写真                            | 衛星映像または航空映像                                            |
| 道順                        | ルート指定と呼ばれることもあります                             |
| エンティティ                          | ジオメトリまたは地物                                         |
| `EntityCollection`                | データ ソースまたはレイヤー                                           |
| `Geopoint`                        | [位置]                                                       |
| `GeoXML`                          | 空間 IO モジュールの XML ファイル                             |
| グラウンド オーバーレイ                    | イメージ レイヤー                                                    |
| ハイブリッド (マップの種類に関して) | 道路を含む衛星映像                                           |
| 情報ボックス                           | ポップアップ                                                          |
| 場所                          | [位置]                                                       |
| `LocationRect`                    | 境界ボックス                                                   |
| マップの種類                          | [Map style] (地図のスタイル)                                                      |
| ナビゲーション バー                    | マップ スタイル ピッカー、ズーム コントロール、ピッチ コントロール、コンパス コントロール |
| 画鋲                           | バブル レイヤー、シンボル レイヤー、HTML マーカー                      |

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップが必要なリソースはありません。

## <a name="next-steps"></a>次の手順

Bing 地図アプリケーションを移行する詳しい方法について次の記事でご確認ください。

> [!div class="nextstepaction"]
> [Web アプリを移行する](migrate-from-bing-maps-web-app.md)
