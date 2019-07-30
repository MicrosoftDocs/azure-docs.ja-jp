---
title: Azure AD ドメイン サービスで Secure LDAP (LDAPS) を有効にする | Microsoft Docs
description: Azure AD Domain Services のマネージド ドメインに対して Secure LDAP (LDAPS) を有効にする
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: iainfou
ms.openlocfilehash: 631689d28399c07330c7f57b08952961973d1a92
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67474321"
---
# <a name="enable-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services のマネージド ドメインに対してセキュリティで保護された LDAP (LDAPS) を有効にする

## <a name="before-you-begin"></a>開始する前に
"[タスク 2 - セキュリティで保護された LDAP 証明書を .PFX ファイルにエクスポートする](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)" を完了します。


## <a name="task-3-enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>タスク 3:Azure portal を使用して、マネージド ドメインに対してセキュリティで保護された LDAP を有効にする
セキュリティで保護された LDAP を有効にするには、次の構成手順を実行します。

1. **[Azure Portal](https://portal.azure.com)** に移動します。

2. **[リソースの検索]** 検索ボックスで 'domain services' を検索します。 検索結果から **[Azure AD Domain Services]** を選択します。 **[Azure AD Domain Services]** ページには、マネージド ドメインが一覧表示されます。

    ![プロビジョニング中のマネージド ドメインを見つける](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. ドメインの詳細を表示するには、マネージド ドメインの名前 (例: "contoso100.com") をクリックします。

    ![Domain Services - プロビジョニング状態](./media/getting-started/domain-services-provisioning-state.png)

3. ナビゲーション ウィンドウで **[Secure LDAP]** をクリックします。

    ![Domain Services - [Secure LDAP] ページ](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. 既定では、セキュリティで保護された LDAP を利用してマネージド ドメインにアクセスする機能は無効になっています。 **[Secure LDAP]** を **[有効にする]** に切り替えます。

    ![セキュリティで保護された LDAP を有効にする](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. 既定では、セキュリティで保護された LDAP を利用し、インターネット経由でマネージド ドメインにアクセスする機能は無効になっています。 必要に応じて、 **[インターネット経由での Secure LDAP アクセスを許可]** を **[有効にする]** に切り替えます。

    > [!WARNING]
    > インターネット経由での Secure LDAP アクセスを有効にすると、インターネットを介してパスワードのブルート フォース攻撃を受けやすくなります。 そのため、必要なソース IP アドレス範囲へのアクセスをロック ダウンするように NSG を設定することをお勧めします。 [LDAPS を利用し、インターネット経由でマネージド ドメインにアクセスする機能を停止する](active-directory-ds-ldaps-bind-lockdown.md#task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet)手順を参照してください。
    >

6. **[Secure LDAP 証明書が入った .PFX ファイル]** に続くフォルダー アイコンをクリックします。 セキュリティで保護された LDAP アクセスでマネージド ドメインにアクセスするために、Secure LDAP 証明書が入った .PFX ファイルのパスを指定します。

7. **[PFX ファイルの暗号化を解除するためのパスワード]** を指定します。 証明書を PFX ファイルにエクスポートするときに使用したパスワードを入力します。

8. 終了したら、 **[保存]** ボタンをクリックします。

9. セキュリティで保護された LDAP がマネージド ドメインに構成されたことが通知されます。 この操作が完了するまでは、ドメインのその他の設定を変更できません。

    ![マネージド ドメインに対してセキュリティで保護された LDAP を構成する](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> マネージド ドメインに対してセキュリティで保護された LDAP を有効にするには、約 10 - 15 分かかります。 指定した LDAPS 証明書が必須条件を満たしていない場合、ディレクトリに対してセキュリティで保護された LDAP は有効化されず、エラーが表示されます。 たとえば、ドメイン名が正しくない場合や、証明書の有効期限が切れているか、まもなく切れる場合です。 その場合、有効な証明書で再試行してください。
>
>

## <a name="next-step"></a>次のステップ
[タスク 4: インターネットからマネージド ドメインにアクセスできるように DNS を構成する](active-directory-ds-ldaps-configure-dns.md)
