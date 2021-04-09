---
title: クイックスタート - Azure IoT Central アプリケーションを作成する | Microsoft Docs
description: クイックスタート - 新しい Azure IoT Central アプリケーションを作成します。 無料プランまたはいずれかの標準料金プランを使用して、アプリケーションを作成します。
author: viv-liu
ms.author: viviali
ms.date: 12/28/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: a1492593c24c2bc350205536006b648608cc5e46
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712642"
---
# <a name="quickstart---create-an-azure-iot-central-application"></a>クイックスタート - Azure IoT Central アプリケーションを作成する

このクイックスタートでは、Azure IoT Central アプリケーションを作成する方法を説明します。

## <a name="prerequisite"></a>前提条件 

"*共同作成者*" アクセス権を備えた Azure サブスクリプションが必要です。

## <a name="create-an-application"></a>アプリケーションの作成

[Azure IoT Central ビルド](https://aka.ms/iotcentral) Web サイトに移動します。 次に、Microsoft 個人アカウントか、職場または学校アカウントを使用してサインインします。

新しいアプリケーションを作成するには、業界に関連する IoT Central テンプレートの一覧を参照して、すばやく作業を開始することも、**カスタム アプリケーション** テンプレートを使用してゼロから始めることもできます。 このクイックスタートでは、**カスタム アプリケーション** テンプレートを使用します。

**カスタム アプリケーション** テンプレートから新しい Azure IoT Central アプリケーションを作成するには、次のようにします。

1. **[ビルド]** ページに移動します。

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-new-application.png" alt-text="「IoT アプリケーションのビルド」ページ":::

1. **[カスタム アプリ]** を選択し、**カスタム アプリケーション** テンプレートが選択されていることを確認します。

1. Azure IoT Central によって、選択したアプリケーション テンプレートに基づいて **アプリケーション名** が自動的に提案されます。 この名前をそのまま使用することも、独自のわかりやすいアプリケーション名を入力することもできます。

1. また、Azure IoT Central は、アプリケーション名に基づいて、一意の **アプリケーション URL** プレフィックスも生成します。 この URL は、アプリケーションにアクセスするために使用します。 必要に応じて、この URL プレフィックスをもっと覚えやすいものに変更します。

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-custom.png" alt-text="Azure IoT Central の [アプリケーションの作成] ページ":::

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png" alt-text="Azure IoT Central の課金情報":::

    > [!Tip]
    > 前のページで **[カスタム アプリ]** を選択した場合は、 **[アプリケーション テンプレート]** ドロップダウンが表示されます。 ドロップダウンには、組織によってユーザーが使用できるようにされている他のテンプレートが表示される場合もあります。

1. 7 日間の無料試用料金プラン、またはいずれかの標準料金プランを使用して、アプリケーションを作成することを選択します。

    - "*無料*" のプランを使用して作成したアプリケーションは 7 日間無料で、最大 5 台のデバイスがサポートされます。 期限切れになるまでは、いつでも標準の料金プランを使用するように変換できます。
        > [!NOTE]
        > "*無料*" プランを使用して作成されたアプリケーションは、Azure サブスクリプションを必要としないため、Azure portal の Azure サブスクリプション一覧にも表示されません。 無料のアプリは、IoT Central ポータルからのみ表示して管理することができます。          
    - "*標準*" のプランを使用して作成したアプリケーションは、デバイス単位で課金されます。**Standard 0**、**Standard 1**、**Standard 2** のいずれかの価格プランから選択でき、どちらも最初の 2 つのデバイスが無料となります。 無料と標準の料金プランについては、[Azure IoT Central の価格に関するページ](https://azure.microsoft.com/pricing/details/iot-central/)で確認できます。 標準の料金プランを使用してアプリケーションを作成する場合は、"*ディレクトリ*"、"*Azure サブスクリプション*"、および "*場所*" を選択する必要があります。
        - "*ディレクトリ*" は、アプリケーションを作成する Azure Active Directory です。 Azure Active Directory には、ユーザー ID、資格情報、およびその他の組織情報が含まれています。 Azure Active Directory を持っていない場合は、Azure サブスクリプションを作成するときに自動的に作成されます。
        - "*Azure サブスクリプション*" を使用すると、Azure サービスのインスタンスを作成できます。 IoT Central では、リソースが自分のサブスクリプション内にプロビジョニングされます。 Azure サブスクリプションがない場合は、[Azure サインアップ ページ](https://aka.ms/createazuresubscription)で無料で作成できます。 Azure サブスクリプションを作成したら、 **[新しいアプリケーション]** ページに戻ります。 新しいサブスクリプションが **[Azure サブスクリプション]** ドロップダウンに表示されるようになりました。
        - "*場所*" は、アプリケーションを作成する [地域](https://azure.microsoft.com/global-infrastructure/geographies/)です。 通常、最適なパフォーマンスを得るには、ご利用のデバイスに物理的に最も近い場所を選択する必要があります。 いったん場所を選択すると、後でアプリケーションを別の場所に移動することはできません。

1. 使用条件を確認し、ページの下部にある **[作成]** を選択します。 数分後に、IoT Central アプリケーションを使用できるようになります。

    :::image type="content" source="media/quick-deploy-iot-central/iotcentral-application.png" alt-text="Azure IoT Central アプリケーション":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、IoT Central アプリケーションを作成しました。 IoT Central についての学習を続ける場合、推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [Azure IoT Central アプリケーションにシミュレートされたデバイスを追加する](./quick-create-simulated-device.md)

デバイス開発者として、一部のコードについて詳しく調べる場合、推奨される次の手順は以下のとおりです。
> [!div class="nextstepaction"]
> [クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](./tutorial-connect-device.md)