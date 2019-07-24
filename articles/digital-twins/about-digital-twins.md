---
title: Azure Digital Twins の概要 | Microsoft Docs
description: 空間インテリジェンスを実現する Azure IoT ソリューション、Azure Digital Twins について詳しく説明します。
author: julieseto
ms.author: jseto
ms.date: 12/14/2018
ms.topic: overview
ms.service: digital-twins
services: digital-twins
manager: bertvanhoof
ms.custom: mvc
ms.openlocfilehash: 2848f9ce97c2bdad59d86031c5894219875b6059
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437114"
---
# <a name="overview-of-azure-digital-twins"></a>Azure Digital Twins の概要

Azure Digital Twins は、物理環境の包括的なモデルを作成する Azure IoT サービスです。 人、空間、デバイスの間の関係と相互作用をモデル化する空間インテリジェンス グラフを作成することができます。

Azure Digital Twins を使用すると、多数の雑多なセンサーのデータではなく、物理空間のデータを照会することができます。 このサービスは、再利用可能でスケーラビリティが高く、空間認識性を備えたエクスペリエンスを構築し、デジタル世界と現実世界の垣根を越えてストリーミング データをリンクするのに役立ちます。 このような他に類を見ないほど的確なコンテキスト機能によって、ご自分のアプリを強化することができます。 たとえば、次のタスクに Azure Digital Twins を使用することができます。

- 工場のメンテナンスの必要性を予測する。
- 配電網の電力必要量をリアルタイムに分析する。
- オフィスの空きスペースの使用を最適化する。

Azure Digital Twins は、あらゆる種類の環境が対象となります。 倉庫やオフィス、学校、病院、銀行は、そのほんの一例です。 スタジアムや工場、パーキング、公園、スマート グリッド、都市に使用することもできます。 たとえば、次のシナリオに Azure Digital Twins を使用可能です。

- 複数の州全体の気温を毎日追跡する。
- 複雑なドローンの経路を監視する。
- 自律型車両を特定する。
- ビルの入居率を分析する。
- 店舗内で最も稼働率の高いレジを調べる。

実世界のビジネス シナリオが何であれ、Azure Digital Twins を通じて、対応するデジタル インスタンスをプロビジョニングすることができます。

次のビデオでは、Azure Digital Twins についてさらに詳しく取り上げています。

