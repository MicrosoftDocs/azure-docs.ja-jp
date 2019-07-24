---
title: Azure Active Directory Domain Services:パスワード ポリシー | Microsoft Docs
description: マネージド ドメインに関するパスワード ポリシーについて説明します
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: ergreenl
ms.openlocfilehash: 513cccb8b83eb4a69df1bc6172f1f02485215e35
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58086005"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>マネージド ドメインに関するパスワードとアカウントのロックアウト ポリシー
この記事では、マネージド ドメインに関する既定のパスワード ポリシーについて説明します。 また、これらのポリシーの構成方法についても説明します。

## <a name="fine-grained-password-policies-fgpp"></a>細かい設定が可能なパスワード ポリシー (FGPP)
単一のドメイン内に多数のパスワード ポリシーを指定するには、細かい設定が可能なパスワード ポリシーを使用します。 FGPP を使用すると、ドメイン内のユーザー セットごとにパスワードとアカウントのロックアウト ポリシーに異なる制限を適用できます。 たとえば、特権アカウントに厳格なパスワード設定を適用することができます。

FGPP を使用して、次のパスワード設定を構成できます。
* パスワードの最小文字数
* パスワードの履歴
* 複雑さの要件を満たす必要があるパスワード
* パスワードの変更禁止期間
* パスワードの有効期間
* アカウント ロックアウト ポリシー
    * アカウントのロックアウト期間
    * 許可される失敗したログオン試行回数
    * 失敗したログオン試行回数のカウントがリセットされるまでの時間


## <a name="default-fine-grained-password-policy-settings-on-a-managed-domain"></a>マネージド ドメインに関する既定の細かい設定が可能なパスワード ポリシー設定
次のスクリーンショットは、Azure Active Directory Domain Services のマネージド ドメインに構成されている既定の細かい設定が可能なパスワード ポリシーを示しています。

![既定の細かい設定が可能なパスワード ポリシー](./media/how-to/default-fgpp.png)

> [!NOTE]
> 既定の組み込みの細かい設定が可能なパスワード ポリシーは変更または削除できません。 'AAD DC Administrators' グループのメンバーはカスタム FGPP を作成し、既定の組み込みの FGPP をオーバーライドする (既定の組み込みの FGPP よりも優先される) ように構成することができます。
>
>

## <a name="password-policy-settings"></a>パスワード ポリシー設定
マネージド ドメインでは、次のパスワード ポリシーが既定で構成されています。
* パスワードの最小文字数: 7
* パスワードの有効期間: 90 日間
* 複雑さの要件を満たす必要があるパスワード

### <a name="account-lockout-settings"></a>アカウント ロックアウトの設定
マネージド ドメインでは、既定で次のアカウント ロックアウト ポリシーが構成されています。
* アカウントのロックアウト期間: 30
* 許可される失敗したログオン試行回数: 5
* 失敗したログオン試行回数のカウントがリセットされるまでの時間: 30 分

2 分以内に、5 回の無効なパスワード使用が行われた場合、ユーザーアカウントは実際には 30 分間ロック アウトされます。 アカウントは、自動的に、30 分後にロック解除されます。


## <a name="create-a-custom-fine-grained-password-policy-fgpp-on-a-managed-domain"></a>マネージド ドメインに関するカスタムの細かい設定が可能なパスワード ポリシー (FGPP) を作成する
カスタムの FGPP を作成し、マネージド ドメイン内の特定のグループに適用することができます。 実際には、この構成によって、マネージド ドメイン用に構成された既定の FGPP が上書きされます。

> [!TIP]
> カスタムの細かい設定が可能なパスワード ポリシーを作成するアクセス許可を持っているのは、**'AAD DC Administrators'** グループのメンバーのみです。
>
>

また、カスタムの細かい設定が可能なパスワード ポリシーを作成し、マネージド ドメイン上で作成したカスタム OU に適用することもできます。

カスタムの FGPP は、次のような理由で構成することがあります。
* 別のアカウント ロックアウト ポリシーを設定する場合。
* マネージド ドメインの既定のパスワード有効期間設定を構成する場合。

マネージド ドメインに関するカスタム FGPP を作成する場合:
1. マネージド ドメインの管理に使用する Windows VM にサインインします。 マネージド ドメインがない場合は、[マネージド ドメインの管理](active-directory-ds-admin-guide-administer-domain.md)に関するページに記載されている手順を実行します。
2. VM 上で **Active Directory 管理センター**を起動します。
3. ドメイン名 ('contoso100.com' など) をクリックします。
4. **[システム]** をダブルクリックしてシステム コンテナーを開きます。
5. **[パスワード設定コンテナー]** ダブルクリックします。
6. **AADDSSTFPSO** というマネージド ドメインの既定の組み込みの FGPP が表示されます。 この組み込みの FGPP は変更できません。 ただし、新しいカスタム FGPP を作成して、既定の FGPP をオーバーライドすることができます。
7. 右側の **[タスク]** パネルで **[新規]** をクリックし、**[パスワード設定]** をクリックします。
8. **[Create Password Settings]\(パスワード設定の作成\)** ダイアログで、カスタムの FGPP の一環で適用されるカスタムのパスワード設定を指定します。 必ず既定の FGPP をオーバーライドするように優先順位を適切に設定してください。

   ![カスタムの FGPP を作成する](./media/how-to/custom-fgpp.png)

   > [!TIP]
   > **必ず [誤って削除されないように保護する] オプションをオフにしてください。** このオプションがオンの場合、FGPP を保存できません。
   >
   >

9. **[直接の適用先]** で **[追加]** ボタンをクリックします。 **[ユーザーまたはグループの選択]** ダイアログで、**[場所]** ボタンをクリックします。

   ![ユーザーとグループを選択](./media/how-to/fgpp-applies-to.png)

10. **[場所]** ダイアログでドメイン名を展開し、**[AADDC Users]** をクリックします。 これで、組み込みのユーザー OU から FGPP を適用するグループを選択できるようになります。

    ![グループが属する OU を選択する](./media/how-to/fgpp-container.png)

11. グループの名前を入力し、**[名前の確認]** ボタンをクリックしてグループが存在することを確認します。

    ![FGPP を適用するグループを選択する](./media/how-to/fgpp-apply-group.png)

12. **[直接の適用先]** セクションにグループの名前が表示されます。 **[OK]** ボタンをクリックしてこれらの変更を保存します。

    ![適用される FGPP](./media/how-to/fgpp-applied.png)

> [!TIP]
> **カスタムの OU のユーザー アカウントにカスタムのパスワード ポリシーを適用するには:** 細かい設定が可能なパスワード ポリシーはグループにのみ適用できます。 カスタムの OU のユーザーに対してのみカスタム パスワード ポリシーを構成するには、その OU 内のユーザーを含むグループを作成します。
>
>

## <a name="next-steps"></a>次の手順
* [Active Directory の細かい設定が可能なパスワード ポリシーについて学びます](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [AD 管理センターを使用して細かい設定が可能なパスワード ポリシーを構成します](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)
