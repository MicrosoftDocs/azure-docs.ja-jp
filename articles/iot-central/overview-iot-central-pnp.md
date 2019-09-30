---
title: Azure IoT Central とは | Microsoft Docs
description: Azure IoT Central は、カスタム IoT ソリューションの構築と管理に使用できるエンド ツー エンドの SaaS ソリューションです。 この記事では、Azure IoT Central の機能について概要を紹介します。
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: bfdad0d81599035e7d8c270ec4e8ee8d6a45125e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858927"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Azure IoT Central (プレビュー機能) とは?

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Azure IoT Central の [IoT プラグ アンド プレイ](../iot-pnp/overview-iot-plug-and-play.md)機能は、現在パブリック プレビューの段階にあります。 運用環境のワークロードに、IoT プラグ アンド プレイ対応の IoT Central アプリケーションを使用しないでください。 運用環境向けには、現在一般公開されているアプリケーション テンプレートから作成された IoT Central アプリケーションを使用してください。

Azure IoT Central は、物理的な世界とデジタルの世界とを結ぶ製品が簡単に作成できるフル マネージドの IoT SaaS (サービスとしてのソフトウェア) ソリューションです。 コネクテッド製品の構想を次の手段で実現することができます。

- 接続されているデバイスから、製品や顧客エクスペリエンスの向上につながる新たな分析情報を導き出す。
- 組織の新たなビジネス機会を創出する。

標準的な IoT プロジェクトと比較して、Azure IoT Central には次のような利点があります。

- 管理の負担が軽減される。
- 運用コストとオーバーヘッドが軽減される。
- 以下のような強みを活かしつつ、アプリケーションを容易にカスタマイズできる。
  - 業界最先端のテクノロジ ([Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)、[Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) など)
  - エンタープライズ レベルのセキュリティ機能 (エンド ツー エンドの暗号化など)

次のビデオで、Azure IoT Central の概要を紹介しています。

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

この記事では、Azure IoT Central の要点を取り上げます。

- プロジェクトに関連する代表的なペルソナ。
- アプリケーションを作成する方法。
- デバイスをアプリケーションに接続する方法。
- アプリケーションを管理する方法。

## <a name="known-issues"></a>既知の問題

> [!Note]
> これらの既知の問題は、IoT Central プレビューのアプリケーションにのみ該当します。

- ルールではすべてのアクションがサポートされているわけではない (メールのみ)。
- 複合型の場合: ルール、分析、デバイス グループがサポートされていない。
- 継続的データ エクスポートで Avro 形式がサポートされていない (互換性がない)。
- シミュレートされるデバイスでは、すべての複合型がサポートされているわけではない。
- GeoJSON は現在サポートされていない。
- マップ タイルは現在サポートされていない。
- ジョブでは複合型がサポートされていない。
- 配列スキーマの種類はサポートされていない。
- アプリケーション テンプレートのエクスポートとアプリケーションのコピーはサポートされていない。
- C デバイス SDK と Node.js のデバイス SDK、サービス SDK のみがサポートされている。
- 北ヨーロッパ リージョンと米国中部リージョンでのみ利用できます。
- デバイス機能モデルには、すべてのインターフェイスが同じファイル内にインラインで定義されている必要があります。

## <a name="personas"></a>ペルソナ

この Azure IoT Central ドキュメントでは、Azure IoT Central アプリケーションと対話する 4 種類のペルソナについて言及しています。

- "_ビルダー_": アプリケーションに接続するデバイスの種類を定義したり、オペレーター向けにアプリケーションをカスタマイズしたりする役割を担います。
- "_オペレーター_": アプリケーションに接続されたデバイスを管理します。
- "_管理者_": 管理タスクを担います (アプリケーション内のユーザーと役割の管理など)。
- "_デバイス開発者_": アプリケーションに接続されたデバイス上で実行するコードを作成します。

## <a name="create-your-azure-iot-central-application"></a>Azure IoT Central アプリケーションの作成

クラウドでホストされる自社のカスタム IoT ソリューションは、ビルダーが Azure IoT Central を使用して作成します。 通常、カスタム IoT ソリューションは次の要素から成ります。

- クラウドベースのアプリケーション。このアプリケーションによってデバイスからテレメトリを受信し、それらのデバイスの管理が可能となります。
- カスタム コードを実行する複数のデバイス。これらのデバイスが、クラウドベースのアプリケーションに接続されます。

新しい Azure IoT Central アプリケーションは、ブラウザーからすばやくデプロイし、具体的な要件に合わせてカスタマイズすることができます。 ビルダーは、自社アプリケーションに接続するデバイス用に、Web ベースのツールを使って "_デバイス テンプレート_" を作成します。 デバイス テンプレートは、特定の種類のデバイスについて、その特性と動作を定義するブループリントです。次に示したのは、その例です。

- 送信するテレメトリ。
- オペレーターによる変更が可能なビジネス プロパティ。
- デバイスによって設定され、かつアプリケーションでは読み取り専用となるデバイス プロパティ。
- デバイスの動作を決定するオペレーターによって設定されるプロパティ。

このデバイス テンプレートには、次のものが含まれています。