> [!VIDEO https://www.youtube.com/embed/TvN_NxpgyzQ]

## <a name="key-capabilities"></a>主な機能

Azure Digital Twins には、次の主要機能があります。

### <a name="spatial-intelligence-graph"></a>空間インテリジェンス グラフ

"[*空間インテリジェンス グラフ*](./concepts-objectmodel-spatialgraph.md)" (または " *空間グラフ*") は、物理環境の仮想表現です。 人、場所、デバイスの関係をモデル化する目的で使用できます。

近隣一帯で相互接続された複数の電力量計を対象とするスマート ユーティリティ アプリを考えてみましょう。 このスマート ユーティリティの会社は、電力使用量と電気料金を正確に監視して予測する必要があります。 場所のコンテキストと請求対象となる顧客に基づいて、各デバイスおよびセンサーをモデル化しなければなりません。 空間インテリジェンス グラフを使用すれば、この種の複雑な関係をモデル化することができます。

### <a name="digital-twin-object-models"></a>デジタル ツインのオブジェクト モデル

[デジタル ツインのオブジェクト モデル](./concepts-objectmodel-spatialgraph.md)は、あらかじめ定義されたデバイス プロトコルとデータ スキーマです。 迅速かつシンプルに開発を行えるよう、ソリューションの分野特有のニーズに沿ったものとなります。

たとえば空間利用率アプリケーションであれば、あらかじめ定義された空間タイプ (キャンパス、ビル、フロア、部屋など) を使用することになるでしょう。

### <a name="multiple-and-nested-tenants"></a>複数のテナントと入れ子になったテナント

安全にスケーリングでき、なおかつ複数のテナントで再利用できるソリューションを構築できます。 また、切り離された安全な方法でアクセスおよび使用できる複数のサブテナントを作成することもできます。

たとえば、1 つのビル内でテナントのデータを他のテナントのデータから切り離すように構成された空間利用率アプリが考えられます。 そのアプリを使用して、1 つのテナントのデータを多数のビルと結合することもできます。

### <a name="advanced-compute-capabilities"></a>高度な計算機能

[ユーザー定義関数](./concepts-user-defined-functions.md)を使用すると、受信[デバイス データ](./concepts-device-ingress.md)に対するカスタム関数を定義して実行し、あらかじめ定義されたエンドポイントにシグナルを送信できます。 この高度な機能によって、デバイス タスクのカスタマイズとオートメーションが強化されます。

たとえば、土壌水分センサーの測定値と気象予報を評価するユーザー定義関数を取り入れたスマート農業アプリケーションが考えられます。 さらに、そのアプリで、散水の必要性についてのシグナルを送信することも可能です。

### <a name="built-in-access-control"></a>ビルトインされたアクセスの制御

アクセスと ID の管理機能 ([ロールベースのアクセス制御](./security-role-based-access-control.md)や [Azure Active Directory](./security-authenticating-apis.md) など) を使用して、個人やデバイスを対象にアクセスを安全に制御することができます。

たとえば、部屋の居住者が特定の範囲内で温度を設定できるように構成された設備管理アプリが考えられます。 施設の管理者には、すべての部屋の温度を任意の値に設定することが許可されます。

### <a name="ecosystem"></a>エコシステム

Azure Digital Twins のインスタンスは、多数の強力な Azure サービスに接続することができます。 そのようなサービスには、Azure Stream Analytics、Azure AI、Azure Storage などがあります。 また、Azure Maps、Microsoft Mixed Reality、Dynamics 365、Office 365 なども挙げられます。

たとえば、Azure Digital Twins を使用して、多数のフロアに配置されたチームとデバイスを表現するスマート オフィス ビル アプリケーションが考えられます。 プロビジョニングされた Digital Twin インスタンスに対してデバイスからライブ データがストリーム配信されると同時に、そのデータを Stream Analytics で処理して、アクションにつながる重要な分析情報を得ることができます。 そのデータは Azure Storage に格納され、共有可能なファイル形式に変換されます。 そのファイルが、Office 365 を使用して組織全体に配布されます。

## <a name="solutions-that-benefit-from-azure-digital-twins"></a>Azure Digital Twins の恩恵を受けるソリューション

Azure Digital Twins は、現実世界とその多くの関係を表現するのに役立ちます。 IoT のモデリング、データ処理、イベント処理、デバイス追跡を単純化することができます。 ごく一部の例として、さまざまな業種でのシナリオを考えてみましょう。 その利点は、次の用途で発揮されます。

* オフィス ビルを構成する最善の方法についての分析情報を収集するために、時間経過に伴う入居率を資産管理会社に伝える。
* モバイル アプリ向けに作業指示チケットをトリガーする。 これを使用して、小売店舗やスポーツの会場で、警備員を派遣したり、清掃業務などのサービスをスケジュールしたりします。
* ビル内の使用中の部屋をリアルタイムで入居者に示す。 そのうえで、ニーズに合ったワーク スペースを入居者が予約できるように支援します。
* 資産がスペース内のどこにあるかを追跡する。
* ユーザーの傾向や電力供給網の制約をモデル化することによって電気自動車の充電を最適化する。

## <a name="azure-digital-twins-in-the-context-of-other-iot-services"></a>他の IoT サービスのコンテキストにおける Azure Digital Twins

現実世界を絶えず追跡する IoT のデバイスやセンサーと Azure Digital Twins との接続には、Azure IoT Hub が使用されます。 次の図は、Azure Digital Twins と他の Azure IoT サービスの関係を示しています。

![Azure Digital Twins は Azure IoT Hub を基盤として構築されたサービス][1]

IoT の詳細については、[Azure IoT のテクノロジとソリューション](https://docs.microsoft.com/azure/iot-fundamentals/iot-services-and-technologies)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

Azure Digital Twins についての短いデモをご覧ください。

>[!div class="nextstepaction"]
>[クイック スタート:Azure Digital Twins を使用して空き部屋を探す](./quickstart-view-occupancy-dotnet.md)

Azure Digital Twins を使用して設備管理アプリケーションについて詳しく見ていきます。

>[!div class="nextstepaction"]
>[チュートリアル:Azure Digital Twins をデプロイし、空間グラフを構成する](./tutorial-facilities-setup.md)

Azure Digital Twins の主要な概念について説明します。

>[!div class="nextstepaction"]
>[Digital Twins オブジェクト モデルと空間インテリジェンス グラフを理解する](./concepts-objectmodel-spatialgraph.md)

<!-- Images -->
[1]: media/overview/azure-digital-twins-in-iot-ecosystem.png