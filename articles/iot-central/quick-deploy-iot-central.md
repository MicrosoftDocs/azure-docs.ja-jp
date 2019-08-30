---
title: Azure IoT Central アプリケーションを作成する | Microsoft Docs
description: 新しい Azure IoT Central アプリケーションを作成します。 アプリケーション テンプレートを使用して、試用版や従量課金制のアプリケーションを作成します。
author: viv-liu
ms.author: viviali
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 78fb48d2584e94e5c38a4648f1ae41e566637601
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875578"
---
# <a name="create-an-azure-iot-central-application"></a>Azure IoT Central アプリケーションの作成

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

"_ビルダー_" は、Azure IoT Central の UI を使用して、Microsoft Azure IoT Central アプリケーションを定義します。 このクイック スタートでは、サンプル "_デバイス テンプレート_" およびシミュレートされた "_デバイス_" を含んだ Azure IoT Central アプリケーションを作成する方法について説明します。

## <a name="create-an-application"></a>アプリケーションの作成

Azure IoT Central の [[Application Manager]\(アプリケーション マネージャー\)](https://aka.ms/iotcentral) ページに移動します。 Microsoft 個人アカウントか、職場または学校のアカウントを使用してサインインする必要があります。

新しい Azure IoT Central アプリケーションの作成を開始するには、 **[New Application]\(新しいアプリケーション\)** を選択します。 これで、 **[アプリケーションの作成]** ページに移動します。

![Azure IoT Central の [Create Application]\(アプリケーションの作成\) ページ](media/quick-deploy-iot-central/iotcentralcreate.png)

新しい Azure IoT Central アプリケーションを作成するには:

1. 以下の支払プランを選択します。
   - **試用版**アプリケーションは、有効期限が切れるまでの 7 日間は無料となります。 有効期限内で、いつでも従量課金制に変更することができます。 **試用版**アプリケーションを作成する場合は、連絡先情報を入力し、Microsoft から情報やヒントを受信するかどうかを選択します。
   - **従量課金制**アプリケーションは、デバイスごとに課金され、最初の 5 個のデバイスは無料です。 **従量課金制**アプリケーションを作成する場合は、"*ディレクトリ*"、"*Azure サブスクリプション*"、および "*リージョン*" を選択する必要があります。
      - "*ディレクトリ*" は、アプリケーションを作成する Azure Active Directory (AD) です。 ユーザー ID、資格情報、およびその他の組織情報が含まれています。 Azure AD を持っていない場合は、Azure サブスクリプションを作成するときに自動的に作成されます。
      - "*Azure サブスクリプション*" を使用すると、Azure サービスのインスタンスを作成できます。 IoT Central では、リソースがサブスクリプション内にプロビジョニングされます。 Azure サブスクリプションがない場合は、[Azure サインアップ ページ](https://aka.ms/createazuresubscription)で作成できます。 Azure サブスクリプションを作成したら、 **[アプリケーションの作成]** ページに戻ります。 新しいサブスクリプションが **[Azure サブスクリプション]** ドロップダウンに表示されます。
      - "*リージョン*" は、アプリケーションを作成する物理的な場所です。 通常は、最適なパフォーマンスを得るために、デバイスに物理的に最も近いリージョンを選択する必要があります。Azure IoT Central を利用可能なリージョンは、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」ページで確認できます。 いったんリージョンを選択すると、後でアプリケーションを別のリージョンに移動することはできません。

      価格の詳細については、[Azure IoT Central の価格に関するページ](https://azure.microsoft.com/pricing/details/iot-central/)を参照してください。

1. アプリケーション テンプレートを選択します。 アプリケーション テンプレートには、使用開始に役立つデバイス テンプレートやダッシュボードなどの事前に定義された項目を含めることができます。

    | アプリケーション テンプレート | 説明 |
    | -------------------- | ----------- |
    | サンプル Contoso       | Refrigerated Vending Machine 用に既に作成したデバイス テンプレートを含むアプリケーションを作成します。 このテンプレートは、Azure IoT Central の調査を開始するために使用します。 |
    | サンプル Devkit       | MXChip または Raspberry Pi デバイスを接続するための準備ができたデバイス テンプレートを含むアプリケーションを作成します。 このテンプレートは、これらのいずれかのデバイスで実験しているデバイス開発者が使用します。 |
    | カスタム アプリケーション   | 独自のデバイス テンプレートおよびデバイスにデータを入力するための空のアプリケーションを作成します。 |

1. わかりやすいアプリケーション名を入力します (**Contoso IoT** など)。 Azure IoT Central によって、一意の URL プレフィックスが自動的に生成されます。 この URL プレフィックスは、もっと覚えやすいものに変更することができます。

1. **Create** をクリックしてください。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、IoT Central アプリケーションを作成しました。 推奨される次の手順は、以下のとおりです。

> [!div class="nextstepaction"]
> [IoT Central のツアー](overview-iot-central-tour.md)
