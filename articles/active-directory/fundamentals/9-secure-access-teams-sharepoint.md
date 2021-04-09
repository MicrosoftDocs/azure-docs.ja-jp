---
title: Azure Active Directory を使用して Microsoft Teams、SharePoint、OneDrive への外部アクセスをセキュリティで保護する
description: 外部アクセス全体のセキュリティの一部として、Microsoft 365 サービスへのアクセスをセキュリティで保護します。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f373c849cd542769fec7a10f20dad04e742c9591
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565140"
---
# <a name="secure-external-access-to-microsoft-teams-sharepoint-and-onedrive-for-business"></a>Microsoft Teams、SharePoint、OneDrive for Business への外部アクセスをセキュリティで保護する 

Microsoft Teams、SharePoint、および OneDrive for Business は、外部ユーザーとの共同作業やコンテンツ共有のために最もよく使用される方法のうちの 3 つです。 "承認済み" の方法の制限が厳しすぎると、ユーザーは承認済みの方法を逸脱し、コンテンツを電子メールで送信したり、個人用の DropBox や OneDrive などの安全でない外部プロセスやアプリケーションを設定することになります。 目標は、セキュリティのニーズとコラボレーションの容易さのバランスを取ることです。

この記事では、Microsoft Teams と SharePoint を使用して、ビジネス目標を達成するための外部コラボレーションを決定して構成する方法について説明します。

## <a name="governance-begins-in-azure-active-directory"></a>Azure Active Directory からの管理の開始

