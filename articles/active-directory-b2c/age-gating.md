---
title: Azure Active Directory B2C で年齢制限を有効にする | Microsoft Docs
description: アプリケーションを使用している未成年者を特定する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 71a3b38da6a63824a42f64052bf16a5fe0e25483
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102525438"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Azure Active Directory B2C で年齢制限を有効にする

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) の年齢制限を使うと、保護者の同意の有無にかかわらず、アプリケーションを使おうとしている未成年者を識別することができます。 未成年者がアプリケーションにサインインするのをブロックできます。 または、ユーザーがサインインを完了できるようにしたり、アプリケーションに未成年者の状態を提供したりできます。 

>[!IMPORTANT]
>この機能はパブリック プレビュー段階にあります。 運用アプリケーションにはこの機能を使わないでください。
>

ユーザー フローに対して年齢制限が有効になっている場合、ユーザーは、誕生日と居住国を入力するよう求められます。 サインインしているユーザーが以前にその情報を入力していない場合、次にサインインするときに入力する必要があります。 ユーザーがサインインするたびに、ルールが適用されます。

![年齢制限情報の収集フローのスクリーンショット](./media/age-gating/age-gating-information-gathering.png)

Azure AD B2C では、ユーザーが入力した情報を使って、ユーザーが未成年かどうかが識別されます。 その後、アカウントの **AgeGroup** フィールドが更新されます。 値は、`null`、`Undefined`、`Minor`、`Adult`、`NotAdult` のいずれかです。  その後、**AgeGroup** フィールドと **consentProvidedForMinor** フィールドを使って、**legalAgeGroupClassification** の値が計算されます。


## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="set-up-your-tenant-for-age-gating"></a>年齢制限用にテナントを設定する

ユーザー フローで年齢制限を使うには、追加のプロパティが含まれるようにテナントを構成する必要があります。

1. 上部メニューの **[ディレクトリ + サブスクリプション]** フィルターを選択して、Azure AD B2C テナントを含むディレクトリを使っていることを確認します。 テナントが含まれるディレクトリを選択します。
1. Azure portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. 左側のメニューで、テナントの **[プロパティ]** を選択します。
1. **[年齢制御]** で、 **[構成]** を選択します。
1. 操作が完了するまで待ちます。テナントは年齢制限対応に設定されます。

::: zone pivot="b2c-user-flow"

## <a name="enable-age-gating-in-your-user-flow"></a>ユーザー フローで年齢制限を有効にする

年齢制限を使うようにテナントを設定した後は、それが有効になっている[ユーザー フロー](user-flow-versions.md)でこの機能を使用できます。 次の手順に従って、年齢制限を有効にします。

1. 年齢制限が有効になっているユーザー フローを作成します。
1. ユーザー フローを作成した後、メニューで **[プロパティ]** を選択します。
1. **[年齢制御]** セクションで、 **[有効]** を選択します。
1. **[サインアップまたはサインイン]** では、ユーザーの管理方法を選択します。
    - 未成年者にアプリケーションへのアクセスを許可します。
    - アプリケーションへのアクセスを承諾年齢未満の未成年に限ってブロックします。
    - すべての未成年者によるアプリケーションへのアクセスをブロックします。
1. **[ブロック時]** の場合は、以下のオプションのいずれかを選択します。
    - **[JSON をアプリケーションに送り返します]** - このオプションでは、未成年者がブロックされたことを示す応答がアプリケーションに返送されます。
    - **[エラー ページを表示します]** - アプリケーションにアクセスできないことを知らせるページがユーザーに表示されます。

## <a name="test-your-user-flow"></a>ユーザー フローをテストする

1. ポリシーをテストするには、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** ボタンを選択します。
1. ローカルまたはソーシャル アカウントを使用してサインインします。 次に、居住地の国を選択し、未成年者をシミュレートする生年月日を選択します。 
1. テストを繰り返し、成人をシミュレートする生年月日を選択します。  

未成年者としてサインインすると、次のエラー メッセージが表示されます: *サインオンはブロックされています。お住まいの国では、プライバシーとオンラインの安全性に関する法律により子どものアカウントにはアクセスできません。*

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="enable-age-gating-in-your-custom-policy"></a>カスタム ポリシーで年齢制限を有効にする

1. [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/age-gating)の年齢制限ポリシーの例を取得します。
1. 各ファイル内で、文字列 `yourtenant` を、使用している Azure AD B2C テナントの名前に置き換えます。 たとえば、B2C テナントの名前が *contosob2c* であれば、`yourtenant.onmicrosoft.com` のすべてのインスタンスは `contosob2c.onmicrosoft.com` になります。
1. ポリシー ファイルをアップロードします。

::: zone-end

## <a name="next-steps"></a>次のステップ

- [Active Directory B2C におけるユーザー アクセスの管理](manage-user-access.md)方法を確認します。

