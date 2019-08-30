---
title: Azure IoT Central アプリケーションを管理する | Microsoft Docs
description: 管理者として、アプリケーション名や URL を変更し、イメージをアップロードすることで Azure IoT Central アプリケーションを管理する方法と、アプリケーションをコピーおよび削除する方法について説明します。
author: viv-liu
ms.author: viviali
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: bdb13ef0d4f05802e4ab0f383c1bf3d82d9ba632
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879359"
---
# <a name="manage-your-iot-central-application"></a>Azure IoT Central アプリケーションを管理する

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

この記事では、管理者として、アプリケーション名や URL を変更し、イメージをアップロードすることでアプリケーションを管理する方法について説明します。また、Azure IoT Central アプリケーションでアプリケーションをコピーおよび削除する方法についても説明します。

**[Administration] (管理)** セクションにアクセスして使用するには、Azure IoT Central アプリケーションの**管理者**ロールが必要です。 Azure IoT Central アプリケーションを作成したユーザーは、自動的にそのアプリケーションの**管理者**ロールに割り当てられます。 

## <a name="change-application-name-and-url"></a>アプリケーションの名前と URL を変更する

**[アプリケーション設定]** ページで、アプリケーションの名前と URL を変更し、 **[保存]** を選択できます。

![[Application Settings] (アプリケーション設定) ページ](media/howto-administer-pnp/image0-a.png)

管理者がアプリケーション用のカスタム テーマを作成した場合、このページには UI で**アプリケーション名**を非表示にするオプションが含まれています。 これは、カスタム テーマ内のアプリケーション ロゴにアプリケーション名が含まれている場合に便利です。 詳細については、「[Customize the Azure IoT Central UI (Azure IoT Central の UI をカスタマイズする)](./howto-customize-ui-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)」を参照してください。

> [!Note]
> URL を変更した場合は、Azure IoT Central の別の顧客が古い URL を取得できます。 その場合、その URL は使用できなくなります。 URL を変更すると、古い URL は機能しなくなるため、使用する新しい URL をユーザーに通知する必要があります。

## <a name="prepare-and-upload-image"></a>イメージの準備とアップロード

アプリケーションのイメージを変更する場合は、「[イメージを準備して Azure IoT Central アプリケーションにアップロードする](howto-prepare-images.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)」を参照してください。

## <a name="copy-an-application"></a>アプリケーションをコピーする

デバイス インスタンス、デバイス データ履歴、およびユーザー データ以外の任意のアプリケーションのコピーを作成できます。 コピーは、課金対象となる従量課金制アプリケーションになります。 この方法で試用版アプリケーションを作成することはできません。

**[コピー]** を選択します。 ダイアログ ボックスに、新しい従量課金制アプリケーションの詳細を入力します。 **[コピー]** を選択して、続行することを確認します。 このフォームのフィールドの詳細については、[アプリケーションの作成](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)に関するクイック スタートを参照してください。

![[Application Settings] (アプリケーション設定) ページ](media/howto-administer-pnp/appcopy2.png)

アプリのコピー操作が成功したら、リンクを使用して新しいアプリケーションに移動できます。

![[Application Settings] (アプリケーション設定) ページ](media/howto-administer-pnp/appcopy3a.png)

アプリケーションをコピーすると、ルールやメール アクションの定義もコピーされます。 Flow、Logic Apps などの一部のアクションは、ルール ID を介して特定のルールに関連付けられています。 ルールが別のアプリケーションにコピーされると、そのルールに独自のルール ID が設定されます。 この場合、ユーザーは新しいアクションを作成してから、新しいルールを関連付ける必要があります。 一般には、ルールとアクションを調べ、それらが新しいアプリで最新の状態になっていることを確認することをお勧めします。

> [!WARNING]
> 特定のデバイスに関する情報を表示するタイルがダッシュボードに含まれている場合、それらのタイルに、新しいアプリケーションで**要求されたリソースが見つからなかった**ことが表示されます。 デバイスに関する情報が新しいアプリケーションに表示されるように、これらのタイルを再構成する必要があります。

## <a name="delete-an-application"></a>アプリケーションの削除

**[削除]** ボタンを使用して、IoT Central アプリケーションを完全に削除します。 この操作を行うと、そのアプリケーションに関連付けられているすべてのデータが完全に削除されます。

> [!Note]
> また、アプリケーションを削除するには、アプリケーションを作成したときに選択した Azure サブスクリプションのリソースを削除する許可も必要です。 詳細は、「[ロールベースのアクセス制御を使用して Azure サブスクリプション リソースへのアクセスを管理する](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)」を参照してください。


## <a name="manage-programatically"></a>プログラムによる管理

Node、Python、C#、フリガナ、Java、Go には､IoT Central の Azure Resource Manager SDK パッケージが用意されています｡ これらのパッケージを使用して、IoT Central アプリケーションを作成、一覧表示、更新、または削除することができます。 パッケージには、認証とエラー処理を管理するヘルパーが含まれています。

Azure Resource Manager SDK を使用する方法例については､[ https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples) をご覧ください｡

詳細については、以下の GitHub リポジトリとパッケージを参照してください。

| 言語 | リポジトリ | Package |
| ---------| ---------- | ------- |
| ノード | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>次の手順
 
ここまでで、Azure IoT Central アプリケーションを管理する方法について学習しました。次は、Azure IoT Central で[ユーザーとロールを管理する](howto-manage-users-roles-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)方法について学習することをお勧めします。