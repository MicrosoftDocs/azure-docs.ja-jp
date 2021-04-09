---
title: IoT ソリューション アクセラレータの概要 - Azure | Microsoft Docs
description: Azure IoT ソリューション アクセラレータについて説明します。 IoT ソリューション アクセラレータは、すぐにデプロイできるエンドツーエンドの完全な IoT ソリューションです。
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: 5012383e64a85ee025273f5339b828f5338e1d4f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97629070"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Azure IoT ソリューション アクセラレータとは

クラウドベースの IoT ソリューションは通常、カスタム コードとクラウド サービスを使用してデバイスの接続性、データ処理と分析、プレゼンテーションを管理します。

IoT ソリューション アクセラレータは、一般的な IoT シナリオが実装され、必要な要素がすべて揃った、すぐにデプロイできる IoT ソリューションです。 このシナリオには、接続済みファクトリとデバイスのシミュレーションが含まれます。 ソリューション アクセラレータをデプロイすると、そのデプロイには、必要なクラウド ベースのサービスと、必要なアプリケーション コードがすべて含まれています。

ソリューション アクセラレータは、IoT ソリューションの出発点です。 すべてのソリューション アクセラレータのソース コードはオープン ソースで、GitHub で入手できます。 ソリューション アクセラレータをダウンロードし、要件に合わせてカスタマイズすることをお勧めします。

カスタムの IoT ソリューションをゼロからビルドする前の学習ツールとして、ソリューション アクセラレータを使用することもできます。 ソリューション アクセラレータには、クラウド ベースの IoT ソリューションの実証済みプラクティスが実装されており、それに従って実行できます。

各ソリューション アクセラレータのアプリケーション コードには、ソリューション アクセラレータを管理するための Web アプリが含まれています。

> [!NOTE]
> リモート監視および予測メンテナンス ソリューションは [Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators) サイトから削除されました。 詳しくは、[Azure IoT Solution Accelerators の概要 (以前のバージョン)](/previous-versions/azure/iot-accelerators/about-iot-accelerators) に関するページを参照してください。

## <a name="supported-iot-scenarios"></a>サポートされる IoT のシナリオ

現時点では、次の 2 つのソリューション アクセラレータをデプロイできます。

### <a name="connected-factory"></a>接続済みファクトリ

[接続済みファクトリ ソリューション アクセラレータ](iot-accelerators-connected-factory-features.md)は、[OPC Unified Architecture](https://opcfoundation.org/about/opc-technologies/opc-ua/) インターフェイスを使用して工業資産からテレメトリを収集し、それらを制御するために使用します。 工業資産には、工場の生産ラインで組み立てやテストを行う作業場が含まれる場合があります。

接続済みファクトリのダッシュボードを使用して、産業用デバイスを監視および管理することができます。

:::image type="content" source="./media/about-iot-accelerators/cf-dashboard-inline.png" alt-text="接続済みファクトリ ソリューションのダッシュボードを示すスクリーンショット。" lightbox="./media/about-iot-accelerators/cf-dashboard-expanded.png":::

### <a name="device-simulation"></a>デバイスのシミュレーション

[デバイスのシミュレーション ソリューション アクセラレータ](iot-accelerators-device-simulation-overview.md)は、現実のテレメトリを生成する、シミュレートされたデバイスを実行するために使用します。 このソリューション アクセラレータを使用して、他のソリューション アクセラレータの動作をテストしたり、独自のカスタム IoT ソリューションをテストしたりできます。

デバイス シミュレーション Web アプリを使用して、シミュレーションを構成および実行できます。

:::image type="content" source="./media/about-iot-accelerators/ds-dashboard-inline.png" alt-text="デバイス シミュレーション ソリューションのダッシュボードを示すスクリーンショット。" lightbox="./media/about-iot-accelerators/ds-dashboard-expanded.png":::

## <a name="design-principles"></a>設計原則

すべてのソリューション アクセラレータは同じ設計原則とゴールに従います。 次を満たすように設計されています。

* **スケーラブル**。何百万ものデバイスを接続して管理できます。
* **拡張可能**。要件に合わせてカスタマイズできるようにします。
* **わかりやすい**。そのしくみや実装方法が理解しやすくなっています。
* **モジュール式**。代替のサービスにスワップアウトできます。
* **セキュリティで保護されている**。組み込みの接続性とデバイスのセキュリティ機能を Azure のセキュリティと組み合わせています。

## <a name="architectures-and-languages"></a>アーキテクチャと言語

元のソリューション アクセラレータは、モデル ビュー コントローラー (MVC) アーキテクチャを使用して .NET で作成されています。 マイクロソフトでは、ソリューション アクセラレータを、新しいマイクロサービス アーキテクチャに更新しています。 次の表に、ソリューション アクセラレータの現在の状態と、GitHub リポジトリへのリンクを示します。

| ソリューション アクセラレータ   | Architecture  | Languages     |
| ---------------------- | ------------- | ------------- |
| 接続済みファクトリ      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| デバイスのシミュレーション      | マイクロサービス | [.NET](https://github.com/Azure/device-simulation-dotnet)          |

マイクロサービス アーキテクチャの詳細については、「[Introduction to the Azure IoT reference architecture (Azure IoT リファレンス アーキテクチャの概要)](/azure/architecture/reference-architectures/iot/)」を参照してください。

## <a name="deployment-options"></a>デプロイ オプション

ソリューション アクセラレータは、[Microsoft Azure IoT ソリューション アクセラレータ](https://www.azureiotsolutions.com/Accelerators#)のサイトからデプロイすることも、コマンド ラインを使用してデプロイすることもできます。

ソリューション アクセラレータを実行するコストは、[基礎となる Azure サービスの実行コスト](https://azure.microsoft.com/pricing)の総計です。 デプロイのオプションを選択するときに、使用する Azure サービスの詳細が表示されます。

## <a name="next-steps"></a>次のステップ

IoT ソリューション アクセラレータの 1 つを試してみるには、[コネクテッド ファクトリ ソリューションを試す](quickstart-connected-factory-deploy.md)クイックスタートをご覧ください。
