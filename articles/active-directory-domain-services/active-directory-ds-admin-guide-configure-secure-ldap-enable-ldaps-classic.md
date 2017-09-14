---
title: "Azure AD ドメイン サービスでのセキュリティで保護された LDAP (LDAPS) の構成 | Microsoft Docs"
description: "Azure AD ドメイン サービスの管理対象ドメインに対するセキュリティで保護された LDAP (LDAPS) の構成"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 9d563c45-9578-410d-96c8-355af64feae8
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 3aafe209aad7383cd0610d147b5fdba673023c93
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


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-classic-azure-portal"></a>タスク 3: Azure クラシック ポータルを使用して、管理対象ドメインに対してセキュリティで保護された LDAP を有効にする
セキュリティで保護された LDAP を有効にするには、次の構成手順を実行します。

1. **[Azure クラシック ポータル](https://manage.windowsazure.com)**に移動します。
2. 左ウィンドウで、 **[Active Directory]** を選択します。
3. Azure AD ドメイン サービスを有効にした Azure AD ディレクトリ ("テナント" とも呼ばれる) を選択します。

    ![Azure AD ディレクトリの選択](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. [ **構成** ] タブをクリックします。

    ![ディレクトリの [構成] タブ](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. **[ドメイン サービス]**というセクションまでスクロールします。 次のスクリーンショットで示すように、 **[セキュリティで保護された LDAP (LDAPS)]** というオプションが表示されます。

    ![Domain Services 構成セクション](./media/active-directory-domain-services-admin-guide/secure-ldap-start.png)
6. **[証明書の構成...]** をクリックして **[セキュリティで保護された LDAP 用の証明書の構成]** ダイアログを表示します。

    ![[セキュリティで保護された LDAP 用の証明書の構成]](./media/active-directory-domain-services-admin-guide/secure-ldap-configure-cert-page.png)
7. **[証明書を含む PFX ファイル]** の下にあるフォルダー アイコンをクリックし、セキュリティで保護された LDAP からの管理対象ドメインへのアクセスに使用する証明書を含む PFX ファイルを指定します。 また、証明書を PFX ファイルにエクスポートするときに指定したパスワードを入力します。 選択後、下部にある完了ボタンをクリックします。

    ![セキュリティで保護された LDAP の PFX ファイルとパスワードを指定する](./media/active-directory-domain-services-admin-guide/secure-ldap-specify-pfx.png)
8. **[構成]** タブの **[ドメイン サービス]** セクションが淡色表示になり、数分間 **[保留中...]** の状態になります。 この期間中に、LDAPS 証明書の精度が検証され、セキュリティで保護された LDAP が管理対象ドメインに対して構成されます。

    ![セキュリティで保護された LDAP - 保留中の状態](./media/active-directory-domain-services-admin-guide/secure-ldap-pending-state.png)

   > [!NOTE]
   > 管理対象ドメインに対してセキュリティで保護された LDAP を有効にするには、約 10 - 15 分かかります。 指定した LDAPS 証明書が必須条件を満たしていない場合、ディレクトリに対してセキュリティで保護された LDAP は有効化されず、エラーが表示されます。 たとえば、ドメイン名が正しくない場合や、証明書の有効期限が切れているか、まもなく切れる場合です。
   >
   >

9. セキュリティで保護された LDAP が管理対象ドメインに対して有効になると、**[保留中...]** のメッセージは表示されなくなり、 証明書の拇印が表示されます。

    ![セキュリティで保護された LDAP 有効](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled.png)

<br>

## <a name="task-4---enable-secure-ldap-access-over-the-internet"></a>タスク 4 - インターネット経由でのセキュリティで保護された LDAP アクセスを有効にする
**オプションのタスク** - インターネット経由で LDAPS を使用して管理対象ドメインにアクセスする予定がない場合、この構成タスクは飛ばしてください。

このタスクを開始する前に、 [タスク 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-classic-azure-portal)で説明した手順が完了していることを確認してください。

1. **[構成]** ページの **[ドメイン サービス]** セクションに、**[インターネット経由でのセキュリティで保護された LDAP アクセスを有効にする]** ためのオプションが表示されます。 既定ではセキュリティで保護された LDAP 経由での管理対象ドメインへのインターネット アクセスは無効になっているので、このオプションは既定で **[いいえ]** に設定されています。

    ![セキュリティで保護された LDAP 有効](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled2.png)
2. **[インターネット経由でのセキュリティで保護された LDAP アクセスを有効にする]** を **[はい]** に切り替えます。 下部のパネルの **[保存]** をクリックします。
    ![セキュリティで保護された LDAP 有効](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access.png)
3. **[構成]** タブの **[ドメイン サービス]** セクションが淡色表示になり、数分間 **[保留中...]** の状態になります。 しばらくすると、セキュリティで保護された LDAP 経由での管理対象ドメインへのインターネット アクセスが有効になります。

    ![セキュリティで保護された LDAP - 保留中の状態](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access-pending-state.png)

   > [!NOTE]
   > 管理対象ドメインに対してセキュリティで保護された LDAP 経由でのインターネット アクセスが有効になるまでには、約 10 分かかります。
   >
   >
4. インターネット経由での管理対象ドメインへのセキュリティで保護された LDAP のアクセスが有効になると、 **[保留中...]** のメッセージは表示されなくなります。 LDAPS 経由でのディレクトリへのアクセスに使用できる外部 IP アドレスが、 **[LDAPS アクセスのための外部 IP アドレス]**フィールドに表示されます。

    ![セキュリティで保護された LDAP 有効](./media/active-directory-domain-services-admin-guide/secure-ldap-internet-access-enabled.png)

<br>

## <a name="task-5---configure-dns-to-access-the-managed-domain-from-the-internet"></a>タスク 5 - インターネットから管理対象ドメインにアクセスできるように DNS を構成する
**オプションのタスク** - インターネット経由で LDAPS を使用して管理対象ドメインにアクセスする予定がない場合、この構成タスクは飛ばしてください。

このタスクを開始する前に、 [タスク 4](#task-4---enable-secure-ldap-access-over-the-internet)で説明した手順が完了していることを確認してください。

管理対象ドメインに対してインターネット経由でのセキュリティで保護された LDAP アクセスを有効にしたら、クライアント コンピューターがこの管理対象ドメインを見つけられるようにするために、DNS を更新する必要があります。 タスク 4 の最後で、**[構成]** タブの **[LDAPS アクセスのための外部 IP アドレス]** に外部 IP アドレスが表示されます。

管理対象ドメインの DNS 名 (例: ldaps.contoso100.com) がこの外部 IP アドレスをポイントするように、外部 DNS プロバイダーを構成します。 この例では、次の DNS エントリを作成する必要があります。

    ldaps.contoso100.com  -> 52.165.38.113

これで、インターネット経由でセキュリティで保護された LDAP を使用して管理対象ドメインに接続する準備ができました。

> [!WARNING]
> LDAPS を使用して管理対象ドメインに正常に接続できるようにするには、クライアント コンピューターで LDAPS 証明書の発行者を信頼する必要があることに注意してください。 エンタープライズ証明機関または公的に信頼された証明機関を使用している場合は、クライアント コンピューターでこれらの証明書発行者が信頼されるため、特に設定を行う必要はありません。 自己署名証明書を使用している場合は、自己署名証明書の公開部分をクライアント コンピューター上の信頼された証明書ストアにインストールする必要があります。
>
>


## <a name="lock-down-ldaps-access-to-your-managed-domain-over-the-internet"></a>LDAPS を利用し、インターネット経由で管理対象ドメインにアクセスする機能を停止する
> [!NOTE]
> **オプションのタスク** - インターネット経由で LDAPS を使用して管理対象ドメインにアクセスする機能を有効にしていない場合、この構成タスクは飛ばしてください。
>
>

このタスクを開始する前に、 [タスク 4](#task-4---enable-secure-ldap-access-over-the-internet)で説明した手順が完了していることを確認してください。

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

