---
title: 職場のデバイスを組織のネットワークに参加させる - AD
description: 職場のデバイスを組織のネットワークに参加させる方法について説明します。
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/03/2018
ms.author: curtand
ms.reviewer: jairoc
ms.openlocfilehash: efb3ee24add847baf8264eccdf71278be5ee9496
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91536972"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>職場のデバイスを組織のネットワークに参加させる
制限されている可能性があるリソースにアクセスできるように、職場所有の Windows 10 デバイスを組織のネットワークに参加させます。

## <a name="what-happens-when-you-join-your-device"></a>デバイスを参加させたときに実行される内容
Windows 10 デバイスを組織のネットワークに参加させている間、次のアクションが実行されます。

- Windows がデバイスを組織のネットワークに登録して、ユーザーが個人アカウントを使用してリソースにアクセスできるようにします。 デバイスが登録されたら、ユーザーが組織のユーザー名とパスワードを使用してサインインし、制限されたリソースにアクセスできるように、Windows は次にデバイスをネットワークに参加させます。

- オプションで、組織の選択に基づいて、[Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md) または[セキュリティ情報](./security-info-setup-signin.md)のどちらかを使用して 2 段階認証を設定するよう求められることがあります。

- オプションで、組織の選択に基づいて、Microsoft Intune などのモバイル デバイス管理で自動的に登録されることがあります。 Microsoft Intune での登録の詳細については、[Intune でのデバイスの登録](/intune-user-help/enroll-your-device-in-intune-all)に関するページを参照してください。

- 組織のアカウントで自動サインインを使用して、サインイン プロセスを実行します。

## <a name="to-join-a-brand-new-windows-10-device"></a>新しい Windows 10 デバイスを参加させるには
デバイスが新しく、まだ設定されていない場合は、Windows OOBE (Out of Box Experience) プロセスを実行してデバイスをネットワークに参加させることができます。

1. 新しいデバイスを起動し、OOBE プロセスを開始します。

2. **[Microsoft アカウントでサインイン]** 画面で、職場または学校のメール アドレスを入力します。

    ![メール アドレスが表示されたサインイン画面](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. **[パスワードの入力]** 画面で、パスワードを入力します。

    ![[パスワードの入力] 画面](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. モバイル デバイスでは、アカウントにアクセスできるようにデバイスを承認します。 

    ![[パスワードの入力] 通知画面を示すスクリーンショット。](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. プライバシーの設定や Windows Hello の設定 (必要な場合) など、OOBE プロセスを完了します。

    これで、デバイスが組織のネットワークに参加しました。

## <a name="to-make-sure-youre-joined-new-device"></a>参加したことを確認するには (新しいデバイスの場合)
設定を参照することによって、参加したことを確認できます。

1. **[設定]** を開いてから、 **[アカウント]** を選択します。

    ![[設定] 画面上の [アカウント]](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. **[職場または学校にアクセスする]** を選択し、 **[ *\<your_organization>* Azure AD に接続しました]** のようなテキストが表示されることを確認します。

    ![[職場または学校にアクセスする] ウィンドウを示すスクリーンショット。[(組織の) Azure AD に接続しました] のアカウントが選択されています。](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>既に構成されている Windows 10 デバイスを参加させるには
デバイスが一定期間使用されており、既に設定されている場合は、次の手順に従ってデバイスをネットワークに参加させることができます。

1. **[設定]** を開いてから、 **[アカウント]** を選択します。

2. **[職場または学校にアクセスする]** を選択してから、 **[接続]** を選択します。

    ![[職場または学校にアクセスする] および [接続] リンク](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. **[職場または学校アカウントの設定]** 画面で、 **[このデバイスを Azure Active Directory に参加させる]** を選択します。

    ![[職場または学校アカウントの設定] 画面](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. **[サインインしましょう]** 画面で、メール アドレス (alain@contoso.com など) を入力し、 **[次へ]** を選択します。

    ![[サインインしましょう] 画面](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. **[パスワードの入力]** 画面で、パスワードを入力し、 **[サインイン]** を選択します。

    ![パスワードの入力](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. モバイル デバイスでは、アカウントにアクセスできるようにデバイスを承認します。 

    ![モバイル通知画面](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. **[これがあなたの組織のネットワークであることを確認してください]** 画面で、情報が正しいことを確認してから、 **[参加]** を選択します。

    ![[これがあなたの組織のネットワークであることを確認してください] 確認画面](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. **[これで完了です]** 画面で、 **[完了]** をクリックします。

    ![[これで完了です] 画面](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>参加したことを確認するには
設定を参照することによって、参加したことを確認できます。

1. **[設定]** を開いてから、 **[アカウント]** を選択します。

    ![[設定] 画面上の [アカウント]](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. **[職場または学校にアクセスする]** を選択し、 **[ *\<your_organization>* Azure AD に接続しました]** のようなテキストが表示されることを確認します。

    ![接続されている contoso アカウントが表示された [職場または学校にアクセスする] 画面](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>次のステップ
デバイスを組織のネットワークに参加させたら、職場または学校アカウントの情報を使用してすべてのリソースにアクセスできるようになります。

- 組織で電話などの個人デバイスを登録させるようにする場合は、「[個人デバイスを組織のネットワークに登録する](user-help-register-device-on-network.md)」を参照してください。

- 組織が Microsoft Intune を使用して管理されていて、登録、サインイン、またはその他の Intune 関連の問題について質問がある場合は、[Intune ユーザー ヘルプ コンテンツ](/intune-user-help/use-managed-devices-to-get-work-done)を参照してください。