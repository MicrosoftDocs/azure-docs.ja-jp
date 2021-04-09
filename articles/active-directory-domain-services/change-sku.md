---
title: Azure AD Domain Services の SKU を変更する | Microsoft Docs
description: ビジネス要件が変わった場合に Azure AD Domain Services マネージド ドメインの SKU レベルを確認する方法について説明します
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 320bd87aa78d26cee44c48f27365febd1dd426ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620292"
---
# <a name="change-the-sku-for-an-existing-azure-active-directory-domain-services-managed-domain"></a>既存の Azure Active Directory Domain Services マネージド ドメインの SKU を変更する

Azure Active Directory Domain Services (Azure AD DS) で使用できるパフォーマンスと機能は、SKU の種類によって変わります。 このような機能の違いには、バックアップの頻度や、一方向の送信フォレストの信頼の最大数などがあります。

マネージド ドメインを作成するときに SKU を選択する必要があり、マネージド ドメインのデプロイ後にビジネス ニーズが変わったときに SKU を上や下に切り替えることができます。 ビジネス要件の変更には、バックアップ頻度を増やしたり、追加のフォレストの信頼を作成したりするニーズなどがあります。 さまざまな SKU の制限と価格の詳細については、[Azure AD DS SKU の概念][concepts-sku]と [Azure AD DS の価格][pricing]に関するページを参照してください。

この記事では、Azure portal を使用して、既存の Azure AD DS マネージド ドメインの SKU を変更する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要に応じて、チュートリアルを完了し、[マネージド ドメインを作成して構成します][create-azure-ad-ds-instance]。

## <a name="sku-change-limitations"></a>SKU の変更の制限

マネージド ドメインがデプロイされた後は、SKU を引き上げたり引き下げたりして変更できます。 ただし、リソース フォレストを使用し、Azure AD DS からオンプレミスの AD DS 環境への一方向の送信フォレストの信頼を作成する場合、SKU の変更操作にはいくつかの制限があります。 *Premium* および *Enterprise* SKU には、作成できる信頼の数に関する制限が定義されています。 現在構成されている上限よりも低い上限の SKU に変更することはできません。

次に例を示します。

* *Premium* SKU に 2 つのフォレストの信頼を作成した場合、*Standard* SKU に変更することはできません。 *Standard* SKU は、フォレストの信頼をサポートしていません。
* また、*Premium* SKU に 7 つの信頼を作成した場合、*Enterprise* SKU に変更することはできません。 *Enterprise SKU* は、最大 5 つの信頼をサポートしています。

これらの制限の詳細については、[Azure AD DS SKU の機能と制限][concepts-sku]に関する記事を参照してください。

## <a name="select-a-new-sku"></a>新しい SKU を選択する

Azure portal を使用してマネージド ドメインの SKU を変更するには、次の手順を実行します。

1. Azure portal の上部で、**Azure AD Domain Services** を検索して選択します。 *aaddscontoso.com* などのマネージド ドメインを一覧から選択します。
1. Azure AD DS ページの左側にあるメニューで、 **[設定] > [SKU]** を選択します。

    ![Azure portal で Azure AD DS マネージド ドメインの [SKU] メニュー オプションを選択します。](media/change-sku/overview-change-sku.png)

1. ドロップダウン メニューから、マネージド ドメインに使用する SKU を選択します。 リソース フォレストがある場合、フォレストの信頼は *Enterprise* SKU 以上でのみ使用できるため、*Standard* SKU を選択できません。

    ドロップダウン メニューから目的の SKU を選択し、 **[保存]** を選択します。

    ![Azure portal のドロップダウン メニューから必要な SKU を選択します。](media/change-sku/change-sku-selection.png)

SKU の種類の変更には、1、2 分かかることがあります。

## <a name="next-steps"></a>次のステップ

リソース フォレストがあり、SKU の変更後に追加の信頼を作成する場合は、[Azure AD DS でのオンプレミス ドメインに対する送信フォレストの作成][create-trust]に関する記事を参照してください。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
