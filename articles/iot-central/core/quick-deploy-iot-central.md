---
title: Azure IoT Central アプリケーションを作成する | Microsoft Docs
description: 新しい Azure IoT Central アプリケーションを作成します。 無料プランまたはいずれかの標準料金プランを使用して、アプリケーションを作成します。
author: viv-liu
ms.author: viviali
ms.date: 07/30/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 4b939505f807385f235def2606d0f29564f5d08f
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552579"
---
# <a name="create-an-azure-iot-central-application"></a>Azure IoT Central アプリケーションの作成

このクイックスタートでは、Azure IoT Central アプリケーションを作成する方法を説明します。

## <a name="create-an-application"></a>アプリケーションの作成

[Azure IoT Central ビルド](https://aka.ms/iotcentral) Web サイトに移動します。 次に、Microsoft 個人アカウントか、職場または学校アカウントを使用してサインインします。

新しいアプリケーションを作成するには、業界に関連する IoT Central テンプレートの一覧を参照して、すばやく作業を開始することも、**カスタム アプリケーション** テンプレートを使用してゼロから始めることもできます。 このクイックスタートでは、**カスタム アプリケーション** テンプレートを使用します。

**カスタム アプリケーション** テンプレートから新しい Azure IoT Central アプリケーションを作成するには、次のようにします。

1. **[ビルド]** ページに移動します。

    ![「IoT アプリケーションのビルド」ページ](media/quick-deploy-iot-central/iotcentralcreate-new-application.png)

1. **[カスタム アプリ]** を選択し、**カスタム アプリケーション** テンプレートが選択されていることを確認します。

1. Azure IoT Central によって、選択したアプリケーション テンプレートに基づいて **アプリケーション名** が自動的に提案されます。 この名前をそのまま使用することも、独自のわかりやすいアプリケーション名を入力することもできます。

1. また、Azure IoT Central は、アプリケーション名に基づいて、一意の **アプリケーション URL** プレフィックスも生成します。 この URL は、アプリケーションにアクセスするために使用します。 必要に応じて、この URL プレフィックスをもっと覚えやすいものに変更します。

    ![Azure IoT Central の [アプリケーションの作成] ページ](media/quick-deploy-iot-central/iotcentralcreate-custom.png)

    ![Azure IoT Central の課金情報](media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png)

    > [!NOTE]
    > 前のページで **[カスタム アプリ]** を選択した場合は、 **[アプリケーション テンプレート]** ドロップダウンが表示されます。 ドロップダウンには、組織によってユーザーが使用できるようにされている他のテンプレートが表示される場合もあります。 

    >[!IMPORTANT]
    >**カスタム アプリケーション (レガシ)** テンプレート (V2) は廃止されました。これまで、レガシ アプリケーション テンプレートで使用できたすべての機能は、最新の**カスタム アプリケーション** テンプレート (V3) で使用できるようになっています。 
    
1. 7 日間の無料試用料金プラン、またはいずれかの標準料金プランを使用して、アプリケーションを作成することを選択します。

    - "*無料*" のプランを使用して作成したアプリケーションは 7 日間無料で、最大 5 台のデバイスがサポートされます。 期限切れになるまでは、いつでも標準の料金プランを使用するように変換できます。
    - "*標準*" のプランを使用して作成したアプリケーションは、デバイス単位で課金されます。**Standard 1** または **Standard 2** 料金プランから選択でき、どちらも最初の 2 つのデバイスが無料となります。 無料と標準の料金プランについては、[Azure IoT Central の価格に関するページ](https://azure.microsoft.com/pricing/details/iot-central/)で確認できます。 標準の料金プランを使用してアプリケーションを作成する場合は、"*ディレクトリ*"、"*Azure サブスクリプション*"、および "*場所*" を選択する必要があります。
        - "*ディレクトリ*" は、アプリケーションを作成する Azure Active Directory です。 Azure Active Directory には、ユーザー ID、資格情報、およびその他の組織情報が含まれています。 Azure Active Directory を持っていない場合は、Azure サブスクリプションを作成するときに自動的に作成されます。
        - "*Azure サブスクリプション*" を使用すると、Azure サービスのインスタンスを作成できます。 IoT Central では、リソースが自分のサブスクリプション内にプロビジョニングされます。 Azure サブスクリプションがない場合は、[Azure サインアップ ページ](https://aka.ms/createazuresubscription)で無料で作成できます。 Azure サブスクリプションを作成したら、 **[新しいアプリケーション]** ページに戻ります。 新しいサブスクリプションが **[Azure サブスクリプション]** ドロップダウンに表示されるようになりました。
        - "*場所*" は、アプリケーションを作成する[地域](https://azure.microsoft.com/global-infrastructure/geographies/)です。 通常、最適なパフォーマンスを得るには、ご利用のデバイスに物理的に最も近い場所を選択する必要があります。 いったん場所を選択すると、後でアプリケーションを別の場所に移動することはできません。

1. 使用条件を確認し、ページの下部にある **[作成]** を選択します。 数分後に、IoT Central アプリケーションを使用できるようになります。

    ![Azure IoT Central アプリケーション](media/quick-deploy-iot-central/iotcentral-application.png)

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、IoT Central アプリケーションを作成しました。 IoT Central についての学習を続ける場合、推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [Azure IoT Central アプリケーションにシミュレートされたデバイスを追加する](./quick-create-simulated-device.md)

デバイス開発者として、一部のコードについて詳しく調べる場合、推奨される次の手順は以下のとおりです。
> [!div class="nextstepaction"]
> [クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](./tutorial-connect-device-nodejs.md)
