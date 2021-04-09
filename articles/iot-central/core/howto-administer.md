---
title: Azure IoT Central アプリケーション設定を変更する | Microsoft Docs
description: 管理者としてアプリケーション名や URL を変更し、Azure IoT Central アプリケーションを管理する方法、イメージをアップロードする方法、アプリケーションを削除する方法について説明します。
author: viv-liu
ms.author: viviali
ms.date: 12/19/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 81cd2ca1cb47e6fdfb4858df930b73c1bd10118a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091825"
---
# <a name="change-iot-central-application-settings"></a>IoT Central アプリケーションの設定を変更する



この記事では、Azure IoT Central アプリケーションで、管理者としてアプリケーションの名前や URL を変更してアプリケーションを管理する方法、イメージをアップロードする方法、アプリケーションを削除する方法について説明します。

**[Administration] (管理)** セクションにアクセスして使用するには、Azure IoT Central アプリケーションの **管理者** ロールが必要です。 Azure IoT Central アプリケーションを作成したユーザーは、自動的にそのアプリケーションの **管理者** ロールに割り当てられます。

## <a name="change-application-name-and-url"></a>アプリケーションの名前と URL を変更する

**[アプリケーション設定]** ページで、アプリケーションの名前と URL を変更し、**[保存]** を選択できます。

![[Application Settings] (アプリケーション設定) ページ](media/howto-administer/image0-a.png)

管理者がアプリケーション用のカスタム テーマを作成した場合、このページには UI で **アプリケーション名** を非表示にするオプションが含まれています。 このオプションは、カスタム テーマ内のアプリケーション ロゴにアプリケーション名が含まれている場合に便利です。 詳細については、「[Customize the Azure IoT Central UI (Azure IoT Central の UI をカスタマイズする)](./howto-customize-ui.md)」を参照してください。

> [!Note]
> URL を変更した場合は、Azure IoT Central の別の顧客が古い URL を取得できます。 その場合、その URL は使用できなくなります。 URL を変更すると、古い URL は機能しなくなるため、使用する新しい URL をユーザーに通知する必要があります。

## <a name="delete-an-application"></a>アプリケーションの削除

**[削除]** ボタンを使用して、IoT Central アプリケーションを完全に削除します。 この操作を行うと、そのアプリケーションに関連付けられているすべてのデータが完全に削除されます。

> [!Note]
> また、アプリケーションを削除するには、アプリケーションを作成したときに選択した Azure サブスクリプションのリソースを削除する許可も必要です。 詳細については、「[Azure サブスクリプション リソースへのアクセスを管理するための Azure ロールの割り当て](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

## <a name="manage-programmatically"></a>プログラムで管理する

Node、Python、C#、フリガナ、Java、Go には､IoT Central の Azure Resource Manager SDK パッケージが用意されています｡ これらのパッケージを使用して、IoT Central アプリケーションを作成、一覧表示、更新、または削除することができます。 パッケージには、認証とエラー処理を管理するヘルパーが含まれています。

Azure Resource Manager SDK を使用する方法例については､[https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples](https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples) をご覧ください｡

詳細については、以下の GitHub リポジトリとパッケージを参照してください。

| Language | リポジトリ | Package |
| ---------| ---------- | ------- |
| Node | [https://github.com/Azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js) | [https://www.npmjs.com/package/@azure/arm-iotcentral](https://www.npmjs.com/package/@azure/arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>次のステップ

ここまでで、Azure IoT Central アプリケーションを管理する方法について学習しました。次は、Azure IoT Central で[ユーザーとロールを管理する](howto-manage-users-roles.md)方法について学習することをお勧めします。
