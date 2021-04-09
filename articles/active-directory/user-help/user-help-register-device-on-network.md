---
title: 個人デバイスを組織のネットワークに登録する - Azure AD
description: 組織の保護されたリソースにアクセスできるように、個人デバイスを組織のネットワークに登録する方法について説明します。
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/31/2020
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 0435b99525c34eb72d7cc5315ccb4359859cd528
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90033049"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>個人デバイスを組織のネットワークに登録する
個人デバイス (通常は電話またはタブレット) を組織のネットワークに登録します。 デバイスが登録されたら、そのデバイスは組織の制限されたリソースにアクセスできるようになります。

>[!Note]
>この記事ではデモンストレーションのために Windows デバイスを使用しますが、iOS、Android、または macOS を実行しているデバイスも登録できます。

## <a name="what-happens-when-you-register-your-device"></a>デバイスを登録したときに実行される内容
デバイスを組織のネットワークに登録している間、次のアクションが実行されます。

- Windows がデバイスを組織のネットワークに登録します。

- オプションで、組織の選択に基づいて、[2 要素認証](multi-factor-authentication-end-user-first-time.md)または[セキュリティ情報](./security-info-setup-signin.md)のどちらかを使用して 2 段階認証を設定するよう求められることがあります。

- オプションで、組織の選択に基づいて、Microsoft Intune などのモバイル デバイス管理で自動的に登録されることがあります。 Microsoft Intune での登録の詳細については、[Intune でのデバイスの登録](/intune-user-help/enroll-your-device-in-intune-all)に関するページを参照してください。

- 職場または学校のアカウントのユーザー名とパスワードを使用して、サインイン プロセスを実行します。

## <a name="to-register-your-windows-device"></a>Windows デバイスを登録するには

個人デバイスをネットワークに登録するには、次の手順に従います。

1. **[設定]** を開いてから、 **[アカウント]** を選択します。

    ![[設定] 画面上の [アカウント]](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. **[職場または学校にアクセスする]** を選択し、 **[職場または学校にアクセスする]** 画面から **[接続]** を選択します。

    ![[接続] オプションが強調表示された [職場または学校にアクセスする] 画面](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. **[職場または学校用のアカウントを追加する]** 画面で、職場または学校のアカウントの電子メール アドレスを入力し、 **[次へ]** を選択します。 たとえば、「 alain@contoso.com 」のように入力します。

4. 職場または学校アカウントでサインインし、 **[サインイン]** を選択します。

5. 本人確認要求の承認 (2 段階認証を使用する場合) や Windows Hello の設定 (必要な場合) など、登録プロセスの残りを完了します。

## <a name="to-verify-that-youre-registered"></a>登録されていることを確認するには
設定を参照することによって、登録されたことを確認できます。

1. **[設定]** を開いてから、 **[アカウント]** を選択します。

    ![[設定] 画面上の [アカウント]](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. **[職場または学校にアクセスする]** を選択し、自身の職場または学校のアカウントが表示されていることを確認します。

    ![接続されている contoso アカウントが表示された [職場または学校にアクセスする] 画面](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>次のステップ
個人デバイスを組織のネットワークに登録したら、ほとんどのリソースにアクセスできるようになります。

- 組織で職場のデバイスを参加させるようにする場合は、「[職場のデバイスを組織のネットワークに参加させる](user-help-join-device-on-network.md)」を参照してください。