---
title: Azure IoT Central とは | Microsoft Docs
description: Azure IoT Central は、カスタム IoT ソリューションの構築と管理に使用できるエンド ツー エンドの SaaS ソリューションです。 この記事では、Azure IoT Central の機能について概要を紹介します。
author: dominicbetts
ms.author: dobett
ms.date: 08/26/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: c5c1f36e77e24b598aa777d384462ee4538bd486
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70048934"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>Azure IoT Central とは

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

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
- アプリケーションが反応するしきい値。
- デバイスの動作を決める設定。

デバイス テンプレートとアプリケーションは、Azure IoT Central から自動的に生成されるシミュレーション データを使って、すぐにテストすることができます。

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

アプリケーションへのアクセスは、管理者が[ユーザー ロールとアクセス許可](howto-administer.md)を使って管理します。

## <a name="next-steps"></a>次の手順

これで Azure IoT Central の概要は終了です。推奨される次の手順は以下のとおりです。

- [Azure IoT Central と Azure IoT ソリューション アクセラレータ](overview-iot-options.md)の違いを理解する。
- [Azure IoT Central の UI](overview-iot-central-tour.md) に慣れる。
- 実際に [Azure IoT Central アプリケーションを作成](quick-deploy-iot-central.md)してみる。
- 一連のチュートリアルに従って、以下の作業方法を確認する。
  - [ビルダーとしてデバイス テンプレートを作成する](tutorial-define-device-type.md)
  - [ビルダーとして、ソリューションを自動化するためのルールを追加する](tutorial-configure-rules.md)
  - [ビルダーとして、オペレーター向けにアプリケーションをカスタマイズする](tutorial-customize-operator.md)
  - [オペレーターとしてデバイスを監視する](tutorial-monitor-devices.md)
  - [オペレーターとして、実デバイスをソリューションに追加する](tutorial-add-device.md)
  - [デバイス開発者として、デバイス向けのコードを作成する](tutorial-add-device.md#prepare-the-client-code)
