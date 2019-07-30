---
title: Azure AD Domain Services ドメイン用の Secure LDAP (LDAPS) 証明書で .PFX ファイルを作成する
description: Azure AD Domain Services マネージド ドメイン用の Secure LDAP 証明書を作成します
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
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: d89034610eb22108efc832d32ed9d8f593e9f12a
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67474389"
---
# <a name="create-a-pfx-file-with-the-secure-ldap-ldaps-certificate-for-a-managed-domain"></a>マネージド ドメイン用の Secure LDAP (LDAPS) 証明書で .PFX ファイルを作成する

## <a name="before-you-begin"></a>開始する前に

「[タスク 1: セキュリティで保護された LDAP 用の証明書を取得する](configure-ldaps.md)」を最後まで行います。

## <a name="task-2-export-the-secure-ldap-certificate-to-a-pfx-file"></a>タスク 2: セキュリティで保護された LDAP 証明書を .PFX ファイルにエクスポートする

このタスクを開始する前に、公開証明機関から Secure LDAP 証明書を取得するか、または自己署名証明書を作成します。

LDAPS 証明書を PFX ファイルにエクスポートするには:

1. **[スタート]** をクリックして「**R**」と入力します。 **[ファイル名を指して実行]** ダイアログで、「**mmc**」と入力して **[OK]** をクリックします。

    ![MMC コンソールを起動する](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)
2. **[ユーザー アカウント制御]** ダイアログで **[はい]** をクリックし、管理者として MMC (Microsoft 管理コンソール) を起動します。
3. **[ファイル]** メニューの **[スナップインの追加と削除]** をクリックします。

    ![MMC コンソールにスナップインを追加する](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)
4. **[スナップインの追加と削除]** ダイアログで **[証明書]** スナップインを選択して、 **[追加 >]** をクリックします。

    ![MMC コンソールに証明書スナップインを追加する](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)
5. **証明書スナップイン** ウィザードで **[コンピューター アカウント]** を選択し、 **[次へ]** をクリックします。

    ![コンピューター アカウントの証明書スナップインを追加する](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)
6. **[コンピューターの選択]** ページで **[ローカル コンピューター (このコンソールを実行しているコンピューター)]** を選択し、 **[完了]** をクリックします。

    ![証明書スナップインを追加する - コンピューターを選択する](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)
7. **[スナップインの追加と削除]** ダイアログで **[OK]** をクリックし、証明書スナップインを MMC に追加します。

    ![MMC に証明書スナップインを追加する - 完了](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)
8. MMC のウィンドウで **[コンソール ルート]** をクリックして展開します。 証明書スナップインが読み込まれます。 **[証明書 (ローカル コンピューター)]** をクリックして展開します。 **[個人]** ノード、 **[証明書]** ノードの順にクリックします。

    ![個人証明書ストアを開く](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)
9. 前の手順で作成した自己署名証明書が表示されます。 証明書のプロパティを確認して、証明書を作成したときに PowerShell ウィンドウで報告されたものとサムプリントが一致することを確認します。
10. 目的の自己署名証明書を選択して **右クリック**します。 右クリック メニューの **[すべてのタスク]** を選択し、 **[エクスポート...]** を選択します。

    ![証明書をエクスポートします。](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)
11. **証明書のエクスポート ウィザード**で **[次へ]** をクリックします。

    ![証明書のエクスポート ウィザード](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)
12. **[秘密キーのエクスポート]** ページで **[はい、秘密キーをエクスポートします]** を選択し、 **[次へ]** をクリックします。

    ![証明書のエクスポート - 秘密キー](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [!WARNING]
    > 証明書とともに秘密キーをエクスポートする必要があります。 証明書の秘密キーが含まれない PFX を指定した場合、マネージド ドメインに対してセキュリティで保護された LDAP を有効にできません。
    >
    >

13. **[エクスポート ファイルの形式]** ページで、エクスポートする証明書のファイル形式として **[Personal Information Exchange - PKCS #12 (.PFX)]** を選択します。

    ![証明書のエクスポート - ファイル形式](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [!NOTE]
    > .PFX ファイル形式のみがサポートされています。 証明書を .CER ファイル形式にエクスポートしないでください。
    >
    >

14. **[セキュリティ]** ページで **[パスワード]** オプションを選択し、.PFX ファイルを保護するためのパスワードを入力します。 次のタスクで必要になるので、このパスワードを覚えておいてください。 **[次へ]** をクリックします。

    ![証明書のエクスポート - パスワード](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [!NOTE]
    > このパスワードを書き留めておいてください。 「[タスク 3 - マネージド ドメインに対してセキュリティで保護された LDAP を有効にする](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)」で、このマネージド ドメインの Secure LDAP を有効にするときに必要になります。
    >
    >

15. **[エクスポートするファイル]** ページで、ファイル名と証明書のエクスポート先を指定します。

    ![証明書のエクスポート - パス](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)
16. 次のページで **[完了]** をクリックして、証明書を PFX ファイルにエクスポートします。 証明書がエクスポートされると、確認のダイアログが表示されます。

    ![証明書のエクスポート完了](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)

## <a name="next-step"></a>次のステップ

[タスク 3: マネージド ドメインに対して Secure LDAP を有効にする](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
