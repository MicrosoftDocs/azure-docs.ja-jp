---
title: "Azure AD ドメイン サービスでのセキュリティで保護された LDAP (LDAPS) の構成 | Microsoft Docs"
description: "Azure AD ドメイン サービスの管理対象ドメインに対するセキュリティで保護された LDAP (LDAPS) の構成"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 3b19f078b0d6dc3e02d951014056406fd1b099a8
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Azure Active Directory Domain Services の管理対象ドメインに対するセキュリティで保護された LDAP (LDAPS) の構成

## <a name="before-you-begin"></a>開始する前に
[タスク 2 - セキュリティで保護された LDAP 証明書を .PFX ファイルにエクスポートする](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)を完了しておきます。

プレビューの Azure Portal エクスペリエンスか Azure クラシック ポータルのいずれかを選択し、このタスクを完了します。
> [!div class="op_single_selector"]
> * **Azure Portal (プレビュー)**: [Azure Portal を利用してセキュリティで保護された LDAP を有効にする](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
> * **Azure クラシック ポータル**: [クラシック Azure Portal を利用してセキュリティで保護された LDAP を有効にする](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps-classic.md)
>
>


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview"></a>タスク 3: Azure Portal (プレビュー) を使用して、管理対象ドメインに対してセキュリティで保護された LDAP を有効にする
セキュリティで保護された LDAP を有効にするには、次の構成手順を実行します。

1. **[Azure Portal](https://portal.azure.com)** に移動します。

2. **[リソースの検索]** 検索ボックスで 'domain services' を検索します。 検索結果から **[Azure AD Domain Services]** を選択します。 **[Azure AD Domain Services]** ブレードには、管理対象ドメインが一覧表示されます。

    ![プロビジョニング中の管理対象ドメインを見つける](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. ドメインの詳細を表示するには、管理対象ドメインの名前 (例: "contoso100.com") をクリックします。

    ![Domain Services - プロビジョニング状態](./media/getting-started/domain-services-provisioning-state.png)

3. ナビゲーション ウィンドウで **[Secure LDAP]** をクリックします。

    ![ドメイン サービス - [Secure LDAP] ブレード](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. 既定では、セキュリティで保護された LDAP を利用して管理対象ドメインにアクセスする機能は無効になっています。 **[Secure LDAP]** を **[有効にする]** に切り替えます。

    ![セキュリティで保護された LDAP を有効にする](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. 既定では、セキュリティで保護された LDAP を利用し、インターネット経由で管理対象ドメインにアクセスする機能は無効になっています。 **[インターネット経由での Secure LDAP アクセスを許可]** を **[有効にする]** に切り替えます。 

6. **[Secure LDAP 証明書が入った .PFX ファイル]** に続くフォルダー アイコンをクリックします。 セキュリティで保護された LDAP アクセスで管理対象ドメインにアクセスするために、Secure LDAP 証明書が入った .PFX ファイルのパスを指定します。

7. **[PFX ファイルの暗号化を解除するためのパスワード]** を指定します。 証明書を PFX ファイルにエクスポートするときに使用したパスワードを入力します。

8. 終了したら、 **[保存]** ボタンをクリックします。

9. セキュリティで保護された LDAP が管理対象ドメインに構成されたことが通知されます。 この操作が完了するまでは、ドメインのその他の設定を変更できません。

    ![管理対象ドメインに対してセキュリティで保護された LDAP を構成する](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> 管理対象ドメインに対してセキュリティで保護された LDAP を有効にするには、約 10 - 15 分かかります。 指定した LDAPS 証明書が必須条件を満たしていない場合、ディレクトリに対してセキュリティで保護された LDAP は有効化されず、エラーが表示されます。 たとえば、ドメイン名が正しくない場合や、証明書の有効期限が切れているか、まもなく切れる場合です。 その場合、有効な証明書で再試行してください。
>
>

<br>

## <a name="task-4---configure-dns-to-access-the-managed-domain-from-the-internet"></a>タスク 4 - インターネットから管理対象ドメインにアクセスできるように DNS を構成する
> [!NOTE]
> **オプションのタスク** - インターネット経由で LDAPS を使用して管理対象ドメインにアクセスする予定がない場合、この構成タスクは飛ばしてください。
>
>

このタスクを開始する前に、 [タスク 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview)で説明した手順が完了していることを確認してください。

管理対象ドメインに対してインターネット経由でのセキュリティで保護された LDAP アクセスを有効にしたら、クライアント コンピューターがこの管理対象ドメインを見つけられるようにするために、DNS を更新する必要があります。 タスク 3 の最後で、**[プロパティ]** ブレードの **[LDAPS アクセスのための外部 IP アドレス]** に外部 IP アドレスが表示されます。

管理対象ドメインの DNS 名 (例: ldaps.contoso100.com) がこの外部 IP アドレスをポイントするように、外部 DNS プロバイダーを構成します。 この例では、次の DNS エントリを作成する必要があります。

    ldaps.contoso100.com  -> 52.165.38.113

これで、インターネット経由でセキュリティで保護された LDAP を使用して管理対象ドメインに接続する準備ができました。

> [!WARNING]
> LDAPS を使用して管理対象ドメインに正常に接続できるようにするには、クライアント コンピューターで LDAPS 証明書の発行者を信頼する必要があることに注意してください。 公的に信頼された証明機関を使用している場合は、クライアント コンピューターでこれらの証明書発行者が信頼されるため、特に設定を行う必要はありません。 自己署名証明書を使用している場合は、自己署名証明書の公開部分をクライアント コンピューター上の信頼された証明書ストアにインストールします。
>
>


## <a name="task-5---lock-down-ldaps-access-to-your-managed-domain-over-the-internet"></a>タスク 5 - LDAPS を利用し、インターネット経由で管理対象ドメインにアクセスする機能を停止する
> [!NOTE]
> **オプションのタスク** - インターネット経由で LDAPS を使用して管理対象ドメインにアクセスする機能を有効にしていない場合、この構成タスクは飛ばしてください。
>
>

このタスクを開始する前に、 [タスク 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview)で説明した手順が完了していることを確認してください。

LDAPS アクセスのために管理対象ドメインをインターネットにさらす行為はセキュリティ上の脅威となります。 セキュリティで保護されている LDAP に使用されるポート (ポート 636) でインターネットから管理対象ドメインに到達できます。 そのため、管理対象ドメインへのアクセスを特定の既知の IP アドレスに制限できます。 セキュリティを強化する目的で、ネットワーク セキュリティ グループ (NSG) を作成し、それと Azure AD Domain Services を有効にしたサブネットを関連付けます。

次の表は NSG のサンプルです。このように設定し、セキュリティで保護された LDAP を利用してインターネット経由でアクセスする機能を停止できます。 この NSG には、指定した IP アドレスから TCP ポート 636 経由で入ってくる LDAPS アクセスのみを許可するルール セットが含まれています。 既定の 'DenyAll' ルールは、インターネットから入ってくるその他すべてのトラフィックに適用されます。 指定した IP アドレスからインターネット経由で入ってくる LDAPS アクセスを許可する NSG ルールには、DenyAll NSG ルールより高い優先度が設定されています。

![サンプル NSG。セキュリティで保護された LDAPS を利用し、インターネット経由でアクセスします。](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**詳細** - [ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)

<br>

## <a name="related-content"></a>関連コンテンツ
* [Azure AD ドメイン サービス - 作業開始ガイド](active-directory-ds-getting-started.md)
* [Azure AD ドメイン サービスで管理されているドメインの管理](active-directory-ds-admin-guide-administer-domain.md)
* [Azure Active Directory Domain Services によって管理されるドメインのグループ ポリシーの管理](active-directory-ds-admin-guide-administer-group-policy.md)
* [ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)
* [ネットワーク セキュリティ グループの作成](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