Microsoft 365 での共有は、Azure Active Directory (Azure AD) の [[外部 ID | 外部コラボレーションの設定]](https://aad.portal.azure.com/) によって部分的に管理されます。 Azure AD で外部共有が無効化または制限されている場合、Microsoft 365 で構成されている共有設定よりも優先されます。 例外として、Azure AD B2B 統合が有効になっていない場合は、ワンタイム パスコード (OTP) を使用してアドホック共有をサポートするように SharePoint と OneDrive を構成できます。

![外部コラボレーションの設定のスクリーンショット](media/secure-external-access/9-external-collaboration-settings.png)

### <a name="guest-user-access"></a>ゲスト ユーザー アクセス

ゲスト ユーザー アクセスには 3 つの選択肢があります。それらによって、ゲスト ユーザーが招待された後に表示できる内容を制御します。 

ゲスト ユーザーが他のゲスト ユーザーの詳細情報を表示できないようにする一方で、グループのメンバーシップを列挙できるようにするには、[ゲスト ユーザーは、ディレクトリ オブジェクトのプロパティとメンバーシップへのアクセスが制限されます] を選択します。

### <a name="guest-invite-settings"></a>ゲスト招待の設定

これらの設定を使用して、ゲストを招待できるユーザーと、それらのゲストを招待する方法を決定します。 これらの設定は、B2B との統合が有効になっている場合にのみ有効になります。

管理者とゲスト招待元ロールのユーザーは招待ができるようにすることをお勧めします。 この設定により、次の例のように、制御されたコラボレーション プロセスを設定できます。

* チーム所有者は、チケットを送信してゲスト招待元ロールの割り当てを受け、さらに、

   * すべてのゲストの招待に責任を持ちます。

   * 基になる SharePoint に直接ユーザーを追加しないことに同意します。

   * 通常のアクセス レビューを実行し、必要に応じてアクセスを取り消すことに責任を持ちます。

* 中央 IT は次のことを行います。

   * 要求されたロールをトレーニング完了時に付与することによって、外部共有を有効にします。

   * Azure AD P2 ライセンスを Microsoft 365 グループの所有者に割り当てて、アクセス レビューを有効にします。
   * Microsoft 365 グループのアクセス レビューを作成します。

   * アクセス レビューが行われていることを確認します。

   * 基になる SharePoint に直接追加されたユーザーを削除します。

 **[ゲストの電子メール ワンタイム パスコードを有効にする (プレビュー)] および [ユーザー フローによるゲストのセルフサービス サインアップを有効にする (プレビュー)]** を **[はい]** に設定します。 この設定は、Azure AD 外部コラボレーション設定との統合を利用します。

### <a name="collaboration-restrictions"></a>コラボレーションの制限

[コラボレーションの制限] には 3 つの選択肢があります。 どれを選択するかはビジネス要件によって決まります。

* **[Allow invitations to be sent to any domain]\(どのドメインに送信される招待も許可\)** は、すべてのユーザーを共同作業に招待できることを意味します。

* **[Deny invitations to the specified domains]\(指定したドメインへの招待を拒否\)** は、それら以外のすべてのユーザーを共同作業に招待できることを意味します。

* **[Allow invitations only to the specified domains]\(指定したドメインに対してのみ招待を許可\)** は、指定したドメイン以外のユーザーを招待できないことを意味します。 

## <a name="govern-access-in-teams"></a>Teams でのアクセスの管理

[Teams においては、外部ユーザー (組織外部のあらゆるユーザー) とゲスト ユーザー (ゲスト アカウントを持つユーザー) が区別されます](/microsoftteams/communicate-with-users-from-other-organizations?WT.mc_id=TeamsAdminCenterCSH%e2%80%8b)。 [Teams 管理ポータル](https://admin.teams.microsoft.com/company-wide-settings/external-communications)の [組織全体の設定] でコラボレーション設定を管理します。 

> [!NOTE]
> Azure Active Directory の外部 ID コラボレーション設定は、有効なアクセス許可を制御します。 Teams で制限を厳しくすることができますが、Azure AD で設定されている内容を緩和することはできません。

* **外部アクセスの設定**。 Teams の既定では、外部アクセスが許可されます。これは、組織がすべての外部ドメインと通信できることを意味します。 特定のドメインを Teams に限って制限または許可する場合は、ここで行うことができます。

* **ゲスト アクセス**。 ゲスト アクセスは、ゲスト ユーザーがチーム内で実行できる操作を制御するものです。

Teams で外部アクセスを管理する方法の詳細については、次のリソースを参照してください。

* [Microsoft Teams での外部アクセスの管理](/microsoftteams/manage-external-access)

* [Microsoft 365 ID モデルと Azure Active Directory](/microsoft-365/enterprise/about-microsoft-365-identity)

* [Microsoft Teams の ID モデルと認証](/MicrosoftTeams/identify-models-authentication)

* [Microsoft Teams の秘密度ラベル](/MicrosoftTeams/sensitivity-labels)

## <a name="govern-access-in-sharepoint-and-onedrive"></a>SharePoint と OneDrive でのアクセスの管理

SharePoint 管理者は、多くの設定をコラボレーションに対して適用できます。 組織全体の設定は、SharePoint 管理センターから管理されます。 設定は、SharePoint サイトごとに調整できます。 組織全体の設定を最低限必要なセキュリティ レベルに設定し、必要に応じて特定のサイトのセキュリティを強化することをお勧めします。 たとえば、リスクの高いプロジェクトの場合、ユーザーを特定のドメインに制限し、メンバーがゲストを招待する機能を無効にすることができます。

### <a name="integrating-sharepoint-and-one-drive-with-azure-ad-b2b"></a>Azure AD B2B との SharePoint および OneDrive の統合

外部コラボレーションを管理するための全体的な戦略の一部として、[Azure AD B2B との SharePoint および OneDrive の統合のプレビューを有効にする](/sharepoint/sharepoint-azureb2b-integration-preview)ことをお勧めします。

Azure AD B2B によって、ゲスト ユーザーの認証と管理を行うことができます。 SharePoint および OneDrive の統合により、[Azure AD B2B ワンタイム パスコード](../external-identities/one-time-passcode.md)が、ファイル、フォルダー、リスト項目、ドキュメント ライブラリ、およびサイトの外部共有のために使用されます。 この機能を使用すると、既存の[セキュリティで保護された外部共有の受信者のエクスペリエンス](/sharepoint/what-s-new-in-sharing-in-targeted-release)からアップグレードされたエクスペリエンスが提供されます。

> [!NOTE]
> Azure AD B2B 統合のプレビューを有効にした場合、SharePoint と OneDrive の共有には、Azure AD 組織の関係の設定が適用されます (たとえば、 **[メンバーは招待ができる]** や **[ゲストは招待ができる]** など)。

### <a name="sharing-policies"></a>共有ポリシー

"*外部共有*" は、SharePoint と OneDrive の両方に対して設定できます。 OneDrive の制限は、SharePoint の設定より緩やかにすることはできません。

 ![SharePoint と OneDrive の外部共有設定のスクリーンショット](media/secure-external-access/9-sharepoint-settings.png)

SharePoint と Azure AD B2B の統合により、コントロールとアカウントの対話方法が変わります。

* **[すべてのユーザー]** 。 推奨されません

   * 統合の状態に関係なく、[すべてのユーザー] リンクを有効にすると、この種類のリンクが使用されたときに Azure ポリシーが適用されないことを意味します。 

   * 管理されたコラボレーションのシナリオでは、この機能を有効にしないでください。 
   > [!NOTE]
   > 特定のサイトに対してこの設定を有効にする必要があるシナリオもあります。その場合は、ここで有効にし、個々のサイトに対してより多くの制限を設定します。

* **[新規および既存のゲスト]** 。 統合を有効にした場合に、お勧めします。

   * **Azure AD B2B 統合** を有効にすると、新規および既存のゲストは、Azure AD ポリシーで管理できる Azure AD B2B ゲスト アカウントを持つことになります。

   * **Azure AD B2B 統合** が有効になっていない場合、新規のゲストには Azure AD B2B アカウントが作成されず、Azure AD から管理することはできません。 既存のゲストが Azure AD B2B アカウントを持っているかどうかは、ゲストの作成方法によって異なります。

* **[既存のゲスト]** 。 統合が有効になっていない場合に、お勧めします。

   * これを有効にすると、ユーザーはディレクトリに既に存在する他のユーザーとのみ共有ができるようになります。

* **[自分の組織内のユーザーのみ]** 。 外部ユーザーと共同作業する必要がある場合はお勧めしません。

   * 統合の状態に関係なく、ユーザーは組織内のユーザーとのみ共有ができます。 

* **[ドメインごとに外部共有を制限する]** 。 SharePoint の既定では、外部アクセスが許可されます。これは、すべての外部ドメインで共有が許可されることを意味します。 特定のドメインを SharePoint に限って制限または許可する場合は、ここで行うことができます。

* **[Allow only users in specific security groups to share externally]\(特定のセキュリティ グループのユーザーのみに外部共有を許可する\)** 。  この設定は、SharePoint と OneDrive でコンテンツを共有できるユーザーを制限し、Azure AD の設定をすべてのアプリケーションに適用します。 共有ができるユーザーを制限すると、ユーザーに安全な共有に関するトレーニングを受けてもらい、完了したら承認済みの共有セキュリティ グループに追加するような場合に便利です。 この設定が選択されている場合、"承認済みの共有先" となるアクセスを取得する方法を持たないユーザーは、代わりに、許可されていない共有の方法を見つけ出す可能性があります。 

* **[Allow guests to share items they don’t own]\(ゲストが所有していないアイテムを共有できるようにする\)** 。 これは無効のままにしておくことをお勧めします。

* **[People who use a verification code must reauthenticate after this many days]\(確認コードを使用するユーザーは、この日数の後に再認証を行う必要があります\) (既定値は 30)** 。 この設定を有効にすることをお勧めします。

### <a name="access-controls"></a>アクセス制御

アクセス制御の設定は、組織内のすべてのユーザーに影響します。 外部ユーザーが準拠しているデバイスを使用しているかどうかについて、制御が可能であるとは限らないため、それらのコントロールについてここでは説明しません。 

* **[アイドル セッションのサインアウト]** 。このコントロールを有効にすることをお勧めします。これにより、一定期間の非アクティブな状態の後、アンマネージド デバイス上のユーザーに警告を表示してサインアウトすることができます。 非アクティブな期間と警告を構成できます。 

* **ネットワークの場所**。 このコントロールを設定すると、組織が所有する IP アドレスからのアクセスのみを許可できることを意味します。 外部コラボレーションのシナリオでは、すべての外部パートナーのアクセスがネットワーク内から、または VPN 経由でのみ行われる場合に限り、これを設定します。

### <a name="file-and-folder-links"></a>ファイルおよびフォルダーのリンク

SharePoint 管理センターでは、ファイルおよびフォルダーのリンクを共有する方法を設定することもできます。 また、サイトごとにこれらの設定を構成することもできます。 

 ![ファイルおよびフォルダーのリンク設定のスクリーンショット](media/secure-external-access/9-file-folder-links.png)

Azure AD B2B との統合を有効にした場合、ファイルおよびフォルダーを組織外部のユーザーと共有すると、ファイルおよびフォルダーが共有されるときに B2B ユーザーが作成されます。

既定のリンクの種類を **[自分の組織内のユーザーのみ]** に、既定のアクセス許可を **[編集]** に設定することをお勧めします。 そうすると、アイテムの共有が慎重に行われるようになります。 そのうえで、特定のコラボレーションのニーズを満たすサイトごとの既定の設定をカスタマイズできます。

### <a name="anyone-links"></a>[すべてのユーザー] のリンク

[すべてのユーザー] のリンクを有効にすることは、お勧めしません。 そうする場合は、有効期限を設定し、アクセス許可を表示に制限することをお勧めします。 ファイルまたはフォルダーに対して [表示のみ] のアクセス許可を選択した場合、ユーザーは [すべてのユーザー] のリンクを、編集権限を含むように変更することはできません。

SharePoint への外部アクセスの管理の詳細については、以下を参照してください。

* [SharePoint 外部共有の概要](/sharepoint/external-sharing-overview)

* [SharePoint および OneDrive の Azure AD B2B との統合](/sharepoint/sharepoint-azureb2b-integration-preview)

#### <a name="next-steps"></a>次のステップ

リソースへの外部アクセスをセキュリティで保護する方法については、次の記事を参照してください。 アクションは、表示されている順序で実行することをお勧めします。

1. [外部アクセスに対するセキュリティ体制を決定する](1-secure-access-posture.md)

2. [現在の状態を理解する](2-secure-access-current-state.md)

3. [ガバナンス プランを作成する](3-secure-access-plan.md)

4. [セキュリティにグループを使用する](4-secure-access-groups.md)

5. [Azure AD B2B に移行する](5-secure-access-b2b.md)

6. [エンタイトルメント管理を使用してアクセスをセキュリティで保護する](6-secure-access-entitlement-managment.md)

7. [条件付きアクセス ポリシーを使用してアクセスをセキュリティで保護する](7-secure-access-conditional-access.md)

8. [秘密度ラベルを使用してアクセスをセキュリティで保護する](8-secure-access-sensitivity-labels.md)

9. [Microsoft Teams、OneDrive、SharePoint へのアクセスをセキュリティで保護する](9-secure-access-teams-sharepoint.md) (このページのトピック。)