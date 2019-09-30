---
title: Azure IoT Central アプリケーションを作成する | Microsoft Docs
description: 新しい Azure IoT Central アプリケーションを作成します。 アプリケーション テンプレートを使用して、試用版や従量課金制のアプリケーションを作成します。
author: viv-liu
ms.author: viviali
ms.date: 06/07/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 9d242c48068e96498a811f52dbc599abd32bc936
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383002"
---
# <a name="create-an-azure-iot-central-application-preview-features"></a>Azure IoT Central アプリケーションを作成する (プレビュー機能)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

このクイックスタートでは、IoT プラグ アンド プレイなどのプレビュー機能を使用する Azure IoT Central アプリケーションを作成する方法について説明します。

> [!WARNING]
> Azure IoT Central の IoT プラグ アンド プレイ機能は、現在パブリック プレビューの段階にあります。 運用環境のワークロードに、IoT プラグ アンド プレイ対応の IoT Central アプリケーションを使用しないでください。 運用環境向けには、現在一般公開されているアプリケーション テンプレートから作成された IoT Central アプリケーションを使用してください。

## <a name="create-an-application"></a>アプリケーションの作成

[Azure IoT Central のアプリケーション マネージャー](https://aka.ms/iotcentral) Web サイトに移動します。 次に、Microsoft 個人アカウントか、職場または学校アカウントを使用してサインインします。

新しい Azure IoT Central アプリケーションの作成を開始するには、 **[New Application]\(新しいアプリケーション\)** を選択します。 このリンクをクリックすると、 **[アプリケーションの作成]** ページが表示されます。

![Azure IoT Central の [アプリケーションの作成] ページ](media/quick-deploy-iot-central-pnp/iotcentralcreate.png)

IoT プラグ アンド プレイなどのプレビュー機能を備えた新しい Azure IoT Central アプリケーションを作成するには:

1. 以下の支払プランを選択します。
   - **試用版**アプリケーションは、有効期限が切れるまでの 7 日間は無料となります。 有効期限内で、いつでも従量課金制に変更することができます。 **試用版**アプリケーションを作成する場合は、連絡先情報を入力し、Microsoft から情報やヒントを受信するかどうかを選択します。
   - **従量課金制**アプリケーションは、デバイスごとに課金され、最初の 5 個のデバイスは無料です。 **従量課金制**アプリケーションを作成する場合は、"*ディレクトリ*"、"*Azure サブスクリプション*"、および "*リージョン*" を選択する必要があります。
      - "*ディレクトリ*" は、アプリケーションを作成する Azure Active Directory (AD) です。 ユーザー ID、資格情報、およびその他の組織情報が含まれています。 Azure AD を持っていない場合は、Azure サブスクリプションを作成するときに自動的に作成されます。
      - "*Azure サブスクリプション*" を使用すると、Azure サービスのインスタンスを作成できます。 IoT Central では、リソースがサブスクリプション内にプロビジョニングされます。 Azure サブスクリプションがない場合は、[Azure サインアップ ページ](https://aka.ms/createazuresubscription)で作成できます。 Azure サブスクリプションを作成したら、 **[アプリケーションの作成]** ページに戻ります。 新しいサブスクリプションが **[Azure サブスクリプション]** ドロップダウンに表示されます。
      - "*リージョン*" は、アプリケーションを作成する物理的な場所です。 パブリック プレビュー期間中に、**プレビュー アプリケーション**で使用できるリージョンは、**北ヨーロッパ**と**米国中部**のみです。

      価格の詳細については、[Azure IoT Central の価格に関するページ](https://azure.microsoft.com/pricing/details/iot-central/)を参照してください。

1. わかりやすいアプリケーション名を選びます (**Contoso IoT** など)。 Azure IoT Central によって、一意の URL プレフィックスが自動的に生成されます。 この URL プレフィックスは、もっと覚えやすいものに変更することができます。

1. **[プレビュー アプリケーション]** テンプレートを選択します。 アプリケーション テンプレートには、使用開始に役立つデバイス テンプレートやダッシュボードなどの事前に定義された項目を含めることができます。

1. ページの下部にある **[作成]** を選択します。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、プレビュー機能を使用する IoT Central アプリケーションを作成しました。 お勧めする次の手順は、次のとおりです。

> [!div class="nextstepaction"]
> [Azure IoT Central アプリケーションで新しいデバイスの種類を定義する](./tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
