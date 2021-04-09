---
title: Azure AD の組み込みロール - Azure Active Directory
description: Azure Active Directory の組み込みロールとアクセス許可について説明します。
services: active-directory
author: rolyon
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 02/17/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: a65b91e3dff3ef412dad8bbe57383a9dbf8c7765
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032247"
---
# <a name="azure-ad-built-in-roles"></a>Azure AD の組み込みロール

Azure Active Directory (Azure AD) を使用して、特権が低いロールで ID のタスクを管理する限定された管理者を指定できます。 ユーザーの追加または変更、管理ロールの割り当て、ユーザーのパスワードのリセット、ユーザーのライセンスの管理、ドメイン名の管理などの目的で管理者を割り当てることができます。 [既定のユーザー アクセス許可](../fundamentals/users-default-permissions.md)は、Azure AD のユーザー設定のみで変更できます。

## <a name="limit-use-of-global-administrator"></a>グローバル管理者の使用を制限する

グローバル管理者ロールに割り当てられているユーザーは、Azure AD 組織内のすべての管理設定の読み取りと変更を行うことができます。 既定では、ユーザーが Microsoft のクラウド サービスに新規登録すると、Azure AD テナントが作成され、そのユーザーはグローバル管理者ロールのメンバーになります。 サブスクリプションを既存のテナントに追加するとき、そのユーザーはグローバル管理者ロールに割り当てられません。 管理者ロールを委任できるのはグローバル管理者と特権ロール管理者だけです。 ビジネスに対するリスクを軽減するには、このロールを割り当てる組織内のユーザーの数をできる限り少なくすることをお勧めします。

ベスト プラクティスとして、組織内でこのロールを割り当てる人は 5 人未満にすることをお勧めします。 組織内でグローバル管理者ロールを割り当てる管理者が 5 人以上になる場合は、次の方法でその利用を減らすことができます。

### <a name="find-the-role-you-need"></a>必要なロールを調べる

多くのロールのリストから必要なロールを見つけるのが面倒な場合は、Azure AD でロールのカテゴリに基づいてロールのサブセットを表示できます。 選択した種類のロールだけが表示される、[Azure AD ロールと管理者](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)に対する新しい **種類** フィルターを確認してください。

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>グローバル管理者ロールを割り当てたときには存在していなかったロールが存在する

