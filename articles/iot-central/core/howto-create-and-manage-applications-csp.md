---
title: Azure IoT Central アプリケーションを CSP ポータルから作成して管理する | Microsoft Docs
description: CSP として、顧客に代わって Azure IoT Central アプリケーションを作成する方法を説明します。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: f3293ada549351cc7273847cde48c0531f06f028
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675809"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Azure IoT Central アプリケーションを CSP ポータルから作成して管理する

Microsoft クラウド ソリューション プロバイダー (CSP) プログラムは、Microsoft リセラー プログラムです。 これは、すべての Microsoft Commercial Online Services を再販売するためのワンストップ プログラムをチャネル パートナーに提供することを目的としています。 詳しくは、「[Cloud Solution Provider プログラムの詳細](https://partner.microsoft.com/cloud-solution-provider)」をご覧ください。

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

CSP として、[Microsoft パートナー センター](https://partnercenter.microsoft.com/partner/home)を通じて、顧客に代わって Microsoft Azure IoT Central アプリケーションを作成して管理できます。 顧客に代わって CSP によって Azure IoT Central アプリケーションが作成される場合、Azure サービスによって管理される他の CSP と同様に、CSP が顧客の課金を管理します。 Azure IoT Central の料金は、Microsoft パートナー センターの請求金額の合計に表示されます。

最初に、Microsoft パートナー ポータルのアカウントにサインインし、Azure IoT Central アプリケーションを作成する顧客を選択します。 左側のナビゲーションから顧客のサービス管理に移動します。

![Microsoft パートナー センターのお客様が見る画面](media/howto-create-and-manage-applications-csp/image1.png)

Azure IoT Central は、管理に使用できるサービスとして表示されます。 ページの Azure IoT Central リンクを選択して新しいアプリケーションを作成するか、この顧客の既存のアプリケーションを管理します。

![管理できる Azure IoT Central](media/howto-create-and-manage-applications-csp/image2.png)

Azure IoT Central の [Application Manager]\(アプリケーション マネージャー\) ページに移動します。 Azure IoT Central では、ユーザーが Microsoft パートナー センターから来て、その特定の顧客を管理することになったというコンテキストが維持されます。 これは、[Application Manager]\(アプリケーション マネージャー\) ページのヘッダーで確認できます。 ここから、この顧客が管理するために前に作成した既存のアプリケーションに移動することも、この顧客のために新しいアプリケーションを作成することもできます。

![CSP のマネージャーの作成](media/howto-create-and-manage-applications-csp/image3.png)

Azure IoT Central アプリケーションを作成するには、左側のメニューの **[Build]\(ビルド\)** を選択します。 いずれかの業界テンプレートを選択するか、 **[カスタム アプリ]** を選択して最初からアプリケーションを作成します。 これによって、[Application Creation]\(アプリケーションの作成\) ページが読み込まれます。 このページのすべてのフィールドに入力してから、 **[作成]** を選択する必要があります。 詳しくは、以下の各フィールドを参照してください。

![[IoT アプリケーションをビルドする] ページを示すスクリーンショット。[ビルド] ボタンが選択されています。](media/howto-create-and-manage-applications-csp/image4.png)

![CSP 用のアプリケーションの作成ページ](media/howto-create-and-manage-applications-csp/image4-1.png)

![CSP 課金情報用のアプリケーションの作成ページ](media/howto-create-and-manage-applications-csp/image4-2.png)

## <a name="pricing-plan"></a>料金プラン

CSP として標準の料金プランが使用されているアプリケーションのみを作成できます。 Azure IoT Central を顧客に示すために、無料の料金プランが使用されるアプリケーションを別途作成できます。 無料と標準の料金プランについては、[Azure IoT Central の価格に関するページ](https://azure.microsoft.com/pricing/details/iot-central/)で確認できます。

CSP として標準の料金プランが使用されているアプリケーションのみを作成できます。 Azure IoT Central を顧客に示すために、無料の料金プランが使用されるアプリケーションを別途作成できます。 無料と標準の料金プランについては、[Azure IoT Central の価格に関するページ](https://azure.microsoft.com/pricing/details/iot-central/)で確認できます。

## <a name="application-name"></a>アプリケーション名

アプリケーションの名前は、 **[Application Manager] (アプリケーション マネージャー)** ページと、各 Azure IoT Central アプリケーション内に表示されます。 Azure IoT Central アプリケーションとして任意の名前を選択できます。 自分および組織内の他のユーザーにとって意味のある名前を選択してください。

## <a name="application-url"></a>アプリケーションの URL

アプリケーションの URL は、アプリケーションへのリンクです。 そのブックマークをブラウザー内に保存するか、またはそれを他のユーザーと共有できます。

アプリケーションの名前を入力すると、アプリケーションの URL が自動生成されます。 必要に応じて、アプリケーションの別の URL を選択できます。 各 Azure IoT Central URL は、Azure IoT Central 内で一意である必要があります。 選択した URL が既に取得されている場合は、エラー メッセージが表示されます。

## <a name="directory"></a>ディレクトリ

Azure IoT Central では、ユーザーが Microsoft パートナー ポータルで選択した顧客を管理することになったというコンテキストが維持されるため、ユーザーは単に ディレクトリ フィールド内にその顧客の Azure Active Directory テナントを確認するだけです。 

Azure Active Directory テナントには、ユーザー ID、資格情報、およびその他の組織情報が含まれています。 1 つの Azure Active Directory テナントに複数の Azure サブスクリプションを関連付けることができます。

詳細については、[Azure Active Directory](../../active-directory/index.yml) に関するページを参照してください。

## <a name="azure-subscription"></a>Azure サブスクリプション

Azure サブスクリプションを使用すると、Azure サービスのインスタンスを作成できます。 Azure IoT Central は、ユーザーがアクセスできるすべての顧客の Azure サブスクリプションを自動的に検索し、それを **[アプリケーションの作成]** ページのドロップダウンに表示します。 新しい Azure IoT Central アプリケーションを作成するための Azure サブスクリプションを選択します。

Azure サブスクリプションがない場合は、Microsoft パートナー センターで作成できます。 Azure サブスクリプションを作成したら、 **[アプリケーションの作成]** ページに戻ります。 新しいサブスクリプションが **[Azure サブスクリプション]** ドロップダウンに表示されます。

詳細については、[Azure サブスクリプション](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) に関するページを参照してください。

## <a name="location"></a>場所

"**場所**" は、アプリケーションを作成する [地域](https://azure.microsoft.com/global-infrastructure/geographies/)です。 通常、最適なパフォーマンスを得るには、ご利用のデバイスに物理的に最も近い場所を選択する必要があります。 現時点では、IoT Central アプリケーションは、**オーストラリア**、**アジア太平洋**、**ヨーロッパ**、または **米国**、**イギリス**、**日本** の地域で作成できます。 いったん場所を選択すると、後でアプリケーションを別の場所に移動することはできません。

## <a name="application-template"></a>アプリケーション テンプレート

アプリケーションに使用するアプリケーション テンプレートを選択します。

## <a name="next-steps"></a>次のステップ

ここでは、CSP として Azure IoT Central アプリケーションを作成する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [アプリケーションを管理する](howto-administer.md)