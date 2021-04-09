---
title: Azure IoT Central でのソリューションの構築 | Microsoft Docs
description: Azure IoT Central は、IoT ソリューションの作成を簡単にする IoT アプリケーション プラットフォームです。 この記事では、IoT Central を使用した統合ソリューションの構築の概要について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 72aa8e5e3284e0ee7fbe63e0fb617b9eba03292e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100416848"
---
# <a name="iot-central-solution-builder-guide"></a>IoT Central ソリューション ビルダーのガイド

*この記事は、ソリューション ビルダーを対象としています。*

IoT Central アプリケーションを使用すると、数百万台ものデバイスを、そのライフ サイクル全体にわたって監視および管理することができます。 このガイドは、IoT Central を使用して統合ソリューションを構築するソリューション ビルダーを対象としています。 IoT Central アプリケーションを使用すると、デバイスの管理、デバイス テレメトリの分析、および他のバックエンド サービスとの統合を行うことができます。

ソリューション ビルダー:

- IoT Central の Web UI でダッシュボードとビューを構成します。
- 組み込みのルールと分析ツールを使用して、接続されているデバイスからビジネス分析情報を取得します。
- データのエクスポートとルールの機能を使用して、IoT Central を他のバックエンド サービスと統合します。

## <a name="configure-dashboards-and-views"></a>ダッシュボードとビューを構成する

IoT Central アプリケーションでは、オペレーターがアプリケーションを表示および操作するために使用する 1 つ以上のダッシュボードを利用できます。 ソリューション ビルダーは、既定のダッシュボードをカスタマイズしたり、特殊なダッシュボードを作成したりできます。

- カスタマイズされたダッシュボードの例については、「[特定業界に焦点を合わせたテンプレート](concepts-app-templates.md#industry-focused-templates)」を参照してください。
- ダッシュボードの詳細については、「[複数のダッシュボードの作成と管理](howto-create-personal-dashboards.md)」と「[アプリケーション ダッシュボードの構成](howto-add-tiles-to-your-dashboard.md)」を参照してください。

デバイスが IoT Central に接続されると、デバイスは、そのデバイスの種類用のデバイス テンプレートに関連付けられます。 デバイス テンプレートには、オペレーターが個々のデバイスを管理するために使用するカスタマイズ可能なビューがあります。 ソリューション開発者は、デバイスの種類で使用可能なビューを作成およびカスタマイズできます。 詳細については、「[ビューの追加](howto-set-up-template.md#add-views)」を参照してください。

## <a name="use-built-in-rules-and-analytics"></a>組み込みのルールと分析を使用する

ソリューション開発者は、IoT Central アプリケーションに、カスタマイズ可能なアクションを実行するルールを追加できます。 ルールにより、デバイスからのデータに基づいて条件が評価され、アクションを実行するタイミングが決定されます。 詳細については、次を参照してください。

- [チュートリアル:Azure IoT Central アプリケーションで規則を作成して通知を設定する](tutorial-create-telemetry-rules.md)
- [Azure IoT Central でルールに対する Webhook アクションを作成する](howto-create-webhooks.md)
- [1 つまたは複数のルールから実行する複数のアクションをグループ化する](howto-use-action-groups.md)

IoT Central には、オペレーターが、接続されているデバイスからのデータを分析するために使用できる組み込みの分析機能があります。 詳細については、「[分析を使用してデバイス データを分析する方法](howto-create-analytics.md)」を参照してください。

## <a name="integrate-with-other-services"></a>他のサービスとの統合

ソリューション ビルダーは、IoT Central のデータのエクスポートとルールの機能を使用して他のサービスと統合できます。 詳細については、以下をご覧ください。

- [データ エクスポートを使用してクラウドの宛先に IoT データをエクスポートする](howto-export-data.md)
- [ワークフローを使用して Azure IoT Central アプリケーションを他のクラウド サービスと統合する](howto-configure-rules-advanced.md)
- [Stream Analytics、Azure Functions、SendGrid を使用してカスタム ルールで Azure IoT Central を拡張する](howto-create-custom-rules.md)
- [Azure Databricks を使用したカスタム分析で Azure IoT Central を拡張する](howto-create-custom-analytics.md)
- [Azure IoT Central データを Power BI ダッシュボードに視覚化する](howto-connect-powerbi.md)

## <a name="next-steps"></a>次のステップ

IoT Central の詳細な使用方法については、次の手順として、クイックスタートの「[Azure IoT Central アプリケーションの作成](./quick-deploy-iot-central.md)」から始めることをお勧めします。
