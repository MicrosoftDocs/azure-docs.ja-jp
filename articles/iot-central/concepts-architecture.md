---
title: Azure IoT Central でのアーキテクチャの概念 | Microsoft Docs
description: この記事では、Azure IoT Central のアーキテクチャに関連する主要な概念を紹介します。
author: dominicbetts
ms.author: dobett
ms.date: 03/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 4f4b917808f4973dc83294391f58d7e0e2d01c4c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798812"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central のアーキテクチャ

この記事では、Microsoft Azure IoT Central のアーキテクチャの概要について説明します。

![最上位のアーキテクチャ](media/concepts-architecture/architecture.png)

## <a name="devices"></a>デバイス

デバイスは、Azure IoT Central アプリケーションとデータを交換します。 デバイスは次のことを実行できます。

- テレメトリなどの測定を送信します。
- アプリケーションと設定を同期します。

Azure IoT Central では、デバイスがアプリケーションと交換できるデータはデバイス テンプレートで指定されます。 デバイス テンプレートの詳細については、「[メタデータ管理](#metadata-management)」を参照してください。

Azure IoT Central アプリケーションへのデバイスの接続方法の詳細については、[デバイス接続](concepts-connectivity.md)に関するページを参照してください。

## <a name="cloud-gateway"></a>クラウド ゲートウェイ

Azure IoT Central は、デバイス接続を可能にするクラウド ゲートウェイとして Azure IoT Hub を使用します。 IoT Hub では、次のことが可能になります。

- クラウド内での大規模なデータ インジェスト。
- デバイスの管理。
- セキュリティ保護されたデバイス接続。

IoT Hub の詳細については、[Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/) に関するページを参照してください。

Azure IoT Central でのデバイス接続の詳細については、[デバイス接続](concepts-connectivity.md)に関するページを参照してください。

## <a name="data-stores"></a>データ ストア

Azure IoT Central は、クラウド内にアプリケーション データを格納します。 格納されるアプリケーション データには、次のものがあります。

- デバイス テンプレート。
- デバイス ID。
- デバイス メタデータ。
- ユーザーおよびロール データ。

Azure IoT Central は、デバイスから送信された測定データのために時系列ストアを使用します。 分析サービスによって使用されるデバイスからの時系列データ。

## <a name="analytics"></a>Analytics

分析サービスは、アプリケーションが表示するカスタム レポート データを生成する役割を果たします。 オペレーターは、アプリケーションに表示される[分析をカスタマイズする](howto-create-analytics.md)ことができます。 分析サービスは [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) 上に構築されており、デバイスから送信された測定データを処理します。

## <a name="rules-and-actions"></a>ルールとアクション

[ルールとアクション](howto-create-telemetry-rules.md)は、アプリケーション内のタスクを自動化するために緊密に連携します。 開発者は、定義されたしきい値を超える温度などのデバイス テレメトリに基づいてルールを定義できます。 Azure IoT Central は、ストリーム プロセッサを使用して、ルールの条件がいつ満たされるかを判定します。 ルールの条件が満たされると、開発者によって定義されたアクションがトリガーされます。 たとえば、アクションは、デバイス内の温度が高すぎることをエンジニアに通知するために電子メールを送信できます。

## <a name="metadata-management"></a>メタデータ管理

Azure IoT Central アプリケーションでは、デバイス テンプレートによって各種のデバイスの動作や機能が定義されます。 たとえば、冷蔵庫デバイス テンプレートは、冷蔵庫がアプリケーションに送信するテレメトリを指定します。

![テンプレートのアーキテクチャ](media/concepts-architecture/template_architecture.png)

デバイス テンプレートでは、次のことが可能です。

- **[Measurements] (測定)** は、デバイスがアプリケーションに送信するテレメトリを指定します。
- **[設定]** は、オペレーターが設定できる構成を指定します。
- **[プロパティ]** は、オペレーターが設定できるメタデータを指定します。
- **[ルール]** は、デバイスから送信されたデータに基づいてアプリケーションの動作を自動化します。
- **[ダッシュボード]** は、アプリケーション内のデバイスのカスタマイズ可能なビューです。

アプリケーションには、各デバイス テンプレートに基づいて 1 つ以上のシミュレートされた実デバイスを割り当てることができます。

## <a name="role-based-access-control-rbac"></a>ロール ベースのアクセス制御 (RBAC)

管理者は、事前に定義されたロールを使用して Azure IoT Central アプリケーションに対する[アクセス ルールを定義できます](howto-administer.md)。 管理者は、ユーザーがアプリケーションのどの領域にアクセスできるかを決定するロールにユーザーを割り当てることができます。

## <a name="security"></a>セキュリティ

Azure IoT Central 内のセキュリティ機能には、次のものがあります。

- データは、転送中および保存時に暗号化されます。
- 認証は、Azure Active Directory または Microsoft アカウントのどちらかによって提供されます。 2 要素認証がサポートされています。
- テナントの完全な分離。
- デバイス レベルのセキュリティ。

## <a name="ui-shell"></a>UI シェル

UI シェルは、最新の、応答性に優れた、HTML5 ブラウザー ベースのアプリケーションです。

## <a name="next-steps"></a>次の手順

ここでは、Azure IoT Central のアーキテクチャについて説明しました。推奨される次の手順は、Azure IoT Central での[デバイス接続](concepts-connectivity.md)の学習です。