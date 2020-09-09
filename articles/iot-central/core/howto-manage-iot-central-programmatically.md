---
title: プログラムによる IoT Central の管理 | Microsoft Docs
description: この記事では、IoT Central をプログラムで作成および管理する方法について説明します。 JavaScript、Python、C#、Ruby、Go などの複数の言語 SDK を使用して、アプリケーションを表示、変更、削除することができます。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 05/19/2020
ms.topic: how-to
ms.openlocfilehash: ba0ee0a610299bbe6b7e550f204cd2fd50d6d71a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83748631"
---
# <a name="manage-iot-central-programmatically"></a>プログラムによる IoT Central の管理

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

[Azure IoT Central アプリケーション マネージャー](https://aka.ms/iotcentral) Web サイトで IoT Central アプリケーションの作成と管理を行うのではなく、Azure SDK を使用してプログラムでアプリケーションを管理することができます。 サポートされる言語には、JavaScript、Python、C#、Ruby、および Go があります。

## <a name="install-the-sdk"></a>SDK のインストール

SDK リポジトリとパッケージ インストール コマンドを次の表に示します。

| SDK リポジトリ | パッケージのインストール |
| -------------- | ------------ |
| [Azure IotCentralClient SDK for JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/iotcentral/arm-iotcentral) | `npm install @azure/arm-iotcentral` |
| [Microsoft Azure SDK for Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/iothub/azure-mgmt-iotcentral/azure/mgmt/iotcentral) | `pip install azure-mgmt-iotcentral` |
| [Azure SDK for .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/iotcentral/Microsoft.Azure.Management.IotCentral) | `dotnet add package Microsoft.Azure.Management.IotCentral` |
| [Microsoft Azure SDK for Ruby - Resource Management (プレビュー)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/management/azure_mgmt_iot_central/lib/2018-09-01/generated/azure_mgmt_iot_central) | `gem install azure_mgmt_iot_central` |
| [Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/iotcentral) | [Maven パッケージ](https://search.maven.org/search?q=a:azure-mgmt-iotcentral) |
| [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/iotcentral/mgmt/2018-09-01/iotcentral) | [パッケージのリリース](https://github.com/Azure/azure-sdk-for-go/releases) |

## <a name="samples"></a>サンプル

[Azure IoT Central ARM SDK サンプル](https://docs.microsoft.com/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/) リポジトリには、Azure IoT Central アプリケーションの作成、更新、一覧表示、および削除を行う方法を示す複数のプログラミング言語のコード サンプルがあります。

## <a name="next-steps"></a>次のステップ

Azure IoT Central アプリケーションをプログラムで管理する方法を学習したので、次に推奨される手順は、[Azure Resource Manager](../../azure-resource-manager/management/overview.md) サービスの学習です。