一部のユーザーをグローバル管理者に昇格させたときには選択できなかった、よりきめ細かいアクセス許可が提供されるロールが、Azure AD に追加されている可能性があります。 時間の経過と共に、以前はグローバル管理者ロールだけが実行できたタスクを遂行する新しいロールがロールアウトされています。 これらは、次の「[すべてのロール](#all-roles)」に反映されています。

## <a name="assign-or-remove-administrator-roles"></a>管理者ロールの割り当てまたは削除

Azure Active Directory でユーザーに管理者ロールを割り当てる方法については、[Azure Active Directory での管理者ロールの表示と割り当て](manage-roles-portal.md)に関するページを参照してください。

> [!Note]
> Azure AD プレミアム P2 ライセンスがあるとき、Privileged Identity Management (PIM) ユーザーに既になっている場合、ロール管理タスクはすべて、Azure AD ではなく、Privileged Identity Management で実行されます。
>
> ![既に PIM を使用していて Premium P2 ライセンスを所有しているユーザーの Azure AD ロールは PIM で管理される](./media/permissions-reference/pim-manages-roles-for-p2.png)

## <a name="all-roles"></a>すべてのロール

> [!div class="mx-tableFixed"]
> | Role | 説明 | テンプレート ID |
> | --- | --- | --- |
> | [アプリケーション管理者](#application-administrator) | アプリ登録とエンタープライズ アプリのすべての側面を作成して管理できます。 | 9b895d92-2cd3-44c7-9d02-a6ac2d5ea5c3 |
> | [アプリケーション開発者](#application-developer) | [ユーザーはアプリケーションを登録できる] の設定とは無関係にアプリケーション登録を作成できます。 | cf1c38e5-3621-4004-a7cb-879624dced7c |
> | [攻撃のペイロードの作成者](#attack-payload-author) | 管理者が後で開始できる攻撃のペイロードを作成できます。 | 9c6df0f2-1e7c-4dc3-b195-66dfbd24aa8f |
> | [攻撃のシミュレーションの管理者](#attack-simulation-administrator) | 攻撃のシミュレーション キャンペーンのすべての側面を作成および管理できます。 | c430b396-e693-46cc-96f3-db01bf8bb62a |
> | [認証管理者](#authentication-administrator) | 管理者以外のユーザーの認証方法の情報を表示、設定、リセットするためにアクセスできます。 | c4e39bd9-1100-46d3-8c65-fb160da0071f |
> | [認証ポリシー管理者](#authentication-policy-administrator) | 認証方法とパスワード保護ポリシーのすべての側面を作成し、管理できます。 | 0526716b-113d-4c15-b2c8-68e3c22b9f80 |
> | [Azure AD 参加済みデバイスのローカル管理者](#azure-ad-joined-device-local-administrator) | このロールに割り当てられたユーザーは、Azure AD 参加済みデバイスのローカル管理者グループに追加されます。 | 9f06204d-73c1-4d4c-880a-6edb90606fd8 |
> | [Azure DevOps 管理者](#azure-devops-administrator) | Azure DevOps 組織のポリシーと設定を管理できます。 | e3973bdf-4987-49ae-837a-ba8e231c7286 |
> | [Azure Information Protection 管理者](#azure-information-protection-administrator) | Azure Information Protection 製品のすべての側面を管理できます。 | 7495fdc4-34c4-4d15-a289-98788ce399fd |
> | [B2C IEF キーセット管理者](#b2c-ief-keyset-administrator) | Identity Experience Framework (IEF) でフェデレーションおよび暗号化のシークレットを管理できます。 | aaf43236-0c0d-4d5f-883a-6955382ac081 |
> | [B2C IEF ポリシー管理者](#b2c-ief-policy-administrator) | Identity Experience Framework (IEF) で信頼フレームワーク ポリシーを作成および管理できます。 | 3edaf663-341e-4475-9f94-5c398ef6c070 |
> | [課金管理者](#billing-administrator) | 支払情報の更新など、よく利用する課金関連タスクを実行できます。 | b0f54661-2d74-4c50-afa3-1ec803f12efe |
> | [クラウド アプリケーション管理者](#cloud-application-administrator) | アプリ登録とエンタープライズ アプリのすべての側面 (アプリ プロキシを除く) を作成して管理できます。 | 158c047a-c907-4556-b7ef-446551a6b5f7 |
> | [クラウド デバイス管理者](#cloud-device-administrator) | Azure AD でデバイスを管理するための制限付きアクセス。 | 7698a772-787b-4ac8-901f-60d6b08affd2 |
> | [コンプライアンス管理者](#compliance-administrator) | Azure AD および Microsoft 365 のコンプライアンスの構成とレポートを読み取り、管理できます。 | 17315797-102d-40b4-93e0-432062caca18 |
> | [コンプライアンス データ管理者](#compliance-data-administrator) | コンプライアンス コンテンツを作成、管理します。 | e6d1a23a-da11-4be4-9570-befc86d067a7 |
> | [条件付きアクセス管理者](#conditional-access-administrator) | 条件付きアクセスの機能を管理できます。 | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9 |
> | [カスタマー ロックボックスのアクセス承認者](#customer-lockbox-access-approver) | Microsoft サポートがお客様の組織データにアクセスする要求を承認することができます。 | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91 |
> | [デスクトップ Analytics 管理者](#desktop-analytics-administrator) | デスクトップの管理ツールとサービスにアクセスして管理できます。 | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4 |
> | [ディレクトリ閲覧者](#directory-readers) | 基本的なディレクトリ情報を読み取ることができます 一般に、アプリケーションとゲストへのディレクトリ読み取りアクセスを許可するために使用されます。 | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b |
> | [ディレクトリ同期アカウント](#directory-synchronization-accounts) | Azure AD Connect サービスでのみ使用されます。 | d29b2b05-8046-44ba-8758-1e26182fcf32 |
> | [ディレクトリ ライター](#directory-writers) | 基本的なディレクトリ情報の読み取りと書き込みを実行できます。 ユーザーではなく、アプリケーションへのアクセスを許可する場合。 | 9360feb5-f418-4baa-8175-e2a00bac4301 |
> | [ドメイン名管理者](#domain-name-administrator) | クラウドおよびオンプレミスのドメイン名を管理できます。 | 8329153b-31d0-4727-b945-745eb3bc5f31 |
> | [Dynamics 365 管理者](#dynamics-365-administrator) | Dynamics 365 製品のすべての側面を管理できます。 | 44367163-eba1-44c3-98af-f5787879f96a |
> | [Exchange 管理者](#exchange-administrator) | Exchange 製品のすべての側面を管理できます。 | 29232cdf-9323-42fd-ade2-1d097af3e4de |
> | [外部 ID ユーザー フロー管理者](#external-id-user-flow-administrator) | ユーザー フローのすべての側面を作成および管理できます。 | 6e591065-9bad-43ed-90f3-e9424366d2f0 |
> | [外部 ID ユーザー フロー属性管理者](#external-id-user-flow-attribute-administrator) | すべてのユーザー フローに対して使用可能な属性スキーマを作成および管理できます。 | 0f971eea-41eb-4569-a71e-57bb8a3eff1e |
> | [外部 ID プロバイダー管理者](#external-identity-provider-administrator) | 直接フェデレーションで使用する ID プロバイダーを構成できます。 | be2f45a1-457d-42af-a067-6ec1fa63bc45 |
> | [全体管理者](#global-administrator) | Azure AD のすべての側面と、Azure AD の ID が使用される Microsoft サービスを管理できます。 | 62e90394-69f5-4237-9190-012177145e10 |
> | [グローバル閲覧者](#global-reader) | グローバル管理者が読み取れるものすべての読み取りが可能ですが、更新することはできません。 | f2ef992c-3afb-46b9-b7cf-a126ee74c451 |
> | [グループ管理者](#groups-administrator) | このロールのメンバーは、グループの作成と管理、名前付けと有効期限ポリシーなどのグループ設定の作成と管理、グループのアクティビティと監査レポートの表示を行うことができます。 | fdd7a751-b60b-444a-984c-02652fe8fa1c |
> | [ゲスト招待元](#guest-inviter) | [メンバーがゲストを招待できる] の設定とは無関係にゲスト ユーザーを招待できます。 | 95e79109-95c0-4d8e-aee3-d01accf2d47b |
> | [ヘルプデスク管理者](#helpdesk-administrator) | 管理者以外のユーザーとヘルプデスク管理者のパスワードをリセットできます。 | 729827e3-9c14-49f7-bb1b-9608f156bbb8 |
> | [ハイブリッド ID の管理者](#hybrid-identity-administrator) | AD から Azure AD へのクラウド プロビジョニングとフェデレーションの設定を管理できます。 | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2 |
> | [Insights 管理者](#insights-administrator) | Microsoft 365 Insights アプリへの管理アクセス権があります。 | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c |
> | [Insights ビジネス リーダー](#insights-business-leader) | M365 Insights アプリを使用して、ダッシュボードと分析情報を表示および共有できます。 | 31e939ad-9672-4796-9c2e-873181342d2d |
> | [Intune 管理者](#intune-administrator) | Intune 製品のすべての側面を管理できます。 | 3a2c62db-5318-420d-8d74-23affee5d9d5 |
> | [Kaizala 管理者](#kaizala-administrator) | Microsoft Kaizala の設定を管理できます。 | 74ef975b-6605-40af-a5d2-b9539d836353 |
> | [ライセンス管理者](#license-administrator) | ユーザーおよびグループの製品ライセンスを管理できます。 | 4d6ac14f-3453-41d0-bef9-a3e0c569773a |
> | [メッセージ センターのプライバシー閲覧者](#message-center-privacy-reader) | Office 365 メッセージ センター内でのみセキュリティ メッセージと更新情報を閲覧することができます。 | ac16e43d-7b2d-40e0-ac05-243ff356ab5b |
> | [メッセージ センター閲覧者](#message-center-reader) | Office 365 メッセージ センター内でのみ自分の組織のメッセージと更新情報を閲覧することができます。 | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b |
> | [Modern Commerce ユーザー](#modern-commerce-user) | 会社、部署、またはチームの商用購入を管理できます。 | d24aef57-1500-4070-84db-2666f29cf966 |
> | [ネットワーク管理者](#network-administrator) | ネットワークの場所を管理し、Microsoft 365 Software as a Service アプリケーションのエンタープライズ ネットワーク設計の分析情報を確認できます。 | d37c8bed-0711-4417-ba38-b4abe66ce4c2 |
> | [Office アプリ管理者](#office-apps-administrator) | Office アプリのクラウド サービスを管理すること (ポリシーと設定の管理を含む) や、"新機能" のコンテンツを選択、選択解除、エンドユーザーのデバイスに公開する機能を管理できます。 | 2b745bdf-0803-4d80-aa65-822c4493daac |
> | [パートナー レベル 1 のサポート](#partner-tier1-support) | 使用しないでください。一般的な使用は想定されていません。 | 4ba39ca4-527c-499a-b93d-d9b492c50246 |
> | [パートナー レベル 2 のサポート](#partner-tier2-support) | 使用しないでください。一般的な使用は想定されていません。 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8 |
> | [パスワード管理者](#password-administrator) | 管理者以外とパスワード管理者のパスワードをリセットできます。 | 966707d0-3269-4727-9be2-8c3a10f19b9d |
> | [Power BI 管理者](#power-bi-administrator) | Power BI 製品のすべての側面を管理できます。 | a9ea8996-122f-4c74-9520-8edcd192826c |
> | [Power Platform 管理者](#power-platform-administrator) | Microsoft Dynamics 365、PowerApps、Microsoft Flow のすべての側面を作成および管理できます。 | 11648597-926c-4cf3-9c36-bcebb0ba8dcc |
> | [プリンター管理者](#printer-administrator) | プリンターとプリンター コネクタのすべての側面を管理できます。 | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f |
> | [プリンター技術者](#printer-technician) | プリンターの登録と登録解除、またプリンターの状態の更新を行うことができます。 | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477 |
> | [特権認証管理者](#privileged-authentication-administrator) | 任意のユーザー (管理者でも管理者以外でも) の認証方法の情報を表示、設定、リセットするためにアクセスできます。 | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13 |
> | [特権ロール管理者](#privileged-role-administrator) | Azure AD でのロールの割り当てと、Privileged Identity Management のすべての側面を管理できます。 | e8611ab8-c189-46e8-94e1-60213ab1f814 |
> | [レポート閲覧者](#reports-reader) | サインインと監査のレポートを読み取ることができます。 | 4a5d8f65-41da-4de4-8968-e035b65339cf |
> | [Search 管理者](#search-administrator) | Microsoft Search 設定のすべての側面を作成および管理できます。 | 0964bb5e-9bdb-4d7b-ac29-58e794862a40 |
> | [Search エディター](#search-editor) | ブックマーク、Q&A、場所、フロアプランなどの編集コンテンツを作成および管理することができます。 | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9 |
> | [セキュリティ管理者](#security-administrator) | セキュリティ情報とレポートを読み取り、Azure AD と Office 365 で構成を管理することができます。 | 194ae4cb-b126-40b2-bd5b-6091b380977d |
> | [セキュリティ オペレーター](#security-operator) | セキュリティ イベントを作成、管理します。 | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f |
> | [Security Reader](#security-reader) | Azure AD と Office 365 のセキュリティ情報とレポートを読み取ることができます。 | 5d6b6bb7-de71-4623-b4af-96380a352509 |
> | [サービス サポート管理者](#service-support-administrator) | サービス正常性に関する情報を読み取り、サポート チケットを管理することができます。 | f023fd81-a637-4b56-95fd-791ac0226033 |
> | [SharePoint 管理者](#sharepoint-administrator) | SharePoint サービスのすべての側面を管理できます。 | f28a1f50-f6e7-4571-818b-6a12f2af6b6c |
> | [Skype for Business 管理者](#skype-for-business-administrator) | Skype for Business 製品のすべての側面を管理できます。 | 75941009-915a-4869-abe7-691bff18279e |
> | [Teams 管理者](#teams-administrator) | Microsoft Teams サービスを管理できます。 | 69091246-20e8-4a56-aa4d-066075b2a7a8 |
> | [Teams 通信管理者](#teams-communications-administrator) | Microsoft Teams サービス内での通話と会議の機能を管理できます。 | baf37b3a-610e-45da-9e62-d9d1e5e8914b |
> | [Teams 通信サポート エンジニア](#teams-communications-support-engineer) | 高度なツールを使用して、Teams 内の通信の問題のトラブルシューティングを行えます。 | f70938a0-fc10-4177-9e90-2178f8765737 |
> | [Teams 通信サポート スペシャリスト](#teams-communications-support-specialist) | 基本的なツールを使用して、Teams 内の通信の問題のトラブルシューティングを行えます。 | fcf91098-03e3-41a9-b5ba-6f0ec8188a12 |
> | [Teams デバイス管理者](#teams-devices-administrator) | Teams 認定デバイスで管理関連タスクを実行できます。 | 3d762c5a-1b6c-493f-843e-55a3b42923d4 |
> | [使用状況の概要のレポート閲覧者](#usage-summary-reports-reader) | Microsoft 365 利用状況分析および生産性スコアのテナント レベルの集計のみを表示できます。 | 75934031-6c7e-415a-99d7-48dbd49e875e |
> | [ユーザー管理者](#user-administrator) | ユーザーとグループのすべての側面を、制限付きの管理者のパスワードをリセットすることも含めて、管理できます。 | fe930be7-5e62-47db-91af-98c3a49a38b1 |

## <a name="application-administrator"></a>アプリケーション管理者

このロールのユーザーは、エンタープライズ アプリケーション、アプリケーション登録、アプリケーション プロキシの設定の全側面を作成して管理できます。 このロールに割り当てられたユーザーは、新しいアプリケーション登録またはエンタープライズ アプリケーションを作成する際に、所有者として追加されないことに注意してください。

さらに、このロールは、委任されたアクセス許可とアプリケーション アクセス許可 (Microsoft Graph と Azure AD Graph の両方に対するアプリケーション アクセス許可を除く) に同意する権限を付与します。

> [!IMPORTANT]
> この例外は、"_他の_" アプリ (たとえば、Microsoft 以外のアプリや自分が登録したアプリなど) に対するアプリケーション アクセス許可には引き続き同意できることを意味します。 アプリ登録の一環としてこれらのアクセス許可を "_要求_" することはできますが、これらのアクセス許可を "_許可する_" (つまり、同意する) には、グローバル管理者などの特権管理者が必要です。
>
>このロールは、アプリケーションの資格情報を管理する権限を付与します。 このロールが割り当てられたユーザーは、アプリケーションに資格情報を追加し、その資格情報を使用してアプリケーションの ID を偽装することができます。 アプリケーションの ID にリソースへのアクセス権 (ユーザーやその他のオブジェクトの作成や更新など) が付与されている場合、このロールに割り当てられたユーザーは、アプリケーションを偽装している間にこのようなアクションを実行することができます。 アプリケーションの ID を偽装するこの機能は、ユーザーがロール割り当てを使用して実行できることを越えた特権の昇格になる可能性があります。 ユーザーにアプリケーション管理者ロールを割り当てると、そのユーザーがアプリケーションの ID を偽装できるようになることを理解することが重要です。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/applications/create | すべての種類のアプリケーションを作成する |
> | microsoft.directory/applications/delete | すべての種類のアプリケーションを削除する |
> | microsoft.directory/applications/applicationProxy/read | すべてのアプリケーション プロキシ プロパティを読み取る |
> | microsoft.directory/applications/applicationProxy/update | すべてのアプリケーション プロキシ プロパティを更新する |
> | microsoft.directory/applications/applicationProxyAuthentication/update | アプリケーション プロキシの認証プロパティを更新する |
> | microsoft.directory/applications/applicationProxySslCertificate/update | アプリケーション プロキシのカスタム ドメインを更新する |
> | microsoft.directory/applications/applicationProxyUrlSettings/update | アプリケーション プロキシの内部 URL と外部 URL を更新する |
> | microsoft.directory/applications/appRoles/update | すべての種類のアプリケーションで appRoles プロパティを更新する |
> | microsoft.directory/applications/audience/update | アプリケーションの対象ユーザー プロパティを更新する |
> | microsoft.directory/applications/authentication/update | すべての種類のアプリケーションで認証を更新する |
> | microsoft.directory/applications/basic/update | アプリケーションの基本プロパティを更新する |
> | microsoft.directory/applications/credentials/update | アプリケーション資格情報を更新する |
> | microsoft.directory/applications/owners/update | アプリケーションの所有者を更新する |
> | microsoft.directory/applications/permissions/update | すべての種類のアプリケーションで、公開されたアクセス許可と必要なアクセス許可を更新する |
> | microsoft.directory/applications/policies/update | アプリケーションのポリシーを更新する |
> | microsoft.directory/applications/verification/update | applicationsverification プロパティを更新する |
> | microsoft.directory/applications/synchronization/standard/read | アプリケーション オブジェクトに関連付けられているプロビジョニング設定を読み取る |
> | microsoft.directory/applicationTemplates/instantiate | アプリケーション テンプレートからギャラリー アプリケーションのインスタンスを作成する |
> | microsoft.directory/auditLogs/allProperties/read | 監査ログのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.directory/connectors/create | アプリケーション プロキシ コネクタを作成する |
> | microsoft.directory/connectors/allProperties/read | アプリケーション プロキシ コネクタのすべてのプロパティを読み取る |
> | microsoft.directory/connectorGroups/create | アプリケーション プロキシ コネクタ グループを作成する |
> | microsoft.directory/connectorGroups/delete | アプリケーション プロキシ コネクタ グループを削除する |
> | microsoft.directory/connectorGroups/allProperties/read | アプリケーション プロキシ コネクタ グループのすべてのプロパティを読み取る |
> | microsoft.directory/connectorGroups/allProperties/update | アプリケーション プロキシ コネクタ グループのすべてのプロパティを更新する |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0 アクセス許可の付与の作成と削除、およびすべてのプロパティの読み取りと更新 |
> | microsoft.directory/applicationPolicies/create | アプリケーション ポリシーを作成する |
> | microsoft.directory/applicationPolicies/delete | アプリケーション ポリシーを削除する |
> | microsoft.directory/applicationPolicies/standard/read | アプリケーション ポリシーの標準プロパティを読み取る |
> | microsoft.directory/applicationPolicies/owners/read | アプリケーション ポリシーの所有者を読み取る |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | オブジェクト リストに適用されているアプリケーション ポリシーを読み取る |
> | microsoft.directory/applicationPolicies/basic/update | アプリケーション ポリシーの標準プロパティを更新する |
> | microsoft.directory/applicationPolicies/owners/update | アプリケーション ポリシーの所有者プロパティを更新する |
> | microsoft.directory/provisioningLogs/allProperties/read | プロビジョニング ログのすべてのプロパティを読み取ります。 |
> | microsoft.directory/servicePrincipals/create | サービス プリンシパルの作成 |
> | microsoft.directory/servicePrincipals/delete | サービス プリンシパルを削除する |
> | microsoft.directory/servicePrincipals/disable | サービス プリンシパルを無効にする |
> | microsoft.directory/servicePrincipals/enable | サービス プリンシパルを有効にする |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | サービス プリンシパルのパスワード シングル サインオン資格情報を管理する |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | アプリケーション プロビジョニングのシークレットと資格情報を管理する |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | アプリケーション プロビジョニングの同期ジョブを開始、再開、一時停止する |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | アプリケーション プロビジョニングの同期ジョブとスキーマを作成および管理する |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | サービス プリンシパルのパスワード シングル サインオン資格情報を読み取る |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-application-admin | ユーザーまたはすべてのユーザーに代わって、アプリケーションのアクセス許可と委任されたアクセス許可に同意を付与する (Microsoft Graph と Azure AD Graph のアプリケーション アクセス許可は除く) |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | サービス プリンシパルのロールの割り当ての更新 |
> | microsoft.directory/servicePrincipals/audience/update | サービス プリンシパルで対象ユーザー プロパティを更新する |
> | microsoft.directory/servicePrincipals/authentication/update | サービス プリンシパルで認証プロパティを更新する |
> | microsoft.directory/servicePrincipals/basic/update | サービス プリンシパルで基本プロパティを更新する |
> | microsoft.directory/servicePrincipals/credentials/update | サービス プリンシパルの資格情報を更新する |
> | microsoft.directory/servicePrincipals/owners/update | サービス プリンシパルの所有者を更新する |
> | microsoft.directory/servicePrincipals/permissions/update | サービスプリンシパルのアクセス許可を更新する |
> | microsoft.directory/servicePrincipals/policies/update | サービス プリンシパルのポリシーを更新する |
> | microsoft.directory/servicePrincipals/tag/update | サービスプリンシパルのタグ プロパティを更新する |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | サービス プリンシパルに関連付けられているプロビジョニング設定を読み取る |
> | microsoft.directory/signInReports/allProperties/read | サインイン情報レポートのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="application-developer"></a>アプリケーション開発者

このロールのユーザーは、[ユーザーはアプリケーションを登録できる] 設定が [いいえ] に設定されている場合に、アプリケーション登録を作成できます。 さらにこのロールでは、[ユーザーはアプリが自身の代わりに会社のデータにアクセスすることを許可できます] 設定が [いいえ] に設定されている場合に、代わりに同意する権限を付与します。 このロールに割り当てられたユーザーは、新しいアプリケーション登録またはエンタープライズ アプリケーションを作成する際に、所有者として追加されます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/applications/createAsOwner | すべての種類のアプリケーションを作成し、作成者が最初の所有者として追加される |
> | microsoft.directory/appRoleAssignments/createAsOwner | アプリケーション ロールの割り当てを作成し、作成者を最初の所有者にする |
> | microsoft.directory/oAuth2PermissionGrants/createAsOwner | OAuth 2.0 アクセス許可付与を作成し、作成者を最初の所有者にする |
> | microsoft.directory/servicePrincipals/createAsOwner | サービス プリンシパルを作成し、作成者を最初の所有者にする |

## <a name="attack-payload-author"></a>攻撃のペイロードの作成者

このロールのユーザーは、攻撃のペイロードを作成することはできますが、それらを実際に起動することやスケジュールすることはできません。 攻撃のペイロードは、それらを使用してシミュレーション作成できるテナントの管理者全員が利用できます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | 攻撃シミュレーターで攻撃ペイロードを作成および管理する |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | 攻撃のシミュレーション、応答、関連付けられているトレーニングのレポートを読み取る |

## <a name="attack-simulation-administrator"></a>攻撃のシミュレーションの管理者

このロールのユーザーは、攻撃シミュレーションの作成、シミュレーションの開始とスケジューリング、シミュレーション結果の確認のすべての側面を作成および管理できます。 このロールのメンバーは、テナント内のすべてのシミュレーションに対してこのアクセス権を所有します。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | 攻撃シミュレーターで攻撃ペイロードを作成および管理する |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | 攻撃のシミュレーション、応答、関連付けられているトレーニングのレポートを読み取る |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | 攻撃シミュレーターで攻撃のシミュレーション テンプレートを作成および管理する |

## <a name="authentication-administrator"></a>認証管理者

このロールを持つユーザーは、管理者以外および一部のロールの任意の認証方法 (パスワードを含む) を設定またはリセットできます。 認証管理者は、パスワード以外の既存の資格情報 (たとえば、MFA、FIDO) に対する再登録を管理者以外のユーザー、または一部のロールに割り当てられているユーザーに要求し、"**このデバイスに MFA を記憶する**" 機能を取り消すことができます。これによって、管理者でないユーザーや次のロールのみが割り当てられているユーザーが次回サインインするときに MFA の入力を求めることもできます。 認証管理者が認証方法を読み取りまたは更新できるロールの一覧については、「[パスワード リセットのアクセス許可](#password-reset-permissions)」を参照してください。

[特権認証管理者](#privileged-authentication-administrator)ロールには、すべてのユーザーに対して再登録と多要素認証を強制する権限があります。

[認証ポリシー管理者](#authentication-policy-administrator)ロールには、テナントの認証方法ポリシーを設定する権限があり、これにより、各ユーザーが登録および使用できる方法が決まります。

| Role | ユーザーの認証方法を管理する | ユーザーごとの MFA を管理する | MFA 設定を管理する | 認証方法ポリシーを管理する | パスワード保護ポリシーを管理する |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 認証管理者 | 一部のユーザーに対してはい (上記を参照) | 一部のユーザーに対してはい (上記を参照) | いいえ | いいえ | いいえ |
| 特権認証管理者| すべてのユーザーに対してはい | すべてのユーザーに対してはい | いいえ | いいえ | いいえ |
| 認証ポリシー管理者 | いいえ |いいえ | はい | はい | はい |

> [!IMPORTANT]
> このロールのユーザーは、機密情報や個人情報または Azure Active Directory の内外の重要な構成にアクセスできるユーザーのパスワードを変更できます。 ユーザーの資格情報を変更するということは、そのユーザーの ID とアクセス許可を引き受けることができるということを意味します。 次に例を示します。
>
>* 所有しているアプリの資格情報を管理できる、アプリケーションの登録とエンタープライズ アプリケーションの所有者。 これらのアプリには、認証管理者に付与されていないAzure AD およびその他の場所への特権アクセス許可がある場合があります。 認証管理者は、このパスからアプリケーション所有者の ID を引き受け、さらにそのアプリケーションの資格情報を更新して特権アプリケーションの ID を引き受けることができます。
>* 機密情報や個人情報または Azure の重要な構成にアクセスできる Azure サブスクリプション所有者。
>* グループ メンバーシップを管理できるセキュリティ グループと Microsoft 365 グループの所有者。 これらのグループは、機密情報や個人情報または Azure AD や別の場所の重要な構成へのアクセス権を付与される場合があります。
>* Exchange Online、Office Security and Compliance Center、人事システムのような Azure AD 以外のサービスの管理者。
>* 機密情報や個人情報にアクセスできる場合がある役員、弁護士、人事担当者のような非管理者。

> [!IMPORTANT]
> このロールは現在、レガシ MFA 管理ポータルでユーザーごとの MFA を管理することはできません。 Azure AD Powershell モジュールの [Set-msoluser](/powershell/module/msonline/set-msoluser) コマンドレットを使用して、同じ機能を実行できます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/users/invalidateAllRefreshTokens | ユーザー更新トークンを無効にして強制的にサインアウトする |
> | microsoft.directory/users/strongAuthentication/update | ユーザーの強力な認証プロパティを更新する |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="authentication-policy-administrator"></a>認証ポリシー管理者

このロールを持つユーザーは、認証方法のポリシー、テナント全体の MFA 設定、およびパスワード保護ポリシーを構成できます。 このロールは、パスワード保護設定を管理するためのアクセス許可を付与します (スマート ロックアウトの構成とカスタムの禁止パスワード リストの更新)。

[認証管理者](#authentication-administrator)と[特権認証管理者](#privileged-authentication-administrator)のロールには、ユーザーの登録済み認証方法を管理するための権限があり、すべてのユーザーに対して再登録と多要素認証を強制できます。

| Role | ユーザーの認証方法を管理する | ユーザーごとの MFA を管理する | MFA 設定を管理する | 認証方法ポリシーを管理する | パスワード保護ポリシーを管理する |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 認証管理者 | 一部のユーザーに対してはい (上記を参照) | 一部のユーザーに対してはい (上記を参照) | いいえ | いいえ | いいえ |
| 特権認証管理者| すべてのユーザーに対してはい | すべてのユーザーに対してはい | いいえ | いいえ | いいえ |
| 認証ポリシー管理者 | いいえ | いいえ | はい | はい | はい |

> [!IMPORTANT]
> このロールは現在、レガシ MFA 管理ポータルで MFA 設定を管理することはできません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/organization/strongAuthentication/update | 組織の強力な認証プロパティを更新する |
> | microsoft.directory/userCredentialPolicies/create | ユーザーの資格情報ポリシーを作成する |
> | microsoft.directory/userCredentialPolicies/delete | ユーザーの資格情報ポリシーを削除する |
> | microsoft.directory/userCredentialPolicies/standard/read | ユーザーの資格情報ポリシーの標準プロパティを読み取る |
> | microsoft.directory/userCredentialPolicies/owners/read | ユーザーの資格情報ポリシーの所有者を読み取る |
> | microsoft.directory/userCredentialPolicies/policyAppliedTo/read | policy.appliesTo navigation リンクを読み取る |
> | microsoft.directory/userCredentialPolicies/basic/update | ユーザーの基本ポリシーを更新する |
> | microsoft.directory/userCredentialPolicies/owners/update | ユーザーの資格情報ポリシーの所有者を更新する |
> | microsoft.directory/userCredentialPolicies/tenantDefault/update | policy.isOrganizationDefault プロパティを更新する |

## <a name="azure-ad-joined-device-local-administrator"></a>Azure AD 参加済みデバイスのローカル管理者

この役割は、[デバイス設定](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)の追加のローカル管理者としてのみ割り当て可能です。 このロールのユーザーは、Azure Active Directory に参加しているすべての Windows 10 デバイスのローカル マシン管理者になります。 Azure Active Directory 内のデバイス オブジェクトを管理することはできません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/groupSettings/standard/read | グループ設定の基本プロパティを読み取る |
> | microsoft.directory/groupSettingTemplates/standard/read | グループ設定テンプレートの基本プロパティを読み取る |

## <a name="azure-devops-administrator"></a>Azure DevOps 管理者

このロールが割り当てられたユーザーは、Azure DevOps ポリシーを管理して、新しい Azure DevOps 組織の作成を、構成可能な一連のユーザーやグループに制限できます。 このロールのユーザーは、会社の Azure AD 組織によってサポートされている Azure DevOps 組織を通じて、このポリシーを管理できます。 このロールは、会社の Azure AD 組織によってサポートされている Azure DevOps 組織の中で、Azure DevOps 固有の他のアクセス許可 (プロジェクト コレクション管理者など) を付与しません。

エンタープライズ Azure DevOps のすべてのポリシーは、このロールのユーザーによって管理できます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.azure.devOps/allEntities/allTasks | Azure DevOps を読み取り、構成する |

## <a name="azure-information-protection-administrator"></a>Azure Information Protection 管理者

このロールが割り当てられたユーザーは、Azure Information Protection サービスのすべてのアクセス許可を持ちます。 このロールでは、Azure Information Protection ポリシーのラベルの構成、保護テンプレートの管理、保護のアクティブ化を行うことができます。 このロールでは、Identity Protection Center、Privileged Identity Management、Microsoft 365 Service Health の監視、および Office 365 のセキュリティ/コンプライアンス センターのアクセス許可は付与されません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection のすべての側面を管理する |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="b2c-ief-keyset-administrator"></a>B2C IEF キーセット管理者

ユーザーは、トークンの暗号化、トークンの署名、および要求の暗号化/暗号化解除のために、ポリシー キーとシークレットを作成して管理できます。  新しいキーを既存のキー コンテナーに追加すると、この制限付き管理者は、既存のアプリケーションに影響を与えることなく、必要に応じてシークレットをロールオーバーできます。  このユーザーは、これらのシークレットとその有効期限の完全な内容を、シークレットを作成した後でも確認できます。

> [!IMPORTANT]
> これは機密性の高いロールです。  キーセット管理者ロールは、慎重に監査し、運用前環境と運用環境では、慎重に割り当てる必要があります。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/b2cTrustFrameworkKeySet/allProperties/allTasks | 認可ポリシーのすべてのプロパティを読み取り、更新する |

## <a name="b2c-ief-policy-administrator"></a>B2C IEF ポリシー管理者

このロールが割り当てられたユーザーは、Azure AD B2C のすべてのカスタム ポリシーを作成、読み取り、更新、および削除することができます。そのため、関連する Azure AD B2C 組織内の Identity Experience Framework を完全に制御できます。 このユーザーは、ポリシーを編集することで、外部の ID プロバイダーとの直接フェデレーションの確立、ディレクトリ スキーマの変更、ユーザー向けのすべてのコンテンツ (HTML、CSS、JavaScript) の変更、認証を完了するための要件の変更、新しいユーザーの作成、ユーザー データの外部システムへの送信 (完全な移行を含む)、パスワードや電話番号などの機密フィールドを含むすべてのユーザー情報の編集を行うことができます。 逆に、このロールでは、暗号化キーを変更したり、組織内のフェデレーションに使用されているシークレットを編集したりすることはできません。

> [!IMPORTANT]
> B2 IEF ポリシー管理者は、非常に機密性の高いロールであるため、運用環境ではごく限られた組織に割り当てる必要があります。  これらのユーザーによるアクティビティは、とりわけ運用環境の組織に対しては、注意深く監査する必要があります。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/b2cTrustFrameworkPolicy/allProperties/allTasks | Azure Active Directory B2C のキー セットを読み取り、構成する |

## <a name="billing-administrator"></a>課金管理者

購入、サブスクリプションの管理、サポート チケットの管理、サービスの正常性の監視を行います。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/organization/basic/update | 組織で基本プロパティを更新する |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.commerce.billing/allEntities/allTasks | Office 365 課金のすべての側面を管理する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="cloud-application-administrator"></a>クラウド アプリケーション管理者

このロールのユーザーは、(アプリケーション プロキシを管理する権限を除き) アプリケーション管理者ロールと同じアクセス許可を持ちます。 このロールは、エンタープライズ アプリケーションとアプリケーション登録の全側面を作成して管理する権限を付与します。 このロールに割り当てられたユーザーは、新しいアプリケーション登録またはエンタープライズ アプリケーションを作成する際に、所有者として追加されません。

さらに、このロールは、委任されたアクセス許可とアプリケーション アクセス許可 (Microsoft Graph と Azure AD Graph の両方に対するアプリケーション アクセス許可を除く) に同意する権限を付与します。

> [!IMPORTANT]
> この例外は、"_他の_" アプリ (たとえば、Microsoft 以外のアプリや自分が登録したアプリなど) に対するアプリケーション アクセス許可には引き続き同意できることを意味します。 アプリ登録の一環としてこれらのアクセス許可を "_要求_" することはできますが、これらのアクセス許可を "_許可する_" (つまり、同意する) には、グローバル管理者などの特権管理者が必要です。
>
>このロールは、アプリケーションの資格情報を管理する権限を付与します。 このロールが割り当てられたユーザーは、アプリケーションに資格情報を追加し、その資格情報を使用してアプリケーションの ID を偽装することができます。 アプリケーションの ID にリソースへのアクセス権 (ユーザーやその他のオブジェクトの作成や更新など) が付与されている場合、このロールに割り当てられたユーザーは、アプリケーションを偽装している間にこのようなアクションを実行することができます。 アプリケーションの ID を偽装するこの機能は、ユーザーがロール割り当てを使用して実行できることを越えた特権の昇格になる可能性があります。 ユーザーにアプリケーション管理者ロールを割り当てると、そのユーザーがアプリケーションの ID を偽装できるようになることを理解することが重要です。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/applications/create | すべての種類のアプリケーションを作成する |
> | microsoft.directory/applications/delete | すべての種類のアプリケーションを削除する |
> | microsoft.directory/applications/appRoles/update | すべての種類のアプリケーションで appRoles プロパティを更新する |
> | microsoft.directory/applications/audience/update | アプリケーションの対象ユーザー プロパティを更新する |
> | microsoft.directory/applications/authentication/update | すべての種類のアプリケーションで認証を更新する |
> | microsoft.directory/applications/basic/update | アプリケーションの基本プロパティを更新する |
> | microsoft.directory/applications/credentials/update | アプリケーション資格情報を更新する |
> | microsoft.directory/applications/owners/update | アプリケーションの所有者を更新する |
> | microsoft.directory/applications/permissions/update | すべての種類のアプリケーションで、公開されたアクセス許可と必要なアクセス許可を更新する |
> | microsoft.directory/applications/policies/update | アプリケーションのポリシーを更新する |
> | microsoft.directory/applications/verification/update | applicationsverification プロパティを更新する |
> | microsoft.directory/applications/synchronization/standard/read | アプリケーション オブジェクトに関連付けられているプロビジョニング設定を読み取る |
> | microsoft.directory/applicationTemplates/instantiate | アプリケーション テンプレートからギャラリー アプリケーションのインスタンスを作成する |
> | microsoft.directory/auditLogs/allProperties/read | 監査ログのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0 アクセス許可の付与の作成と削除、およびすべてのプロパティの読み取りと更新 |
> | microsoft.directory/applicationPolicies/create | アプリケーション ポリシーを作成する |
> | microsoft.directory/applicationPolicies/delete | アプリケーション ポリシーを削除する |
> | microsoft.directory/applicationPolicies/standard/read | アプリケーション ポリシーの標準プロパティを読み取る |
> | microsoft.directory/applicationPolicies/owners/read | アプリケーション ポリシーの所有者を読み取る |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | オブジェクト リストに適用されているアプリケーション ポリシーを読み取る |
> | microsoft.directory/applicationPolicies/basic/update | アプリケーション ポリシーの標準プロパティを更新する |
> | microsoft.directory/applicationPolicies/owners/update | アプリケーション ポリシーの所有者プロパティを更新する |
> | microsoft.directory/provisioningLogs/allProperties/read | プロビジョニング ログのすべてのプロパティを読み取ります。 |
> | microsoft.directory/servicePrincipals/create | サービス プリンシパルの作成 |
> | microsoft.directory/servicePrincipals/delete | サービス プリンシパルを削除する |
> | microsoft.directory/servicePrincipals/disable | サービス プリンシパルを無効にする |
> | microsoft.directory/servicePrincipals/enable | サービス プリンシパルを有効にする |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | サービス プリンシパルのパスワード シングル サインオン資格情報を管理する |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | アプリケーション プロビジョニングのシークレットと資格情報を管理する |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | アプリケーション プロビジョニングの同期ジョブを開始、再開、一時停止する |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | アプリケーション プロビジョニングの同期ジョブとスキーマを作成および管理する |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | サービス プリンシパルのパスワード シングル サインオン資格情報を読み取る |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-application-admin | ユーザーまたはすべてのユーザーに代わって、アプリケーションのアクセス許可と委任されたアクセス許可に同意を付与する (Microsoft Graph と Azure AD Graph のアプリケーション アクセス許可は除く)  |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | サービス プリンシパルのロールの割り当ての更新 |
> | microsoft.directory/servicePrincipals/audience/update | サービス プリンシパルで対象ユーザー プロパティを更新する |
> | microsoft.directory/servicePrincipals/authentication/update | サービス プリンシパルで認証プロパティを更新する |
> | microsoft.directory/servicePrincipals/basic/update | サービス プリンシパルで基本プロパティを更新する |
> | microsoft.directory/servicePrincipals/credentials/update | サービス プリンシパルの資格情報を更新する |
> | microsoft.directory/servicePrincipals/owners/update | サービス プリンシパルの所有者を更新する |
> | microsoft.directory/servicePrincipals/permissions/update | サービスプリンシパルのアクセス許可を更新する |
> | microsoft.directory/servicePrincipals/policies/update | サービス プリンシパルのポリシーを更新する |
> | microsoft.directory/servicePrincipals/tag/update | サービスプリンシパルのタグ プロパティを更新する |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | サービス プリンシパルに関連付けられているプロビジョニング設定を読み取る |
> | microsoft.directory/signInReports/allProperties/read | サインイン情報レポートのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="cloud-device-administrator"></a>クラウド デバイス管理者

このロールのユーザーは、Azure AD でデバイスを有効化、無効化、および削除することができ、Azure portal で Windows 10 の BitLocker キーを読み取る (ある場合) ことができます。 このロールでは、デバイス上の他のプロパティを管理するアクセス許可の付与は行いません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | 監査ログのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.directory/bitlockerKeys/key/read | デバイス上の bitlocker メタデータとキーを読み取る |
> | microsoft.directory/devices/delete | Azure AD からデバイスを削除する |
> | microsoft.directory/devices/disable | Azure AD でデバイスを無効にする |
> | microsoft.directory/devices/enable | Azure AD でデバイスを有効にする |
> | microsoft.directory/devices/extensionAttributes/update | devices.extensionAttributes プロパティのすべての値を更新する |
> | microsoft.directory/deviceManagementPolicies/standard/read | デバイス管理アプリケーション ポリシーの標準プロパティの読み取り |
> | microsoft.directory/deviceManagementPolicies/basic/update | デバイス管理アプリケーション ポリシーの標準プロパティを更新する |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | デバイス登録ポリシーの標準プロパティの読み取り |
> | microsoft.directory/deviceRegistrationPolicy/basic/update | デバイス登録ポリシーの基本プロパティを更新する |
> | microsoft.directory/signInReports/allProperties/read | サインイン情報レポートのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |

## <a name="compliance-administrator"></a>コンプライアンス管理者

このロールのユーザーには、Microsoft 365 コンプライアンス センター、Microsoft 365 管理センター、Azure、および Office 365 セキュリティ/コンプライアンス センターのコンプライアンス関連の機能を管理する権限があります。 割り当て先は、Exchange 管理センター、Teams および Skype for Business の管理センター内のすべての機能を管理したり、Azure および Microsoft 365 のサポート チケットを作成したりすることもできます。 詳しくは、「[管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。

場所 | できること
----- | ----------
[Microsoft 365 コンプライアンス センター](https://protection.office.com) | Microsoft 365 サービス全体での組織のデータの保護および管理<br>コンプライアンス アラートの管理
[コンプライアンス マネージャー](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 組織の法令遵守活動の追跡、割り当て、確認
[Office 365 セキュリティ/コンプライアンス センター](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | データ ガバナンスの管理<br>法律およびデータ調査の実行<br>データ主体の要求の管理<br><br>このロールには、Office 365 セキュリティ/コンプライアンス センターのロールベースのアクセス制御の[コンプライアンス管理者ロール グループ](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)と同じアクセス許可が付与されています。
[Intune](/intune/role-based-access-control) | Intune のすべての監査データの表示
[Cloud App Security](/cloud-app-security/manage-admins) | 読み取り専用アクセス許可があり、アラートを管理できる<br>ファイル ポリシーの作成と変更、ファイル ガバナンス アクションの許可<br>データ管理下のすべての組み込みレポートの表示

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.directory/entitlementManagement/allProperties/read | Azure AD エンタイトルメント管理ですべてのプロパティを読み取る |
> | microsoft.office365.complianceManager/allEntities/allTasks | Office 365 コンプライアンス マネージャーの全側面の管理 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="compliance-data-administrator"></a>コンプライアンス データ管理者

このロールが割り当てられたユーザーには、Microsoft 365 コンプライアンス センター、Microsoft 365 管理センター、および Azure のデータを追跡するための権限があります。 ユーザーは、Exchange 管理センター、Compliance Manager、Teams および Skype for Business の管理センター内のコンプライアンス データを追跡したり、Azure および Microsoft 365 のサポート チケットを作成したりすることもできます。 [このドキュメント](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)には、コンプライアンス管理者とコンプライアンス データ管理者との違いが詳しく記載されています。

場所 | できること
----- | ----------
[Microsoft 365 コンプライアンス センター](https://protection.office.com) | Microsoft 365 サービス全体のコンプライアンス関連ポリシーの監視<br>コンプライアンス アラートの管理
[コンプライアンス マネージャー](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 組織の法令遵守活動の追跡、割り当て、確認
[Office 365 セキュリティ/コンプライアンス センター](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | データ ガバナンスの管理<br>法律およびデータ調査の実行<br>データ主体の要求の管理<br><br>このロールには、Office 365 セキュリティ/コンプライアンス センターのロールベースのアクセス制御の[コンプライアンス データ管理者ロール グループ](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)と同じアクセス許可が付与されています。
[Intune](/intune/role-based-access-control) | Intune のすべての監査データの表示
[Cloud App Security](/cloud-app-security/manage-admins) | 読み取り専用アクセス許可があり、アラートを管理できる<br>ファイル ポリシーの作成と変更、ファイル ガバナンス アクションの許可<br>データ管理下のすべての組み込みレポートの表示

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Microsoft Cloud App Security ですべてのリソースの作成と削除、標準プロパティの読み取りと更新を行う |
> | microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection のすべての側面を管理する |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.complianceManager/allEntities/allTasks | Office 365 コンプライアンス マネージャーの全側面の管理 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="conditional-access-administrator"></a>条件付きアクセス管理者

このロールのユーザーは、Azure Active Directory の条件付きアクセスの設定を管理できます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/conditionalAccessPolicies/create | 条件付きアクセス ポリシーを作成する |
> | microsoft.directory/conditionalAccessPolicies/delete | 条件付きアクセス ポリシーを削除する |
> | microsoft.directory/conditionalAccessPolicies/standard/read | policies.conditionalAccess プロパティを読み取る |
> | microsoft.directory/conditionalAccessPolicies/owners/read | policies.conditionalAccess プロパティを読み取る |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | policies.conditionalAccess プロパティを読み取る |
> | microsoft.directory/conditionalAccessPolicies/basic/update | 条件付きアクセス ポリシーの基本プロパティを更新する |
> | microsoft.directory/conditionalAccessPolicies/owners/update | policies.conditionalAccess プロパティを更新する |
> | microsoft.directory/conditionalAccessPolicies/tenantDefault/update | policies.conditionalAccess プロパティを更新する |
> | microsoft.directory/crossTenantAccessPolicies/create | テナント間アクセス ポリシーを作成する |
> | microsoft.directory/crossTenantAccessPolicies/delete | テナント間アクセス ポリシーを削除する |
> | microsoft.directory/crossTenantAccessPolicies/standard/read | テナント間アクセス ポリシーの基本プロパティを読み取る |
> | microsoft.directory/crossTenantAccessPolicies/owners/read | テナント間アクセス ポリシーの所有者を読み取る |
> | microsoft.directory/crossTenantAccessPolicies/policyAppliedTo/read | テナント間アクセス ポリシーの policyAppliedTo プロパティを読み取る |
> | microsoft.directory/crossTenantAccessPolicies/basic/update | テナント間アクセス ポリシーの基本プロパティを更新する |
> | microsoft.directory/crossTenantAccessPolicies/owners/update | テナント間アクセス ポリシーの所有者を更新する |
> | microsoft.directory/crossTenantAccessPolicies/tenantDefault/update | テナント間アクセス ポリシーの既定のテナントを更新する |

## <a name="customer-lockbox-access-approver"></a>カスタマー ロックボックスのアクセス承認者

組織内の[カスタマー ロックボックス要求](/office365/admin/manage/customer-lockbox-requests)を管理します。 彼らはカスタマー ロックボックス要求の電子メール通知を受信し、Microsoft 365 管理センターから要求の承認と拒否を行うことができます。 カスタマー ロックボックス機能を有効または無効にすることもできます。 グローバル管理者のみが、このロールに割り当てられているユーザーのパスワードをリセットできます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.office365.lockbox/allEntities/allTasks | カスタマー ロックボックスのすべての側面を管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="desktop-analytics-administrator"></a>デスクトップ Analytics 管理者

このロールのユーザーは、デスクトップ Analytics および Office のカスタマイズとポリシーのサービスを管理できます。 デスクトップ Analytics の場合、これには、資産インベントリの表示、デプロイ プランの作成、デプロイと正常性の状態の表示に対する権限が含まれます。 Office のカスタマイズとポリシーのサービスの場合、このロールによりユーザーは Office のポリシーを管理することができます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | Desktop Analytics のすべての側面を管理する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="directory-readers"></a>ディレクトリ リーダー

このロールのユーザーは、基本的なディレクトリ情報を読み取ることができます。 このロールは次の目的で使用してください。

* 読み取りアクセスをすべてのゲスト ユーザーに付与せず、特定のゲスト ユーザー セットに付与する。
* [Restrict access to Azure AD portal to admins only] (Azure AD ポータルへのアクセスを管理者のみに制限する) が [はい] に設定されている場合に、管理者以外の特定のユーザー セットに Azure portal へのアクセスを付与する。
* Directory.Read.All を選択できないディレクトリへのアクセスをサービス プリンシパルに付与する。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/administrativeUnits/standard/read | 管理単位の基本プロパティを読み取る |
> | microsoft.directory/administrativeUnits/members/read | 管理単位のメンバーを読み取る |
> | microsoft.directory/applications/standard/read | アプリケーションの標準プロパティを読み取る |
> | microsoft.directory/applications/owners/read | アプリケーションの所有者を読み取る |
> | microsoft.directory/applications/policies/read | アプリケーションのポリシーを読み取る |
> | microsoft.directory/contacts/standard/read | Azure AD で連絡先の基本プロパティを読み取る |
> | microsoft.directory/contacts/memberOf/read | Azure AD ですべての連絡先のグループ メンバーシップを読み取る |
> | microsoft.directory/contracts/standard/read | パートナー コントラクトの基本プロパティを読み取る |
> | microsoft.directory/devices/standard/read | デバイスで基本プロパティを読み取る |
> | microsoft.directory/devices/memberOf/read | デバイス メンバーシップを読み取る |
> | microsoft.directory/devices/registeredOwners/read | デバイスの登録済み所有者を読み取る |
> | microsoft.directory/devices/registeredUsers/read | デバイスの登録済みユーザーを読み取る |
> | microsoft.directory/directoryRoles/standard/read | Azure AD ロールの基本プロパティを更新する |
> | microsoft.directory/directoryRoles/eligibleMembers/read | Azure AD ロールの対象メンバーを読み取る |
> | microsoft.directory/directoryRoles/members/read | Azure AD ロールのすべてのメンバーを読み取る |
> | microsoft.directory/domains/standard/read | ドメインで基本プロパティを読み取る |
> | microsoft.directory/groups/standard/read | グループで基本プロパティを読み取る |
> | microsoft.directory/groups/appRoleAssignments/read | グループのアプリケーション ロールの割り当てを読み取る |
> | microsoft.directory/groups/memberOf/read | Azure AD でグループがメンバーになっているグループを読み取る |
> | microsoft.directory/groups/members/read | グループのメンバーを読み取る |
> | microsoft.directory/groups/owners/read | グループの所有者を読み取る |
> | microsoft.directory/groups/settings/read | グループの設定を読み取る |
> | microsoft.directory/groupSettings/standard/read | グループ設定の基本プロパティを読み取る |
> | microsoft.directory/groupSettingTemplates/standard/read | グループ設定テンプレートの基本プロパティを読み取る |
> | microsoft.directory/oAuth2PermissionGrants/standard/read | OAuth 2.0 アクセス許可付与の基本プロパティを読み取る |
> | microsoft.directory/organization/standard/read | 組織で基本プロパティを読み取る |
> | microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | パスワードレス認証用に信頼された証明機関を読み取る |
> | microsoft.directory/applicationPolicies/standard/read | アプリケーション ポリシーの標準プロパティを読み取る |
> | microsoft.directory/roleAssignments/standard/read | ロールの割り当ての基本プロパティを読み取る |
> | microsoft.directory/roleDefinitions/standard/read | ロールの定義の基本プロパティを読み取る |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | サービス プリンシパルのロールの割り当てを読み取る |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | サービス プリンシパルに割り当てられたロールの割り当てを読み取る |
> | microsoft.directory/servicePrincipals/standard/read | サービス プリンシパルの基本プロパティを読み取る |
> | microsoft.directory/servicePrincipals/memberOf/read | サービス プリンシパルのグループ メンバーシップを読み取る |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | サービス プリンシパルの委任されたアクセス許可付与を読み取る |
> | microsoft.directory/servicePrincipals/owners/read | サービス プリンシパルの所有者を読み取る |
> | microsoft.directory/servicePrincipals/ownedObjects/read | サービスプリンシパルの所有オブジェクトを読み取る |
> | microsoft.directory/servicePrincipals/policies/read | サービス プリンシパルのポリシーを読み取る |
> | microsoft.directory/subscribedSkus/standard/read | サブスクリプションの基本プロパティの読み取り |
> | microsoft.directory/users/standard/read | ユーザーの基本プロパティを読み取る |
> | microsoft.directory/users/appRoleAssignments/read | ユーザーのアプリケーション ロールの割り当てを読み取る |
> | microsoft.directory/users/directReports/read | ユーザーの直属の部下を読み取る |
> | microsoft.directory/users/manager/read | ユーザーのマネージャーを読み取る |
> | microsoft.directory/users/memberOf/read | ユーザーのグループ メンバーシップを読み取る |
> | microsoft.directory/users/oAuth2PermissionGrants/read | ユーザーの委任されたアクセス許可付与を読み取る |
> | microsoft.directory/users/ownedDevices/read | ユーザーの所有デバイスを読み取る |
> | microsoft.directory/users/ownedObjects/read | ユーザーの所有オブジェクトを読み取る |
> | microsoft.directory/users/registeredDevices/read | ユーザーの登録済みデバイスを読み取る |

## <a name="directory-synchronization-accounts"></a>ディレクトリ同期アカウント

使用しないでください。 このロールは、自動的に Azure AD Connect サービスに割り当てられます。他の用途に使用するためのものではなく、他の用途ではサポートされていません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/applications/create | すべての種類のアプリケーションを作成する |
> | microsoft.directory/applications/delete | すべての種類のアプリケーションを削除する |
> | microsoft.directory/applications/appRoles/update | すべての種類のアプリケーションで appRoles プロパティを更新する |
> | microsoft.directory/applications/audience/update | アプリケーションの対象ユーザー プロパティを更新する |
> | microsoft.directory/applications/authentication/update | すべての種類のアプリケーションで認証を更新する |
> | microsoft.directory/applications/basic/update | アプリケーションの基本プロパティを更新する |
> | microsoft.directory/applications/credentials/update | アプリケーション資格情報を更新する |
> | microsoft.directory/applications/owners/update | アプリケーションの所有者を更新する |
> | microsoft.directory/applications/permissions/update | すべての種類のアプリケーションで、公開されたアクセス許可と必要なアクセス許可を更新する |
> | microsoft.directory/applications/policies/update | アプリケーションのポリシーを更新する |
> | microsoft.directory/organization/dirSync/update | 組織のディレクトリ同期プロパティを更新する |
> | microsoft.directory/policies/create | Azure AD でポリシーを作成する |
> | microsoft.directory/policies/delete | Azure AD でポリシーを削除する |
> | microsoft.directory/policies/standard/read | ポリシーの基本プロパティを読み取る |
> | microsoft.directory/policies/owners/read | ポリシーの所有者を読み取る |
> | microsoft.directory/policies/policyAppliedTo/read | policies.policyAppliedTo プロパティを読み取る |
> | microsoft.directory/policies/basic/update | ポリシーの基本プロパティを更新する |
> | microsoft.directory/policies/owners/update | ポリシーの所有者を更新する |
> | microsoft.directory/policies/tenantDefault/update | 既定の組織ポリシーを更新する |
> | microsoft.directory/servicePrincipals/create | サービス プリンシパルの作成 |
> | microsoft.directory/servicePrincipals/delete | サービス プリンシパルを削除する |
> | microsoft.directory/servicePrincipals/enable | サービス プリンシパルを有効にする |
> | microsoft.directory/servicePrincipals/disable | サービス プリンシパルを無効にする |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | サービス プリンシパルのパスワード シングル サインオン資格情報を管理する |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | サービス プリンシパルのパスワード シングル サインオン資格情報を読み取る |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | サービス プリンシパルのロールの割り当てを読み取る |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | サービス プリンシパルに割り当てられたロールの割り当てを読み取る |
> | microsoft.directory/servicePrincipals/standard/read | サービス プリンシパルの基本プロパティを読み取る |
> | microsoft.directory/servicePrincipals/memberOf/read | サービス プリンシパルのグループ メンバーシップを読み取る |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | サービス プリンシパルの委任されたアクセス許可付与を読み取る |
> | microsoft.directory/servicePrincipals/owners/read | サービス プリンシパルの所有者を読み取る |
> | microsoft.directory/servicePrincipals/ownedObjects/read | サービスプリンシパルの所有オブジェクトを読み取る |
> | microsoft.directory/servicePrincipals/policies/read | サービス プリンシパルのポリシーを読み取る |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | サービス プリンシパルのロールの割り当ての更新 |
> | microsoft.directory/servicePrincipals/audience/update | サービス プリンシパルで対象ユーザー プロパティを更新する |
> | microsoft.directory/servicePrincipals/authentication/update | サービス プリンシパルで認証プロパティを更新する |
> | microsoft.directory/servicePrincipals/basic/update | サービス プリンシパルで基本プロパティを更新する |
> | microsoft.directory/servicePrincipals/credentials/update | サービス プリンシパルの資格情報を更新する |
> | microsoft.directory/servicePrincipals/owners/update | サービス プリンシパルの所有者を更新する |
> | microsoft.directory/servicePrincipals/permissions/update | サービスプリンシパルのアクセス許可を更新する |
> | microsoft.directory/servicePrincipals/policies/update | サービス プリンシパルのポリシーを更新する |
> | microsoft.directory/servicePrincipals/tag/update | サービスプリンシパルのタグ プロパティを更新する |

## <a name="directory-writers"></a>ディレクトリ ライター

このロールのユーザーは、ユーザー、グループ、およびサービス プリンシパルの基本情報の読み取りと更新が可能です。 このロールは、[同意フレームワーク](../develop/quickstart-register-app.md)をサポートしていないアプリケーションにのみ割り当てます。 このロールをユーザーに割り当てることはできません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/groups/assignLicense | グループベースのライセンスのグループに製品ライセンスを割り当てる |
> | microsoft.directory/groups/create | ロール割り当て可能なグループを除き、グループを削除する |
> | microsoft.directory/groups/reprocessLicenseAssignment | グループベースのライセンスのライセンス割り当てを再処理する |
> | microsoft.directory/groups/basic/update | ロール割り当て可能なグループを除き、グループの基本プロパティを更新する |
> | microsoft.directory/groups/classification/update | ロール割り当て可能なグループを除き、グループの分類プロパティを更新する |
> | microsoft.directory/groups/dynamicMembershipRule/update | ロール割り当て可能なグループを除き、グループの動的メンバーシップ規則を更新する |
> | microsoft.directory/groups/groupType/update | グループの groupType プロパティを更新する |
> | microsoft.directory/groups/members/update | ロール割り当て可能なグループを除き、グループのメンバーを更新する |
> | microsoft.directory/groups/onPremWriteBack/update | オンプレミスに書き戻されるように Azure AD グループを更新する |
> | microsoft.directory/groups/owners/update | ロール割り当て可能なグループを除くグループの所有者の更新 |
> | microsoft.directory/groups/settings/update | グループの設定を更新する |
> | microsoft.directory/groups/visibility/update | グループの可視性プロパティを更新する |
> | microsoft.directory/groupSettings/create | グループ設定の作成 |
> | microsoft.directory/groupSettings/delete | グループ設定の削除 |
> | microsoft.directory/groupSettings/basic/update | グループ設定の基本プロパティを更新する |
> | microsoft.directory/oAuth2PermissionGrants/create | OAuth 2.0 アクセス許可付与を作成する |
> | microsoft.directory/oAuth2PermissionGrants/basic/update | OAuth 2.0 アクセス許可付与を更新する |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | アプリケーション プロビジョニングのシークレットと資格情報を管理する |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | アプリケーション プロビジョニングの同期ジョブを開始、再開、一時停止する |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | アプリケーション プロビジョニングの同期ジョブとスキーマを作成および管理する |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | サービス プリンシパルにグループのデータへの直接アクセスを許可する |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | サービス プリンシパルのロールの割り当ての更新 |
> | microsoft.directory/users/assignLicense | ユーザー ライセンスの管理 |
> | microsoft.directory/users/create | ユーザーの追加 |
> | microsoft.directory/users/disable | ユーザーの無効化 |
> | microsoft.directory/users/enable | ユーザーの有効化 |
> | microsoft.directory/users/invalidateAllRefreshTokens | ユーザー更新トークンを無効にして強制的にサインアウトする |
> | microsoft.directory/users/reprocessLicenseAssignment | ユーザーのライセンス割り当てを再処理する |
> | microsoft.directory/users/basic/update | ユーザーの基本プロパティを更新する |
> | microsoft.directory/users/manager/update | ユーザーのマネージャーを更新する |
> | microsoft.directory/users/userPrincipalName/update | ユーザーのユーザー プリンシパル名を更新する |

## <a name="domain-name-administrator"></a>ドメイン名管理者

このロールのユーザーは、ドメイン名の管理 (読み取り、追加、検証、更新、および削除) を行うことができます。 また、ユーザー、グループ、およびアプリケーションに関するディレクトリ情報も読み取ることができます。これらのオブジェクトはドメインの依存関係を所有しているためです。 オンプレミス環境の場合、このロールを持つユーザーは、関連付けられたユーザーが常にオンプレミスで認証されるように、フェデレーションのドメイン名を構成できます。 これらのユーザーは、シングル サインオンを介してオンプレミスのパスワードを使用して Azure AD ベースのサービスにサインインできます。 フェデレーション設定を Azure AD Connect 経由で同期する必要があるため、ユーザーは Azure AD Connect を管理する権限も持っています。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/domains/allProperties/allTasks | ドメインの作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |

## <a name="dynamics-365-administrator"></a>Dynamics 365 管理者

このロールが割り当てられたユーザーは、Microsoft Dynamics 365 Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、サポート チケットを管理し、サービス正常性を監視できます。 詳細については、[サービス管理者ロールを使用した Azure AD 組織の管理](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)に関する記事をご覧ください。

> [!NOTE]
> Microsoft Graph API と Azure AD PowerShell では、このロールは "Dynamics 365 サービス管理者" として識別されます。 [Azure portal](https://portal.azure.com) では、"Dynamics 365 管理者" になります。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.dynamics365/allEntities/allTasks | Dynamics 365 のすべての側面を管理する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="exchange-administrator"></a>Exchange 管理者

このロールが割り当てられたユーザーは、Microsoft Exchange Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。 また、すべての Microsoft 365 グループの作成および管理、サポート チケットの管理、サービスの正常性の監視を行うこともできます。 詳細については、「[管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。

> [!NOTE]
> Microsoft Graph API と Azure AD PowerShell では、このロールは "Exchange サービス管理者" として識別されます。 [Azure portal](https://portal.azure.com) では、"Exchange 管理者" になります。 [Exchange 管理センター](https://go.microsoft.com/fwlink/p/?LinkID=529144)では "Exchange Online 管理者" になります。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/groups/hiddenMembers/read | グループの非表示メンバーの読み取り |
> | microsoft.directory/groups.unified/create | ロールの割り当て可能なグループを除外して、Microsoft 365 グループを作成する |
> | microsoft.directory/groups.unified/delete | ロールの割り当て可能なグループを除外して、Microsoft 365 グループを削除する |
> | microsoft.directory/groups.unified/restore | Microsoft 365 グループを復元します。 |
> | microsoft.directory/groups.unified/basic/update | ロールの割り当て可能なグループを除外して、Microsoft 365 グループの基本プロパティを更新する |
> | microsoft.directory/groups.unified/members/update | ロールの割り当て可能なグループを除外して、Microsoft 365 グループのメンバーを更新する |
> | microsoft.directory/groups.unified/owners/update | ロールの割り当て可能なグループを除外して、Microsoft 365 グループの所有者を更新する |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.exchange/allEntities/allTasks | Exchange Online のすべての側面を管理する |
> | microsoft.office365.network/performance/allProperties/read | Microsoft 365 管理センターで、すべてのネットワーク パフォーマンス プロパティを読み取る |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 の使用状況レポートを読み取る |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="external-id-user-flow-administrator"></a>外部 ID ユーザー フロー管理者

このロールが割り当てられたユーザーは、Azure portal でユーザー フロー ("組み込み" ポリシーとも呼ばれます) を作成および管理することができます。 これらのユーザーは、HTML/CSS/JavaScript コンテンツのカスタマイズ、MFA 要件の変更、トークン内のクレームの選択、API コネクタの管理、および Azure AD 組織内のすべてのユーザー フローのセッション設定の構成を行うことができます。 その一方で、このロールには、ユーザーのデータを確認したり、組織スキーマに含まれている属性を変更したりする機能は含まれていません。 Identity Experience Framework ポリシー (カスタム ポリシーとも呼ばれます) の変更も、このロールの範囲外です。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/b2cUserFlow/allProperties/allTasks | Azure Active Directory B2C のユーザー属性を読み取り、構成する |

## <a name="external-id-user-flow-attribute-administrator"></a>外部 ID ユーザー フロー属性管理者

このロールが割り当てられたユーザーは、Azure AD 組織内のすべてのユーザー フローで使用可能なカスタム属性を追加または削除できます。  そのため、このロールが割り当てられたユーザーは、エンド ユーザー スキーマを変更、またはエンド ユーザー スキーマに新しい要素を追加して、すべてのユーザー フローの動作に影響を及ぼしたり、エンド ユーザーが求め、最終的には要求としてアプリケーションに送信されるデータを間接的に変更したりできます。  このロールでユーザー フローを編集することはできません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/b2cUserAttribute/allProperties/allTasks | Azure Active Directory B2C のカスタム ポリシーを読み取り、構成する |

## <a name="external-identity-provider-administrator"></a>外部 ID プロバイダー管理者

この管理者は、Azure AD 組織と外部 ID プロバイダー間のフェデレーションを管理します。  このロールが割り当てられたユーザーは、新しい ID プロバイダーを追加し、使用可能なすべての設定 (認証パス、サービス ID、割り当てられたキー コンテナーなど) を構成できます。  このユーザーは、Azure AD 組織が外部 ID プロバイダーからの認証を信頼できるようにすることができます。  その結果としてエンド ユーザー エクスペリエンスに及ぼす影響は、組織の種類によって異なります。

* 従業員とパートナー向けの Azure AD 組織:(たとえば Gmail との) フェデレーションの追加は、まだ招待に応じていないすべてのゲストの招待にすぐに影響します。 「[Google を B2B ゲスト ユーザーの ID プロバイダーとして追加する](../external-identities/google-federation.md)」を参照してください。
* Azure Active Directory B2C 組織:(たとえば Facebook、または別の Azure AD 組織との) フェデレーションの追加は、ユーザー フロー (組み込みポリシーとも呼ばれます) で ID プロバイダーがオプションとして追加されるまで、エンド ユーザー フローにすぐに影響することはありません。 例については、[ID プロバイダーとしての Microsoft アカウントの構成](../../active-directory-b2c/identity-provider-microsoft-account.md)に関するページを参照してください。  ユーザー フローを変更するには、"B2C ユーザー フロー管理者" の制限されたロールが必要です。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/identityProviders/allProperties/allTasks | Azure Active Directory B2C の ID プロバイダーを読み取り、構成する |

## <a name="global-administrator"></a>グローバル管理者

このロールが割り当てられたユーザーは、Azure Active Directory のすべての管理機能と、Azure Active Directory の ID を使用するサービス (Microsoft 365 セキュリティ センター、Microsoft 365 security center コンプライアンス センター、Exchange Online、SharePoint Online、Skype for Business Online など) にアクセスできます。 さらにグローバル管理者は、[アクセスを昇格させる](../../role-based-access-control/elevate-access-global-admin.md)ことで、すべての Azure サブスクリプションと管理グループを管理できます。 これにより、グローバル管理者は各 Azure AD テナントを使用して、すべての Azure リソースに対するフル アクセスを取得できます。 Azure AD 組織にサインアップしたユーザーがグローバル管理者になります。 会社に複数のグローバル管理者がいてもかまいません。 グローバル管理者は、すべてのユーザーと他のすべての管理者のパスワードをリセットできます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | 管理単位 (メンバーを含む) の作成と管理。 |
> | microsoft.directory/applications/allProperties/allTasks | アプリケーションの作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.directory/applications/synchronization/standard/read | アプリケーション オブジェクトに関連付けられているプロビジョニング設定を読み取る |
> | microsoft.directory/applicationTemplates/instantiate | アプリケーション テンプレートからギャラリー アプリケーションのインスタンスを作成する |
> | microsoft.directory/appRoleAssignments/allProperties/allTasks | appRoleAssignments の作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.directory/auditLogs/allProperties/read | 監査ログのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.directory/authorizationPolicy/allProperties/allTasks | 認可ポリシーのすべての側面を管理する |
> | microsoft.directory/bitlockerKeys/key/read | デバイス上の bitlocker メタデータとキーを読み取る |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Microsoft Cloud App Security ですべてのリソースの作成と削除、標準プロパティの読み取りと更新を行う |
> | microsoft.directory/connectors/create | アプリケーション プロキシ コネクタを作成する |
> | microsoft.directory/connectors/allProperties/read | アプリケーション プロキシ コネクタのすべてのプロパティを読み取る |
> | microsoft.directory/connectorGroups/create | アプリケーション プロキシ コネクタ グループを作成する |
> | microsoft.directory/connectorGroups/delete | アプリケーション プロキシ コネクタ グループを削除する |
> | microsoft.directory/connectorGroups/allProperties/read | アプリケーション プロキシ コネクタ グループのすべてのプロパティを読み取る |
> | microsoft.directory/connectorGroups/allProperties/update | アプリケーション プロキシ コネクタ グループのすべてのプロパティを更新する |
> | microsoft.directory/contacts/allProperties/allTasks | 連絡先の作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.directory/contracts/allProperties/allTasks | パートナー コントラクトの作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.directory/devices/allProperties/allTasks | デバイスの作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.directory/deviceManagementPolicies/standard/read | デバイス管理アプリケーション ポリシーの標準プロパティの読み取り |
> | microsoft.directory/deviceManagementPolicies/basic/update | デバイス管理アプリケーション ポリシーの標準プロパティを更新する |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | デバイス登録ポリシーの標準プロパティの読み取り |
> | microsoft.directory/deviceRegistrationPolicy/basic/update | デバイス登録ポリシーの基本プロパティを更新する |
> | microsoft.directory/directoryRoles/allProperties/allTasks | ディレクトリ ロールの作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.directory/directoryRoleTemplates/allProperties/allTasks | Azure AD ロール テンプレートの作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.directory/domains/allProperties/allTasks | ドメインの作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | Azure AD エンタイトルメント管理でのリソースの作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.directory/groups/allProperties/allTasks | グループの作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.directory/groupsAssignableToRoles/allProperties/update | isAssignableToRole プロパティが true に設定されているグループを更新する |
> | microsoft.directory/groupsAssignableToRoles/create | isAssignableToRole プロパティが true に設定されているグループを作成する |
> | microsoft.directory/groupsAssignableToRoles/delete | isAssignableToRole プロパティが true に設定されているグループを削除する |
> | microsoft.directory/groupSettings/allProperties/allTasks | グループ設定の作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.directory/groupSettingTemplates/allProperties/allTasks | グループ設定テンプレートの作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.directory/identityProtection/allProperties/allTasks | Azure AD Identity Protection ですべてのリソースの作成と削除、標準プロパティの読み取りと更新を行う |
> | microsoft.directory/loginOrganizationBranding/allProperties/allTasks | loginTenantBranding の作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0 アクセス許可の付与の作成と削除、およびすべてのプロパティの読み取りと更新 |
> | microsoft.directory/organization/allProperties/allTasks | 組織の作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.directory/policies/allProperties/allTasks | ポリシーの作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.directory/conditionalAccessPolicies/allProperties/allTasks | 条件付きアクセス ポリシーのすべてのプロパティを管理する |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Privileged Identity Management のすべてのリソースを読み取る |
> | microsoft.directory/provisioningLogs/allProperties/read | プロビジョニング ログのすべてのプロパティを読み取ります。 |
> | microsoft.directory/roleAssignments/allProperties/allTasks | ロールの割り当ての作成と削除、およびすべてのロールの割り当てプロパティの読み取りと更新 |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | ロールの定義の作成と削除、およびすべてのプロパティの読み取りと更新 |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | scopedRoleMemberships の作成と削除、およびすべてのプロパティの読み取りと更新 |
> | microsoft.directory/serviceAction/activateService | サービスに対して "サービスのアクティブ化" アクションを実行できる |
> | microsoft.directory/serviceAction/disableDirectoryFeature | "ディレクトリ機能を無効にする" サービス アクションを実行できる |
> | microsoft.directory/serviceAction/enableDirectoryFeature | "ディレクトリ機能を有効にする" サービス アクションを実行できる |
> | microsoft.directory/serviceAction/getAvailableExtentionProperties | Getavailableextentionproperties サービス アクションを実行できる |
> | microsoft.directory/servicePrincipals/allProperties/allTasks | サービス プリンシパルの作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-company-admin | 任意のアプリケーションに対するすべてのアクセス許可に同意を付与する |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | サービス プリンシパルにグループのデータへの直接アクセスを許可する  |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | サービス プリンシパルに関連付けられているプロビジョニング設定を読み取る |
> | microsoft.directory/signInReports/allProperties/read | サインイン情報レポートのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.directory/subscribedSkus/allProperties/allTasks | サブスクリプションの購入と管理、サブスクリプションの削除を行う |
> | microsoft.directory/users/allProperties/allTasks | ユーザーの作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.directory/permissionGrantPolicies/create | アクセス許可付与ポリシーを作成する |
> | microsoft.directory/permissionGrantPolicies/delete | アクセス許可付与ポリシーを削除する |
> | microsoft.directory/permissionGrantPolicies/standard/read | アクセス許可付与ポリシーの標準プロパティを読み取る |
> | microsoft.directory/permissionGrantPolicies/basic/update | アクセス許可付与ポリシーの基本プロパティを更新する |
> | microsoft.azure.advancedThreatProtection/allEntities/allTasks | Azure Advanced Threat Protection のすべての側面を管理する |
> | microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection のすべての側面を管理する |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.commerce.billing/allEntities/allTasks | Office 365 課金のすべての側面を管理する |
> | microsoft.dynamics365/allEntities/allTasks | Dynamics 365 のすべての側面を管理する |
> | microsoft.flow/allEntities/allTasks | Power Automate のすべての側面を管理する |
> | microsoft.intune/allEntities/allTasks | Microsoft Intune のすべての側面を管理する |
> | microsoft.office365.complianceManager/allEntities/allTasks | Office 365 コンプライアンス マネージャーの全側面の管理 |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | Desktop Analytics のすべての側面を管理する |
> | microsoft.office365.exchange/allEntities/allTasks | Exchange Online のすべての側面を管理する |
> | microsoft.office365.lockbox/allEntities/allTasks | カスタマー ロックボックスのすべての側面を管理する |
> | microsoft.office365.messageCenter/messages/read | Microsoft 365 管理センターのメッセージ センターで、セキュリティ メッセージを除くメッセージを読み取る |
> | microsoft.office365.messageCenter/securityMessages/read | Microsoft 365 管理センターのメッセージ センターでセキュリティ メッセージを読み取る |
> | microsoft.office365.protectionCenter/allEntities/allProperties/allTasks | Office 365 プロテクション センターのすべての側面を管理する |
> | microsoft.office365.search/content/manage | Microsoft Search でコンテンツの作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | Microsoft 365 セキュリティおよびコンプライアンス センターですべてのリソースの作成と削除、標準プロパティの読み取りと更新を行う |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.sharePoint/allEntities/allTasks | SharePoint ですべてのリソースの作成と削除、標準プロパティの読み取りと更新を行う |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Skype for Business Online の全側面の管理 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 の使用状況レポートを読み取る |
> | microsoft.office365.userCommunication/allEntities/allTasks | 新機能のメッセージを表示できるかどうかを読み取り、更新する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |
> | microsoft.powerApps/allEntities/allTasks | Power Apps のすべての側面を管理する |
> | microsoft.powerApps.powerBI/allEntities/allTasks | Power BI のすべての側面を管理する |
> | microsoft.windows.defenderAdvancedThreatProtection/allEntities/allTasks | エンドポイントに対して Microsoft Defender のすべての側面を管理する |

## <a name="global-reader"></a>グローバル閲覧者

このロールのユーザーは、Microsoft 365 の各サービスにわたって設定と管理情報を読み取ることができますが、管理アクションを実行することはできません。 グローバル閲覧者は、グローバル管理者に対応する読み取り専用のロールです。 計画、監査、調査については、グローバル管理者ではなくグローバル閲覧者を割り当てます。 Exchange 管理者など、制限付きの他の管理者ロールとグローバル閲覧者を組み合わせて使用すると、全体管理者ロールを割り当てる必要がなく作業が楽になります。 グローバル閲覧者は、Microsoft 365 管理センター、Exchange 管理センター、SharePoint 管理センター、Teams 管理センター、セキュリティ センター、コンプライアンス センター、Azure AD 管理センター、デバイス管理の管理センターと連動します。

> [!NOTE]
> グローバル閲覧者ロールには現在、制限事項がいくつか与えられています。
>
>- [OneDrive 管理センター](https://admin.onedrive.com/) - OneDrive 管理センターでは、グローバル閲覧者ロールはサポートされていません。
>- [M365 管理者センター](https://admin.microsoft.com/Adminportal/Home#/homepage) - グローバル閲覧者はカスタマー ロックボックス要求を閲覧できません。 M365 管理者センターの左側のウィンドウにある **[サポート]** の下に **[カスタマー ロックボックス要求]** タブがありません。
>- [Office セキュリティ/コンプライアンス センター](https://sip.protection.office.com/homepage) - グローバル閲覧者は、SCC 監査ログを読み取ることも、コンテンツ検索を実行することも、セキュリティ スコアを確認することもできません。
>- [Teams 管理者センター](https://admin.teams.microsoft.com) - グローバル閲覧者は **Teams ライフサイクル**、**分析 & レポート**、**IP 電話デバイス管理**、**アプリ カタログ** を閲覧できません。
>- [Privileged Access Management (PAM)](/office365/securitycompliance/privileged-access-management-overview) では、グローバル閲覧者ロールがサポートされていません。
>- [Azure Information Protection](/azure/information-protection/what-is-information-protection) - グローバル閲覧者は、[中央レポート](/azure/information-protection/reports-aip)のみでサポートされ、Azure AD 組織が [統合ラベル付けプラットフォーム](/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)にない場合にサポートされます。
>
> 以上の機能は現在、開発中です。
>

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/applications/applicationProxy/read | すべてのアプリケーション プロキシ プロパティを読み取る |
> | microsoft.directory/applications/synchronization/standard/read | アプリケーション オブジェクトに関連付けられているプロビジョニング設定を読み取る |
> | microsoft.directory/auditLogs/allProperties/read | 監査ログのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.directory/bitlockerKeys/key/read | デバイス上の bitlocker メタデータとキーを読み取る |
> | microsoft.directory/connectors/allProperties/read | アプリケーション プロキシ コネクタのすべてのプロパティを読み取る |
> | microsoft.directory/connectorGroups/allProperties/read | アプリケーション プロキシ コネクタ グループのすべてのプロパティを読み取る |
> | microsoft.directory/entitlementManagement/allProperties/read | Azure AD エンタイトルメント管理ですべてのプロパティを読み取る |
> | microsoft.directory/deviceManagementPolicies/standard/read | デバイス管理アプリケーション ポリシーの標準プロパティの読み取り |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | デバイス登録ポリシーの標準プロパティの読み取り |
> | microsoft.directory/groups/hiddenMembers/read | グループの非表示メンバーの読み取り |
> | microsoft.directory/policies/standard/read | ポリシーの基本プロパティを読み取る |
> | microsoft.directory/policies/owners/read | ポリシーの所有者を読み取る |
> | microsoft.directory/policies/policyAppliedTo/read | policies.policyAppliedTo プロパティを読み取る |
> | microsoft.directory/conditionalAccessPolicies/standard/read | policies.conditionalAccess プロパティを読み取る |
> | microsoft.directory/conditionalAccessPolicies/owners/read | policies.conditionalAccess プロパティを読み取る |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | policies.conditionalAccess プロパティを読み取る |
> | microsoft.directory/provisioningLogs/allProperties/read | プロビジョニング ログのすべてのプロパティを読み取ります。 |
> | microsoft.directory/servicePrincipals/authentication/read | サービス プリンシパルの認証プロパティを読み取る |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | サービス プリンシパルに関連付けられているプロビジョニング設定を読み取る |
> | microsoft.directory/signInReports/allProperties/read | サインイン情報レポートのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.directory/users/strongAuthentication/read | ユーザーの強力な認証プロパティを読み取る |
> | microsoft.commerce.billing/allEntities/read | Office 365 課金情報のすべてのリソースを読み取る |
> | microsoft.office365.exchange/allEntities/read | Exchange Online のすべてのリソースを読み取る |
> | microsoft.office365.messageCenter/messages/read | Microsoft 365 管理センターのメッセージ センターで、セキュリティ メッセージを除くメッセージを読み取る |
> | microsoft.office365.messageCenter/securityMessages/read | Microsoft 365 管理センターのメッセージ センターでセキュリティ メッセージを読み取る |
> | microsoft.office365.network/performance/allProperties/read | Microsoft 365 管理センターで、すべてのネットワーク パフォーマンス プロパティを読み取る |
> | microsoft.office365.protectionCenter/allEntities/allProperties/read | Office 365 プロテクション センターのすべての側面を読み取る |
> | microsoft.office365.securityComplianceCenter/allEntities/read | Microsoft 365 セキュリティおよびコンプライアンス センターで標準プロパティを読み取る |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 の使用状況レポートを読み取る |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="groups-administrator"></a>グループ管理者

このロールのユーザーは、グループとその設定 (命名ポリシーや有効期限ポリシーなど) を作成/管理できます。 このロールにユーザーを割り当てることにより、Outlook だけでなく、Teams、SharePoint、Yammer などのさまざまなワークロードにわたって、組織内のすべてのグループを管理する機能がユーザーに付与されるということを理解しておくことが重要です。 また、そのユーザーは、Microsoft 管理センター、Azure portal などのさまざまな管理者ポータル全般のさまざまなグループ設定を管理できるほか、Teams や SharePoint 管理センターなどのワークロード固有の場合についても管理できます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/groups/assignLicense | グループベースのライセンスのグループに製品ライセンスを割り当てる |
> | microsoft.directory/groups/create | ロール割り当て可能なグループを除き、グループを削除する |
> | microsoft.directory/groups/delete | ロール割り当て可能なグループを除くグループの削除 |
> | microsoft.directory/groups/hiddenMembers/read | グループの非表示メンバーの読み取り |
> | microsoft.directory/groups/reprocessLicenseAssignment | グループベースのライセンスのライセンス割り当てを再処理する |
> | microsoft.directory/groups/restore | 削除されたグループの復元 |
> | microsoft.directory/groups/basic/update | ロール割り当て可能なグループを除き、グループの基本プロパティを更新する |
> | microsoft.directory/groups/classification/update | ロール割り当て可能なグループを除き、グループの分類プロパティを更新する |
> | microsoft.directory/groups/dynamicMembershipRule/update | ロール割り当て可能なグループを除き、グループの動的メンバーシップ規則を更新する |
> | microsoft.directory/groups/groupType/update | グループの groupType プロパティを更新する |
> | microsoft.directory/groups/members/update | ロール割り当て可能なグループを除き、グループのメンバーを更新する |
> | microsoft.directory/groups/onPremWriteBack/update | オンプレミスに書き戻されるように Azure AD グループを更新する |
> | microsoft.directory/groups/owners/update | ロール割り当て可能なグループを除くグループの所有者の更新 |
> | microsoft.directory/groups/settings/update | グループの設定を更新する |
> | microsoft.directory/groups/visibility/update | グループの可視性プロパティを更新する |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | サービス プリンシパルにグループのデータへの直接アクセスを許可する  |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="guest-inviter"></a>ゲスト招待元

このロールが割り当てられたユーザーは、 **[メンバーは招待ができる]** ユーザー設定が [いいえ] に設定されている場合に、Azure Active Directory B2B ゲスト ユーザーの招待を管理できます。 B2B コラボレーションの詳細については、「[Azure AD B2B コラボレーションとは](../external-identities/what-is-b2b.md)」をご覧ください。 その他の権限は含まれません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/users/inviteGuest | ゲスト ユーザーを招待する |
> | microsoft.directory/users/standard/read | ユーザーの基本プロパティを読み取る |
> | microsoft.directory/users/appRoleAssignments/read | ユーザーのアプリケーション ロールの割り当てを読み取る |
> | microsoft.directory/users/directReports/read | ユーザーの直属の部下を読み取る |
> | microsoft.directory/users/manager/read | ユーザーのマネージャーを読み取る |
> | microsoft.directory/users/memberOf/read | ユーザーのグループ メンバーシップを読み取る |
> | microsoft.directory/users/oAuth2PermissionGrants/read | ユーザーの委任されたアクセス許可付与を読み取る |
> | microsoft.directory/users/ownedDevices/read | ユーザーの所有デバイスを読み取る |
> | microsoft.directory/users/ownedObjects/read | ユーザーの所有オブジェクトを読み取る |
> | microsoft.directory/users/registeredDevices/read | ユーザーの登録済みデバイスを読み取る |

## <a name="helpdesk-administrator"></a>ヘルプデスク管理者

このロールが割り当てられたユーザーは、パスワードの変更、更新トークンの無効化、サービス要求の管理、サービス正常性の監視を行うことができます。 更新トークンを無効にすると、ユーザーは再度サインインすることを強制されます。 ヘルプデスク管理者がユーザーのパスワードをリセットして更新トークンを無効にできるかどうかは、ユーザーが割り当てられているロールに依存します。 ヘルプデスク管理者がパスワードをリセットして更新トークンを無効にできるロールの一覧については、「[パスワード リセットのアクセス許可](#password-reset-permissions)」を参照してください。

> [!IMPORTANT]
> このロールを持つユーザーは、機密情報や個人情報または Azure Active Directory の内外の重要な構成にアクセスできるユーザーのパスワードを変更できます。 ユーザーのパスワードを変更するということは、そのユーザーの ID およびアクセス許可を取得できるということを意味します。 次に例を示します。
>
>- 所有しているアプリの資格情報を管理できる、アプリケーションの登録とエンタープライズ アプリケーションの所有者。 これらのアプリには、Azure AD およびヘルプデスク管理者に付与されていない場所への特権アクセス許可がある場合があります。 ヘルプデスク管理者は、このパスからアプリケーション所有者の ID を取得し、さらにそのアプリケーションの資格情報を更新して特権アプリケーションの ID を取得できる場合があります。
>- 機密情報や個人情報または Azure の重要な構成にアクセスする可能性がある Azure サブスクリプション所有者。
>- グループ メンバーシップを管理できるセキュリティ グループと Microsoft 365 グループの所有者。 これらのグループは、機密情報や個人情報または Azure AD や別の場所の重要な構成へのアクセス権を付与される場合があります。
>- Exchange Online、Office Security and Compliance Center、人事システムのような Azure AD 以外のサービスの管理者。
>- 機密情報や個人情報にアクセスできる場合がある役員、弁護士、人事担当者のような非管理者。

ユーザーのサブセットに管理アクセス許可を委任する、およびポリシーを適用するには、[管理単位](administrative-units.md)を使用します。

このロールは、[Azure portal](https://portal.azure.com/) で以前は "パスワード管理者" と呼ばれていました。 Azure AD での名前である "ヘルプデスク管理者" は、Azure AD PowerShell および Microsoft Graph API での名前に一致するようになりました。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/bitlockerKeys/key/read | デバイス上の bitlocker メタデータとキーを読み取る |
> | microsoft.directory/users/invalidateAllRefreshTokens | ユーザー更新トークンを無効にして強制的にサインアウトする |
> | microsoft.directory/users/password/update | すべてのユーザーのパスワードをリセットする |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="hybrid-identity-administrator"></a>ハイブリッド ID の管理者

このロールのユーザーは、クラウド プロビジョニングを使用して AD から Azure AD へのプロビジョニング構成設定を作成、管理、デプロイしたり、フェデレーション設定を管理したりできます。 ユーザーはこのロールを使用して、ログのトラブルシューティングと監視を行うこともできます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/applications/create | すべての種類のアプリケーションを作成する |
> | microsoft.directory/applications/delete | すべての種類のアプリケーションを削除する |
> | microsoft.directory/applications/appRoles/update | すべての種類のアプリケーションで appRoles プロパティを更新する |
> | microsoft.directory/applications/audience/update | アプリケーションの対象ユーザー プロパティを更新する |
> | microsoft.directory/applications/authentication/update | すべての種類のアプリケーションで認証を更新する |
> | microsoft.directory/applications/basic/update | アプリケーションの基本プロパティを更新する |
> | microsoft.directory/applications/credentials/update | アプリケーション資格情報を更新する |
> | microsoft.directory/applications/owners/update | アプリケーションの所有者を更新する |
> | microsoft.directory/applications/permissions/update | すべての種類のアプリケーションで、公開されたアクセス許可と必要なアクセス許可を更新する |
> | microsoft.directory/applications/policies/update | アプリケーションのポリシーを更新する |
> | microsoft.directory/applications/synchronization/standard/read | アプリケーション オブジェクトに関連付けられているプロビジョニング設定を読み取る |
> | microsoft.directory/applicationTemplates/instantiate | アプリケーション テンプレートからギャラリー アプリケーションのインスタンスを作成する |
> | microsoft.directory/auditLogs/allProperties/read | 監査ログのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.directory/cloudProvisioning/allProperties/allTasks | Azure AD クラウド プロビジョニング サービスのすべてのプロパティを読み取りと構成。 |
> | microsoft.directory/domains/allProperties/read | ドメインのすべてのプロパティの読み取る |
> | microsoft.directory/domains/federation/update | ドメインのフェデレーション プロパティを更新する |
> | microsoft.directory/organization/dirSync/update | 組織のディレクトリ同期プロパティを更新する |
> | microsoft.directory/provisioningLogs/allProperties/read | プロビジョニング ログのすべてのプロパティを読み取ります。 |
> | microsoft.directory/servicePrincipals/create | サービス プリンシパルの作成 |
> | microsoft.directory/servicePrincipals/delete | サービス プリンシパルを削除する |
> | microsoft.directory/servicePrincipals/disable | サービス プリンシパルを無効にする |
> | microsoft.directory/servicePrincipals/enable | サービス プリンシパルを有効にする |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | アプリケーション プロビジョニングのシークレットと資格情報を管理する |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | アプリケーション プロビジョニングの同期ジョブを開始、再開、一時停止する |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | アプリケーション プロビジョニングの同期ジョブとスキーマを作成および管理する |
> | microsoft.directory/servicePrincipals/audience/update | サービス プリンシパルで対象ユーザー プロパティを更新する |
> | microsoft.directory/servicePrincipals/authentication/update | サービス プリンシパルで認証プロパティを更新する |
> | microsoft.directory/servicePrincipals/basic/update | サービス プリンシパルで基本プロパティを更新する |
> | microsoft.directory/servicePrincipals/credentials/update | サービス プリンシパルの資格情報を更新する |
> | microsoft.directory/servicePrincipals/owners/update | サービス プリンシパルの所有者を更新する |
> | microsoft.directory/servicePrincipals/permissions/update | サービスプリンシパルのアクセス許可を更新する |
> | microsoft.directory/servicePrincipals/policies/update | サービス プリンシパルのポリシーを更新する |
> | microsoft.directory/servicePrincipals/tag/update | サービスプリンシパルのタグ プロパティを更新する |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | サービス プリンシパルに関連付けられているプロビジョニング設定を読み取る |
> | microsoft.directory/signInReports/allProperties/read | サインイン情報レポートのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.messageCenter/messages/read | Microsoft 365 管理センターのメッセージ センターで、セキュリティ メッセージを除くメッセージを読み取る |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="insights-administrator"></a>Insights 管理者

このロールのユーザーは、[M365 Insights アプリケーション](https://go.microsoft.com/fwlink/?linkid=2129521)の管理機能の完全なセットにアクセスできます。 このロールでは、ディレクトリ情報の読み取り、サービスの正常性の監視、サポート チケットの提出、Insights 管理者設定の側面へのアクセスを行うことができます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.insights/allEntities/allTasks | Insights アプリのすべての側面を管理する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="insights-business-leader"></a>Insights ビジネス リーダー

このロールのユーザーは、[M365 Insights アプリケーション](https://go.microsoft.com/fwlink/?linkid=2129521)を使用して、一連のダッシュボードと分析情報にアクセスできます。 これには、すべてのダッシュボード、表示される分析情報、およびデータ探索機能へのフル アクセスが含まれます。 このロールのユーザーには、製品の構成設定へのアクセス権がありません (これは Insights 管理者ロールの責任範囲です)。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.insights/reports/read | Insights アプリでレポートとダッシュボードを表示する |
> | microsoft.insights/programs/update | Insights アプリでプログラムをデプロイし、管理する |

## <a name="intune-administrator"></a>Intune 管理者

このロールが割り当てられたユーザーは、Microsoft Intune Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。 さらに、このロールはポリシーを関連付けるためにユーザーとデバイスを管理することができ、グループを作成および管理することもできます。 詳細については、「[Microsoft Intune でのロール ベースの管理制御 (RBAC)](/intune/role-based-access-control)」を参照してください。

このロールでは、すべてのセキュリティ グループを作成および管理できます。 ただし、Intune 管理者には Office グループに対する管理者権限がありません。 つまり、管理者は、組織内のすべての Office グループの所有者およびメンバーシップを更新することはできません。 ただし、自分で作成した Office グループを管理することはできます。これは、エンド ユーザーの特権の一部として提供されます。 そのため、自分が作成したすべての Office グループ (セキュリティ グループではありません) は、自分の 250 のクォータに対してカウントする必要があります。

> [!NOTE]
> Microsoft Graph API と Azure AD PowerShell では、このロールは "Intune サービス管理者" として識別されます。 [Azure portal](https://portal.azure.com) では、"Intune 管理者" になります。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/bitlockerKeys/key/read | デバイス上の bitlocker メタデータとキーを読み取る |
> | microsoft.directory/contacts/create | 連絡先を作成する |
> | microsoft.directory/contacts/delete | 連絡先を削除する |
> | microsoft.directory/contacts/basic/update | 連絡先の基本プロパティを更新する |
> | microsoft.directory/devices/create | デバイスを作成する (Azure AD に登録する) |
> | microsoft.directory/devices/delete | Azure AD からデバイスを削除する |
> | microsoft.directory/devices/disable | Azure AD でデバイスを無効にする |
> | microsoft.directory/devices/enable | Azure AD でデバイスを有効にする |
> | microsoft.directory/devices/basic/update | デバイスの基本プロパティを更新する |
> | microsoft.directory/devices/extensionAttributes/update | devices.extensionAttributes プロパティのすべての値を更新する |
> | microsoft.directory/devices/registeredOwners/update | デバイスの登録済み所有者を更新する |
> | microsoft.directory/devices/registeredUsers/update | デバイスの登録済みユーザーを更新する |
> | microsoft.directory/deviceManagementPolicies/standard/read | デバイス管理アプリケーション ポリシーの標準プロパティの読み取り |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | デバイス登録ポリシーの標準プロパティの読み取り |
> | microsoft.directory/groups/hiddenMembers/read | グループの非表示メンバーの読み取り |
> | microsoft.directory/groups.security/create | ロールの割り当て可能なグループを除外して、セキュリティ グループを作成する |
> | microsoft.directory/groups.security/delete | ロールの割り当て可能なグループを除外して、セキュリティ グループを削除する |
> | microsoft.directory/groups.security/basic/update | ロールの割り当て可能なグループを除外して、セキュリティ グループの基本プロパティを更新する |
> | microsoft.directory/groups.security/classification/update | セキュリティ グループの分類プロパティをロール割り当て可能なグループの除外で更新 |
> | microsoft.directory/groups.security/dynamicMembershipRule/update | セキュリティ グループの dynamicMembershipRule プロパティをロール割り当て可能なグループの除外で更新 |
> | microsoft.directory/groups.security/groupType/update | セキュリティ グループのグループの種類プロパティをロール割り当て可能なグループの除外で更新 |
> | microsoft.directory/groups.security/members/update | ロールの割り当て可能なグループを除外して、セキュリティ グループのメンバーを更新する |
> | microsoft.directory/groups.security/owners/update | ロールの割り当て可能なグループを除外して、セキュリティ グループの所有者を更新する |
> | microsoft.directory/groups.security/visibility/update | セキュリティ グループの可視性プロパティをロール割り当て可能なグループの除外で更新 |
> | microsoft.directory/users/basic/update | ユーザーの基本プロパティを更新する |
> | microsoft.directory/users/manager/update | ユーザーのマネージャーを更新する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.intune/allEntities/allTasks | Microsoft Intune のすべての側面を管理する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="kaizala-administrator"></a>Kaizala 管理者

このロールが割り当てられたユーザーは、Microsoft Kaizala 内で設定を管理するグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、サポート チケットを管理し、サービス正常性を監視できます。 さらに、このユーザーは、組織のメンバーによる Kaizala の導入と使用法に関連したレポート、および Kaizala アクションを使用して生成されるビジネス レポートにもアクセスできます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="license-administrator"></a>ライセンス管理者

このロールのユーザーは、ユーザーに対するライセンス割り当ての追加、削除、更新、グループに対する (グループベースのライセンスを使用した) ライセンス割り当ての追加、削除、更新に加え、ユーザーに対する利用場所の管理を行うことができます。 このロールでは、サブスクリプションの購入と管理、グループの作成と管理を行う権限は与えられません。また、利用場所を超える範囲でのユーザーの作成と管理を行う権限も与えられません。 このロールには、サポート チケットの表示、作成、管理のためのアクセス権がありません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/groups/assignLicense | グループベースのライセンスのグループに製品ライセンスを割り当てる |
> | microsoft.directory/groups/reprocessLicenseAssignment | グループベースのライセンスのライセンス割り当てを再処理する |
> | microsoft.directory/users/assignLicense | ユーザー ライセンスの管理 |
> | microsoft.directory/users/reprocessLicenseAssignment | ユーザーのライセンス割り当てを再処理する |
> | microsoft.directory/users/usageLocation/update | ユーザーの利用場所を更新する |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="message-center-privacy-reader"></a>メッセージ センターのプライバシー閲覧者

このロールのユーザーは、データのプライバシー メッセージを含め、メッセージ センター内のすべての通知を監視できます。 メッセージ センターのプライバシー閲覧者は、データのプライバシーに関連したものも含めてメール通知を受け取り、メッセージ センターの設定を使用して登録を解除することができます。 データのプライバシー メッセージを読み取ることができるのは、グローバル管理者とメッセージ センターのプライバシー閲覧者のみになります。 さらに、このロールには、グループ、ドメイン、サブスクリプションを表示する権限が含まれています。 このロールには、サービス要求を表示、作成、または管理するアクセス許可はありません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Microsoft 365 管理センターのメッセージ センターで、セキュリティ メッセージを除くメッセージを読み取る |
> | microsoft.office365.messageCenter/securityMessages/read | Microsoft 365 管理センターのメッセージ センターでセキュリティ メッセージを読み取る |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="message-center-reader"></a>メッセージ センター閲覧者

このロールのユーザーは、自分の組織の Exchange、Intune、Microsoft Teams などのサービス構成に対する[メッセージ センター](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093)の通知や、正常性に関して注意を促す更新情報を監視できます。 メッセージ センター閲覧者は、投稿の毎週のメール ダイジェストを受け取り、Microsoft 365 でメッセージ センターの投稿を共有できます。 Azure AD では、このロールに割り当てられているユーザーはユーザーやグループなどの読み取り専用アクセスのみを持ちます。 このロールには、サポート チケットの表示、作成、管理のためのアクセス権がありません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Microsoft 365 管理センターのメッセージ センターで、セキュリティ メッセージを除くメッセージを読み取る |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="modern-commerce-user"></a>Modern Commerce ユーザー

使用しないでください。 このロールは、Commerce から自動的に割り当てられます。他の用途に使用するためのものではなく、他の用途ではサポートされていません。 詳細は以下を参照してください。

Modern Commerce ユーザー ロールは、特定のユーザーに Microsoft 365 管理センターにアクセスして、**ホーム**、**課金**、**サポート** の左側のナビゲーション エントリを表示するためのアクセス許可を付与します。 これらの領域で利用可能なコンテンツは、ユーザーが自分自身または組織のために購入した製品を管理するために、ユーザーに割り当てられた[コマース固有のロール](../../cost-management-billing/manage/understand-mca-roles.md)によって制御されます。 これには、請求書の支払いや、課金アカウントや課金プロファイルへのアクセスなどのタスクが含まれる場合があります。

Modern Commerce ユーザー ロールを持つユーザーは、通常、他の Microsoft 購入システムの管理権限を持っていますが、管理センターへのアクセスに使用されるグローバル管理者ロールまたは課金管理者ロールは持っていません。

**Modern Commerce ユーザー ロールが割り当てられるタイミング**

* **Microsoft 365 管理センターでのセルフサービス購入** – セルフサービス購入により、ユーザーは新製品を自分で購入またはサインアップして、新製品を試す機会が得られます。 これらの製品は管理センターで管理されています。 セルフサービス購入を行うユーザーには、コマース システムでのロールと Modern Commerce ユーザー ロールが割り当てられ、これにより、管理センターで購入を管理できるようになります。 管理者は [PowerShell](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell) を使用して、(Power BI、Power Apps、Power Automate に対する) セルフサービス購入をブロックできます。 詳細については、「[セルフサービスによる購入に関するよくあるご質問](/microsoft-365/commerce/subscriptions/self-service-purchase-faq)」を参照してください。
* **Microsoft コマーシャル マーケットプレースからの購入** - セルフサービス購入と同様に、ユーザーが Microsoft AppSource または Azure Marketplace から製品またはサービスを購入するときに、グローバル管理者ロールまたは課金管理者ロールを持っていない場合は、Modern Commerce ユーザー ロールが割り当てられます。 場合によっては、ユーザーがこれらの購入をブロックされる場合があります。 詳細については、[Microsoft コマーシャル マーケットプレース](../../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase)に関するページを参照してください。
* **Microsoft からの提案** – 提案は、組織が Microsoft の製品やサービスを購入するための Microsoft からの正式なオファーです。 提案に同意する人が Azure AD でグローバル管理者ロールまたは課金管理者ロールを持っていない場合、提案を完了するためのコマース固有のロールと、管理センターにアクセスするための Modern Commerce ユーザー ロールの両方が割り当てられます。 管理センターにアクセスするときは、そのコマース固有のロールによって承認された機能のみを使用できます。
* **コマース固有のロール** – 一部のユーザーには、コマース固有のロールが割り当てられます。 ユーザーがグローバル管理者または課金管理者でない場合は、管理センターにアクセスできるように、Modern Commerce ユーザー ロールを取得します。

ユーザーから Modern Commerce ユーザー ロールが割り当て解除されると、Microsoft 365 管理センターにアクセスできなくなります。 ユーザーが自身または組織のために製品を管理している場合は、それらを管理できなくなります。 これには、ライセンスの割り当て、支払い方法の変更、請求書の支払い、サブスクリプションを管理するためのその他のタスクが含まれます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.commerce.billing/partners/read | Microsoft 365 課金のパートナー プロパティを読み取る |
> | microsoft.commerce.volumeLicenseServiceCenter/allEntities/allTasks | ボリューム ライセンス サービス センターのすべての側面を管理する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/basic/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="network-administrator"></a>ネットワーク管理者

このロールのユーザーは、ユーザーの場所からのネットワーク テレメトリに基づいて、Microsoft によるネットワーク境界アーキテクチャに関する推奨事項を確認できます。 Microsoft 365 のネットワーク パフォーマンスは、通常はユーザーの場所に固有である、企業の顧客の慎重なネットワーク境界アーキテクチャに依存します。 このロールを使用では、検出されたユーザーの場所と、それらの場所に対するネットワーク パラメーターの構成を編集して、テレメトリの測定と設計に関する推奨事項の改善できます

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.office365.network/locations/allProperties/allTasks | ネットワークの場所のすべての側面を管理する |
> | microsoft.office365.network/performance/allProperties/read | Microsoft 365 管理センターで、すべてのネットワーク パフォーマンス プロパティを読み取る |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="office-apps-administrator"></a>Office アプリ管理者

このロールのユーザーは、Microsoft 365 アプリのクラウド設定を管理できます。 これには、クラウド ポリシーの管理、セルフサービス ダウンロードの管理、Office アプリ関連レポートを表示する機能が含まれます。 このロールではさらに、サポート チケットの管理とメインの管理センター内でのサービスの正常性の監視を行うこともできます。 このロールに割り当てられたユーザーは、Office アプリの新機能の通知も管理できます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.messageCenter/messages/read | Microsoft 365 管理センターのメッセージ センターで、セキュリティ メッセージを除くメッセージを読み取る |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.userCommunication/allEntities/allTasks | 新機能のメッセージを表示できるかどうかを読み取り、更新する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="partner-tier1-support"></a>パートナー レベル 1 のサポート

使用しないでください。 このロールは非推奨となっており、将来的に Azure AD から削除されます。 このロールは少数の Microsoft 再販パートナーを対象としており、一般的な使用を目的としたものではありません。

> [!IMPORTANT]
> このロールでは、管理者以外のパスワードをリセットし、更新トークンを無効にすることができます。 このロールは非推奨とされており、API では返されなくなるため、使用しないでください。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/applications/appRoles/update | すべての種類のアプリケーションで appRoles プロパティを更新する |
> | microsoft.directory/applications/audience/update | アプリケーションの対象ユーザー プロパティを更新する |
> | microsoft.directory/applications/authentication/update | すべての種類のアプリケーションで認証を更新する |
> | microsoft.directory/applications/basic/update | アプリケーションの基本プロパティを更新する |
> | microsoft.directory/applications/credentials/update | アプリケーション資格情報を更新する |
> | microsoft.directory/applications/owners/update | アプリケーションの所有者を更新する |
> | microsoft.directory/applications/permissions/update | すべての種類のアプリケーションで、公開されたアクセス許可と必要なアクセス許可を更新する |
> | microsoft.directory/applications/policies/update | アプリケーションのポリシーを更新する |
> | microsoft.directory/contacts/create | 連絡先を作成する |
> | microsoft.directory/contacts/delete | 連絡先を削除する |
> | microsoft.directory/contacts/basic/update | 連絡先の基本プロパティを更新する |
> | microsoft.directory/groups/create | ロール割り当て可能なグループを除き、グループを削除する |
> | microsoft.directory/groups/delete | ロール割り当て可能なグループを除くグループの削除 |
> | microsoft.directory/groups/restore | 削除されたグループの復元 |
> | microsoft.directory/groups/members/update | ロール割り当て可能なグループを除き、グループのメンバーを更新する |
> | microsoft.directory/groups/owners/update | ロール割り当て可能なグループを除くグループの所有者の更新 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0 アクセス許可の付与の作成と削除、およびすべてのプロパティの読み取りと更新 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | サービス プリンシパルのロールの割り当ての更新 |
> | microsoft.directory/users/assignLicense | ユーザー ライセンスの管理 |
> | microsoft.directory/users/create | ユーザーの追加 |
> | microsoft.directory/users/delete | ユーザーを削除する |
> | microsoft.directory/users/disable | ユーザーの無効化 |
> | microsoft.directory/users/enable | ユーザーの有効化 |
> | microsoft.directory/users/invalidateAllRefreshTokens | ユーザー更新トークンを無効にして強制的にサインアウトする |
> | microsoft.directory/users/restore | 削除されたユーザーを復元する |
> | microsoft.directory/users/basic/update | ユーザーの基本プロパティを更新する |
> | microsoft.directory/users/manager/update | ユーザーのマネージャーを更新する |
> | microsoft.directory/users/password/update | すべてのユーザーのパスワードをリセットする |
> | microsoft.directory/users/userPrincipalName/update | ユーザーのユーザー プリンシパル名を更新する |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="partner-tier2-support"></a>パートナー レベル 2 のサポート

使用しないでください。 このロールは非推奨となっており、将来的に Azure AD から削除されます。 このロールは少数の Microsoft 再販パートナーを対象としており、一般的な使用を目的としたものではありません。

> [!IMPORTANT]
> このロールは、すべての非管理者および管理者 (グローバル管理者を含む) のパスワードをリセットし、更新トークンを無効にすることができます。 このロールは非推奨とされており、API では返されなくなるため、使用しないでください。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/applications/appRoles/update | すべての種類のアプリケーションで appRoles プロパティを更新する |
> | microsoft.directory/applications/audience/update | アプリケーションの対象ユーザー プロパティを更新する |
> | microsoft.directory/applications/authentication/update | すべての種類のアプリケーションで認証を更新する |
> | microsoft.directory/applications/basic/update | アプリケーションの基本プロパティを更新する |
> | microsoft.directory/applications/credentials/update | アプリケーション資格情報を更新する |
> | microsoft.directory/applications/owners/update | アプリケーションの所有者を更新する |
> | microsoft.directory/applications/permissions/update | すべての種類のアプリケーションで、公開されたアクセス許可と必要なアクセス許可を更新する |
> | microsoft.directory/applications/policies/update | アプリケーションのポリシーを更新する |
> | microsoft.directory/contacts/create | 連絡先を作成する |
> | microsoft.directory/contacts/delete | 連絡先を削除する |
> | microsoft.directory/contacts/basic/update | 連絡先の基本プロパティを更新する |
> | microsoft.directory/domains/basic/allTasks | ドメインの作成と削除、標準プロパティの読み取りと更新を行う |
> | microsoft.directory/groups/create | ロール割り当て可能なグループを除き、グループを削除する |
> | microsoft.directory/groups/delete | ロール割り当て可能なグループを除くグループの削除 |
> | microsoft.directory/groups/restore | 削除されたグループの復元 |
> | microsoft.directory/groups/members/update | ロール割り当て可能なグループを除き、グループのメンバーを更新する |
> | microsoft.directory/groups/owners/update | ロール割り当て可能なグループを除くグループの所有者の更新 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0 アクセス許可の付与の作成と削除、およびすべてのプロパティの読み取りと更新 |
> | microsoft.directory/organization/basic/update | 組織で基本プロパティを更新する |
> | microsoft.directory/roleAssignments/allProperties/allTasks | ロールの割り当ての作成と削除、およびすべてのロールの割り当てプロパティの読み取りと更新 |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | ロールの定義の作成と削除、およびすべてのプロパティの読み取りと更新 |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | scopedRoleMemberships の作成と削除、およびすべてのプロパティの読み取りと更新 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | サービス プリンシパルのロールの割り当ての更新 |
> | microsoft.directory/subscribedSkus/standard/read | サブスクリプションの基本プロパティの読み取り |
> | microsoft.directory/users/assignLicense | ユーザー ライセンスの管理 |
> | microsoft.directory/users/create | ユーザーの追加 |
> | microsoft.directory/users/delete | ユーザーを削除する |
> | microsoft.directory/users/disable | ユーザーの無効化 |
> | microsoft.directory/users/enable | ユーザーの有効化 |
> | microsoft.directory/users/invalidateAllRefreshTokens | ユーザー更新トークンを無効にして強制的にサインアウトする |
> | microsoft.directory/users/restore | 削除されたユーザーを復元する |
> | microsoft.directory/users/basic/update | ユーザーの基本プロパティを更新する |
> | microsoft.directory/users/manager/update | ユーザーのマネージャーを更新する |
> | microsoft.directory/users/password/update | すべてのユーザーのパスワードをリセットする |
> | microsoft.directory/users/userPrincipalName/update | ユーザーのユーザー プリンシパル名を更新する |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="password-administrator"></a>パスワード管理者

このロールのユーザーは、制限付きでパスワードを管理することができます。 このロールでは、サービス要求を管理したり、サービスの正常性を監視したりすることはできません。 パスワード管理者がユーザーのパスワードをリセットできるかどうかは、ユーザーが割り当てられているロールに依存します。 パスワード管理者がパスワードをリセットできるロールの一覧については、「[パスワード リセットのアクセス許可](#password-reset-permissions)」を参照してください。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/users/password/update | すべてのユーザーのパスワードをリセットする |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="power-bi-administrator"></a>Power BI 管理者

このロールが割り当てられたユーザーは、Microsoft Power BI 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、サポート チケットを管理し、サービス正常性を監視できます。 詳細については、「[Power BI 管理者の役割について](/power-bi/service-admin-role)」を参照してください。

> [!NOTE]
> Microsoft Graph API と Azure AD PowerShell では、このロールは "Power BI サービス管理者" として識別されます。 [Azure portal](https://portal.azure.com) では、"Power BI 管理者" になります。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |
> | microsoft.powerApps.powerBI/allEntities/allTasks | Power BI のすべての側面を管理する |

## <a name="power-platform-administrator"></a>Power Platform 管理者

このロールのユーザーは、環境、PowerApps、フロー、データ損失防止ポリシーのすべての側面を作成および管理できます。 さらに、このロールを持つユーザーは、サポート チケットを管理し、サービス正常性を監視できます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.dynamics365/allEntities/allTasks | Dynamics 365 のすべての側面を管理する |
> | microsoft.flow/allEntities/allTasks | Power Automate のすべての側面を管理する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |
> | microsoft.powerApps/allEntities/allTasks | Power Apps のすべての側面を管理する |

## <a name="printer-administrator"></a>プリンター管理者

このロールのユーザーは、プリンターを登録して、ユニバーサル印刷コネクタの設定など、Microsoft ユニバーサル印刷ソリューションのすべてのプリンター構成のすべての側面を管理できます。 すべての委任された印刷アクセス許可要求に同意することができます。 プリンター管理者は、印刷レポートにアクセスすることもできます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.azure.print/allEntities/allProperties/allTasks | Microsoft Print でプリンターとコネクタの作成と削除、すべてのプロパティの読み取りと更新を行う |

## <a name="printer-technician"></a>プリンター技術者

このロールのユーザーは、プリンターを登録して、Microsoft ユニバーサル印刷ソリューションでプリンターの状態を管理できます。 すべてのコネクタ情報を読み取ることもできます。 プリンター技術者が行うことができない主要なタスクは、プリンターに対するユーザー アクセス許可の設定と、プリンターの共有です。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.azure.print/connectors/allProperties/read | Microsoft Print でコネクタのすべてのプロパティを読み取る |
> | microsoft.azure.print/printers/allProperties/read | Microsoft Print でプリンターのすべてのプロパティを読み取る |
> | microsoft.azure.print/printers/register | Microsoft Print でプリンターを登録する |
> | microsoft.azure.print/printers/unregister | Microsoft Print でプリンターを登録解除する |
> | microsoft.azure.print/printers/basic/update | Microsoft Print でプリンターの基本プロパティを更新する |

## <a name="privileged-authentication-administrator"></a>特権認証管理者

このロールを持つユーザーは、グローバル管理者を含む任意のユーザーの任意の認証方法 (パスワードを含む) を設定またはリセットできます。 特権認証管理者は、パスワード以外の既存の資格情報 (MFA や FIDO など) に対する再登録をユーザーに強制することができます。また、'このデバイスに MFA を記憶する' 機能を取り消して、すべてのユーザーが次回サインインするときに MFA の入力を求めることができます。

[認証管理者](#authentication-administrator)ロールには、標準ユーザーと、一部の管理者ロールを持つユーザーに対して、再登録と多要素認証を強制する権限があります。

[認証ポリシー管理者](#authentication-policy-administrator)ロールには、テナントの認証方法ポリシーを設定する権限があり、これにより、各ユーザーが登録および使用できる方法が決まります。

| Role | ユーザーの認証方法を管理する | ユーザーごとの MFA を管理する | MFA 設定を管理する | 認証方法ポリシーを管理する | パスワード保護ポリシーを管理する |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 認証管理者 | 一部のユーザーに対してはい (上記を参照) | 一部のユーザーに対してはい (上記を参照) | いいえ | いいえ | いいえ |
| 特権認証管理者| すべてのユーザーに対してはい | すべてのユーザーに対してはい | いいえ | いいえ | いいえ |
| 認証ポリシー管理者 | いいえ | いいえ | はい | はい | はい |

> [!IMPORTANT]
> このロールのユーザーは、機密情報や個人情報または Azure Active Directory の内外の重要な構成にアクセスできるユーザーのパスワードを変更できます。 ユーザーの資格情報を変更するということは、そのユーザーの ID とアクセス許可を引き受けることができるということを意味します。 次に例を示します。
>
>* 所有しているアプリの資格情報を管理できる、アプリケーションの登録とエンタープライズ アプリケーションの所有者。 これらのアプリには、認証管理者に付与されていないAzure AD およびその他の場所への特権アクセス許可がある場合があります。 認証管理者は、このパスからアプリケーション所有者の ID を引き受け、さらにそのアプリケーションの資格情報を更新して特権アプリケーションの ID を引き受けることができます。
>* 機密情報や個人情報または Azure の重要な構成にアクセスできる Azure サブスクリプション所有者。
>* グループ メンバーシップを管理できるセキュリティ グループと Microsoft 365 グループの所有者。 これらのグループは、機密情報や個人情報または Azure AD や別の場所の重要な構成へのアクセス権を付与される場合があります。
>* Exchange Online、Office Security and Compliance Center、人事システムのような Azure AD 以外のサービスの管理者。
>* 機密情報や個人情報にアクセスできる場合がある役員、弁護士、人事担当者のような非管理者。


> [!IMPORTANT]
> このロールは現在、レガシ MFA 管理ポータルでユーザーごとの MFA を管理することはできません。 Azure AD Powershell モジュールの [Set-msoluser](https://docs.microsoft.com/powershell/module/msonline/set-msoluser) コマンドレットを使用して、同じ機能を実行できます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/users/invalidateAllRefreshTokens | ユーザー更新トークンを無効にして強制的にサインアウトする |
> | microsoft.directory/users/strongAuthentication/update | ユーザーの強力な認証プロパティを更新する |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="privileged-role-administrator"></a>特権ロール管理者

このロールが割り当てられたユーザーは、Azure Active Directory と Azure AD Privileged Identity Management 内でロールの割り当てを管理できます。 Azure AD ロールに割り当てることができるグループの作成と管理が可能です。 さらに、このロールは、Privileged Identity Management と管理単位のすべての側面を管理できます。

> [!IMPORTANT]
> このロールは、全体管理者ロールを含むすべての Azure AD ロールの割り当てを管理する権限を付与します。 このロールには、Azure AD でユーザーの作成や更新などの特権付きの機能が含まれていません。 ただし、このロールが割り当てられたユーザーは、追加のロールを割り当てることで、自分または他のユーザーの追加の特権を付与できます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | 管理単位 (メンバーを含む) の作成と管理。 |
> | microsoft.directory/appRoleAssignments/allProperties/allTasks | appRoleAssignments の作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.directory/authorizationPolicy/allProperties/allTasks | 認可ポリシーのすべての側面を管理する |
> | microsoft.directory/directoryRoles/allProperties/allTasks | ディレクトリ ロールの作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.directory/groupsAssignableToRoles/allProperties/update | isAssignableToRole プロパティが true に設定されているグループを更新する |
> | microsoft.directory/groupsAssignableToRoles/create | isAssignableToRole プロパティが true に設定されているグループを作成する |
> | microsoft.directory/groupsAssignableToRoles/delete | isAssignableToRole プロパティが true に設定されているグループを削除する |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0 アクセス許可の付与の作成と削除、およびすべてのプロパティの読み取りと更新 |
> | microsoft.directory/privilegedIdentityManagement/allProperties/allTasks | Privileged Identity Management ですべてのリソースの作成と削除、標準プロパティの読み取りと更新を行う |
> | microsoft.directory/roleAssignments/allProperties/allTasks | ロールの割り当ての作成と削除、およびすべてのロールの割り当てプロパティの読み取りと更新 |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | ロールの定義の作成と削除、およびすべてのプロパティの読み取りと更新 |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | scopedRoleMemberships の作成と削除、およびすべてのプロパティの読み取りと更新 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | サービス プリンシパルのロールの割り当ての更新 |
> | microsoft.directory/servicePrincipals/permissions/update | サービスプリンシパルのアクセス許可を更新する |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-company-admin | 任意のアプリケーションに対するすべてのアクセス許可に同意を付与する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="reports-reader"></a>レポート閲覧者

このロールが割り当てられたユーザーは、Microsoft 365 管理センターで使用状況のレポート データとレポート ダッシュボードを表示できます。また、Power BI で導入コンテキスト パックを表示できます。 さらに、Azure AD のサインイン レポートとアクティビティ、および Microsoft Graph レポート API から返されるデータにもアクセスできます。 レポート閲覧者ロールが割り当てられたユーザーは、関連する使用状況と導入メトリックにのみアクセスできます。 製品固有の管理センター (Exchange など) の設定を構成したり、アクセスしたりする管理者アクセス許可はありません。 このロールには、サポート チケットの表示、作成、管理のためのアクセス権がありません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | 監査ログのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.directory/provisioningLogs/allProperties/read | プロビジョニング ログのすべてのプロパティを読み取ります。 |
> | microsoft.directory/signInReports/allProperties/read | サインイン情報レポートのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 の使用状況レポートを読み取る |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="search-administrator"></a>Search 管理者

このロールのユーザーには、Microsoft 365 管理センター内のすべての Microsoft Search 管理機能へのフル アクセスがあります。 さらに、これらのユーザーは、メッセージ センターを表示し、サービス正常性を監視し、サービス要求を作成することができます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Microsoft 365 管理センターのメッセージ センターで、セキュリティ メッセージを除くメッセージを読み取る |
> | microsoft.office365.search/content/manage | Microsoft Search でコンテンツの作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="search-editor"></a>Search エディター

このロールのユーザーは、ブックマーク、Q&A、および場所を含め、Microsoft 365 管理センターで Microsoft Search のコンテンツを作成、管理、および削除できます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Microsoft 365 管理センターのメッセージ センターで、セキュリティ メッセージを除くメッセージを読み取る |
> | microsoft.office365.search/content/manage | Microsoft Search でコンテンツの作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="security-administrator"></a>セキュリティ管理者

このロールが割り当てられたユーザーは、Microsoft 365 セキュリティ センター、Azure Active Directory Identity Protection、Azure Active Directory Authentication、Azure Information Protection、Office 365 セキュリティ/コンプライアンス センターのセキュリティ関連機能を管理するアクセス許可を持ちます。 Office 365 のアクセス許可の詳細については、「[セキュリティ/コンプライアンス センターでのアクセス許可](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)」をご覧ください。

場所 | できること
--- | ---
[Microsoft 365 セキュリティ センター](https://protection.office.com) | Microsoft 365 サービス全体のセキュリティ関連ポリシーの監視<br>セキュリティの脅威とアラートの管理<br>レポートの表示
Identity Protection Center | セキュリティ閲覧者ロールのすべてのアクセス許可<br>さらに、パスワードのリセットを除く Identity Protection センターのすべての操作を行う権限
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | セキュリティ閲覧者ロールのすべてのアクセス許可<br>Azure AD ロールの割り当てまたは設定を管理することは **できません**
[Office 365 セキュリティ/コンプライアンス センター](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | セキュリティ ポリシーの管理<br>セキュリティの脅威の表示、調査、対応<br>レポートの表示
Azure Advanced Threat Protection | 疑わしいセキュリティ アクティビティの監視と対応
Windows Defender ATP および EDR | ロールを割り当てる<br>コンピューター グループを管理する<br>エンドポイントの脅威の検出と自動修復の構成<br>アラートの表示、調査、対応
[Intune](/intune/role-based-access-control) | ユーザー、デバイス、登録、構成、アプリケーション情報の表示<br>Intune に変更を加えることはできません
[Cloud App Security](/cloud-app-security/manage-admins) | 管理者の追加、ポリシーと設定の追加、ログのアップロード、ガバナンス アクションの実行
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | セキュリティ ポリシーの表示、セキュリティ状態の表示、セキュリティ ポリシーの編集、アラートと推奨事項の表示、アラートと推奨事項の却下を行うことができます
[Microsoft 365 サービス正常性](/office365/enterprise/view-service-health) | Microsoft 365 サービスの正常性の表示
[スマート ロックアウト](../authentication/howto-password-smart-lockout.md) | サインイン イベントが失敗したときのロックアウトのしきい値と期間を定義します。
[パスワード保護](../authentication/concept-password-ban-bad.md) | カスタムの禁止パスワードの一覧またはオンプレミスのパスワード保護を構成します。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/applications/policies/update | アプリケーションのポリシーを更新する |
> | microsoft.directory/auditLogs/allProperties/read | 監査ログのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.directory/bitlockerKeys/key/read | デバイス上の bitlocker メタデータとキーを読み取る |
> | microsoft.directory/entitlementManagement/allProperties/read | Azure AD エンタイトルメント管理ですべてのプロパティを読み取る |
> | microsoft.directory/identityProtection/allProperties/read | Azure AD Identity Protection のすべてのリソースを読み取る |
> | microsoft.directory/identityProtection/allProperties/update | Azure AD Identity Protection のすべてのリソースを更新する |
> | microsoft.directory/policies/create | Azure AD でポリシーを作成する |
> | microsoft.directory/policies/delete | Azure AD でポリシーを削除する |
> | microsoft.directory/policies/basic/update | ポリシーの基本プロパティを更新する |
> | microsoft.directory/policies/owners/update | ポリシーの所有者を更新する |
> | microsoft.directory/policies/tenantDefault/update | 既定の組織ポリシーを更新する |
> | microsoft.directory/conditionalAccessPolicies/create | 条件付きアクセス ポリシーを作成する |
> | microsoft.directory/conditionalAccessPolicies/delete | 条件付きアクセス ポリシーを削除する |
> | microsoft.directory/conditionalAccessPolicies/standard/read | policies.conditionalAccess プロパティを読み取る |
> | microsoft.directory/conditionalAccessPolicies/owners/read | policies.conditionalAccess プロパティを読み取る |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | policies.conditionalAccess プロパティを読み取る |
> | microsoft.directory/conditionalAccessPolicies/basic/update | 条件付きアクセス ポリシーの基本プロパティを更新する |
> | microsoft.directory/conditionalAccessPolicies/owners/update | policies.conditionalAccess プロパティを更新する |
> | microsoft.directory/conditionalAccessPolicies/tenantDefault/update | policies.conditionalAccess プロパティを更新する |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Privileged Identity Management のすべてのリソースを読み取る |
> | microsoft.directory/provisioningLogs/allProperties/read | プロビジョニング ログのすべてのプロパティを読み取ります。 |
> | microsoft.directory/servicePrincipals/policies/update | サービス プリンシパルのポリシーを更新する |
> | microsoft.directory/signInReports/allProperties/read | サインイン情報レポートのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.protectionCenter/allEntities/standard/read | Office 365 プロテクション センターですべてのリソースの標準プロパティを読み取る |
> | microsoft.office365.protectionCenter/allEntities/basic/update | Office 365 プロテクション センターですべてのリソースの基本プロパティを更新する |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | 攻撃シミュレーターで攻撃ペイロードを作成および管理する |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | 攻撃のシミュレーション、応答、関連付けられているトレーニングのレポートを読み取る |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | 攻撃シミュレーターで攻撃のシミュレーション テンプレートを作成および管理する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="security-operator"></a>セキュリティ オペレーター

このロールのユーザーはアラートを管理できるほか、Microsoft 365 セキュリティ センター、Azure Active Directory、Identity Protection、Privileged Identity Management、および Office 365 セキュリティ/コンプライアンス センターにおけるすべての情報を含むセキュリティ関連機能への読み取り専用グローバル アクセス権があります。 Office 365 のアクセス許可の詳細については、「[セキュリティ/コンプライアンス センターでのアクセス許可](/office365/securitycompliance/permissions-in-the-security-and-compliance-center)」をご覧ください。

場所 | できること
--- | ---
[Microsoft 365 セキュリティ センター](https://protection.office.com) | セキュリティ閲覧者ロールのすべてのアクセス許可<br>セキュリティの脅威アラートの表示、調査、対応
Azure AD Identity Protection | セキュリティ閲覧者ロールのすべてのアクセス許可<br>さらに、パスワードのリセットとアラート電子メールの構成を除く Identity Protection センターのすべての操作を行う権限。
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | セキュリティ閲覧者ロールのすべてのアクセス許可
[Office 365 セキュリティ/コンプライアンス センター](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | セキュリティ閲覧者ロールのすべてのアクセス許可<br>セキュリティ アラートの表示、調査、対応
Windows Defender ATP および EDR | セキュリティ閲覧者ロールのすべてのアクセス許可<br>セキュリティ アラートの表示、調査、対応
[Intune](/intune/role-based-access-control) | セキュリティ閲覧者ロールのすべてのアクセス許可
[Cloud App Security](/cloud-app-security/manage-admins) | セキュリティ閲覧者ロールのすべてのアクセス許可
[Microsoft 365 サービス正常性](/office365/enterprise/view-service-health) | Microsoft 365 サービスの正常性の表示

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | 監査ログのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Microsoft Cloud App Security ですべてのリソースの作成と削除、標準プロパティの読み取りと更新を行う |
> | microsoft.directory/identityProtection/allProperties/allTasks | Azure AD Identity Protection ですべてのリソースの作成と削除、標準プロパティの読み取りと更新を行う |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Privileged Identity Management のすべてのリソースを読み取る |
> | microsoft.directory/signInReports/allProperties/read | サインイン情報レポートのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.azure.advancedThreatProtection/allEntities/allTasks | Azure Advanced Threat Protection のすべての側面を管理する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.intune/allEntities/read | Microsoft Intune のすべてのリソースを読み取る |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | Microsoft 365 セキュリティおよびコンプライアンス センターですべてのリソースの作成と削除、標準プロパティの読み取りと更新を行う |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.windows.defenderAdvancedThreatProtection/allEntities/allTasks | エンドポイントに対して Microsoft Defender のすべての側面を管理する |

## <a name="security-reader"></a>セキュリティ閲覧者

このロールのユーザーには、Microsoft 365 セキュリティ センター、Azure Active Directory、Identity Protection、Privileged Identity Management におけるすべての情報を含むセキュリティ関連機能への読み取り専用グローバル アクセス権に加えて、Azure Active Directory サインイン レポートと監査ログ、および Office 365 セキュリティ/コンプライアンス センターで閲覧する権限があります。 Office 365 のアクセス許可の詳細については、「[セキュリティ/コンプライアンス センターでのアクセス許可](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)」をご覧ください。

場所 | できること
--- | ---
[Microsoft 365 セキュリティ センター](https://protection.office.com) | Microsoft 365 サービス全体のセキュリティ関連ポリシーの表示<br>セキュリティの脅威とアラートの表示<br>レポートの表示
Identity Protection Center | 各セキュリティ機能の全セキュリティ レポートと設定情報の閲覧<br><ul><li>スパム対策<li>暗号化<li>データ損失の防止<li>マルウェア対策<li>高度な脅威保護<li>フィッシング詐欺対策<li>メール フロー ルール
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Azure AD Privileged Identity Management に表示されるすべての情報への読み取り専用アクセス権を持ちます。Azure AD ロール割り当てに関するポリシーとレポート、およびセキュリティ レビュー。<br>Azure AD Privileged Identity Management へのサインアップおよびそれに対する変更を行うことは **できません**。 このロールのユーザーは、追加のロール (グローバル管理者や特権ロール管理者など) の資格がある場合、Privileged Identity Management ポータルまたは PowerShell からそれらのロールを有効化することができます。
[Office 365 セキュリティ/コンプライアンス センター](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | セキュリティ ポリシーの表示<br>セキュリティの脅威の表示および調査<br>レポートの表示
Windows Defender ATP および EDR | アラートを表示して調査します。 Windows Defender ATP でロールベースのアクセス制御を有効にすると、Azure AD セキュリティ閲覧者ロールなどの読み取り専用アクセス許可を持つユーザーは、Windows Defender ATP ロールが割り当てられるまで、アクセス権を失います。
[Intune](/intune/role-based-access-control) | ユーザー、デバイス、登録、構成、アプリケーション情報の表示。 Intune に変更を加えることはできません。
[Cloud App Security](/cloud-app-security/manage-admins) | 読み取り専用アクセス許可があり、アラートを管理できる
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | 推奨事項とアラート、セキュリティ ポリシー、セキュリティの状態を表示することはできますが、変更することはできません
[Microsoft 365 サービス正常性](/office365/enterprise/view-service-health) | Microsoft 365 サービスの正常性の表示

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | 監査ログのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.directory/bitlockerKeys/key/read | デバイス上の bitlocker メタデータとキーを読み取る |
> | microsoft.directory/entitlementManagement/allProperties/read | Azure AD エンタイトルメント管理ですべてのプロパティを読み取る |
> | microsoft.directory/identityProtection/allProperties/read | Azure AD Identity Protection のすべてのリソースを読み取る |
> | microsoft.directory/policies/standard/read | ポリシーの基本プロパティを読み取る |
> | microsoft.directory/policies/owners/read | ポリシーの所有者を読み取る |
> | microsoft.directory/policies/policyAppliedTo/read | policies.policyAppliedTo プロパティを読み取る |
> | microsoft.directory/conditionalAccessPolicies/standard/read | policies.conditionalAccess プロパティを読み取る |
> | microsoft.directory/conditionalAccessPolicies/owners/read | policies.conditionalAccess プロパティを読み取る |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | policies.conditionalAccess プロパティを読み取る |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Privileged Identity Management のすべてのリソースを読み取る |
> | microsoft.directory/provisioningLogs/allProperties/read | プロビジョニング ログのすべてのプロパティを読み取ります。 |
> | microsoft.directory/signInReports/allProperties/read | サインイン情報レポートのすべてのプロパティ (特権プロパティを含む) を読み取る |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.office365.protectionCenter/allEntities/standard/read | Office 365 プロテクション センターですべてのリソースの標準プロパティを読み取る |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/read | 攻撃シミュレーターで攻撃ペイロードのすべてのプロパティを読み取る |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | 攻撃のシミュレーション、応答、関連付けられているトレーニングのレポートを読み取る |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/read | 攻撃シミュレーターで攻撃シミュレーション テンプレートのすべてのプロパティを読み取る |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="service-support-administrator"></a>サービス サポート管理者

このロールが割り当てられたユーザーは、Azure サービスと Microsoft 365 サービスについて Microsoft へのサポート要求を開くことができます。また、[Azure portal](https://portal.azure.com) と [Microsoft 365 管理センター](https://admin.microsoft.com)で、サービス ダッシュボードとメッセージ センターを表示できます。 詳細については、[管理者の役割](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)に関する記事をご覧ください。

> [!NOTE]
> 以前は、このロールは、[Azure portal](https://portal.azure.com) および [Microsoft 365 管理センター](https://admin.microsoft.com)の「サービス管理者」と呼ばれていました。 Microsoft Graph API、Azure AD Graph API、Azure AD PowerShell の既存の名前と整合性を図り、このロールの名前を「サービス サポート管理者」に変更しました。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="sharepoint-administrator"></a>SharePoint 管理者

このロールが割り当てられたユーザーは、Microsoft SharePoint Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、すべての Microsoft 365 グループを作成および管理し、サポート チケットを管理し、サービス正常性を監視できます。 詳細については、[管理者の役割](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)に関する記事をご覧ください。

> [!NOTE]
> Microsoft Graph API と Azure AD PowerShell では、このロールは "SharePoint サービス管理者" として識別されます。 [Azure portal](https://portal.azure.com) では、"SharePoint 管理者" になります。

> [!NOTE]
> このロールでは、Microsoft Intune 用の Microsoft Graph API に対するスコープ付きのアクセス許可も付与されます。これにより、SharePoint リソースと OneDrive リソースに関連するポリシーの管理と構成を行うことができます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/groups.unified/create | ロールの割り当て可能なグループを除外して、Microsoft 365 グループを作成する |
> | microsoft.directory/groups.unified/delete | ロールの割り当て可能なグループを除外して、Microsoft 365 グループを削除する |
> | microsoft.directory/groups.unified/restore | Microsoft 365 グループを復元します。 |
> | microsoft.directory/groups.unified/basic/update | ロールの割り当て可能なグループを除外して、Microsoft 365 グループの基本プロパティを更新する |
> | microsoft.directory/groups.unified/members/update | ロールの割り当て可能なグループを除外して、Microsoft 365 グループのメンバーを更新する |
> | microsoft.directory/groups.unified/owners/update | ロールの割り当て可能なグループを除外して、Microsoft 365 グループの所有者を更新する |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.network/performance/allProperties/read | Microsoft 365 管理センターで、すべてのネットワーク パフォーマンス プロパティを読み取る |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.sharePoint/allEntities/allTasks | SharePoint ですべてのリソースの作成と削除、標準プロパティの読み取りと更新を行う |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 の使用状況レポートを読み取る |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="skype-for-business-administrator"></a>Skype for Business 管理者

このロールが割り当てられたユーザーは、Microsoft Skype for Business 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、Azure Active Directory で Skype 固有のユーザー属性を管理します。 さらに、このロールはサポート チケットを管理し、サービス正常性を監視して、Teams と Skype for Business の管理センターにアクセスすることができます。 アカウントには、Teams のライセンスが付与されている必要もあります。そうでないと、Teams の PowerShell コマンドレットを実行できません。 詳細については、[Skype for Business の管理者ロール](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5)に関するページを参照してください。Teams のライセンス情報については、[Skype for Business と Microsoft Teams アドオンのライセンス](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)に関するページを参照してください

> [!NOTE]
> Microsoft Graph API と Azure AD PowerShell では、このロールは "Lync サービス管理者" として識別されます。 [Azure portal](https://portal.azure.com/) では、"Skype for Business 管理者" になります。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Skype for Business Online の全側面の管理 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 の使用状況レポートを読み取る |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="teams-administrator"></a>Teams 管理者

このロールのユーザーは、Microsoft Teams および Skype for Business の管理センターと、対応する PowerShell モジュールを使用して、Microsoft Teams のワークロードの全側面を管理できます。 これにはその他の領域の、テレフォニー、メッセージング、会議、およびチーム自体に関連するすべての管理ツールが含まれます。 このロールはさらに、すべての Microsoft 365 グループの作成および管理、サポート チケットの管理、サービスの正常性の監視を行うこともできます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/groups/hiddenMembers/read | グループの非表示メンバーの読み取り |
> | microsoft.directory/groups.unified/create | ロールの割り当て可能なグループを除外して、Microsoft 365 グループを作成する |
> | microsoft.directory/groups.unified/delete | ロールの割り当て可能なグループを除外して、Microsoft 365 グループを削除する |
> | microsoft.directory/groups.unified/restore | Microsoft 365 グループを復元します。 |
> | microsoft.directory/groups.unified/basic/update | ロールの割り当て可能なグループを除外して、Microsoft 365 グループの基本プロパティを更新する |
> | microsoft.directory/groups.unified/members/update | ロールの割り当て可能なグループを除外して、Microsoft 365 グループのメンバーを更新する |
> | microsoft.directory/groups.unified/owners/update | ロールの割り当て可能なグループを除外して、Microsoft 365 グループの所有者を更新する |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | サービス プリンシパルにグループのデータへの直接アクセスを許可する  |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.network/performance/allProperties/read | Microsoft 365 管理センターで、すべてのネットワーク パフォーマンス プロパティを読み取る |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Skype for Business Online の全側面の管理 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 の使用状況レポートを読み取る |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |
> | microsoft.teams/allEntities/allProperties/allTasks | Teams のすべてのリソースを管理する |

## <a name="teams-communications-administrator"></a>Teams 通信管理者

このロールのユーザーは、音声とテレフォニーに関連する Microsoft Teams のワークロードの各側面を管理できます。 これには、電話番号の割り当て、音声と会議のポリシー、および通話分析ツールセットへのフル アクセスのための管理ツールが含まれます。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Skype for Business Online の全側面の管理 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 の使用状況レポートを読み取る |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |
> | microsoft.teams/callQuality/allProperties/read | 通話品質ダッシュボード (CQD) のすべてのデータを読み取る |
> | microsoft.teams/meetings/allProperties/allTasks | 会議ポリシー、構成、会議ブリッジを含む、会議を管理する |
> | microsoft.teams/voice/allProperties/allTasks | 通話ポリシーや電話番号のインベントリおよび割り当てを含む、音声を管理する |

## <a name="teams-communications-support-engineer"></a>Teams 通信サポート エンジニア

このロールのユーザーは、Microsoft Teams と Skype for Business の管理センターでユーザー通話のトラブルシューティング ツールを使用して、Microsoft Teams と Skype for Business での通信に関する問題をトラブルシューティングできます。 このロールのユーザーは、関係するすべての参加者の完全な通話記録情報を表示できます。 このロールには、サポート チケットの表示、作成、管理のためのアクセス権がありません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Skype for Business Online の全側面の管理 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |
> | microsoft.teams/callQuality/allProperties/read | 通話品質ダッシュボード (CQD) のすべてのデータを読み取る |

## <a name="teams-communications-support-specialist"></a>Teams 通信サポート スペシャリスト

このロールのユーザーは、Microsoft Teams と Skype for Business の管理センターでユーザー通話のトラブルシューティング ツールを使用して、Microsoft Teams と Skype for Business での通信に関する問題をトラブルシューティングできます。 このロールのユーザーが表示できるのは、調査した特定ユーザーの通話における詳細のみです。 このロールには、サポート チケットの表示、作成、管理のためのアクセス権がありません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Skype for Business Online の全側面の管理 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |
> | microsoft.teams/callQuality/standard/read | 通話品質ダッシュボード (CQD) の基本データを読み取る |

## <a name="teams-devices-administrator"></a>Teams デバイス管理者

このロールを持つユーザーは、Teams 管理センターから [Teams 認定デバイス](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices)を管理できます。 このロールでは、すべてのデバイスを一目で見ることができ、デバイスの検索とフィルター処理を行えます。 ユーザーは、ログイン アカウント、デバイスの製造元やモデルなど、各デバイスの詳細を確認できます。 ユーザーは、デバイスの設定を変更したり、ソフトウェアのバージョンを更新したりできます。 このロールでは、Teams アクティビティとデバイスの通話品質を調べるアクセス許可は付与されません。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |
> | microsoft.teams/devices/standard/read | 構成ポリシーを含む、Teams 認定デバイスのすべての側面を管理する |

## <a name="usage-summary-reports-reader"></a>使用状況の概要のレポート閲覧者

このロールのユーザーは、使用状況と生産性スコアについて、Microsoft 365 管理センターでテナント レベルの集計データと関連する分析情報にアクセスできますが、ユーザー レベルの詳細や分析情報にはアクセスできません。 2 つのレポートについて、Microsoft 365 管理センターでは、テナント レベルの集計データとユーザー レベルの詳細が区別されます。 このロールは、顧客と法務チームの両方によって求められた個々のユーザーの識別可能データに対して追加の保護レイヤーを提供します。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.office365.usageReports/allEntities/standard/read | テナントレベルの集計された Office 365 利用状況レポートを読み取る |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="user-administrator"></a>ユーザー管理者

このロールが割り当てられたユーザーは、ユーザーを作成し、いくつか制限はありますがユーザーのすべての側面を管理できる (表参照) ほか、パスワードの有効期限ポリシーを更新できます。 また、このロールのユーザーは、グループを作成し、すべてのグループを管理することができます。 このロールでは、ユーザー ビューを作成および管理、サポート チケットの管理、サービスの正常性の監視を行うこともできます。 ユーザー管理者には、ほとんどの管理者ロールのユーザーについて、一部のユーザー プロパティを管理するためのアクセス許可が与えられません。 このロールのユーザーには、MFA を管理するためのアクセス許可がありません。 この制限の例外に相当するロールを次の表にまとめています。

| ユーザー管理者のアクセス許可 | Notes |
| --- | --- |
| ユーザーとグループを作成する<br/>ユーザー ビューの作成と管理<br/>Office サポート チケットの管理<br/>パスワードの有効期限ポリシーを更新する |  |
| ライセンスを管理する<br/>ユーザー プリンシパル名を除くすべてのユーザーのプロパティを管理する | すべての管理者を含むすべてのユーザーに適用されます |
| 削除と復元<br/>無効化と有効化<br/>ユーザー プリンシパル名を含むすべてのユーザーのプロパティを管理する<br/>(FIDO) デバイス キーを更新する | 管理者以外のユーザー、または次のいずれかのロールのユーザーに適用されます。<ul><li>ヘルプデスク管理者</li><li>ロールのないユーザー</li><li>ユーザー管理者</li></ul> |
| 更新トークンを無効にする<br/>[パスワードのリセット] | ユーザー管理者がパスワードをリセットして更新トークンを無効にできるロールの一覧については、「[パスワード リセットのアクセス許可](#password-reset-permissions)」を参照してください。 |

> [!IMPORTANT]
> このロールを持つユーザーは、機密情報や個人情報または Azure Active Directory の内外の重要な構成にアクセスできるユーザーのパスワードを変更できます。 ユーザーのパスワードを変更するということは、そのユーザーの ID およびアクセス許可を取得できるということを意味します。 次に例を示します。
>
>- 所有しているアプリの資格情報を管理できる、アプリケーションの登録とエンタープライズ アプリケーションの所有者。 これらのアプリには、Azure AD およびユーザー管理者に付与されていない場所への特権アクセス許可がある場合があります。 ユーザー管理者は、このパスからアプリケーション所有者の ID を取得し、さらにそのアプリケーションの資格情報を更新して特権アプリケーションの ID を取得できる場合があります。
>- 機密情報や個人情報または Azure の重要な構成にアクセスできる Azure サブスクリプション所有者。
>- グループ メンバーシップを管理できるセキュリティ グループと Microsoft 365 グループの所有者。 これらのグループは、機密情報や個人情報または Azure AD や別の場所の重要な構成へのアクセス権を付与される場合があります。
>- Exchange Online、Office Security and Compliance Center、人事システムのような Azure AD 以外のサービスの管理者。
>- 機密情報や個人情報にアクセスできる場合がある役員、弁護士、人事担当者のような非管理者。

> [!div class="mx-tableFixed"]
> | アクション | 説明 |
> | --- | --- |
> | microsoft.directory/appRoleAssignments/create | アプリケーション ロールの割り当てを作成する |
> | microsoft.directory/appRoleAssignments/delete | アプリケーション ロールの割り当てを削除する |
> | microsoft.directory/appRoleAssignments/basic/update | アプリケーション ロールの割り当ての基本プロパティを更新する |
> | microsoft.directory/contacts/create | 連絡先を作成する |
> | microsoft.directory/contacts/delete | 連絡先を削除する |
> | microsoft.directory/contacts/basic/update | 連絡先の基本プロパティを更新する |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | Azure AD エンタイトルメント管理でのリソースの作成と削除、すべてのプロパティの読み取りと更新を行う |
> | microsoft.directory/groups/assignLicense | グループベースのライセンスのグループに製品ライセンスを割り当てる |
> | microsoft.directory/groups/create | ロール割り当て可能なグループを除き、グループを削除する |
> | microsoft.directory/groups/delete | ロール割り当て可能なグループを除くグループの削除 |
> | microsoft.directory/groups/hiddenMembers/read | グループの非表示メンバーの読み取り |
> | microsoft.directory/groups/reprocessLicenseAssignment | グループベースのライセンスのライセンス割り当てを再処理する |
> | microsoft.directory/groups/restore | 削除されたグループの復元 |
> | microsoft.directory/groups/basic/update | ロール割り当て可能なグループを除き、グループの基本プロパティを更新する |
> | microsoft.directory/groups/classification/update | ロール割り当て可能なグループを除き、グループの分類プロパティを更新する |
> | microsoft.directory/groups/dynamicMembershipRule/update | ロール割り当て可能なグループを除き、グループの動的メンバーシップ規則を更新する |
> | microsoft.directory/groups/groupType/update | グループの groupType プロパティを更新する |
> | microsoft.directory/groups/members/update | ロール割り当て可能なグループを除き、グループのメンバーを更新する |
> | microsoft.directory/groups/onPremWriteBack/update | オンプレミスに書き戻されるように Azure AD グループを更新する |
> | microsoft.directory/groups/owners/update | ロール割り当て可能なグループを除くグループの所有者の更新 |
> | microsoft.directory/groups/settings/update | グループの設定を更新する |
> | microsoft.directory/groups/visibility/update | グループの可視性プロパティを更新する |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0 アクセス許可の付与の作成と削除、およびすべてのプロパティの読み取りと更新 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | サービス プリンシパルのロールの割り当ての更新 |
> | microsoft.directory/users/assignLicense | ユーザー ライセンスの管理 |
> | microsoft.directory/users/create | ユーザーの追加 |
> | microsoft.directory/users/delete | ユーザーを削除する |
> | microsoft.directory/users/disable | ユーザーの無効化 |
> | microsoft.directory/users/enable | ユーザーの有効化 |
> | microsoft.directory/users/inviteGuest | ゲスト ユーザーを招待する |
> | microsoft.directory/users/invalidateAllRefreshTokens | ユーザー更新トークンを無効にして強制的にサインアウトする |
> | microsoft.directory/users/reprocessLicenseAssignment | ユーザーのライセンス割り当てを再処理する |
> | microsoft.directory/users/restore | 削除されたユーザーを復元する |
> | microsoft.directory/users/basic/update | ユーザーの基本プロパティを更新する |
> | microsoft.directory/users/manager/update | ユーザーのマネージャーを更新する |
> | microsoft.directory/users/password/update | すべてのユーザーのパスワードをリセットする |
> | microsoft.directory/users/userPrincipalName/update | ユーザーのユーザー プリンシパル名を更新する |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health を読み取り、構成する |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットを作成および管理する |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 管理センターで Service Health を読み取り、構成する |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 サービス要求を作成および管理する |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 管理センターですべてのリソースの基本プロパティを読み取る |

## <a name="deprecated-roles"></a>非推奨のロール

次のロールは使用しないでください。 これらは非推奨となっており、将来的に Azure AD から削除されます。

* アドホック ライセンス管理者
* デバイスの参加
* デバイス マネージャー
* デバイス ユーザー
* メールで確認済みのユーザー作成者
* メールボックス管理者
* デバイスの社内参加

## <a name="roles-not-shown-in-the-portal"></a>ポータルに表示されないロール

PowerShell または MS Graph API によって返されるすべてのロールが Azure portal に表示されるわけではありません。 それらの違いを次の表にまとめます。

API 名 | Azure portal での名前 | Notes
-------- | ------------------- | -------------
デバイスの参加 | 非推奨 | [非推奨になったロールのドキュメント](#deprecated-roles)
デバイス マネージャー | 非推奨 | [非推奨になったロールのドキュメント](#deprecated-roles)
デバイス ユーザー | 非推奨 | [非推奨になったロールのドキュメント](#deprecated-roles)
ディレクトリ同期アカウント | 使用するべきではないため、表示されません | [ディレクトリ同期アカウントのドキュメント](#directory-synchronization-accounts)
ゲスト ユーザー | 使用できないため、表示されません | NA
Partner Tier 1 サポート | 使用するべきではないため、表示されません | [Partner Tier 1 サポートのドキュメント](#partner-tier1-support)
Partner Tier 2 サポート | 使用するべきではないため、表示されません | [Partner Tier 2 サポートのドキュメント](#partner-tier2-support)
制限されたゲストユーザー | 使用できないため、表示されません | NA
User | 使用できないため、表示されません | NA
デバイスの社内参加 | 非推奨 | [非推奨になったロールのドキュメント](#deprecated-roles)

## <a name="password-reset-permissions"></a>パスワード リセットのアクセス許可

列見出しは、パスワードをリセットできるロールを表します。 テーブルの行には、パスワードをリセットできるロールが含まれます。

パスワードをリセットできる | パスワード管理者 | ヘルプデスク管理者 | 認証管理者 | [ユーザー管理者] | 特権認証管理者 | グローバル管理者
------ | ------ | ------ | ------ | ------ | ------ | ------
認証管理者 | &nbsp; | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
ディレクトリ リーダー | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
グローバル管理者 | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:\*
グループ管理者 | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
ゲスト招待元 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
ヘルプデスク管理者 | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
メッセージ センター閲覧者 | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
パスワード管理者 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
特権認証管理者 | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
特権ロール管理者 | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
レポート閲覧者 | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
ユーザー (管理者ロールなし) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
[ユーザー管理者] | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
使用状況の概要のレポート閲覧者 | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:

\* グローバル管理者は、自分のグローバル管理者の割り当てを削除することはできません。 これは、組織にグローバル管理者がいなくなる状況を防ぐためです。

## <a name="next-steps"></a>次のステップ

* Azure サブスクリプションの管理者としてユーザーを割り当てる方法の詳細については、[Azure サブスクリプションの管理者としてユーザーを割り当てる](../../role-based-access-control/role-assignments-portal-subscription-admin.md)に関する記事をご覧ください
* Microsoft Azure でリソース アクセスを制御する方法の詳細については、[さまざまなロールの概要](../../role-based-access-control/rbac-and-directory-admin-roles.md)に関するページを参照してください
* サブスクリプションと Azure AD テナントの関係の詳細、またはサブスクリプションの関連付けまたは追加を行う手順の詳細については、「[Azure サブスクリプションを Azure Active Directory テナントに関連付けるまたは追加する](../fundamentals/active-directory-how-subscriptions-associated-directory.md)」を参照してください。
