---
title: クイック スタート:セキュリティ モジュール ツインを作成する
description: このクイックスタートでは、Azure Security Center for IoT で使用する Azure Security Center for IoT モジュール ツインを作成する方法について説明します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 7b867f5bcbbdc5deab2d67f356df69ee870c21f3
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142249"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>クイック スタート:azureiotsecurity モジュール ツインを作成する

このクイックスタートでは、新しいデバイス用に個々の _azureiotsecurity_ モジュール ツインを作成する方法と、IoT Hub 内のすべてのデバイス用にモジュール ツインをバッチ作成する方法について説明します。

## <a name="understanding-azureiotsecurity-module-twins"></a>azureiotsecurity モジュール ツインについて

Azure に構築された IoT ソリューションでは、デバイス ツインが、デバイスの管理とプロセスの自動化の両方で主要な役割を果たします。

Azure Security Center for IoT は、既存の IoT デバイス管理プラットフォームと完全に統合されるため、デバイスのセキュリティ状態を管理したり、既存のデバイス制御機能を利用したりすることができます。
Azure Security Center for IoT の統合は、IoT Hub ツイン メカニズムを利用することによって実現されます。

Azure IoT Hub でのモジュール ツインの一般的な概念の詳細については、[IoT Hub モジュール ツイン](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)に関するページを参照してください。

Azure Security Center for IoT では、モジュール ツイン メカニズムを利用して、各デバイス用の _azureiotsecurity_ というセキュリティ モジュール ツインが管理されます。

セキュリティ モジュール ツインでは、各デバイスのデバイス セキュリティに関連するすべての情報が保持されます。

Azure Security Center for IoT の機能を最大限に活用するには、サービス内のすべてのデバイスに対してこれらのセキュリティ モジュール ツインを作成、構成、および使用する必要があります。

## <a name="create-azureiotsecurity-module-twin"></a>azureiotsecurity モジュール ツインを作成する

_azureiotsecurity_ モジュール ツインは、次の 2 つの方法で作成できます。

1. [モジュール バッチ スクリプト](https://aka.ms/iot-security-github-create-module) - 既定の設定を使用して、新しいデバイスまたはモジュール ツインのないデバイス用にモジュール ツインを自動的に作成します。
1. 各デバイス用の特定の構成を使用して、各モジュール ツインを個別に手動で編集します。

>[!NOTE]
> バッチ方式を使用しても、既存の azureiotsecurity モジュール ツインは上書きされません。 バッチ方式を使用すると、まだセキュリティ モジュール ツインがないデバイス用の新しいモジュール ツインのみが作成されます。

既存のモジュール ツインの構成を変更する方法については、[エージェントの構成](how-to-agent-configuration.md)に関するページを参照してください。

デバイス用の新しい _azureiotsecurity_ モジュール ツインを手動で作成するには、次の手順を使用します。

1. IoT Hub で、セキュリティ モジュール ツインの作成対象のデバイスを探して選択します。
1. デバイスをクリックし、 **[モジュール ID の追加]** をクリックします。
1. **[モジュール ID 名]** フィールドで、「**azureiotsecurity**」と入力します。

1. **[保存]** をクリックします。

## <a name="verify-creation-of-a-module-twin"></a>モジュール ツインの作成の確認

特定のデバイスのセキュリティ モジュール ツインが存在するかどうかを確認するには、以下の操作を行います。

1. Azure IoT Hub で、 **[エクスプローラー]** メニューの **[IoT デバイス]** を選択します。
1. デバイス ID を入力するか、 **[デバイスのクエリ] フィールド**のオプションを選択して、 **[デバイスのクエリ]** をクリックします。
    ![デバイスのクエリ](./media/quickstart/verify-security-module-twin.png)
1. デバイスを選択するか、またはダブルクリックして、[デバイスの詳細] ページを開きます。
1. **[モジュール ID]** メニューを選択し、デバイスに関連付けられているモジュール ID の一覧に **azureiotsecurity** モジュールが存在することを確認します。
    ![デバイスに関連付けられているモジュール](./media/quickstart/verify-security-module-twin-3.png)

Azure Security Center for IoT モジュール ツインのプロパティに対するカスタマイズの詳細については、[エージェントの構成](how-to-agent-configuration.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

次の記事に進んで、セキュリティに関する推奨事項を調査する方法について学びましょう。

> [!div class="nextstepaction"]
> [セキュリティに関する推奨事項の調査](quickstart-investigate-security-recommendations.md)