- "_デバイス機能モデル_": デバイスから送信されるテレメトリやデバイスからレポートされるプロパティなど、デバイスが実装する必要がある機能を記述します
- クラウド プロパティ: デバイス上に格納されません。
- カスタマイズ、ダッシュボード、およびフォーム: ご利用の IoT Central アプリケーションの一部を成します。

### <a name="create-device-templates"></a>デバイス テンプレートを作成する

[IoT プラグ アンド プレイ](../iot-pnp/overview-iot-plug-and-play.md)を使用すれば、IoT Central では埋め込みデバイスコードを記述することなく、デバイスを統合できます。 IoT プラグ アンド プレイの中核となるのは、デバイスの機能を記述するデバイス機能モデル スキーマです。 IoT Central プレビュー アプリケーション内のデバイス テンプレートでは、これらの IoT プラグ アンド プレイ デバイス機能モデルが使用されます。

ビルダーは、デバイス テンプレートを作成する場合、次に示すいくつかのオプションを利用できます。

- IoT Central でデバイス テンプレートを設計してから、そのデバイス機能モデルをデバイス コードに実装します。
- [Azure Certified for IoT デバイス カタログ](https://aka.ms/iotdevcat)からデバイス機能モデルをインポートし、次に、IoT Central アプリケーションで必要とされるクラウド プロパティ、カスタマイズ、およびダッシュボードを追加します。
- Visual Studio Code を使用してデバイス機能モデルを作成します。 モデルからデバイス コードを実装し、デバイスを IoT Central アプリケーションに接続します。 IoT Central によって、リポジトリからデバイス機能モデルが検索され、シンプルなデバイス テンプレートが自動的に作成されます。
- Visual Studio Code を使用してデバイス機能モデルを作成します。 ご利用のデバイス コードをモデルから実装します。 ご利用の IoT Central アプリケーションにデバイス機能モデルを手動でインポートしてから、IoT Central アプリケーションに必要なクラウドのプロパティ、カスタマイズ、ダッシュボードを追加します。

ビルダーは、IoT Central を使用して、デバイス テンプレートを検証するためのテスト デバイス用コードを生成できます。

### <a name="customize-the-ui"></a>UI のカスタマイズ

また、ビルダーは、日常的にアプリケーションを使うオペレーター向けに、Azure IoT Central アプリケーションの UI をカスタマイズすることもできます。 ビルダーが行うことのできるカスタマイズの例を次に示します。

- プロパティと設定のレイアウトをデバイス テンプレートで定義する。
- オペレーターによる分析情報の検出と問題の解決を迅速化するカスタム ダッシュボードを構成する。
- 接続されているデバイスから時系列データを探すカスタム分析を構成する。

## <a name="connect-your-devices"></a>デバイスの接続

アプリケーションに接続できるデバイスの種類がビルダーによって定義されたら、そのデバイス上で実行するコードをデバイス開発者が作成します。 デバイス開発者は、Microsoft のオープンソース [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) を使用してデバイス コードを作成します。 Azure IoT Central アプリケーションに自社のデバイスを接続するというお客様のニーズを満たすために、これらの SDK は多様な言語、プラットフォーム、プロトコルに対応しています。 これらの SDK を利用することで、次のようなデバイス機能が容易に実装できます。

- 安全な接続を作成する。
- テレメトリを送信する。
- ステータスを報告する。
- 構成の更新を受信する。

詳細については、ブログ記事「[Benefits of using the Azure IoT SDKs, and pitfalls to avoid if you don't (Azure IoT SDK を使う利点と使わなかった場合に回避すべきリスク)](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)」を参照してください。

## <a name="manage-your-application"></a>アプリケーションの管理

Azure IoT Central アプリケーションは、完全に Microsoft によってホストされるため、アプリケーションの管理オーバーヘッドが軽減されます。

Azure IoT Central ソリューションのデバイスは、オペレーターが Azure IoT Central アプリケーションを使用して管理します。 オペレーターは、次のようなタスクを実行します。

- アプリケーションに接続されたデバイスを監視する。
- デバイスの問題をトラブルシューティングして修復する。
- 新しいデバイスをプロビジョニングする。

ビルダーは、接続デバイスからのデータ ストリーミングに対して作用するカスタム ルールやカスタム アクションを定義できます。 それらのルールをオペレーターがデバイス レベルで有効または無効にすることで、アプリケーション内のタスクを制御したり自動化したりすることができます。

アプリケーションへのアクセスは、管理者が[ユーザー ロールとアクセス許可](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)を使って管理します。

## <a name="next-steps"></a>次の手順

これで Azure IoT Central の概要は終了です。推奨される次の手順は以下のとおりです。

- [Azure IoT Central と Azure IoT ソリューション アクセラレータ](overview-iot-options.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)の違いを理解する。
- [Azure IoT Central の UI](overview-iot-central-tour-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) に慣れる。
- 実際に [Azure IoT Central アプリケーションを作成](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)してみる。
- 一連のチュートリアルに従って、以下の作業方法を確認する。
  - [ビルダーとしてデバイス テンプレートを作成する](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [ビルダーとして、ソリューションを自動化するためのルールを追加する](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [オペレーターとしてデバイスを監視する](tutorial-monitor-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [オペレーターとして、デバイスをソリューションに追加する](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- [IoT プラグ アンド プレイ](../iot-pnp/overview-iot-plug-and-play.md)について詳細を学習する
