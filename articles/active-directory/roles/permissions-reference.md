---
title: Azure AD ロールの説明とアクセス許可 - Azure Active Directory | Microsoft Docs
description: 管理者ロールは、ユーザーの追加、管理者ロールの割り当て、ユーザー パスワードのリセット、ユーザー ライセンスの管理、ドメインの管理などを行うことができます。
services: active-directory
author: rolyon
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 02/01/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: da85c80dd6450fd4427f83586e75cf1e9d62a605
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428776"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Azure Active Directory での管理者ロールのアクセス許可

Azure Active Directory (Azure AD) を使用して、特権が低いロールで ID のタスクを管理する限定された管理者を指定できます。 ユーザーの追加または変更、管理ロールの割り当て、ユーザーのパスワードのリセット、ユーザーのライセンスの管理、ドメイン名の管理などの目的で管理者を割り当てることができます。 [既定のユーザー アクセス許可](../fundamentals/users-default-permissions.md)は、Azure AD のユーザー設定のみで変更できます。

## <a name="limit-use-of-global-administrator"></a>グローバル管理者の使用を制限する

グローバル管理者ロールに割り当てられているユーザーは、Azure AD 組織内のすべての管理設定の読み取りと変更を行うことができます。 既定では、ユーザーが Microsoft のクラウド サービスに新規登録すると、Azure AD テナントが作成され、そのユーザーはグローバル管理者ロールのメンバーになります。 サブスクリプションを既存のテナントに追加するとき、そのユーザーはグローバル管理者ロールに割り当てられません。 管理者ロールを委任できるのはグローバル管理者と特権ロール管理者だけです。 ビジネスに対するリスクを軽減するには、このロールを割り当てる組織内のユーザーの数をできる限り少なくすることをお勧めします。

ベスト プラクティスとして、組織内でこのロールを割り当てる人は 5 人未満にすることをお勧めします。 組織内でグローバル管理者ロールを割り当てる管理者が 5 人以上になる場合は、次の方法でその利用を減らすことができます。

### <a name="find-the-role-you-need"></a>必要なロールを調べる

多くのロールのリストから必要なロールを見つけるのが面倒な場合は、Azure AD でロールのカテゴリに基づいてロールのサブセットを表示できます。 選択した種類のロールだけが表示される、[Azure AD ロールと管理者](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)に対する新しい **種類** フィルターを確認してください。

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>グローバル管理者ロールを割り当てたときには存在していなかったロールが存在する

一部のユーザーをグローバル管理者に昇格させたときには選択できなかった、よりきめ細かいアクセス許可が提供されるロールが、Azure AD に追加されている可能性があります。 時間の経過と共に、以前はグローバル管理者ロールだけが実行できたタスクを遂行する新しいロールがロールアウトされています。 これらは、次の「[使用可能なロール](#available-roles)」に反映されています。

## <a name="assign-or-remove-administrator-roles"></a>管理者ロールの割り当てまたは削除

Azure Active Directory でユーザーに管理者ロールを割り当てる方法については、[Azure Active Directory での管理者ロールの表示と割り当て](manage-roles-portal.md)に関するページを参照してください。

> [!Note]
> Azure AD プレミアム P2 ライセンスがあるとき、Privileged Identity Management (PIM) ユーザーに既になっている場合、ロール管理タスクはすべて、Azure AD ではなく、Privileged Identity Management で実行されます。
>
> ![既に PIM を使用していて Premium P2 ライセンスを所有しているユーザーの Azure AD ロールは PIM で管理される](./media/permissions-reference/pim-manages-roles-for-p2.png)

## <a name="available-roles"></a>使用可能なロール

次の管理者ロールを使用できます。

### <a name="application-administrator"></a>[アプリケーション管理者](#application-administrator-permissions)

このロールのユーザーは、エンタープライズ アプリケーション、アプリケーション登録、アプリケーション プロキシの設定の全側面を作成して管理できます。 このロールに割り当てられたユーザーは、新しいアプリケーション登録またはエンタープライズ アプリケーションを作成する際に、所有者として追加されないことに注意してください。

このロールでは、委任されたアクセス許可とアプリケーション アクセス許可 (Microsoft Graph API に対するアプリケーション アクセス許可を除く) に "_同意する_" 権限も付与されます。

> [!IMPORTANT]
> この例外は、"_他の_" アプリ (たとえば、Microsoft 以外のアプリや自分が登録したアプリなど) に対するアクセス許可には引き続き同意できるが、Azure AD 自体に対するアクセス許可には同意できないことを意味します。 アプリ登録の一環としてこれらのアクセス許可を "_要求_" することはできますが、 これらのアクセス許可を "_許可する_" (つまり、同意する) には、Azure AD 管理者が必要です。つまり、悪意のあるユーザーは、ディレクトリ全体に書き込み可能なアプリを作成してそれに同意し、そのアプリのアクセス許可を通じて自分自身をグローバル管理者に昇格させるなどの方法によって、自分のアクセス許可を簡単に昇格させることはできません。
>
>このロールは、アプリケーションの資格情報を管理する権限を付与します。 このロールが割り当てられたユーザーは、アプリケーションに資格情報を追加し、その資格情報を使用してアプリケーションの ID を偽装することができます。 アプリケーションの ID にリソースへのアクセス権 (ユーザーやその他のオブジェクトの作成や更新など) が付与されている場合、このロールに割り当てられたユーザーは、アプリケーションを偽装している間にこのようなアクションを実行することができます。 アプリケーションの ID を偽装するこの機能は、ユーザーがロール割り当てを使用して実行できることを越えた特権の昇格になる可能性があります。 ユーザーにアプリケーション管理者ロールを割り当てると、そのユーザーがアプリケーションの ID を偽装できるようになることを理解することが重要です。

### <a name="application-developer"></a>[アプリケーション開発者](#application-developer-permissions)

このロールのユーザーは、[ユーザーはアプリケーションを登録できる] 設定が [いいえ] に設定されている場合に、アプリケーション登録を作成できます。 さらにこのロールでは、[ユーザーはアプリが自身の代わりに会社のデータにアクセスすることを許可できます] 設定が [いいえ] に設定されている場合に、代わりに同意する権限を付与します。 このロールに割り当てられたユーザーは、新しいアプリケーション登録またはエンタープライズ アプリケーションを作成する際に、所有者として追加されます。

### <a name="authentication-administrator"></a>[認証管理者](#authentication-administrator-permissions)

このロールのユーザーは、一部のユーザーのパスワード以外の資格情報の設定やリセットを実行でき、すべてのユーザーのパスワードを更新できます。 認証管理者は、パスワード以外の既存の資格情報 (たとえば、MFA、FIDO) に対する再登録を管理者以外のユーザー、または一部のロールに割り当てられているユーザーに要求し、"**このデバイスに MFA を記憶する**" 機能を取り消すことができます。これによって、管理者でないユーザーや次のロールのみが割り当てられているユーザーが次回サインインするときに MFA の入力を求めることもできます。 認証管理者がユーザーのパスワードをリセットできるかどうかは、ユーザーが割り当てられているロールに依存します。 認証管理者がパスワードをリセットできるロールの一覧については、「[パスワード リセットのアクセス許可](#password-reset-permissions)」を参照してください。

[特権認証管理者](#privileged-authentication-administrator)ロールには、すべてのユーザーに対して再登録と多要素認証を強制できるアクセス許可があります。

> [!IMPORTANT]
> このロールのユーザーは、機密情報や個人情報または Azure Active Directory の内外の重要な構成にアクセスできるユーザーのパスワードを変更できます。 ユーザーの資格情報を変更するということは、そのユーザーの ID とアクセス許可を引き受けることができるということを意味します。 次に例を示します。
>
>* 所有しているアプリの資格情報を管理できる、アプリケーションの登録とエンタープライズ アプリケーションの所有者。 これらのアプリには、認証管理者に付与されていないAzure AD およびその他の場所への特権アクセス許可がある場合があります。 認証管理者は、このパスからアプリケーション所有者の ID を引き受け、さらにそのアプリケーションの資格情報を更新して特権アプリケーションの ID を引き受けることができる場合があります。
>* 機密情報や個人情報または Azure の重要な構成にアクセスできる Azure サブスクリプション所有者。
>* グループ メンバーシップを管理できるセキュリティ グループと Microsoft 365 グループの所有者。 これらのグループは、機密情報や個人情報または Azure AD や別の場所の重要な構成へのアクセス権を付与される場合があります。
>* Exchange Online、Office Security and Compliance Center、人事システムのような Azure AD 以外のサービスの管理者。
>* 機密情報や個人情報にアクセスできる場合がある役員、弁護士、人事担当者のような非管理者。

### <a name="attack-payload-author"></a>[攻撃のペイロードの作成者](#attack-payload-author-permissions)

このロールのユーザーは、攻撃のペイロードを作成することはできますが、それらを実際に起動することやスケジュールすることはできません。 攻撃のペイロードは、それらを使用してシミュレーション作成できるテナントの管理者全員が利用できます。

### <a name="attack-simulation-administrator"></a>[攻撃のシミュレーションの管理者](#attack-simulation-administrator-permissions)

このロールのユーザーは、攻撃シミュレーションの作成、シミュレーションの開始とスケジューリング、シミュレーション結果の確認のすべての側面を作成および管理できます。 このロールのメンバーは、テナント内のすべてのシミュレーションに対してこのアクセス権を所有します。

### <a name="azure-devops-administrator"></a>[Azure DevOps 管理者](#azure-devops-administrator-permissions)

このロールが割り当てられたユーザーは、Azure DevOps ポリシーを管理して、新しい Azure DevOps 組織の作成を、構成可能な一連のユーザーやグループに制限できます。 このロールのユーザーは、会社の Azure AD 組織によってサポートされている Azure DevOps 組織を通じて、このポリシーを管理できます。 このロールは、会社の Azure AD 組織によってサポートされている Azure DevOps 組織の中で、Azure DevOps 固有の他のアクセス許可 (プロジェクト コレクション管理者など) を付与しません。

エンタープライズ Azure DevOps のすべてのポリシーは、このロールのユーザーによって管理できます。

### <a name="azure-information-protection-administrator"></a>[Azure Information Protection 管理者](#azure-information-protection-administrator-permissions)

このロールが割り当てられたユーザーは、Azure Information Protection サービスのすべてのアクセス許可を持ちます。 このロールでは、Azure Information Protection ポリシーのラベルの構成、保護テンプレートの管理、保護のアクティブ化を行うことができます。 このロールでは、Identity Protection Center、Privileged Identity Management、Microsoft 365 Service Health の監視、および Office 365 のセキュリティ/コンプライアンス センターのアクセス許可は付与されません。

### <a name="b2c-ief-keyset-administrator"></a>[B2C IEF キーセット管理者](#b2c-ief-keyset-administrator-permissions)

ユーザーは、トークンの暗号化、トークンの署名、および要求の暗号化/暗号化解除のために、ポリシー キーとシークレットを作成して管理できます。  新しいキーを既存のキー コンテナーに追加すると、この制限付き管理者は、既存のアプリケーションに影響を与えることなく、必要に応じてシークレットをロールオーバーできます。  このユーザーは、これらのシークレットとその有効期限の完全な内容を、シークレットを作成した後でも確認できます。

> [!IMPORTANT]
> これは機密性の高いロールです。  キーセット管理者ロールは、慎重に監査し、運用前環境と運用環境では、慎重に割り当てる必要があります。

### <a name="b2c-ief-policy-administrator"></a>[B2C IEF ポリシー管理者](#b2c-ief-policy-administrator-permissions)

このロールが割り当てられたユーザーは、Azure AD B2C のすべてのカスタム ポリシーを作成、読み取り、更新、および削除することができます。そのため、関連する Azure AD B2C 組織内の Identity Experience Framework を完全に制御できます。 このユーザーは、ポリシーを編集することで、外部の ID プロバイダーとの直接フェデレーションの確立、ディレクトリ スキーマの変更、ユーザー向けのすべてのコンテンツ (HTML、CSS、JavaScript) の変更、認証を完了するための要件の変更、新しいユーザーの作成、ユーザー データの外部システムへの送信 (完全な移行を含む)、パスワードや電話番号などの機密フィールドを含むすべてのユーザー情報の編集を行うことができます。 逆に、このロールでは、暗号化キーを変更したり、組織内のフェデレーションに使用されているシークレットを編集したりすることはできません。

> [!IMPORTANT]
> B2 IEF ポリシー管理者は、非常に機密性の高いロールであるため、運用環境ではごく限られた組織に割り当てる必要があります。  これらのユーザーによるアクティビティは、とりわけ運用環境の組織に対しては、注意深く監査する必要があります。

### <a name="billing-administrator"></a>[課金管理者](#billing-administrator-permissions)

購入、サブスクリプションの管理、サポート チケットの管理、サービスの正常性の監視を行います。

### <a name="cloud-application-administrator"></a>[クラウド アプリケーション管理者](#cloud-application-administrator-permissions)

このロールのユーザーは、(アプリケーション プロキシを管理する権限を除き) アプリケーション管理者ロールと同じアクセス許可を持ちます。 このロールは、エンタープライズ アプリケーションとアプリケーション登録の全側面を作成して管理する権限を付与します。 さらに、このロールは、委任されたアクセス許可とアプリケーション アクセス許可 (Microsoft Graph と Azure AD Graph を除く) に同意する権限を付与します。 このロールに割り当てられたユーザーは、新しいアプリケーション登録またはエンタープライズ アプリケーションを作成する際に、所有者として追加されません。

> [!IMPORTANT]
> このロールは、アプリケーションの資格情報を管理する権限を付与します。 このロールが割り当てられたユーザーは、アプリケーションに資格情報を追加し、その資格情報を使用してアプリケーションの ID を偽装することができます。 アプリケーションの ID にリソースへのアクセス権 (ユーザーやその他のオブジェクトの作成や更新など) が付与されている場合、このロールに割り当てられたユーザーは、アプリケーションを偽装している間にこのようなアクションを実行することができます。 アプリケーションの ID を偽装するこの機能は、ユーザーがロール割り当てを使用して実行できることを越えた特権の昇格になる可能性があります。 ユーザーにクラウド アプリケーション管理者ロールを割り当てると、そのユーザーがアプリケーションの ID を偽装できるようになることを理解することが重要です。


### <a name="cloud-device-administrator"></a>[クラウド デバイス管理者](#cloud-device-administrator-permissions)

このロールのユーザーは、Azure AD でデバイスを有効化、無効化、および削除することができ、Azure portal で Windows 10 の BitLocker キーを読み取る (ある場合) ことができます。 このロールでは、デバイス上の他のプロパティを管理するアクセス許可の付与は行いません。

### <a name="compliance-administrator"></a>[コンプライアンス管理者](#compliance-administrator-permissions)

このロールのユーザーには、Microsoft 365 コンプライアンス センター、Microsoft 365 管理センター、Azure、および Office 365 セキュリティ/コンプライアンス センターのコンプライアンス関連の機能を管理する権限があります。 割り当て先は、Exchange 管理センター、Teams および Skype for Business の管理センター内のすべての機能を管理したり、Azure および Microsoft 365 のサポート チケットを作成したりすることもできます。 詳しくは、「[管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。

場所 | できること
----- | ----------
[Microsoft 365 コンプライアンス センター](https://protection.office.com) | Microsoft 365 サービス全体での組織のデータの保護および管理<br>コンプライアンス アラートの管理
[コンプライアンス マネージャー](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 組織の法令遵守活動の追跡、割り当て、確認
[Office 365 セキュリティ/コンプライアンス センター](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | データ ガバナンスの管理<br>法律およびデータ調査の実行<br>データ主体の要求の管理<br><br>このロールには、Office 365 セキュリティ/コンプライアンス センターのロールベースのアクセス制御の[コンプライアンス管理者ロール グループ](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)と同じアクセス許可が付与されています。
[Intune](/intune/role-based-access-control) | Intune のすべての監査データの表示
[Cloud App Security](/cloud-app-security/manage-admins) | 読み取り専用アクセス許可があり、アラートを管理できる<br>ファイル ポリシーの作成と変更、ファイル ガバナンス アクションの許可<br>データ管理下のすべての組み込みレポートの表示

### <a name="compliance-data-administrator"></a>[コンプライアンス データ管理者](#compliance-data-administrator-permissions)

このロールが割り当てられたユーザーには、Microsoft 365 コンプライアンス センター、Microsoft 365 管理センター、および Azure のデータを追跡するための権限があります。 ユーザーは、Exchange 管理センター、Compliance Manager、Teams および Skype for Business の管理センター内のコンプライアンス データを追跡したり、Azure および Microsoft 365 のサポート チケットを作成したりすることもできます。 [このドキュメント](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)には、コンプライアンス管理者とコンプライアンス データ管理者との違いが詳しく記載されています。

場所 | できること
----- | ----------
[Microsoft 365 コンプライアンス センター](https://protection.office.com) | Microsoft 365 サービス全体のコンプライアンス関連ポリシーの監視<br>コンプライアンス アラートの管理
[コンプライアンス マネージャー](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 組織の法令遵守活動の追跡、割り当て、確認
[Office 365 セキュリティ/コンプライアンス センター](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | データ ガバナンスの管理<br>法律およびデータ調査の実行<br>データ主体の要求の管理<br><br>このロールには、Office 365 セキュリティ/コンプライアンス センターのロールベースのアクセス制御の[コンプライアンス データ管理者ロール グループ](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)と同じアクセス許可が付与されています。
[Intune](/intune/role-based-access-control) | Intune のすべての監査データの表示
[Cloud App Security](/cloud-app-security/manage-admins) | 読み取り専用アクセス許可があり、アラートを管理できる<br>ファイル ポリシーの作成と変更、ファイル ガバナンス アクションの許可<br>データ管理下のすべての組み込みレポートの表示

### <a name="conditional-access-administrator"></a>[条件付きアクセス管理者](#conditional-access-administrator-permissions)

このロールのユーザーは、Azure Active Directory の条件付きアクセスの設定を管理できます。

### <a name="customer-lockbox-access-approver"></a>[カスタマー ロックボックスのアクセス承認者](#customer-lockbox-access-approver-permissions)

組織内の[カスタマー ロックボックス要求](/office365/admin/manage/customer-lockbox-requests)を管理します。 彼らはカスタマー ロックボックス要求の電子メール通知を受信し、Microsoft 365 管理センターから要求の承認と拒否を行うことができます。 カスタマー ロックボックス機能を有効または無効にすることもできます。 グローバル管理者のみが、このロールに割り当てられているユーザーのパスワードをリセットできます。

### <a name="desktop-analytics-administrator"></a>[デスクトップ Analytics 管理者](#desktop-analytics-administrator-permissions)


このロールのユーザーは、デスクトップ Analytics および Office のカスタマイズとポリシーのサービスを管理できます。 デスクトップ Analytics の場合、これには、資産インベントリの表示、デプロイ プランの作成、デプロイと正常性の状態の表示に対する権限が含まれます。 Office のカスタマイズとポリシーのサービスの場合、このロールによりユーザーは Office のポリシーを管理することができます。

### <a name="device-administrators"></a>[デバイス管理者](#device-administrators-permissions)

この役割は、[デバイス設定](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)の追加のローカル管理者としてのみ割り当て可能です。 このロールのユーザーは、Azure Active Directory に参加しているすべての Windows 10 デバイスのローカル マシン管理者になります。 Azure Active Directory 内のデバイス オブジェクトを管理することはできません。

### <a name="directory-readers"></a>[ディレクトリ閲覧者](#directory-readers-permissions)

このロールのユーザーは、基本的なディレクトリ情報を読み取ることができます。 このロールは次の目的で使用してください。

* 読み取りアクセスをすべてのゲスト ユーザーに付与せず、特定のゲスト ユーザー セットに付与する。
* [Restrict access to Azure AD portal to admins only] (Azure AD ポータルへのアクセスを管理者のみに制限する) が [はい] に設定されている場合に、管理者以外の特定のユーザー セットに Azure portal へのアクセスを付与する。
* Directory.Read.All を選択できないディレクトリへのアクセスをサービス プリンシパルに付与する。

### <a name="directory-synchronization-accounts"></a>[ディレクトリ同期アカウント](#directory-synchronization-accounts-permissions)

使用しないでください。 このロールは、自動的に Azure AD Connect サービスに割り当てられます。他の用途に使用するためのものではなく、他の用途ではサポートされていません。

### <a name="directory-writers"></a>[ディレクトリ ライター](#directory-writers-permissions)
このロールのユーザーは、ユーザー、グループ、およびサービス プリンシパルの基本情報の読み取りと更新が可能です。 このロールは、[同意フレームワーク](../develop/quickstart-register-app.md)をサポートしていないアプリケーションにのみ割り当てます。 このロールをユーザーに割り当てることはできません。

### <a name="dynamics-365-administrator--crm-administrator"></a>[Dynamics 365 管理者/CRM 管理者](#crm-service-administrator-permissions)

このロールが割り当てられたユーザーは、Microsoft Dynamics 365 Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、サポート チケットを管理し、サービス正常性を監視できます。 詳細については、[サービス管理者ロールを使用した Azure AD 組織の管理](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)に関する記事をご覧ください。

> [!NOTE]
> Microsoft Graph API と Azure AD PowerShell では、このロールは "Dynamics 365 サービス管理者" として識別されます。 [Azure portal](https://portal.azure.com) では、"Dynamics 365 管理者" になります。

### <a name="exchange-administrator"></a>[Exchange 管理者](#exchange-service-administrator-permissions)

このロールが割り当てられたユーザーは、Microsoft Exchange Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。 また、すべての Microsoft 365 グループの作成および管理、サポート チケットの管理、サービスの正常性の監視を行うこともできます。 詳細については、「[管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。

> [!NOTE]
> Microsoft Graph API と Azure AD PowerShell では、このロールは "Exchange サービス管理者" として識別されます。 [Azure portal](https://portal.azure.com) では、"Exchange 管理者" になります。 [Exchange 管理センター](https://go.microsoft.com/fwlink/p/?LinkID=529144)では "Exchange Online 管理者" になります。


### <a name="external-id-user-flow-administrator"></a>[外部 ID ユーザー フロー管理者](#external-id-user-flow-administrator-permissions)

このロールが割り当てられたユーザーは、Azure portal でユーザー フロー ("組み込み" ポリシーとも呼ばれます) を作成および管理することができます。 これらのユーザーは、HTML/CSS/JavaScript コンテンツのカスタマイズ、MFA 要件の変更、トークン内のクレームの選択、API コネクタの管理、および Azure AD 組織内のすべてのユーザー フローのセッション設定の構成を行うことができます。 その一方で、このロールには、ユーザーのデータを確認したり、組織スキーマに含まれている属性を変更したりする機能は含まれていません。 Identity Experience Framework ポリシー (カスタム ポリシーとも呼ばれます) の変更も、このロールの範囲外です。

### <a name="external-id-user-flow-attribute-administrator"></a>[外部 ID ユーザー フロー属性管理者](#external-id-user-flow-attribute-administrator-permissions)

このロールが割り当てられたユーザーは、Azure AD 組織内のすべてのユーザー フローで使用可能なカスタム属性を追加または削除できます。  そのため、このロールが割り当てられたユーザーは、エンド ユーザー スキーマを変更、またはエンド ユーザー スキーマに新しい要素を追加して、すべてのユーザー フローの動作に影響を及ぼしたり、エンド ユーザーが求め、最終的には要求としてアプリケーションに送信されるデータを間接的に変更したりできます。  このロールでユーザー フローを編集することはできません。

### <a name="external-identity-provider-administrator"></a>[外部 ID プロバイダー管理者](#external-identity-provider-administrator-permissions)

この管理者は、Azure AD 組織と外部 ID プロバイダー間のフェデレーションを管理します。  このロールが割り当てられたユーザーは、新しい ID プロバイダーを追加し、使用可能なすべての設定 (認証パス、サービス ID、割り当てられたキー コンテナーなど) を構成できます。  このユーザーは、Azure AD 組織が外部 ID プロバイダーからの認証を信頼できるようにすることができます。  その結果としてエンド ユーザー エクスペリエンスに及ぼす影響は、組織の種類によって異なります。

* 従業員とパートナー向けの Azure AD 組織: (たとえば Gmail との) フェデレーションの追加は、まだ招待に応じていないすべてのゲストの招待にすぐに影響します。 「[Google を B2B ゲスト ユーザーの ID プロバイダーとして追加する](../external-identities/google-federation.md)」を参照してください。
* Azure Active Directory B2C 組織:(たとえば Facebook、または別の Azure AD 組織との) フェデレーションの追加は、ユーザー フロー (組み込みポリシーとも呼ばれます) で ID プロバイダーがオプションとして追加されるまで、エンド ユーザー フローにすぐに影響することはありません。 例については、[ID プロバイダーとしての Microsoft アカウントの構成](../../active-directory-b2c/identity-provider-microsoft-account.md)に関するページを参照してください。  ユーザー フローを変更するには、"B2C ユーザー フロー管理者" の制限されたロールが必要です。

### <a name="global-administrator"></a>[全体管理者](#global-administrator-permissions)

このロールが割り当てられたユーザーは、Azure Active Directory のすべての管理機能と、Azure Active Directory の ID を使用するサービス (Microsoft 365 セキュリティ センター、Microsoft 365 security center コンプライアンス センター、Exchange Online、SharePoint Online、Skype for Business Online など) にアクセスできます。 さらにグローバル管理者は、[アクセスを昇格させる](../../role-based-access-control/elevate-access-global-admin.md)ことで、すべての Azure サブスクリプションと管理グループを管理できます。 これにより、グローバル管理者は各 Azure AD テナントを使用して、すべての Azure リソースに対するフル アクセスを取得できます。 Azure AD 組織にサインアップしたユーザーがグローバル管理者になります。 会社に複数のグローバル管理者がいてもかまいません。 グローバル管理者は、すべてのユーザーと他のすべての管理者のパスワードをリセットできます。

### <a name="global-reader"></a>[グローバル閲覧者](#global-reader-permissions)

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

### <a name="groups-administrator"></a>[グループ管理者](#groups-administrator-permissions)

このロールのユーザーは、グループとその設定 (命名ポリシーや有効期限ポリシーなど) を作成/管理できます。 このロールにユーザーを割り当てることにより、Outlook だけでなく、Teams、SharePoint、Yammer などのさまざまなワークロードにわたって、組織内のすべてのグループを管理する機能がユーザーに付与されるということを理解しておくことが重要です。 また、そのユーザーは、Microsoft 管理センター、Azure portal などのさまざまな管理者ポータル全般のさまざまなグループ設定を管理できるほか、Teams や SharePoint 管理センターなどのワークロード固有の場合についても管理できます。

### <a name="guest-inviter"></a>[ゲスト招待元](#guest-inviter-permissions)

このロールが割り当てられたユーザーは、 **[メンバーは招待ができる]** ユーザー設定が [いいえ] に設定されている場合に、Azure Active Directory B2B ゲスト ユーザーの招待を管理できます。 B2B コラボレーションの詳細については、「[Azure AD B2B コラボレーションとは](../external-identities/what-is-b2b.md)」をご覧ください。 その他の権限は含まれません。

### <a name="helpdesk-administrator"></a>[ヘルプデスク管理者](#helpdesk-administrator-permissions)

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

### <a name="hybrid-identity-administrator"></a>[ハイブリッド ID の管理者](#hybrid-identity-administrator-permissions)

このロールのユーザーは、クラウド プロビジョニングを使用して AD から Azure AD へのプロビジョニング構成設定を作成、管理、デプロイしたり、フェデレーション設定を管理したりできます。 ユーザーはこのロールを使用して、ログのトラブルシューティングと監視を行うこともできます。  

### <a name="insights-administrator"></a>[Insights 管理者](#insights-administrator-permissions)
このロールのユーザーは、[M365 Insights アプリケーション](https://go.microsoft.com/fwlink/?linkid=2129521)の管理機能の完全なセットにアクセスできます。 このロールでは、ディレクトリ情報の読み取り、サービスの正常性の監視、サポート チケットの提出、Insights 管理者設定の側面へのアクセスを行うことができます。

### <a name="insights-business-leader"></a>[Insights ビジネス リーダー](#insights-business-leader-permissions)
このロールのユーザーは、[M365 Insights アプリケーション](https://go.microsoft.com/fwlink/?linkid=2129521)を使用して、一連のダッシュボードと分析情報にアクセスできます。 これには、すべてのダッシュボード、表示される分析情報、およびデータ探索機能へのフル アクセスが含まれます。 このロールのユーザーには、製品の構成設定へのアクセス権がありません (これは Insights 管理者ロールの責任範囲です)。

### <a name="intune-administrator"></a>[Intune 管理者](#intune-service-administrator-permissions)

このロールが割り当てられたユーザーは、Microsoft Intune Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。 さらに、このロールはポリシーを関連付けるためにユーザーとデバイスを管理することができ、グループを作成および管理することもできます。 詳細については、「[Microsoft Intune でのロール ベースの管理制御 (RBAC)](/intune/role-based-access-control)」を参照してください。

このロールでは、すべてのセキュリティ グループを作成および管理できます。 ただし、Intune 管理者には Office グループに対する管理者権限がありません。 つまり、管理者は、組織内のすべての Office グループの所有者およびメンバーシップを更新することはできません。 ただし、自分で作成した Office グループを管理することはできます。これは、エンド ユーザーの特権の一部として提供されます。 そのため、自分が作成したすべての Office グループ (セキュリティ グループではありません) は、自分の 250 のクォータに対してカウントする必要があります。

> [!NOTE]
> Microsoft Graph API と Azure AD PowerShell では、このロールは "Intune サービス管理者" として識別されます。 [Azure portal](https://portal.azure.com) では、"Intune 管理者" になります。

### <a name="kaizala-administrator"></a>[Kaizala 管理者](#kaizala-administrator-permissions)

このロールが割り当てられたユーザーは、Microsoft Kaizala 内で設定を管理するグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、サポート チケットを管理し、サービス正常性を監視できます。 さらに、このユーザーは、組織のメンバーによる Kaizala の導入と使用法に関連したレポート、および Kaizala アクションを使用して生成されるビジネス レポートにもアクセスできます。

### <a name="license-administrator"></a>[ライセンス管理者](#license-administrator-permissions)

このロールのユーザーは、ユーザーに対するライセンス割り当ての追加、削除、更新、グループに対する (グループベースのライセンスを使用した) ライセンス割り当ての追加、削除、更新に加え、ユーザーに対する利用場所の管理を行うことができます。 このロールでは、サブスクリプションの購入と管理、グループの作成と管理を行う権限は与えられません。また、利用場所を超える範囲でのユーザーの作成と管理を行う権限も与えられません。 このロールには、サポート チケットの表示、作成、管理のためのアクセス権がありません。

### <a name="message-center-privacy-reader"></a>[メッセージ センターのプライバシー閲覧者](#message-center-privacy-reader-permissions)

このロールのユーザーは、データのプライバシー メッセージを含め、メッセージ センター内のすべての通知を監視できます。 メッセージ センターのプライバシー閲覧者は、データのプライバシーに関連したものも含めてメール通知を受け取り、メッセージ センターの設定を使用して登録を解除することができます。 データのプライバシー メッセージを読み取ることができるのは、グローバル管理者とメッセージ センターのプライバシー閲覧者のみになります。 さらに、このロールには、グループ、ドメイン、サブスクリプションを表示する権限が含まれています。 このロールには、サービス要求を表示、作成、または管理するアクセス許可はありません。

### <a name="message-center-reader"></a>[メッセージ センター閲覧者](#message-center-reader-permissions)

このロールのユーザーは、自分の組織の Exchange、Intune、Microsoft Teams などのサービス構成に対する[メッセージ センター](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093)の通知や、正常性に関して注意を促す更新情報を監視できます。 メッセージ センター閲覧者は、投稿の毎週のメール ダイジェストを受け取り、Microsoft 365 でメッセージ センターの投稿を共有できます。 Azure AD では、このロールに割り当てられているユーザーはユーザーやグループなどの読み取り専用アクセスのみを持ちます。 このロールには、サポート チケットの表示、作成、管理のためのアクセス権がありません。

### <a name="modern-commerce-user"></a>[Modern Commerce ユーザー](#modern-commerce-user-permissions)

使用しないでください。 このロールは、Commerce から自動的に割り当てられます。他の用途に使用するためのものではなく、他の用途ではサポートされていません。 詳細は以下を参照してください。

Modern Commerce ユーザー ロールは、特定のユーザーに Microsoft 365 管理センターにアクセスして、**ホーム**、**課金**、**サポート** の左側のナビゲーション エントリを表示するためのアクセス許可を付与します。 これらの領域で利用可能なコンテンツは、ユーザーが自分自身または組織のために購入した製品を管理するために、ユーザーに割り当てられた[コマース固有のロール](../../cost-management-billing/manage/understand-mca-roles.md)によって制御されます。 これには、請求書の支払いや、課金アカウントや課金プロファイルへのアクセスなどのタスクが含まれる場合があります。 

Modern Commerce ユーザー ロールを持つユーザーは、通常、他の Microsoft 購入システムの管理権限を持っていますが、管理センターへのアクセスに使用されるグローバル管理者ロールまたは課金管理者ロールは持っていません。 

**Modern Commerce ユーザー ロールが割り当てられるタイミング**

* **Microsoft 365 管理センターでのセルフサービス購入** – セルフサービス購入により、ユーザーは新製品を自分で購入またはサインアップして、新製品を試す機会が得られます。 これらの製品は管理センターで管理されています。 セルフサービス購入を行うユーザーには、コマース システムでのロールと Modern Commerce ユーザー ロールが割り当てられ、これにより、管理センターで購入を管理できるようになります。 管理者は [PowerShell](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell) を使用して、(Power BI、Power Apps、Power Automate に対する) セルフサービス購入をブロックできます。 詳細については、「[セルフサービスによる購入に関するよくあるご質問](/microsoft-365/commerce/subscriptions/self-service-purchase-faq)」を参照してください。  
* **Microsoft コマーシャル マーケットプレースからの購入** - セルフサービス購入と同様に、ユーザーが Microsoft AppSource または Azure Marketplace から製品またはサービスを購入するときに、グローバル管理者ロールまたは課金管理者ロールを持っていない場合は、Modern Commerce ユーザー ロールが割り当てられます。 場合によっては、ユーザーがこれらの購入をブロックされる場合があります。 詳細については、[Microsoft コマーシャル マーケットプレース](../../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase)に関するページを参照してください。
* **Microsoft からの提案** – 提案は、組織が Microsoft の製品やサービスを購入するための Microsoft からの正式なオファーです。 提案に同意する人が Azure AD でグローバル管理者ロールまたは課金管理者ロールを持っていない場合、提案を完了するためのコマース固有のロールと、管理センターにアクセスするための Modern Commerce ユーザー ロールの両方が割り当てられます。 管理センターにアクセスするときは、そのコマース固有のロールによって承認された機能のみを使用できます。
* **コマース固有のロール** – 一部のユーザーには、コマース固有のロールが割り当てられます。 ユーザーがグローバル管理者または課金管理者でない場合は、管理センターにアクセスできるように、Modern Commerce ユーザー ロールを取得します。  

ユーザーから Modern Commerce ユーザー ロールが割り当て解除されると、Microsoft 365 管理センターにアクセスできなくなります。 ユーザーが自身または組織のために製品を管理している場合は、それらを管理できなくなります。 これには、ライセンスの割り当て、支払い方法の変更、請求書の支払い、サブスクリプションを管理するためのその他のタスクが含まれます。

### <a name="network-administrator"></a>[ネットワーク管理者](#network-administrator-permissions)

このロールのユーザーは、ユーザーの場所からのネットワーク テレメトリに基づいて、Microsoft によるネットワーク境界アーキテクチャに関する推奨事項を確認できます。 Microsoft 365 のネットワーク パフォーマンスは、通常はユーザーの場所に固有である、企業の顧客の慎重なネットワーク境界アーキテクチャに依存します。 このロールを使用では、検出されたユーザーの場所と、それらの場所に対するネットワーク パラメーターの構成を編集して、テレメトリの測定と設計に関する推奨事項の改善できます
### <a name="office-apps-administrator"></a>[Office アプリ管理者](#office-apps-administrator-permissions)

このロールのユーザーは、Microsoft 365 アプリのクラウド設定を管理できます。 これには、クラウド ポリシーの管理、セルフサービス ダウンロードの管理、Office アプリ関連レポートを表示する機能が含まれます。 このロールではさらに、サポート チケットの管理とメインの管理センター内でのサービスの正常性の監視を行うこともできます。 このロールに割り当てられたユーザーは、Office アプリの新機能の通知も管理できます。 

### <a name="partner-tier1-support"></a>[パートナー レベル 1 のサポート](#partner-tier1-support-permissions)

使用しないでください。 このロールは非推奨となっており、将来的に Azure AD から削除されます。 このロールは少数の Microsoft 再販パートナーを対象としており、一般的な使用を目的としたものではありません。

### <a name="partner-tier2-support"></a>[パートナー レベル 2 のサポート](#partner-tier2-support-permissions)

使用しないでください。 このロールは非推奨となっており、将来的に Azure AD から削除されます。 このロールは少数の Microsoft 再販パートナーを対象としており、一般的な使用を目的としたものではありません。

### <a name="password-administrator"></a>[パスワード管理者](#password-administrator-permissions)

このロールのユーザーは、制限付きでパスワードを管理することができます。 このロールでは、サービス要求を管理したり、サービスの正常性を監視したりすることはできません。 パスワード管理者がユーザーのパスワードをリセットできるかどうかは、ユーザーが割り当てられているロールに依存します。 パスワード管理者がパスワードをリセットできるロールの一覧については、「[パスワード リセットのアクセス許可](#password-reset-permissions)」を参照してください。

### <a name="power-bi-administrator"></a>[Power BI 管理者](#power-bi-service-administrator-permissions)

このロールが割り当てられたユーザーは、Microsoft Power BI 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、サポート チケットを管理し、サービス正常性を監視できます。 詳細については、「[Power BI 管理者の役割について](/power-bi/service-admin-role)」を参照してください。

> [!NOTE]
> Microsoft Graph API と Azure AD PowerShell では、このロールは "Power BI サービス管理者" として識別されます。 [Azure portal](https://portal.azure.com) では、"Power BI 管理者" になります。

### <a name="power-platform-administrator"></a>[Power Platform 管理者](#power-platform-administrator-permissions)

このロールのユーザーは、環境、PowerApps、フロー、データ損失防止ポリシーのすべての側面を作成および管理できます。 さらに、このロールを持つユーザーは、サポート チケットを管理し、サービス正常性を監視できます。

### <a name="printer-administrator"></a>[プリンター管理者](#printer-administrator-permissions)

このロールのユーザーは、プリンターを登録して、ユニバーサル印刷コネクタの設定など、Microsoft ユニバーサル印刷ソリューションのすべてのプリンター構成のすべての側面を管理できます。 すべての委任された印刷アクセス許可要求に同意することができます。 プリンター管理者は、印刷レポートにアクセスすることもできます。

### <a name="printer-technician"></a>[プリンター技術者](#printer-technician-permissions)

このロールのユーザーは、プリンターを登録して、Microsoft ユニバーサル印刷ソリューションでプリンターの状態を管理できます。 すべてのコネクタ情報を読み取ることもできます。 プリンター技術者が行うことができない主要なタスクは、プリンターに対するユーザー アクセス許可の設定と、プリンターの共有です。

### <a name="privileged-authentication-administrator"></a>[特権認証管理者](#privileged-authentication-administrator-permissions)

このロールのユーザーは、グローバル管理者を含むすべてのユーザーに対して、パスワード以外の資格情報の設定やリセットを実行でき、すべてのユーザーのパスワードを更新できます。 特権認証管理者は、パスワード以外の既存の資格情報 (MFA や FIDO など) に対する再登録をユーザーに強制することができます。また、'このデバイスに MFA を記憶する' 機能を取り消して、すべてのユーザーが次回サインインするときに MFA の入力を求めることができます。

### <a name="privileged-role-administrator"></a>[特権ロール管理者](#privileged-role-administrator-permissions)

このロールが割り当てられたユーザーは、Azure Active Directory と Azure AD Privileged Identity Management 内でロールの割り当てを管理できます。 Azure AD ロールに割り当てることができるグループの作成と管理が可能です。 さらに、このロールは、Privileged Identity Management と管理単位のすべての側面を管理できます。

> [!IMPORTANT]
> このロールは、全体管理者ロールを含むすべての Azure AD ロールの割り当てを管理する権限を付与します。 このロールには、Azure AD でユーザーの作成や更新などの特権付きの機能が含まれていません。 ただし、このロールが割り当てられたユーザーは、追加のロールを割り当てることで、自分または他のユーザーの追加の特権を付与できます。

### <a name="reports-reader"></a>[レポート閲覧者](#reports-reader-permissions)

このロールが割り当てられたユーザーは、Microsoft 365 管理センターで使用状況のレポート データとレポート ダッシュボードを表示できます。また、Power BI で導入コンテキスト パックを表示できます。 さらに、Azure AD のサインイン レポートとアクティビティ、および Microsoft Graph レポート API から返されるデータにもアクセスできます。 レポート閲覧者ロールが割り当てられたユーザーは、関連する使用状況と導入メトリックにのみアクセスできます。 製品固有の管理センター (Exchange など) の設定を構成したり、アクセスしたりする管理者アクセス許可はありません。 このロールには、サポート チケットの表示、作成、管理のためのアクセス権がありません。

### <a name="search-administrator"></a>[Search 管理者](#search-administrator-permissions)

このロールのユーザーには、Microsoft 365 管理センター内のすべての Microsoft Search 管理機能へのフル アクセスがあります。 さらに、これらのユーザーは、メッセージ センターを表示し、サービス正常性を監視し、サービス要求を作成することができます。

### <a name="search-editor"></a>[Search エディター](#search-editor-permissions)

このロールのユーザーは、ブックマーク、Q&A、および場所を含め、Microsoft 365 管理センターで Microsoft Search のコンテンツを作成、管理、および削除できます。

### <a name="security-administrator"></a>[セキュリティ管理者](#security-administrator-permissions)

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

### <a name="security-operator"></a>[セキュリティ オペレーター](#security-operator-permissions)

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

### <a name="security-reader"></a>[Security Reader](#security-reader-permissions)

このロールのユーザーには、Microsoft 365 セキュリティ センター、Azure Active Directory、Identity Protection、Privileged Identity Management におけるすべての情報を含むセキュリティ関連機能への読み取り専用グローバル アクセス権に加えて、Azure Active Directory サインイン レポートと監査ログ、および Office 365 セキュリティ/コンプライアンス センターで閲覧する権限があります。 Office 365 のアクセス許可の詳細については、「[セキュリティ/コンプライアンス センターでのアクセス許可](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)」をご覧ください。

場所 | できること
--- | ---
[Microsoft 365 セキュリティ センター](https://protection.office.com) | Microsoft 365 サービス全体のセキュリティ関連ポリシーの表示<br>セキュリティの脅威とアラートの表示<br>レポートの表示
Identity Protection Center | 各セキュリティ機能の全セキュリティ レポートと設定情報の閲覧<br><ul><li>スパム対策<li>暗号化<li>データ損失の防止<li>マルウェア対策<li>高度な脅威保護<li>フィッシング詐欺対策<li>メールフロー ルール
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Azure AD Privileged Identity Management に表示されるすべての情報への読み取り専用アクセス権を持ちます。Azure AD ロール割り当てに関するポリシーとレポート、およびセキュリティ レビュー。<br>Azure AD Privileged Identity Management へのサインアップおよびそれに対する変更を行うことは **できません**。 このロールのユーザーは、追加のロール (グローバル管理者や特権ロール管理者など) の資格がある場合、Privileged Identity Management ポータルまたは PowerShell からそれらのロールを有効化することができます。
[Office 365 セキュリティ/コンプライアンス センター](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | セキュリティ ポリシーの表示<br>セキュリティの脅威の表示および調査<br>レポートの表示
Windows Defender ATP および EDR | アラートを表示して調査します。 Windows Defender ATP でロールベースのアクセス制御を有効にすると、Azure AD セキュリティ閲覧者ロールなどの読み取り専用アクセス許可を持つユーザーは、Windows Defender ATP ロールが割り当てられるまで、アクセス権を失います。
[Intune](/intune/role-based-access-control) | ユーザー、デバイス、登録、構成、アプリケーション情報の表示。 Intune に変更を加えることはできません。
[Cloud App Security](/cloud-app-security/manage-admins) | 読み取り専用アクセス許可があり、アラートを管理できる
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | 推奨事項とアラート、セキュリティ ポリシー、セキュリティの状態を表示することはできますが、変更することはできません
[Microsoft 365 サービス正常性](/office365/enterprise/view-service-health) | Microsoft 365 サービスの正常性の表示

### <a name="service-support-administrator"></a>[サービス サポート管理者](#service-support-administrator-permissions)

このロールが割り当てられたユーザーは、Azure サービスと Microsoft 365 サービスについて Microsoft へのサポート要求を開くことができます。また、[Azure portal](https://portal.azure.com) と [Microsoft 365 管理センター](https://admin.microsoft.com)で、サービス ダッシュボードとメッセージ センターを表示できます。 詳細については、[管理者の役割](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)に関する記事をご覧ください。

> [!NOTE]
> 以前は、このロールは、[Azure portal](https://portal.azure.com) および [Microsoft 365 管理センター](https://admin.microsoft.com)の「サービス管理者」と呼ばれていました。 Microsoft Graph API、Azure AD Graph API、Azure AD PowerShell の既存の名前と整合性を図り、このロールの名前を「サービス サポート管理者」に変更しました。

### <a name="sharepoint-administrator"></a>[SharePoint 管理者](#sharepoint-service-administrator-permissions)

このロールが割り当てられたユーザーは、Microsoft SharePoint Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、すべての Microsoft 365 グループを作成および管理し、サポート チケットを管理し、サービス正常性を監視できます。 詳細については、[管理者の役割](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)に関する記事をご覧ください。

> [!NOTE]
> Microsoft Graph API と Azure AD PowerShell では、このロールは "SharePoint サービス管理者" として識別されます。 [Azure portal](https://portal.azure.com) では、"SharePoint 管理者" になります。

> [!NOTE]
> このロールでは、Microsoft Intune 用の Microsoft Graph API に対するスコープ付きのアクセス許可も付与されます。これにより、SharePoint リソースと OneDrive リソースに関連するポリシーの管理と構成を行うことができます。

### <a name="skype-for-business--lync-administrator"></a>[Skype for Business/Lync 管理者](#lync-service-administrator-permissions)

このロールが割り当てられたユーザーは、Microsoft Skype for Business 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、Azure Active Directory で Skype 固有のユーザー属性を管理します。 さらに、このロールはサポート チケットを管理し、サービス正常性を監視して、Teams と Skype for Business の管理センターにアクセスすることができます。 アカウントには、Teams のライセンスが付与されている必要もあります。そうでないと、Teams の PowerShell コマンドレットを実行できません。 詳細については、[Skype for Business の管理者ロール](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5)に関するページを参照してください。Teams のライセンス情報については、[Skype for Business と Microsoft Teams アドオンのライセンス](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)に関するページを参照してください

> [!NOTE]
> Microsoft Graph API と Azure AD PowerShell では、このロールは "Lync サービス管理者" として識別されます。 [Azure portal](https://portal.azure.com/) では、"Skype for Business 管理者" になります。

### <a name="teams-communications-administrator"></a>[Teams 通信管理者](#teams-communications-administrator-permissions)

このロールのユーザーは、音声とテレフォニーに関連する Microsoft Teams のワークロードの各側面を管理できます。 これには、電話番号の割り当て、音声と会議のポリシー、および通話分析ツールセットへのフル アクセスのための管理ツールが含まれます。

### <a name="teams-communications-support-engineer"></a>[Teams 通信サポート エンジニア](#teams-communications-support-engineer-permissions)

このロールのユーザーは、Microsoft Teams と Skype for Business の管理センターでユーザー通話のトラブルシューティング ツールを使用して、Microsoft Teams と Skype for Business での通信に関する問題をトラブルシューティングできます。 このロールのユーザーは、関係するすべての参加者の完全な通話記録情報を表示できます。 このロールには、サポート チケットの表示、作成、管理のためのアクセス権がありません。

### <a name="teams-communications-support-specialist"></a>[Teams 通信サポート スペシャリスト](#teams-communications-support-specialist-permissions)

このロールのユーザーは、Microsoft Teams と Skype for Business の管理センターでユーザー通話のトラブルシューティング ツールを使用して、Microsoft Teams と Skype for Business での通信に関する問題をトラブルシューティングできます。 このロールのユーザーが表示できるのは、調査した特定ユーザーの通話における詳細のみです。 このロールには、サポート チケットの表示、作成、管理のためのアクセス権がありません。

### <a name="teams-devices-administrator"></a>[Teams デバイス管理者](#teams-devices-administrator-permissions)

このロールを持つユーザーは、Teams 管理センターから [Teams 認定デバイス](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices)を管理できます。 このロールでは、すべてのデバイスを一目で見ることができ、デバイスの検索とフィルター処理を行えます。 ユーザーは、ログイン アカウント、デバイスの製造元やモデルなど、各デバイスの詳細を確認できます。 ユーザーは、デバイスの設定を変更したり、ソフトウェアのバージョンを更新したりできます。 このロールでは、Teams アクティビティとデバイスの通話品質を調べるアクセス許可は付与されません。 

### <a name="teams-service-administrator"></a>[Teams サービス管理者](#teams-service-administrator-permissions)

このロールのユーザーは、Microsoft Teams および Skype for Business の管理センターと、対応する PowerShell モジュールを使用して、Microsoft Teams のワークロードの全側面を管理できます。 これにはその他の領域の、テレフォニー、メッセージング、会議、およびチーム自体に関連するすべての管理ツールが含まれます。 このロールはさらに、すべての Microsoft 365 グループの作成および管理、サポート チケットの管理、サービスの正常性の監視を行うこともできます。

### <a name="usage-summary-reports-reader"></a>[使用状況の概要のレポート閲覧者](#usage-summary-reports-reader-permissions)

このロールのユーザーは、使用状況と生産性スコアについて、Microsoft 365 管理センターでテナント レベルの集計データと関連する分析情報にアクセスできますが、ユーザー レベルの詳細や分析情報にはアクセスできません。 2 つのレポートについて、Microsoft 365 管理センターでは、テナント レベルの集計データとユーザー レベルの詳細が区別されます。 このロールは、顧客と法務チームの両方によって求められた個々のユーザーの識別可能データに対して追加の保護レイヤーを提供します。 

### <a name="user-administrator"></a>[ユーザー管理者](#user-administrator-permissions)

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

## <a name="role-permissions"></a>ロールのアクセス許可

以降の表は、各ロールに割り当てられている Azure Active Directory の具体的なアクセス許可の説明です。 一部のロールは、Azure Active Directory 以外の Microsoft サービスにおけるその他のアクセス許可を持っている場合があります。

### <a name="application-administrator-permissions"></a>アプリケーション管理者のアクセス許可

アプリ登録とエンタープライズ アプリのすべての側面を作成して管理できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/Application/appProxyAuthentication/update | Azure Active Directory でのサービス プリンシパルのアプリ プロキシ認証プロパティの更新。 |
| microsoft.directory/Application/appProxyUrlSettings/update | Azure Active Directory でのアプリケーション プロキシの内部 URL と外部 URL の更新。 |
| microsoft.directory/applications/applicationProxy/read | アプリケーション プロキシのすべてのプロパティの読み取り。 |
| microsoft.directory/applications/applicationProxy/update | アプリケーション プロキシのすべてのプロパティの更新。 |
| microsoft.directory/applications/audience/update | Azure Active Directory での applications.audience property プロパティの更新。 |
| microsoft.directory/applications/authentication/update | Azure Active Directory での applications.authentication プロパティの更新。 |
| microsoft.directory/applications/basic/update | Azure Active Directory での applications の基本プロパティの更新。 |
| microsoft.directory/applications/create | Azure Active Directory での applications の作成。 |
| microsoft.directory/applications/credentials/update | Azure Active Directory での applications.credentials プロパティの更新。 |
| microsoft.directory/applications/delete | Azure Active Directory での applications の削除。 |
| microsoft.directory/applications/owners/update | Azure Active Directory での applications.owners プロパティの更新。 |
| microsoft.directory/applications/permissions/update | Azure Active Directory での applications.permissions プロパティの更新。 |
| microsoft.directory/applications/policies/update | Azure Active Directory での applications.policies プロパティの更新。 |
| microsoft.directory/appRoleAssignments/create | Azure Active Directory での appRoleAssignments の作成。 |
| microsoft.directory/appRoleAssignments/read | Azure Active Directory での appRoleAssignments の読み取り。 |
| microsoft.directory/appRoleAssignments/update | Azure Active Directory での appRoleAssignments の更新。 |
| microsoft.directory/appRoleAssignments/delete | Azure Active Directory での appRoleAssignments の削除。 |
| microsoft.directory/auditLogs/allProperties/read | Azure Active Directory での auditLogs 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.directory/connectorGroups/allProperties/read | Azure Active Directory でのアプリケーション プロキシ コネクタ グループ プロパティの読み取り。 |
| microsoft.directory/connectorGroups/allProperties/update | Azure Active Directory でのアプリケーション プロキシ コネクタ グループ プロパティの更新。 |
| microsoft.directory/connectorGroups/create | Azure Active Directory でのアプリケーション プロキシ コネクタ グループの作成。 |
| microsoft.directory/connectorGroups/delete | Azure Active Directory でのアプリケーション プロキシ コネクタ グループの削除。 |
| microsoft.directory/connectors/allProperties/read | Azure Active Directory でのアプリケーション プロキシ コネクタのすべてのプロパティの読み取り。 |
| microsoft.directory/connectors/create | Azure Active Directory でのアプリケーション プロキシ コネクタの作成。 |
| microsoft.directory/policies/applicationConfiguration/basic/read | Azure Active Directory での policies.applicationConfiguration プロパティの読み取り。 |
| microsoft.directory/policies/applicationConfiguration/basic/update | Azure Active Directory での policies.applicationConfiguration プロパティの更新。 |
| microsoft.directory/policies/applicationConfiguration/create | Azure Active Directory での policies の作成。 |
| microsoft.directory/policies/applicationConfiguration/delete | Azure Active Directory での policies の削除。 |
| microsoft.directory/policies/applicationConfiguration/owners/read | Azure Active Directory での policies.applicationConfiguration プロパティの読み取り。 |
| microsoft.directory/policies/applicationConfiguration/owners/update | Azure Active Directory での policies.applicationConfiguration プロパティの更新。 |
| microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | Azure Active Directory での policies.applicationConfiguration プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの更新。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Azure Active Directory での servicePrincipals.appRoleAssignments プロパティの更新。 |
| microsoft.directory/servicePrincipals/audience/update | Azure Active Directory での servicePrincipals.audience プロパティの更新。 |
| microsoft.directory/servicePrincipals/authentication/update | Azure Active Directory での servicePrincipals.authentication プロパティの更新。 |
| microsoft.directory/servicePrincipals/basic/update | Azure Active Directory での servicePrincipals の基本プロパティの更新。 |
| microsoft.directory/servicePrincipals/create | Azure Active Directory での servicePrincipals の作成。 |
| microsoft.directory/servicePrincipals/credentials/update | Azure Active Directory での servicePrincipals.credentials プロパティの更新。 |
| microsoft.directory/servicePrincipals/delete | Azure Active Directory での servicePrincipals の削除。 |
| microsoft.directory/servicePrincipals/owners/update | Azure Active Directory での servicePrincipals.owners プロパティの更新。 |
| microsoft.directory/servicePrincipals/permissions/update | Azure Active Directory での servicePrincipals.permissions プロパティの更新。 |
| microsoft.directory/servicePrincipals/policies/update | Azure Active Directory での servicePrincipals.policies プロパティの更新。 |
| microsoft.directory/signInReports/allProperties/read | Azure Active Directory での signInReports 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/standard/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |

### <a name="application-developer-permissions"></a>アプリケーション開発者のアクセス許可

[ユーザーはアプリケーションを登録できる] の設定とは無関係にアプリケーション登録を作成できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/applications/createAsOwner | Azure Active Directory での applications の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.directory/appRoleAssignments/createAsOwner | Azure Active Directory での appRoleAssignments の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.directory/oAuth2PermissionGrants/createAsOwner | Azure Active Directory での oAuth2PermissionGrants の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.directory/servicePrincipals/createAsOwner | Azure Active Directory での servicePrincipals の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |

### <a name="authentication-administrator-permissions"></a>認証管理者のアクセス許可

管理者以外のユーザーの認証方法の情報を表示、設定、リセットできます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/users/invalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.directory/users/strongAuthentication/update | MFA 資格情報などの強力な認証プロパティを更新します。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.directory/users/password/update | Microsoft 365 組織内のすべてのユーザーのパスワードの更新。 詳細については、オンライン ドキュメントを参照してください。 |

### <a name="attack-payload-author-permissions"></a>攻撃のペイロードの作成者のアクセス許可

管理者が後でデプロイできる攻撃のペイロードを作成できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | 攻撃シミュレーターでの攻撃ペイロードの作成と管理。 |
| microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | 攻撃のシミュレーション、応答、関連付けられているトレーニングのレポートの読み取り。 |

### <a name="attack-simulation-administrator-permissions"></a>攻撃のシミュレーションの管理者のアクセス許可

攻撃のシミュレーション キャンペーンのすべての側面を作成および管理できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | 攻撃シミュレーターでの攻撃ペイロードの作成と管理。 |
| microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | 攻撃のシミュレーション、応答、関連付けられているトレーニングのレポートの読み取り。 |
| microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | 攻撃シミュレーターでの攻撃のシミュレーション テンプレートの作成および管理。 |

### <a name="azure-devops-administrator-permissions"></a>Azure DevOps 管理者のアクセス許可

Azure DevOps 組織のポリシーと設定を管理できます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記の[ロールの説明](#azure-devops-administrator)を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.devOps/allEntities/allTasks | Azure DevOps の構成の読み取りと構成。 |

### <a name="azure-information-protection-administrator-permissions"></a>Azure Information Protection 管理者のアクセス許可

Azure Information Protection サービスのすべての側面を管理できます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記の[ロールの説明](#)を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection の全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/standard/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |

### <a name="b2c-ief-keyset-administrator-permissions"></a>B2C IEF キーセット管理者のアクセス許可

Identity Experience Framework でのフェデレーションのシークレットと暗号化の管理。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Azure Active Directory B2C でのキー セットの読み取りと構成。 |

### <a name="b2c-ief-policy-administrator-permissions"></a>B2C IEF ポリシー管理者のアクセス許可

Identity Experience Framework での信頼フレームワーク ポリシーの作成と管理。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Azure Active Directory B2C でのカスタム ポリシーの読み取りと構成。 |

### <a name="billing-administrator-permissions"></a>課金管理者のアクセス許可

支払情報の更新など、よく利用する課金関連タスクを実行できます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/organization/basic/update | Azure Active Directory での organization の基本プロパティの更新。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.commerce.billing/allEntities/allTasks | 課金の全側面の管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="cloud-application-administrator-permissions"></a>クラウド アプリケーション管理者のアクセス許可

アプリ登録とエンタープライズ アプリのすべての側面 (アプリ プロキシを除く) を作成して管理できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/applications/audience/update | Azure Active Directory での applications.audience property プロパティの更新。 |
| microsoft.directory/applications/authentication/update | Azure Active Directory での applications.authentication プロパティの更新。 |
| microsoft.directory/applications/basic/update | Azure Active Directory での applications の基本プロパティの更新。 |
| microsoft.directory/applications/create | Azure Active Directory での applications の作成。 |
| microsoft.directory/applications/credentials/update | Azure Active Directory での applications.credentials プロパティの更新。 |
| microsoft.directory/applications/delete | Azure Active Directory での applications の削除。 |
| microsoft.directory/applications/owners/update | Azure Active Directory での applications.owners プロパティの更新。 |
| microsoft.directory/applications/permissions/update | Azure Active Directory での applications.permissions プロパティの更新。 |
| microsoft.directory/applications/policies/update | Azure Active Directory での applications.policies プロパティの更新。 |
| microsoft.directory/appRoleAssignments/create | Azure Active Directory での appRoleAssignments の作成。 |
| microsoft.directory/appRoleAssignments/update | Azure Active Directory での appRoleAssignments の更新。 |
| microsoft.directory/appRoleAssignments/delete | Azure Active Directory での appRoleAssignments の削除。 |
| microsoft.directory/auditLogs/allProperties/read | Azure Active Directory での auditLogs 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.directory/policies/applicationConfiguration/create | Azure Active Directory での policies の作成。 |
| microsoft.directory/policies/applicationConfiguration/basic/read | Azure Active Directory での policies.applicationConfiguration プロパティの読み取り。 |
| microsoft.directory/policies/applicationConfiguration/basic/update | Azure Active Directory での policies.applicationConfiguration プロパティの更新。 |
| microsoft.directory/policies/applicationConfiguration/delete | Azure Active Directory での policies の削除。 |
| microsoft.directory/policies/applicationConfiguration/owners/read | Azure Active Directory での policies.applicationConfiguration プロパティの読み取り。 |
| microsoft.directory/policies/applicationConfiguration/owners/update | Azure Active Directory での policies.applicationConfiguration プロパティの更新。 |
| microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | Azure Active Directory での policies.applicationConfiguration プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの更新。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Azure Active Directory での servicePrincipals.appRoleAssignments プロパティの更新。 |
| microsoft.directory/servicePrincipals/audience/update | Azure Active Directory での servicePrincipals.audience プロパティの更新。 |
| microsoft.directory/servicePrincipals/authentication/update | Azure Active Directory での servicePrincipals.authentication プロパティの更新。 |
| microsoft.directory/servicePrincipals/basic/update | Azure Active Directory での servicePrincipals の基本プロパティの更新。 |
| microsoft.directory/servicePrincipals/create | Azure Active Directory での servicePrincipals の作成。 |
| microsoft.directory/servicePrincipals/credentials/update | Azure Active Directory での servicePrincipals.credentials プロパティの更新。 |
| microsoft.directory/servicePrincipals/delete | Azure Active Directory での servicePrincipals の削除。 |
| microsoft.directory/servicePrincipals/owners/update | Azure Active Directory での servicePrincipals.owners プロパティの更新。 |
| microsoft.directory/servicePrincipals/permissions/update | Azure Active Directory での servicePrincipals.permissions プロパティの更新。 |
| microsoft.directory/servicePrincipals/policies/update | Azure Active Directory での servicePrincipals.policies プロパティの更新。 |
| microsoft.directory/signInReports/allProperties/read | Azure Active Directory での signInReports 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/standard/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |

### <a name="cloud-device-administrator-permissions"></a>クラウド デバイス管理者のアクセス許可

Azure AD でデバイスを管理するためのフル アクセス。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Azure Active Directory での auditLogs 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.directory/bitlockerKeys/key/read | Azure Active Directory での bitlocker キー オブジェクトおよびプロパティ (回復キーを含む) の読み取り。 |
| microsoft.directory/devices/delete | Azure Active Directory での devices の削除。 |
| microsoft.directory/devices/disable | Azure Active Directory での devices の無効化。 |
| microsoft.directory/devices/enable | Azure Active Directory での devices の有効化。 |
| microsoft.directory/devices/extensionAttributes/update | Azure Active Directory での devices.extensionAttributes プロパティのすべての値の更新。 |
| microsoft.directory/signInReports/allProperties/read | Azure Active Directory での signInReports 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |

### <a name="global-administrator-permissions"></a>グループ管理者のアクセス許可

Azure AD のすべての側面と、Azure AD の ID が使用される Microsoft サービスを管理できます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | microsoft.aad.cloudAppSecurity でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.directory/administrativeUnits/allProperties/allTasks | Azure Active Directory での administrativeUnits の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/applications/allProperties/allTasks | Azure Active Directory での applications の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/appRoleAssignments/allProperties/allTasks | Azure Active Directory での appRoleAssignments の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/auditLogs/allProperties/read | Azure Active Directory での auditLogs 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.directory/bitlockerKeys/key/read | Azure Active Directory での bitlocker キー オブジェクトおよびプロパティ (回復キーを含む) の読み取り。 |
| microsoft.directory/contacts/allProperties/allTasks | Azure Active Directory での contacts の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/contracts/allProperties/allTasks | Azure Active Directory での contracts の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/devices/allProperties/allTasks | Azure Active Directory での devices の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/directoryRoles/allProperties/allTasks | Azure Active Directory での directoryRoles の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/directoryRoleTemplates/allProperties/allTasks | Azure Active Directory での directoryRoleTemplates の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/domains/allProperties/allTasks | Azure Active Directory での domains の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/entitlementManagement/allProperties/allTasks | Azure AD エンタイトルメント管理でのリソースの作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/groups/allProperties/allTasks | Azure Active Directory での groups の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/groupsAssignableToRoles/allProperties/update | Azure Active Directory での isAssignableToRole プロパティが true に設定されているグループの更新。 |
| microsoft.directory/groupsAssignableToRoles/create | Azure Active Directory での isAssignableToRole プロパティが true に設定されているグループの作成。 |
| microsoft.directory/groupsAssignableToRoles/delete | Azure Active Directory での isAssignableToRole プロパティが true に設定されているグループの削除。 |
| microsoft.directory/groupSettings/allProperties/allTasks | Azure Active Directory での groupSettings の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/groupSettingTemplates/allProperties/allTasks | Azure Active Directory での groupSettingTemplates の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/loginTenantBranding/allProperties/allTasks | Azure Active Directory での loginTenantBranding の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Azure Active Directory での oAuth2PermissionGrants の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/organization/allProperties/allTasks | Azure Active Directory での organization の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/policies/allProperties/allTasks | Azure Active Directory での policies の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/roleAssignments/allProperties/allTasks | Azure Active Directory での roleAssignments の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/roleDefinitions/allProperties/allTasks | Azure Active Directory での roleDefinitions の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Azure Active Directory での scopedRoleMemberships の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/serviceAction/activateService | Azure Active Directory で Activateservice サービス アクションを実行可能 |
| microsoft.directory/serviceAction/disableDirectoryFeature | Azure Active Directory で Disabledirectoryfeature サービス アクションを実行可能 |
| microsoft.directory/serviceAction/enableDirectoryFeature | Azure Active Directory で Enabledirectoryfeature サービス アクションを実行可能 |
| microsoft.directory/serviceAction/getAvailableExtentionProperties | Azure Active Directory で Getavailableextentionproperties サービス アクションを実行可能 |
| microsoft.directory/servicePrincipals/allProperties/allTasks | Azure Active Directory での servicePrincipals の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/signInReports/allProperties/read | Azure Active Directory での signInReports 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.directory/subscribedSkus/allProperties/allTasks | Azure Active Directory での subscribedSkus の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/users/allProperties/allTasks | Azure Active Directory での users の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directorySync/allEntities/allTasks | Azure AD Connect でのすべてのアクションの実行。 |
| microsoft.aad.identityProtection/allEntities/allTasks | microsoft.aad.identityProtection でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | microsoft.aad.privilegedIdentityManagement でのすべてのリソースの読み取り。 |
| microsoft.azure.advancedThreatProtection/allEntities/read | microsoft.azure.advancedThreatProtection でのすべてのリソースの読み取り。 |
| microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection の全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.commerce.billing/allEntities/allTasks | 課金の全側面の管理。 |
| microsoft.intune/allEntities/allTasks | Intune の全側面の管理。 |
| microsoft.office365.complianceManager/allEntities/allTasks | Office 365 コンプライアンス マネージャーの全側面の管理 |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | デスクトップ Analytics のすべての側面を管理します。 |
| microsoft.office365.exchange/allEntities/allTasks | Exchange Online の全側面の管理。 |
| microsoft.office365.lockbox/allEntities/allTasks | Office 365 カスタマ― ロックボックスの全側面の管理 |
| microsoft.office365.messageCenter/messages/read | Microsoft.office365.messageCenter でメッセージを読み取ります。 |
| microsoft.office365.messageCenter/securityMessages/read | Microsoft.office365.messageCenter でセキュリティ メッセージを読み取ります。 |
| microsoft.office365.protectionCenter/allEntities/allTasks | Office 365 プロテクション センターの全側面の管理。 |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | microsoft.office365.securityComplianceCenter でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.sharepoint/allEntities/allTasks | microsoft.office365.sharepoint でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Skype for Business Online の全側面の管理。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.usageReports/allEntities/read | Office 365 の使用状況レポートの読み取り。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Dynamics 365 の全側面の管理。 |
| microsoft.powerApps.powerBI/allEntities/allTasks | Power BI の全側面の管理。 |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | microsoft.windows.defenderAdvancedThreatProtection でのすべてのリソースの読み取り。 |

### <a name="compliance-administrator-permissions"></a>コンプライアンス管理者のアクセス許可

Azure AD および Microsoft 365 のコンプライアンスの構成とレポートを読み取り、管理できます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.directory/entitlementManagement/allProperties/read | Azure AD エンタイトルメント管理でのすべてのプロパティの読み取り。 |
| microsoft.office365.complianceManager/allEntities/allTasks | Office 365 コンプライアンス マネージャーの全側面の管理 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |

### <a name="compliance-data-administrator-permissions"></a>コンプライアンス データ管理者のアクセス許可

コンプライアンス コンテンツを作成、管理します。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory.cloudAppSecurity/allEntities/allTasks | Microsoft Cloud App Security の読み取りと構成。 |
| microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection の全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.office365.complianceManager/allEntities/allTasks | Office 365 コンプライアンス マネージャーの全側面の管理 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |

### <a name="conditional-access-administrator-permissions"></a>条件付きアクセス管理者のアクセス許可

条件付きアクセスの機能を管理できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/policies/conditionalAccess/basic/read | Azure Active Directory での policies.conditionalAccess プロパティの読み取り。 |
| microsoft.directory/policies/conditionalAccess/basic/update | Azure Active Directory での policies.conditionalAccess プロパティの更新。 |
| microsoft.directory/policies/conditionalAccess/create | Azure Active Directory での policies の作成。 |
| microsoft.directory/policies/conditionalAccess/delete | Azure Active Directory での policies の削除。 |
| microsoft.directory/policies/conditionalAccess/owners/read | Azure Active Directory での policies.conditionalAccess プロパティの読み取り。 |
| microsoft.directory/policies/conditionalAccess/owners/update | Azure Active Directory での policies.conditionalAccess プロパティの更新。 |
| microsoft.directory/policies/conditionalAccess/policiesAppliedTo/read | Azure Active Directory での policies.conditionalAccess プロパティの読み取り。 |
| microsoft.directory/policies/conditionalAccess/tenantDefault/update | Azure Active Directory での policies.conditionalAccess プロパティの更新。 |

### <a name="crm-service-administrator-permissions"></a>CRM サービス管理者のアクセス許可

Dynamics 365 製品のすべての側面を管理できます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Dynamics 365 の全側面の管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="customer-lockbox-access-approver-permissions"></a>カスタマー ロックボックスのアクセス承認者のアクセス許可

Microsoft サポートがお客様の組織データにアクセスする要求を承認することができます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.lockbox/allEntities/allTasks | Office 365 カスタマ― ロックボックスの全側面の管理 |

### <a name="desktop-analytics-administrator-permissions"></a>デスクトップ Analytics 管理者のアクセス許可

デスクトップ Analytics および Office のカスタマイズとポリシーのサービスを管理できます。 デスクトップ Analytics の場合、これには、資産インベントリの表示、デプロイ プランの作成、デプロイと正常性の状態の表示に対する権限が含まれます。 Office のカスタマイズとポリシーのサービスの場合、このロールによりユーザーは Office のポリシーを管理することができます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | デスクトップ Analytics のすべての側面を管理します。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="device-administrators-permissions"></a>デバイス管理者のアクセス許可

このロールに割り当てられたユーザーは、Azure AD 参加済みデバイスのローカル管理者グループに追加されます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/groupSettings/basic/read | Azure Active Directory での groupSettings の基本プロパティの読み取り。 |
| microsoft.directory/groupSettingTemplates/basic/read | Azure Active Directory での groupSettingTemplates の基本プロパティの読み取り。 |

### <a name="directory-readers-permissions"></a>ディレクトリ閲覧者のアクセス許可
基本的なディレクトリ情報を読み取ることができます ユーザーではなく、アプリケーションへのアクセスを許可する場合。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/administrativeUnits/basic/read | Azure Active Directory での administrativeUnits の基本プロパティの読み取り。 |
| microsoft.directory/administrativeUnits/members/read | Azure Active Directory での administrativeUnits.members プロパティの読み取り。 |
| microsoft.directory/applications/basic/read | Azure Active Directory での applications の基本プロパティの読み取り。 |
| microsoft.directory/applications/owners/read | Azure Active Directory での applications.owners プロパティの読み取り。 |
| microsoft.directory/applications/policies/read | Azure Active Directory での applications.policies プロパティの読み取り。 |
| microsoft.directory/contacts/basic/read | Azure Active Directory での contacts の基本プロパティの読み取り。 |
| microsoft.directory/contacts/memberOf/read | Azure Active Directory での contacts.memberOf プロパティの読み取り。 |
| microsoft.directory/contracts/basic/read | Azure Active Directory での contracts の基本プロパティの読み取り。 |
| microsoft.directory/devices/basic/read | Azure Active Directory での devices の基本プロパティの読み取り。 |
| microsoft.directory/devices/memberOf/read | Azure Active Directory での devices.memberOf プロパティの読み取り。 |
| microsoft.directory/devices/registeredOwners/read | Azure Active Directory での devices.registeredOwners プロパティの読み取り。 |
| microsoft.directory/devices/registeredUsers/read | Azure Active Directory での devices.registeredUsers プロパティの読み取り。 |
| microsoft.directory/directoryRoles/basic/read | Azure Active Directory での directoryRoles の基本プロパティの読み取り。 |
| microsoft.directory/directoryRoles/eligibleMembers/read | Azure Active Directory での directoryRoles.eligibleMembers プロパティの読み取り。 |
| microsoft.directory/directoryRoles/members/read | Azure Active Directory での directoryRoles.members プロパティの読み取り。 |
| microsoft.directory/domains/basic/read | Azure Active Directory での domains の基本プロパティの読み取り。 |
| microsoft.directory/groups/appRoleAssignments/read | Azure Active Directory での groups.appRoleAssignments プロパティの読み取り。 |
| microsoft.directory/groups/basic/read | Azure Active Directory での groups の基本プロパティの読み取り。 |
| microsoft.directory/groups/memberOf/read | Azure Active Directory での groups.memberOf プロパティの読み取り。 |
| microsoft.directory/groups/members/read | Azure Active Directory での groups.members プロパティの読み取り。 |
| microsoft.directory/groups/owners/read | Azure Active Directory での groups.owners プロパティの読み取り。 |
| microsoft.directory/groups/settings/read | Azure Active Directory での groups.settings プロパティの読み取り。 |
| microsoft.directory/groupSettings/basic/read | Azure Active Directory での groupSettings の基本プロパティの読み取り。 |
| microsoft.directory/groupSettingTemplates/basic/read | Azure Active Directory での groupSettingTemplates の基本プロパティの読み取り。 |
| microsoft.directory/oAuth2PermissionGrants/basic/read | Azure Active Directory での oAuth2PermissionGrants の基本プロパティの読み取り。 |
| microsoft.directory/organization/basic/read | Azure Active Directory での organization の基本プロパティの読み取り。 |
| microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | Azure Active Directory での organization.trustedCAsForPasswordlessAuth プロパティの読み取り。 |
| microsoft.directory/roleAssignments/basic/read | Azure Active Directory での roleAssignments の基本プロパティの読み取り。 |
| microsoft.directory/roleDefinitions/basic/read | Azure Active Directory での roleDefinitions の基本プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/read | Azure Active Directory での servicePrincipals.appRoleAssignments プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/basic/read | Azure Active Directory での servicePrincipals の基本プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/memberOf/read | Azure Active Directory での servicePrincipals.memberOf プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Azure Active Directory での servicePrincipals.oAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/ownedObjects/read | Azure Active Directory での servicePrincipals.ownedObjects プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/owners/read | Azure Active Directory での servicePrincipals.owners プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/policies/read | Azure Active Directory での servicePrincipals.policies プロパティの読み取り。 |
| microsoft.directory/subscribedSkus/basic/read | Azure Active Directory での subscribedSkus の基本プロパティの読み取り。 |
| microsoft.directory/users/appRoleAssignments/read | Azure Active Directory での users.appRoleAssignments プロパティの読み取り。 |
| microsoft.directory/users/basic/read | Azure Active Directory での users の基本プロパティの読み取り。 |
| microsoft.directory/users/directReports/read | Azure Active Directory での users.directReports プロパティの読み取り。 |
| microsoft.directory/users/manager/read | Azure Active Directory での users.manager プロパティの読み取り。 |
| microsoft.directory/users/memberOf/read | Azure Active Directory での users.memberOf プロパティの読み取り。 |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | Azure Active Directory での users.oAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.directory/users/ownedDevices/read | Azure Active Directory での users.ownedDevices プロパティの読み取り。 |
| microsoft.directory/users/ownedObjects/read | Azure Active Directory での users.ownedObjects プロパティの読み取り。 |
| microsoft.directory/users/registeredDevices/read | Azure Active Directory での users.registeredDevices プロパティの読み取り。 |

### <a name="directory-synchronization-accounts-permissions"></a>ディレクトリ同期アカウントのアクセス許可

Azure AD Connect サービスでのみ使用されます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/organization/dirSync/update | Azure Active Directory での organization.dirSync プロパティの更新。 |
| microsoft.directory/policies/create | Azure Active Directory での policies の作成。 |
| microsoft.directory/policies/delete | Azure Active Directory での policies の削除。 |
| microsoft.directory/policies/basic/read | Azure Active Directory での policies の基本プロパティの読み取り。 |
| microsoft.directory/policies/basic/update | Azure Active Directory での policies の基本プロパティの更新。 |
| microsoft.directory/policies/owners/read | Azure Active Directory での policies.owners プロパティの読み取り。 |
| microsoft.directory/policies/owners/update | Azure Active Directory での policies.owners プロパティの更新。 |
| microsoft.directory/policies/policiesAppliedTo/read | Azure Active Directory での policies.policiesAppliedTo プロパティの読み取り。 |
| microsoft.directory/policies/tenantDefault/update | Azure Active Directory での policies.tenantDefault プロパティの更新。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの更新。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/read | Azure Active Directory での servicePrincipals.appRoleAssignments プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Azure Active Directory での servicePrincipals.appRoleAssignments プロパティの更新。 |
| microsoft.directory/servicePrincipals/audience/update | Azure Active Directory での servicePrincipals.audience プロパティの更新。 |
| microsoft.directory/servicePrincipals/authentication/update | Azure Active Directory での servicePrincipals.authentication プロパティの更新。 |
| microsoft.directory/servicePrincipals/basic/read | Azure Active Directory での servicePrincipals の基本プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/basic/update | Azure Active Directory での servicePrincipals の基本プロパティの更新。 |
| microsoft.directory/servicePrincipals/create | Azure Active Directory での servicePrincipals の作成。 |
| microsoft.directory/servicePrincipals/credentials/update | Azure Active Directory での servicePrincipals.credentials プロパティの更新。 |
| microsoft.directory/servicePrincipals/memberOf/read | Azure Active Directory での servicePrincipals.memberOf プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Azure Active Directory での servicePrincipals.oAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/owners/read | Azure Active Directory での servicePrincipals.owners プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/owners/update | Azure Active Directory での servicePrincipals.owners プロパティの更新。 |
| microsoft.directory/servicePrincipals/ownedObjects/read | Azure Active Directory での servicePrincipals.ownedObjects プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/permissions/update | Azure Active Directory での servicePrincipals.permissions プロパティの更新。 |
| microsoft.directory/servicePrincipals/policies/read | Azure Active Directory での servicePrincipals.policies プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/policies/update | Azure Active Directory での servicePrincipals.policies プロパティの更新。 |
| microsoft.directorySync/allEntities/allTasks | Azure AD Connect でのすべてのアクションの実行。 |

### <a name="directory-writers-permissions"></a>ディレクトリ ライターのアクセス許可

基本的なディレクトリ情報の読み取りと書き込みを実行できます ユーザーではなく、アプリケーションへのアクセスを許可する場合。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Azure Active Directory での groups.appRoleAssignments プロパティの更新。 |
| microsoft.directory/groups/assignLicense | Azure Active Directory でのグループのライセンスの管理。 |
| microsoft.directory/groups/basic/update | Azure Active Directory での groups の基本プロパティの更新。  |
| microsoft.directory/groups/classification/update | Azure Active Directory でのグループの分類プロパティの更新。 |
| microsoft.directory/groups/create | Azure Active Directory での groups の作成。 |
| microsoft.directory/groups/groupType/update | Azure Active Directory でのグループの groupType プロパティの更新。 |
| microsoft.directory/groups/members/update | Azure Active Directory での groups.members プロパティの更新。 |
| microsoft.directory/groups/owners/update | Azure Active Directory での groups.owners プロパティの更新。 |
| microsoft.directory/groups/reprocessLicenseAssignment | Azure Active Directory でのグループのライセンス割り当ての再処理。 |
| microsoft.directory/groups/securityEnabled/update | Azure Active Directory でのグループの secutiryEnabled プロパティの更新。 |
| microsoft.directory/groups/settings/update | Azure Active Directory での groups.settings プロパティの更新。 |
| microsoft.directory/groups/visibility/update | グループの可視性プロパティの更新 |
| microsoft.directory/groupSettings/basic/update | Azure Active Directory での groupSettings の基本プロパティの更新。 |
| microsoft.directory/groupSettings/create | Azure Active Directory での groupSettings の作成。 |
| microsoft.directory/groupSettings/delete | Azure Active Directory での groupSettings の削除。 |
| microsoft.directory/oAuth2PermissionGrants/basic/update | Azure Active Directory での oAuth2PermissionGrants の基本プロパティの更新。 |
| microsoft.directory/oAuth2PermissionGrants/create | Azure Active Directory での oAuth2PermissionGrants の作成。 |
| microsoft.directory/servicePrincipals/synchronizationCredentials/manage | アプリケーション プロビジョニングのシークレットと資格情報の管理。 |
| microsoft.directory/servicePrincipals/synchronizationJobs/manage | アプリケーション プロビジョニングの同期ジョブの開始、再開、および一時停止。 |
| microsoft.directory/servicePrincipals/synchronizationSchema/manage | アプリケーション プロビジョニングの同期ジョブとスキーマの作成と管理。 |
| microsoft.directory/users/appRoleAssignments/update | Azure Active Directory での users.appRoleAssignments プロパティの更新。 |
| microsoft.directory/users/assignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.directory/users/basic/update | Azure Active Directory での users の基本プロパティの更新。 |
| microsoft.directory/users/create | Azure Active Directory での users の作成。 |
| microsoft.directory/users/disable | Azure Active Directory でのユーザー アカウントの無効化。 |
| microsoft.directory/users/enable | Azure Active Directory でのユーザー アカウントの有効化 |
| microsoft.directory/users/invalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化と、ユーザーに対する次回サインイン時の再認証の要求 |
| microsoft.directory/users/manager/update | Azure Active Directory での users.manager プロパティの更新。 |
| microsoft.directory/users/reprocessLicenseAssignment | Azure Active Directory でのユーザーのライセンス割り当ての再処理。 |
| microsoft.directory/users/userPrincipalName /update | Azure Active Directory での users.userPrincipalName プロパティの更新。 |

### <a name="exchange-service-administrator-permissions"></a>Exchange サービス管理者のアクセス許可

Exchange 製品のすべての側面を管理できます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.directory/groups/unified/appRoleAssignments/update | Azure Active Directory 内での groups.unified プロパティの更新。 |
| microsoft.directory/groups/unified/basic/update | Microsoft 365 グループの基本プロパティを更新します。 |
| microsoft.directory/groups/unified/create | Microsoft 365 グループを作成します。 |
| microsoft.directory/groups/unified/delete | Microsoft 365 グループを削除します。 |
| microsoft.directory/groups/unified/members/update | Microsoft 365 グループのメンバーシップを更新します。 |
| microsoft.directory/groups/unified/owners/update | Microsoft 365 グループの所有権を更新します。 |
| microsoft.office365.exchange/allEntities/allTasks | Exchange Online の全側面の管理。 |
| microsoft.office365.network/performance/allProperties/read | Microsoft 365 管理センターのネットワーク パフォーマンス ページを読み取ります。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.usageReports/allEntities/read | Office 365 の使用状況レポートの読み取り。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |

### <a name="external-id-user-flow-administrator-permissions"></a>外部 ID ユーザー フロー管理者のアクセス許可

ユーザー フローのすべての側面の作成と管理。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Azure Active Directory B2C でのユーザー フローの読み取りと構成。 |

### <a name="external-id-user-flow-attribute-administrator-permissions"></a>外部 ID ユーザー フロー属性管理者のアクセス許可

すべてのユーザー フローに使用可能な属性スキーマの作成と管理。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Azure Active Directory B2C でユーザー属性の読み取りと構成。 |

### <a name="external-identity-provider-administrator-permissions"></a>外部 ID プロバイダー管理者のアクセス許可

直接フェデレーションで使用するための ID プロバイダーの構成。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Azure Active Directory B2C での ID プロバイダーの読み取りと構成。 |

### <a name="global-reader-permissions"></a>グローバル閲覧者のアクセス許可
全体管理者が閲覧できるものはすべて閲覧できますが、何も編集できません。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記の[ロールの説明](#global-reader)を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.commerce.billing/allEntities/read    | 課金のあらゆる側面の読み取り。 |
| microsoft.directory/administrativeUnits/basic/read    | Azure Active Directory での administrativeUnits の基本プロパティの読み取り。 |
| microsoft.directory/administrativeUnits/members/read    | Azure Active Directory での administrativeUnits.members プロパティの読み取り。 |
| microsoft.directory/applications/basic/read    | Azure Active Directory での applications の基本プロパティの読み取り。 |
| microsoft.directory/applications/owners/read    | Azure Active Directory での applications.owners プロパティの読み取り。 |
| microsoft.directory/applications/policies/read    | Azure Active Directory での applications.policies プロパティの読み取り。 |
| microsoft.directory/bitlockerKeys/key/read | Azure Active Directory での bitlocker キー オブジェクトおよびプロパティ (回復キーを含む) の読み取り。 |
| microsoft.directory/contacts/basic/read    | Azure Active Directory での contacts の基本プロパティの読み取り。 |
| microsoft.directory/contacts/memberOf/read    | Azure Active Directory での contacts.memberOf プロパティの読み取り。 |
| microsoft.directory/contracts/basic/read    | Azure Active Directory での contracts の基本プロパティの読み取り。 |
| microsoft.directory/devices/basic/read    | Azure Active Directory での devices の基本プロパティの読み取り。 |
| microsoft.directory/devices/memberOf/read    | Azure Active Directory での devices.memberOf プロパティの読み取り。 |
| microsoft.directory/devices/registeredOwners/read    | Azure Active Directory での devices.registeredOwners プロパティの読み取り。 |
| microsoft.directory/devices/registeredUsers/read    | Azure Active Directory での devices.registeredUsers プロパティの読み取り。 |
| microsoft.directory/directoryRoles/basic/read    | Azure Active Directory での directoryRoles の基本プロパティの読み取り。 |
| microsoft.directory/directoryRoles/eligibleMembers/read    | Azure Active Directory での directoryRoles.eligibleMembers プロパティの読み取り。 |
| microsoft.directory/directoryRoles/members/read    | Azure Active Directory での directoryRoles.members プロパティの読み取り。 |
| microsoft.directory/domains/basic/read    | Azure Active Directory での domains の基本プロパティの読み取り。 |
| microsoft.directory/entitlementManagement/allProperties/read | Azure AD エンタイトルメント管理でのすべてのプロパティの読み取り。 |
| microsoft.directory/groups/appRoleAssignments/read    | Azure Active Directory での groups.appRoleAssignments プロパティの読み取り。 |
| microsoft.directory/groups/basic/read    | Azure Active Directory での groups の基本プロパティの読み取り。 |
| microsoft.directory/groups/hiddenMembers/read    | Azure Active Directory での groups.hiddenMembers プロパティの読み取り。 |
| microsoft.directory/groups/memberOf/read    | Azure Active Directory での groups.memberOf プロパティの読み取り。 |
| microsoft.directory/groups/members/read    | Azure Active Directory での groups.members プロパティの読み取り。 |
| microsoft.directory/groups/owners/read    | Azure Active Directory での groups.owners プロパティの読み取り。 |
| microsoft.directory/groups/settings/read    | Azure Active Directory での groups.settings プロパティの読み取り。 |
| microsoft.directory/groupSettings/basic/read    | Azure Active Directory での groupSettings の基本プロパティの読み取り。 |
| microsoft.directory/groupSettingTemplates/basic/read    | Azure Active Directory での groupSettingTemplates の基本プロパティの読み取り。 |
| microsoft.directory/oAuth2PermissionGrants/basic/read    | Azure Active Directory での oAuth2PermissionGrants の基本プロパティの読み取り。 |
| microsoft.directory/organization/basic/read    | Azure Active Directory での organization の基本プロパティの読み取り。 |
| microsoft.directory/organization/trustedCAsForPasswordlessAuth/read    | Azure Active Directory での organization.trustedCAsForPasswordlessAuth プロパティの読み取り。 |
| microsoft.directory/policies/standard/read    | Azure Active Directory の標準ポリシーの読み取り。 |
| microsoft.directory/roleAssignments/basic/read    | Azure Active Directory での roleAssignments の基本プロパティの読み取り。 |
| microsoft.directory/roleDefinitions/basic/read    | Azure Active Directory での roleDefinitions の基本プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read    | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/read    | Azure Active Directory での servicePrincipals.appRoleAssignments プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/basic/read    | Azure Active Directory での servicePrincipals の基本プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/memberOf/read    | Azure Active Directory での servicePrincipals.memberOf プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read    | Azure Active Directory での servicePrincipals.oAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/ownedObjects/read    | Azure Active Directory での servicePrincipals.ownedObjects プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/owners/read    | Azure Active Directory での servicePrincipals.owners プロパティの読み取り。 |
| microsoft.directory/servicePrincipals/policies/read    | Azure Active Directory での servicePrincipals.policies プロパティの読み取り。 |
| microsoft.directory/signInReports/allProperties/read    | Azure Active Directory での signInReports 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.directory/subscribedSkus/basic/read    | Azure Active Directory での subscribedSkus の基本プロパティの読み取り。 |
| microsoft.directory/users/appRoleAssignments/read    | Azure Active Directory での users.appRoleAssignments プロパティの読み取り。 |
| microsoft.directory/users/basic/read    | Azure Active Directory での users の基本プロパティの読み取り。 |
| microsoft.directory/users/directReports/read    | Azure Active Directory での users.directReports プロパティの読み取り。 |
| microsoft.directory/users/manager/read    | Azure Active Directory での users.manager プロパティの読み取り。 |
| microsoft.directory/users/memberOf/read    | Azure Active Directory での users.memberOf プロパティの読み取り。 |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read    | Azure Active Directory での users.oAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.directory/users/ownedDevices/read    | Azure Active Directory での users.ownedDevices プロパティの読み取り。 |
| microsoft.directory/users/ownedObjects/read    | Azure Active Directory での users.ownedObjects プロパティの読み取り。 |
| microsoft.directory/users/registeredDevices/read    | Azure Active Directory での users.registeredDevices プロパティの読み取り。 |
| microsoft.directory/users/strongAuthentication/read    | MFA 資格情報などの強力な認証プロパティの読み取り。 |
| microsoft.office365.exchange/allEntities/read    | Exchange Online のあらゆる側面の読み取り。 |
| microsoft.office365.messageCenter/messages/read    | Microsoft.office365.messageCenter でメッセージを読み取ります。 |
| microsoft.office365.messageCenter/securityMessages/read    | Microsoft.office365.messageCenter でセキュリティ メッセージを読み取ります。 |
| microsoft.office365.network/performance/allProperties/read | Microsoft 365 管理センターのネットワーク パフォーマンス ページを読み取ります。 |
| microsoft.office365.protectionCenter/allEntities/read    | Office 365 プロテクション センターの全側面の読み取り。 |
| microsoft.office365.securityComplianceCenter/allEntities/read    | microsoft.office365.securityComplianceCenter のすべての標準プロパティの読み取り。 |
| microsoft.office365.usageReports/allEntities/read    | Office 365 の使用状況レポートの読み取り。 |
| microsoft.office365.webPortal/allEntities/standard/read    | microsoft.office365.webPortal のすべてのリソースの標準プロパティの読み取り。 |

### <a name="groups-administrator-permissions"></a>グループ管理者の権限
名前付けや有効期限ポリシーなど、グループやグループの設定のすべての側面を管理できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/groups/basic/read | Azure Active Directory での Group の標準プロパティの読み取り。  |
| microsoft.directory/groups/basic/update | Azure Active Directory での groups の基本プロパティの更新。 |
| microsoft.directory/groups/create | Azure Active Directory での groups の作成。 |
| microsoft.directory/groups/createAsOwner | Azure Active Directory での groups の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.directory/groups/delete | Azure Active Directory での groups の削除。 |
| microsoft.directory/groups/hiddenMembers/read | Azure Active Directory での groups.hiddenMembers プロパティの読み取り。 |
| microsoft.directory/groups/members/update | Azure Active Directory での groups.members プロパティの更新。 |
| microsoft.directory/groups/owners/update | Azure Active Directory での groups.owners プロパティの更新。 |
| microsoft.directory/groups/restore | Azure Active Directory での groups の復元。 |
| microsoft.directory/groups/settings/update | Azure Active Directory での groups.settings プロパティの更新。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.office365.messageCenter/messages/read | Microsoft.office365.messageCenter でメッセージを読み取ります。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="guest-inviter-permissions"></a>ゲスト招待元のアクセス許可
[メンバーがゲストを招待できる] の設定とは無関係にゲスト ユーザーを招待できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/users/appRoleAssignments/read | Azure Active Directory での users.appRoleAssignments プロパティの読み取り。 |
| microsoft.directory/users/basic/read | Azure Active Directory での users の基本プロパティの読み取り。 |
| microsoft.directory/users/directReports/read | Azure Active Directory での users.directReports プロパティの読み取り。 |
| microsoft.directory/users/inviteGuest | Azure Active Directory でのゲスト ユーザーの招待。 |
| microsoft.directory/users/manager/read | Azure Active Directory での users.manager プロパティの読み取り。 |
| microsoft.directory/users/memberOf/read | Azure Active Directory での users.memberOf プロパティの読み取り。 |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | Azure Active Directory での users.oAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.directory/users/ownedDevices/read | Azure Active Directory での users.ownedDevices プロパティの読み取り。 |
| microsoft.directory/users/ownedObjects/read | Azure Active Directory での users.ownedObjects プロパティの読み取り。 |
| microsoft.directory/users/registeredDevices/read | Azure Active Directory での users.registeredDevices プロパティの読み取り。 |

### <a name="helpdesk-administrator-permissions"></a>ヘルプデスク管理者のアクセス許可

管理者以外のユーザーとヘルプデスク管理者のパスワードをリセットできます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Azure Active Directory での devices.bitLockerRecoveryKeys プロパティの読み取り。 |
| microsoft.directory/users/invalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.directory/users/password/update | Azure Active Directory でのすべてのユーザーのパスワードの更新。 詳細については、オンライン ドキュメントを参照してください。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="hybrid-identity-administrator-permissions"></a>ハイブリッド ID の管理者のアクセス許可

AD から Azure AD へのクラウド プロビジョニングとフェデレーションの設定を管理できます。 

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.directory/applications/audience/update  | Azure Active Directory での applications.audience property プロパティの更新。 |
| microsoft.directory/applications/authentication/update | Azure Active Directory での applications.authentication プロパティの更新。  |
| microsoft.directory/applications/basic/update | Azure Active Directory での applications の基本プロパティの更新。 |
| microsoft.directory/applications/create | Azure Active Directory での applications の作成。 |
| microsoft.directory/applications/credentials/update | Azure Active Directory での applications.credentials プロパティの更新。 |
| microsoft.directory/applications/delete | Azure Active Directory での applications の削除。 |
| microsoft.directory/applications/owners/update | Azure Active Directory での applications.owners プロパティの更新。 |
| microsoft.directory/applications/permissions/update | Azure Active Directory での applications.permissions プロパティの更新。 |
| microsoft.directory/applications/policies/update | Azure Active Directory での applications.policies プロパティの更新。 |
| microsoft.directory/applicationTemplates/instantiate | アプリケーション テンプレートからギャラリー アプリケーションのインスタンス化。 |
| microsoft.directory/auditLogs/allProperties/read | Azure Active Directory での auditLogs 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.directory/cloudProvisioning/allProperties/allTasks | Azure AD クラウド プロビジョニング サービスのすべてのプロパティを読み取りと構成。 |
| microsoft.directory/domains/allProperties/read | ドメインのすべてのプロパティの読み取り。 |
| microsoft.directory/domains/federation/update | ドメインのフェデレーション プロパティの更新。 |
| microsoft.directory/organization/dirSync/update | Azure Active Directory での organization.dirSync プロパティの更新。 |
| microsoft.directory/provisioningLogs/allProperties/read | プロビジョニング ログのすべてのプロパティの読み取り。 |
| microsoft.directory/servicePrincipals/audience/update | Azure Active Directory での servicePrincipals.audience プロパティの更新。 |
| microsoft.directory/servicePrincipals/authentication/update | Azure Active Directory での servicePrincipals.authentication プロパティの更新。 |
| microsoft.directory/servicePrincipals/basic/update | Azure Active Directory での servicePrincipals の基本プロパティの更新。 |
| microsoft.directory/servicePrincipals/create | Azure Active Directory での servicePrincipals の作成。 |
| microsoft.directory/servicePrincipals/credentials/update | Azure Active Directory での servicePrincipals.credentials プロパティの更新。 |
| microsoft.directory/servicePrincipals/delete | Azure Active Directory での servicePrincipals の削除。 |
| microsoft.directory/servicePrincipals/owners/update | Azure Active Directory での servicePrincipals.owners プロパティの更新。 |
| microsoft.directory/servicePrincipals/permissions/update | Azure Active Directory での servicePrincipals.permissions プロパティの更新。 |
| microsoft.directory/servicePrincipals/policies/update | Azure Active Directory での servicePrincipals.policies プロパティの更新。 |
| microsoft.directory/servicePrincipals/synchronizationJobs/manage | Azure AD で同期ジョブのすべての側面の管理。 |
| microsoft.directory/servicePrincipals/synchronizationSchema/manage | Azure AD で同期スキーマのすべての側面の管理。 |
| microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Azure AD で同期資格情報のすべての側面の管理。 |
| microsoft.directory/servicePrincipals/tag/update | Azure Active Directory で servicePrincipals.tag プロパティの更新。 |
| microsoft.directory/signInReports/allProperties/read | Azure Active Directory での signInReports 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.office365.messageCenter/messages/read | Microsoft.office365.messageCenter でメッセージを読み取ります。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="insights-administrator-permissions"></a>Insights 管理者のアクセス許可

Microsoft 365 Insights アプリへの管理アクセス権があります。 

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.insights/allEntities/allTasks | Insights の全側面の管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |

### <a name="insights-business-leader-permissions"></a>Insights ビジネス リーダーのアクセス許可

M365 Insights アプリを使用して、ダッシュボードと分析情報を表示および共有できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.insights/reports/read | Insights アプリでのレポートとダッシュボードの表示。 |
| microsoft.insights/programs/update | Insights アプリでのプログラムのデプロイと管理。 |

### <a name="intune-service-administrator-permissions"></a>Intune サービス管理者のアクセス許可

Intune 製品のすべての側面を管理できます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/bitlockerKeys/key/read | Azure Active Directory での bitlocker キー オブジェクトおよびプロパティ (回復キーを含む) の読み取り。 |
| microsoft.directory/contacts/basic/update | Azure Active Directory での contacts の基本プロパティの更新。 |
| microsoft.directory/contacts/create | Azure Active Directory での contacts の作成。 |
| microsoft.directory/contacts/delete | Azure Active Directory での contacts の削除。 |
| microsoft.directory/devices/basic/update | Azure Active Directory での devices の基本プロパティの更新。 |
| microsoft.directory/devices/create | Azure Active Directory での devices の作成。 |
| microsoft.directory/devices/delete | Azure Active Directory での devices の削除。 |
| microsoft.directory/devices/disable | Azure Active Directory での devices の無効化。 |
| microsoft.directory/devices/enable | Azure Active Directory での devices の有効化。 |
| microsoft.directory/devices/extensionAttributes/update | Azure Active Directory での devices.extensionAttributes プロパティのすべての値の更新。 |
| microsoft.directory/devices/registeredOwners/update | Azure Active Directory での devices.registeredOwners プロパティの更新。 |
| microsoft.directory/devices/registeredUsers/update | Azure Active Directory での devices.registeredUsers プロパティの更新。 |
| microsoft.directory/groups/appRoleAssignments/update | Azure Active Directory での groups.appRoleAssignments プロパティの更新。 |
| microsoft.directory/groups/basic/update | Azure Active Directory での groups の基本プロパティの更新。 |
| microsoft.directory/groups/create | Azure Active Directory での groups の作成。 |
| microsoft.directory/groups/createAsOwner | Azure Active Directory での groups の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.directory/groups/delete | Azure Active Directory での groups の削除。 |
| microsoft.directory/groups/hiddenMembers/read | Azure Active Directory での groups.hiddenMembers プロパティの読み取り。 |
| microsoft.directory/groups/members/update | Azure Active Directory での groups.members プロパティの更新。 |
| microsoft.directory/groups/owners/update | Azure Active Directory での groups.owners プロパティの更新。 |
| microsoft.directory/groups/restore | Azure Active Directory での groups の復元。 |
| microsoft.directory/groups/settings/update | Azure Active Directory での groups.settings プロパティの更新。 |
| microsoft.directory/users/appRoleAssignments/update | Azure Active Directory での users.appRoleAssignments プロパティの更新。 |
| microsoft.directory/users/basic/update | Azure Active Directory での users の基本プロパティの更新。 |
| microsoft.directory/users/manager/update | Azure Active Directory での users.manager プロパティの更新。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.intune/allEntities/allTasks | Intune の全側面の管理。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |

### <a name="kaizala-administrator-permissions"></a>Kaizala 管理者のアクセス許可

Microsoft Kaizala の設定を管理できます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | Microsoft 365 管理センターの読み取り。 |

### <a name="license-administrator-permissions"></a>ライセンス管理者のアクセス許可

ユーザーおよびグループの製品ライセンスを管理できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/users/assignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.directory/users/usageLocation/update | Azure Active Directory での users.usageLocation プロパティの更新。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |

### <a name="lync-service-administrator-permissions"></a>Lync サービス管理者のアクセス許可

Skype for Business 製品のすべての側面を管理できます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Skype for Business Online の全側面の管理。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.usageReports/allEntities/read    | Office 365 の使用状況レポートの読み取り。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |


### <a name="message-center-privacy-reader-permissions"></a>メッセージ センターのプライバシー閲覧者のアクセス許可

メッセージ センターの投稿、データのプライバシー メッセージ、グループ、ドメイン、およびサブスクリプションを読み取ることができます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.messageCenter/messages/read | Microsoft.office365.messageCenter でメッセージを読み取ります。 |
| microsoft.office365.messageCenter/securityMessages/read | Microsoft.office365.messageCenter でセキュリティ メッセージを読み取ります。 |

### <a name="message-center-reader-permissions"></a>メッセージ センター閲覧者のアクセス許可
メッセージ センター内でのみ自分の組織のメッセージと更新情報を閲覧することができます。 

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.messageCenter/messages/read | Microsoft.office365.messageCenter でメッセージを読み取ります。 |

### <a name="modern-commerce-user-permissions"></a>Modern Commerce ユーザーのアクセス許可
会社、部署、またはチームの商用購入を管理できます。 

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.commerce.billing/partners/read | Microsoft 365 課金のパートナー プロパティを読み取ります。 |
| microsoft.commerce.volumeLicenseServiceCenter/allEntities/allTasks | ボリューム ライセンス サービス センターのすべての側面を管理します。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 独自の Office 365 サポート チケットを作成して表示します。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |


### <a name="network-administrator-permissions"></a>ネットワーク管理者のアクセス許可
ネットワークの場所を管理し、Microsoft 365 Software as a Service アプリケーションのエンタープライズ ネットワーク設計の分析情報を確認できます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.office365.network/performance/allProperties/read | M365 管理者センターのネットワーク パフォーマンス ページを読み取ります。  |
| microsoft.office365.network/locations/allProperties/allTasks | 各場所に対するネットワークの場所のプロパティの読み取りと構成。 |

### <a name="office-apps-administrator-permissions"></a>Office アプリ管理者のアクセス許可
Office アプリのクラウド サービスを管理すること (ポリシーと設定の管理を含む) や、"新機能" のコンテンツをエンドユーザーのデバイスのために選択、選択解除、公開する機能を管理することができます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.office365.messageCenter/messages/read | Microsoft.office365.messageCenter でメッセージを読み取ります。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.userCommunication/allEntities/allTasks | 新機能のメッセージを表示できるかどうかを読み取って、更新します。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |

### <a name="partner-tier1-support-permissions"></a>パートナー レベル 1 のサポートのアクセス許可

使用しないでください。一般的な使用は想定されていません。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/applications/appRoles/update | アプリ ロールの管理とアプリケーションの委任されたアクセス許可の要求。 |
| microsoft.directory/applications/audience/update | すべての種類のアプリケーションでのオーディエンスの更新。 |
| microsoft.directory/applications/authentication/update | すべての種類のアプリケーションでの認証の更新。 |
| microsoft.directory/applications/basic/update | すべての種類のアプリケーションでの基本プロパティの更新。 |
| microsoft.directory/applications/credentials/update | すべての種類のアプリケーションでの資格情報の更新。 |
| microsoft.directory/applications/owners/update | すべての種類のアプリケーションでの所有者の更新。 |
| microsoft.directory/applications/permissions/update | すべての種類のアプリケーションでの、公開されたアクセス許可と必要なアクセス許可の更新。 |
| microsoft.directory/applications/policies/update | Azure Active Directory での applications.policies プロパティの更新。 |
| microsoft.directory/contacts/basic/update | Azure Active Directory での contacts の基本プロパティの更新。 |
| microsoft.directory/contacts/create | Azure Active Directory での contacts の作成。 |
| microsoft.directory/contacts/delete | Azure Active Directory での contacts の削除。 |
| microsoft.directory/groups/create | Azure Active Directory での groups の作成。 |
| microsoft.directory/groups/createAsOwner | Azure Active Directory での groups の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.directory/groups/members/update | Azure Active Directory での groups.members プロパティの更新。 |
| microsoft.directory/groups/owners/update | Azure Active Directory での groups.owners プロパティの更新。 |
| microsoft.directory/users/appRoleAssignments/update | Azure Active Directory での users.appRoleAssignments プロパティの更新。 |
| microsoft.directory/users/assignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.directory/users/basic/update | Azure Active Directory での users の基本プロパティの更新。 |
| microsoft.directory/users/delete | Azure Active Directory での users の削除。 |
| microsoft.directory/users/invalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.directory/users/manager/update | Azure Active Directory での users.manager プロパティの更新。 |
| microsoft.directory/users/password/update | Azure Active Directory でのすべてのユーザーのパスワードの更新。 詳細については、オンライン ドキュメントを参照してください。 |
| microsoft.directory/users/restore | Azure Active Directory での削除済みユーザーの復元。 |
| microsoft.directory/users/userPrincipalName/update | Azure Active Directory での users.userPrincipalName プロパティの更新。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="partner-tier2-support-permissions"></a>パートナー レベル 2 のサポートのアクセス許可

使用しないでください。一般的な使用は想定されていません。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/applications/appRoles/update | アプリ ロールの管理とアプリケーションの委任されたアクセス許可の要求。 |
| microsoft.directory/applications/audience/update | すべての種類のアプリケーションでのオーディエンスの更新。 |
| microsoft.directory/applications/authentication/update | すべての種類のアプリケーションでの認証の更新。 |
| microsoft.directory/applications/basic/update | すべての種類のアプリケーションでの基本プロパティの更新。 |
| microsoft.directory/applications/credentials/update | すべての種類のアプリケーションでの資格情報の更新。 |
| microsoft.directory/applications/owners/update | すべての種類のアプリケーションでの所有者の更新。 |
| microsoft.directory/applications/permissions/update | すべての種類のアプリケーションでの、公開されたアクセス許可と必要なアクセス許可の更新。 |
| microsoft.directory/applications/policies/update | Azure Active Directory での applications.policies プロパティの更新。 |
| microsoft.directory/contacts/basic/update | Azure Active Directory での contacts の基本プロパティの更新。 |
| microsoft.directory/contacts/create | Azure Active Directory での contacts の作成。 |
| microsoft.directory/contacts/delete | Azure Active Directory での contacts の削除。 |
| microsoft.directory/domains/allTasks | Azure Active Directory での domains の作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.directory/groups/create | Azure Active Directory での groups の作成。 |
| microsoft.directory/groups/delete | Azure Active Directory での groups の削除。 |
| microsoft.directory/groups/members/update | Azure Active Directory での groups.members プロパティの更新。 |
| microsoft.directory/groups/restore | Azure Active Directory での groups の復元。 |
| microsoft.directory/organization/basic/update | Azure Active Directory での organization の基本プロパティの更新。 |
| microsoft.directory/users/appRoleAssignments/update | Azure Active Directory での users.appRoleAssignments プロパティの更新。 |
| microsoft.directory/users/assignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.directory/users/basic/update | Azure Active Directory での users の基本プロパティの更新。 |
| microsoft.directory/users/delete | Azure Active Directory での users の削除。 |
| microsoft.directory/users/invalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.directory/users/manager/update | Azure Active Directory での users.manager プロパティの更新。 |
| microsoft.directory/users/password/update | Azure Active Directory でのすべてのユーザーのパスワードの更新。 詳細については、オンライン ドキュメントを参照してください。 |
| microsoft.directory/users/restore | Azure Active Directory での削除済みユーザーの復元。 |
| microsoft.directory/users/userPrincipalName/update | Azure Active Directory での users.userPrincipalName プロパティの更新。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="password-administrator-permissions"></a>パスワード管理者のアクセス許可

管理者以外とパスワード管理者のパスワードをリセットできます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/users/password/update | Azure Active Directory でのすべてのユーザーのパスワードの更新。 詳細については、オンライン ドキュメントを参照してください。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |

### <a name="power-bi-service-administrator-permissions"></a>Power BI サービス管理者のアクセス許可

Power BI 製品のすべての側面を管理できます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>
| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.powerApps.powerBI/allEntities/allTasks | Power BI の全側面の管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |


### <a name="power-platform-administrator-permissions"></a>Power Platform 管理者のアクセス許可

Microsoft Dynamics 365、PowerApps、Power Automate のすべての側面を作成および管理できます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>
| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.dynamics365/allEntities/allTasks | Dynamics 365 の全側面の管理。 |
| microsoft.flow/allEntities/allTasks | Power Automate の全側面の管理。 |
| microsoft.powerApps/allEntities/allTasks | Power Apps のすべての側面を管理します。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="printer-administrator-permissions"></a>プリンター管理者のアクセス許可

プリンターとプリンター コネクタのすべての側面を管理できます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>
| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.print/allEntities/allProperties/allTasks | プリンターとコネクタの作成と削除、および Microsoft Print でのすべてのプロパティの読み取りと更新。 |

### <a name="printer-technician-permissions"></a>プリンター技術者のアクセス許可

プリンターの登録と登録解除、またプリンターの状態の更新を行うことができます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>
| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.print/connectors/allProperties/read | Microsoft Print 内のコネクタのすべてのプロパティの読み取り。 |
| microsoft.azure.print/printers/allProperties/read | Microsoft Print 内のプリンターのすべてのプロパティの読み取り。 |
| microsoft.azure.print/printers/basic/update | Microsoft Print 内のプリンターの基本プロパティの更新。 |
| microsoft.azure.print/printers/register | Microsoft Print でのプリンターの登録。 |
| microsoft.azure.print/printers/unregister | Microsoft Print でのプリンターの登録解除。 |

### <a name="privileged-authentication-administrator-permissions"></a>特権認証管理者のアクセス許可

任意のユーザー (管理者または管理者以外) の認証方法の情報を表示、設定、リセットできます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/users/invalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.directory/users/strongAuthentication/update | MFA 資格情報などの強力な認証プロパティを更新します。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.directory/users/password/update | Microsoft 365 組織内のすべてのユーザーのパスワードの更新。 詳細については、オンライン ドキュメントを参照してください。 |

### <a name="privileged-role-administrator-permissions"></a>特権ロール管理者のアクセス許可

Azure AD でのロールの割り当てと、Privileged Identity Management のすべての側面を管理できます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/groupsAssignableToRoles/allProperties/update | Azure Active Directory での isAssignableToRole プロパティが true に設定されているグループの更新。 |
| microsoft.directory/groupsAssignableToRoles/create | Azure Active Directory での isAssignableToRole プロパティが true に設定されているグループの作成。 |
| microsoft.directory/groupsAssignableToRoles/delete | Azure Active Directory での isAssignableToRole プロパティが true に設定されているグループの削除。 |
| microsoft.directory/privilegedIdentityManagement/allEntities/allTasks | microsoft.aad.privilegedIdentityManagement でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/allTasks | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの読み取りと構成。 |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Azure Active Directory での servicePrincipals.oAuth2PermissionGrants プロパティの読み取りと構成。 |
| microsoft.directory/administrativeUnits/allProperties/allTasks | 管理単位 (メンバーを含む) の作成と管理。 |
| microsoft.directory/roleAssignments/allProperties/allTasks | ロールの割り当ての作成と管理。 |
| microsoft.directory/roleDefinitions/allProperties/allTasks | ロール定義の作成と管理。 |

### <a name="reports-reader-permissions"></a>レポート閲覧者のアクセス許可

サインインと監査のレポートを読み取ることができます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Azure Active Directory での auditLogs 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.directory/signInReports/allProperties/read | Azure Active Directory での signInReports 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.office365.usageReports/allEntities/read | Office 365 の使用状況レポートの読み取り。 |

### <a name="search-administrator-permissions"></a>Search 管理者のアクセス許可

Microsoft Search 設定のすべての側面を作成および管理できます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Microsoft.office365.messageCenter でメッセージを読み取ります。 |
| microsoft.office365.search/allEntities/allProperties/allTasks | microsoft.Office365.search でのすべてのリソースの作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |

### <a name="search-editor-permissions"></a>Search エディターのアクセス許可

ブックマーク、Q&A、場所、フロアプランなどの編集コンテンツを作成および管理することができます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Microsoft.office365.messageCenter でメッセージを読み取ります。 |
| microsoft.office365.search/content/allProperties/allTasks | microsoft.Office365.search でのコンテンツの作成と削除、およびすべてのプロパティの読み取りと更新。 |

### <a name="security-administrator-permissions"></a>セキュリティ管理者のアクセス許可

セキュリティ情報とレポートを読み取り、Azure AD と Microsoft 365 で構成を管理することができます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.directory/applications/policies/update | Azure Active Directory での applications.policies プロパティの更新。 |
| microsoft.directory/auditLogs/allProperties/read | Azure Active Directory での auditLogs 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.directory/bitlockerKeys/key/read | Azure Active Directory での bitlocker キー オブジェクトおよびプロパティ (回復キーを含む) の読み取り。 |
| microsoft.directory/entitlementManagement/allProperties/read | Azure AD エンタイトルメント管理でのすべてのプロパティの読み取り。 |
| microsoft.directory/identityProtection/allProperties/read | microsoft.aad.identityProtection でのすべてのリソースの読み取り。 |
| microsoft.directory/identityProtection/allProperties/update | microsoft.aad.identityProtection でのすべてのリソースの更新。 |
| microsoft.directory/policies/basic/update | Azure Active Directory での policies の基本プロパティの更新。 |
| microsoft.directory/policies/create | Azure Active Directory での policies の作成。 |
| microsoft.directory/policies/delete | Azure Active Directory での policies の削除。 |
| microsoft.directory/policies/owners/update | Azure Active Directory での policies.owners プロパティの更新。 |
| microsoft.directory/policies/tenantDefault/update | Azure Active Directory での policies.tenantDefault プロパティの更新。 |
| microsoft.directory/privilegedIdentityManagement/allProperties/read | microsoft.aad.privilegedIdentityManagement でのすべてのリソースの読み取り。 |
| microsoft.directory/servicePrincipals/policies/update | Azure Active Directory での servicePrincipals.policies プロパティの更新。 |
| microsoft.directory/signInReports/allProperties/read | Azure Active Directory での signInReports 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.office365.protectionCenter/allEntities/read | Office 365 プロテクション センターの全側面の読み取り。 |
| microsoft.office365.protectionCenter/allEntities/update | microsoft.office365.protectionCenter でのすべてのリソースの更新。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |

### <a name="security-operator-permissions"></a>セキュリティ オペレーターのアクセス許可

セキュリティ イベントを作成、管理します。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.advancedThreatProtection/allEntities/read | Azure AD Advanced Threat Protection の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.directory/cloudAppSecurity/allProperties/allTasks | Microsoft Cloud App Security の読み取りと構成。 |
| microsoft.directory/identityProtection/allProperties/read | microsoft.aad.identityProtection でのすべてのリソースの読み取り。 |
| microsoft.directory/privilegedIdentityManagement/allProperties/read | microsoft.aad.privilegedIdentityManagement でのすべてのリソースの読み取り。 |
| microsoft.directory/provisioningLogs/allProperties/read | プロビジョニング ログのすべてのプロパティの読み取り。 |
| microsoft.intune/allEntities/allTasks | Intune の全側面の管理。 |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | セキュリティ/コンプライアンス センターの読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Windows Defender Advanced Threat Protection の読み取りと構成。 |


### <a name="security-reader-permissions"></a>セキュリティ閲覧者のアクセス許可

Azure AD と Microsoft 365 のセキュリティ情報とレポートを読み取ることができます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Azure Active Directory での auditLogs 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.directory/bitlockerKeys/key/read | Azure Active Directory での bitlocker キー オブジェクトおよびプロパティ (回復キーを含む) の読み取り。 |
| microsoft.directory/entitlementManagement/allProperties/read | Azure AD エンタイトルメント管理でのすべてのプロパティの読み取り。 |
| microsoft.directory/policies/conditionalAccess/basic/read | Azure Active Directory での policies.conditionalAccess プロパティの読み取り。 |
| microsoft.directory/signInReports/allProperties/read | Azure Active Directory での signInReports 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.aad.identityProtection/allEntities/read | microsoft.aad.identityProtection でのすべてのリソースの読み取り。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | microsoft.aad.privilegedIdentityManagement でのすべてのリソースの読み取り。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.protectionCenter/allEntities/read | Office 365 プロテクション センターの全側面の読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |

### <a name="service-support-administrator-permissions"></a>サービス サポート管理者のアクセス許可

サービス正常性に関する情報を読み取り、サポート チケットを管理することができます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="sharepoint-service-administrator-permissions"></a>SharePoint サービス管理者のアクセス許可

SharePoint サービスのすべての側面を管理できます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.directory/groups/unified/appRoleAssignments/update | Azure Active Directory 内での groups.unified プロパティの更新。 |
| microsoft.directory/groups/unified/basic/update | Microsoft 365 グループの基本プロパティを更新します。 |
| microsoft.directory/groups/unified/create | Microsoft 365 グループを作成します。 |
| microsoft.directory/groups/unified/delete | Microsoft 365 グループを削除します。 |
| microsoft.directory/groups/unified/members/update | Microsoft 365 グループのメンバーシップを更新します。 |
| microsoft.directory/groups/unified/owners/update | Microsoft 365 グループの所有権を更新します。 |
| microsoft.office365.network/performance/allProperties/read | M365 管理者センターのネットワーク パフォーマンス ページを読み取ります。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.sharepoint/allEntities/allTasks | microsoft.office365.sharepoint でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.usageReports/allEntities/read    | Office 365 の使用状況レポートの読み取り。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |

### <a name="teams-communications-administrator-permissions"></a>Teams 通信管理者のアクセス許可

Microsoft Teams サービス内での通話と会議の機能を管理できます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.usageReports/allEntities/read | Office 365 の使用状況レポートの読み取り。 |
| microsoft.teams/meetings/allProperties/allTasks | 会議ポリシー、構成、会議ブリッジなどの会議の管理。 |
| microsoft.teams/voice/allProperties/allTasks | 通話ポリシーや電話番号のインベントリおよび割り当てなどの音声の管理。 |
| microsoft.teams/callQuality/allProperties/read | 通話品質ダッシュボード (CQD) のすべてのデータの読み取り。 |

### <a name="teams-communications-support-engineer-permissions"></a>Teams 通信サポート エンジニアのアクセス許可

高度なツールを使用して、Teams 内の通信の問題のトラブルシューティングを行えます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.teams/callQuality/allProperties/read | 通話品質ダッシュボード (CQD) のすべてのデータの読み取り。 |

### <a name="teams-communications-support-specialist-permissions"></a>Teams 通信サポート スペシャリストのアクセス許可

基本的なツールを使用して、Teams 内の通信の問題のトラブルシューティングを行えます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.teams/callQuality/basic/read | 通話品質ダッシュボード (CQD) の基本データの読み取り。 |

### <a name="teams-devices-administrator-permissions"></a>Teams デバイス管理者のアクセス許可

Teams 認定デバイスで管理関連タスクを実行できます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.teams/devices/basic/read | 構成ポリシーを含む、Teams 認定デバイスのすべての側面の管理。 |

### <a name="teams-service-administrator-permissions"></a>Teams サービス管理者のアクセス許可

Microsoft Teams サービスを管理できます。

> [!NOTE]
> このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
>
>

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.directory/groups/hiddenMembers/read | Azure Active Directory での groups.hiddenMembers プロパティの読み取り。 |
| microsoft.directory/groups/unified/appRoleAssignments/update | Azure Active Directory 内での groups.unified プロパティの更新。 |
| microsoft.directory/groups/unified/basic/update | Microsoft 365 グループの基本プロパティを更新します。 |
| microsoft.directory/groups/unified/create | Microsoft 365 グループを作成します。 |
| microsoft.directory/groups/unified/delete | Microsoft 365 グループを削除します。 |
| microsoft.directory/groups/unified/members/update | Microsoft 365 グループのメンバーシップを更新します。 |
| microsoft.directory/groups/unified/owners/update | Microsoft 365 グループの所有権を更新します。 |
| microsoft.office365.network/performance/allProperties/read | M365 管理者センターのネットワーク パフォーマンス ページを読み取ります。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.usageReports/allEntities/read | Office 365 の使用状況レポートの読み取り。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.teams/allEntities/allProperties/allTasks | Teams 内のすべてのリソースの管理。 |

### <a name="usage-summary-reports-reader-permissions"></a>使用状況の概要のレポート閲覧者のアクセス許可
M365 利用状況分析および生産性スコアのテナント レベルの集計のみを表示できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.office365.usageReports/allEntities/standard/read | テナント レベルの集計された Office 365 利用状況レポートの読み取り。 |
| microsoft.office365.webPortal/allEntities/standard/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。|

### <a name="user-administrator-permissions"></a>ユーザー管理者のアクセス許可
ユーザーとグループのすべての側面を、制限付きの管理者のパスワードをリセットすることも含めて、管理できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.directory/appRoleAssignments/create | Azure Active Directory での appRoleAssignments の作成。 |
| microsoft.directory/appRoleAssignments/delete | Azure Active Directory での appRoleAssignments の削除。 |
| microsoft.directory/appRoleAssignments/update | Azure Active Directory での appRoleAssignments の更新。 |
| microsoft.directory/contacts/basic/update | Azure Active Directory での contacts の基本プロパティの更新。 |
| microsoft.directory/contacts/create | Azure Active Directory での contacts の作成。 |
| microsoft.directory/contacts/delete | Azure Active Directory での contacts の削除。 |
| microsoft.directory/entitlementManagement/allProperties/allTasks | Azure AD エンタイトルメント管理でのリソースの作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.directory/groups/appRoleAssignments/update | Azure Active Directory での groups.appRoleAssignments プロパティの更新。 |
| microsoft.directory/groups/basic/update | Azure Active Directory での groups の基本プロパティの更新。 |
| microsoft.directory/groups/create | Azure Active Directory での groups の作成。 |
| microsoft.directory/groups/createAsOwner | Azure Active Directory での groups の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.directory/groups/delete | Azure Active Directory での groups の削除。 |
| microsoft.directory/groups/hiddenMembers/read | Azure Active Directory での groups.hiddenMembers プロパティの読み取り。 |
| microsoft.directory/groups/members/update | Azure Active Directory での groups.members プロパティの更新。 |
| microsoft.directory/groups/owners/update | Azure Active Directory での groups.owners プロパティの更新。 |
| microsoft.directory/groups/restore | Azure Active Directory での groups の復元。 |
| microsoft.directory/groups/settings/update | Azure Active Directory での groups.settings プロパティの更新。 |
| microsoft.directory/users/appRoleAssignments/update | Azure Active Directory での users.appRoleAssignments プロパティの更新。 |
| microsoft.directory/users/assignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.directory/users/basic/update | Azure Active Directory での users の基本プロパティの更新。 |
| microsoft.directory/users/create | Azure Active Directory での users の作成。 |
| microsoft.directory/users/delete | Azure Active Directory での users の削除。 |
| microsoft.directory/users/invalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.directory/users/manager/update | Azure Active Directory での users.manager プロパティの更新。 |
| microsoft.directory/users/password/update | Azure Active Directory でのすべてのユーザーのパスワードの更新。 詳細については、オンライン ドキュメントを参照してください。 |
| microsoft.directory/users/restore | Azure Active Directory での削除済みユーザーの復元。 |
| microsoft.directory/users/userPrincipalName/update | Azure Active Directory での users.userPrincipalName プロパティの更新。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | ディレクトリレベル サービスの Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

## <a name="role-template-ids"></a>ロール テンプレート ID

ロール テンプレート ID は、Microsoft Graph API または PowerShell のユーザーによって主に使用されます。

グラフの表示名 | Azure portal の表示名 | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
アプリケーション管理者 | アプリケーション管理者 | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
アプリケーション開発者 | アプリケーション開発者 | CF1C38E5-3621-4004-A7CB-879624DCED7C
認証管理者 | 認証管理者 | c4e39bd9-1100-46d3-8c65-fb160da0071f
攻撃のペイロードの作成者 | 攻撃のペイロードの作成者 | 9c6df0f2-1e7c-4dc3-b195-66dfbd24aa8f
攻撃のシミュレーションの管理者 | 攻撃のシミュレーションの管理者 | c430b396-e693-46cc-96f3-db01bf8bb62a
Azure AD 参加済みデバイスのローカル管理者 | Azure AD 参加済みデバイスのローカル管理者 | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Azure DevOps 管理者 | Azure DevOps 管理者 | e3973bdf-4987-49ae-837a-ba8e231c7286
Azure Information Protection 管理者 | Azure Information Protection 管理者 | 7495fdc4-34c4-4d15-a289-98788ce399fd
B2C IEF キーセット管理者 | B2C IEF キーセット管理者 | aaf43236-0c0d-4d5f-883a-6955382ac081
B2C IEF ポリシー管理者 | B2C IEF ポリシー管理者 | 3edaf663-341e-4475-9f94-5c398ef6c070
課金管理者 | 課金管理者 | b0f54661-2d74-4c50-afa3-1ec803f12efe
クラウド アプリケーション管理者 | クラウド アプリケーション管理者 | 158c047a-c907-4556-b7ef-446551a6b5f7
クラウド デバイス管理者 | クラウド デバイス管理者 | 7698a772-787b-4ac8-901f-60d6b08affd2
コンプライアンス管理者 | コンプライアンス管理者 | 17315797-102d-40b4-93e0-432062caca18
コンプライアンス データ管理者 | コンプライアンス データ管理者 | e6d1a23a-da11-4be4-9570-befc86d067a7
条件付きアクセス管理者 | 条件付きアクセス管理者 | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
カスタマー ロックボックスのアクセス承認者 | カスタマー ロックボックスのアクセス承認者 | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
デスクトップ Analytics 管理者 | デスクトップ Analytics 管理者 | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
デバイスの参加 | 非推奨 | 9c094953-4995-41c8-84c8-3ebb9b32c93f
デバイス マネージャー | 非推奨 | 2b499bcd-da44-4968-8aec-78e1674fa64d
デバイス ユーザー | 非推奨 | d405c6df-0af8-4e3b-95e4-4d06e542189e
ディレクトリ リーダー | ディレクトリ閲覧者 | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
ディレクトリ同期アカウント | 使用するべきではないため、表示されません | d29b2b05-8046-44ba-8758-1e26182fcf32
ディレクトリ ライター | ディレクトリ ライター | 9360feb5-f418-4baa-8175-e2a00bac4301
Dynamics 365 管理者 | Dynamics 365 管理者 | 44367163-eba1-44c3-98af-f5787879f96a
Exchange 管理者 | Exchange 管理者 | 29232cdf-9323-42fd-ade2-1d097af3e4de
外部 ID ユーザー フロー管理者 | 外部 ID ユーザー フロー管理者 | 6e591065-9bad-43ed-90f3-e9424366d2f0
外部 ID ユーザー フロー属性管理者 | 外部 ID ユーザー フロー属性管理者 | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
外部 ID プロバイダー管理者 | 外部 ID プロバイダー管理者 | be2f45a1-457d-42af-a067-6ec1fa63bc45
グローバル管理者 | 全体管理者 | 62e90394-69f5-4237-9190-012177145e10
グローバル閲覧者 | グローバル閲覧者 | f2ef992c-3afb-46b9-b7cf-a126ee74c451
グループ管理者 | グループ管理者 | fdd7a751-b60b-444a-984c-02652fe8fa1c 
ゲスト招待元 | ゲスト招待元 | 95e79109-95c0-4d8e-aee3-d01accf2d47b
ヘルプデスク管理者 | ヘルプデスク管理者 | 729827e3-9c14-49f7-bb1b-9608f156bbb8
ハイブリッド ID の管理者 | ハイブリッド ID の管理者 | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2
Insights 管理者 | Insights 管理者 | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c
Insights ビジネス リーダー | Insights ビジネス リーダー | 31e939ad-9672-4796-9c2e-873181342d2d
Intune 管理者 | Intune 管理者 | 3a2c62db-5318-420d-8d74-23affee5d9d5
Kaizala 管理者 | Kaizala 管理者 | 74ef975b-6605-40af-a5d2-b9539d836353
ライセンス管理者 | ライセンス管理者 | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
メッセージ センターのプライバシー閲覧者 | メッセージ センターのプライバシー閲覧者 | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
メッセージ センター閲覧者 | メッセージ センター閲覧者 | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Modern Commerce ユーザー | Modern Commerce ユーザー | d24aef57-1500-4070-84db-2666f29cf966
ネットワーク管理者 | ネットワーク管理者 | d37c8bed-0711-4417-ba38-b4abe66ce4c2
Office アプリ管理者 | Office アプリ管理者 | 2b745bdf-0803-4d80-aa65-822c4493daac
パートナー レベル 1 のサポート | 使用するべきではないため、表示されません | 4ba39ca4-527c-499a-b93d-d9b492c50246
パートナー レベル 2 のサポート | 使用するべきではないため、表示されません | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
パスワード管理者 | パスワード管理者 | 966707d0-3269-4727-9be2-8c3a10f19b9d
Power BI 管理者 | Power BI 管理者 | a9ea8996-122f-4c74-9520-8edcd192826c
Power Platform 管理者 | Power Platform 管理者 | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
プリンター管理者 | プリンター管理者 | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f
プリンター技術者 | プリンター技術者 | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477
特権認証管理者 | 特権認証管理者 | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
特権ロール管理者 | 特権ロール管理者 | e8611ab8-c189-46e8-94e1-60213ab1f814
レポート閲覧者 | レポート閲覧者 | 4a5d8f65-41da-4de4-8968-e035b65339cf
Search 管理者 | Search 管理者 | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Search エディター | Search エディター | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
セキュリティ管理者 | セキュリティ管理者 | 194ae4cb-b126-40b2-bd5b-6091b380977d
セキュリティ オペレーター | セキュリティ オペレーター | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
セキュリティ閲覧者 | セキュリティ閲覧者 | 5d6b6bb7-de71-4623-b4af-96380a352509
サービス サポート管理者 | サービス サポート管理者 | f023fd81-a637-4b56-95fd-791ac0226033
SharePoint 管理者 | SharePoint 管理者 | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Skype for Business 管理者 | Skype for Business 管理者 | 75941009-915a-4869-abe7-691bff18279e
Teams 通信管理者 | Teams 通信管理者 | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Teams 通信サポート エンジニア | Teams 通信サポート エンジニア | f70938a0-fc10-4177-9e90-2178f8765737
Teams 通信サポート スペシャリスト | Teams 通信サポート スペシャリスト | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Teams デバイス管理者 | Teams デバイス管理者 | 3d762c5a-1b6c-493f-843e-55a3b42923d4
Teams 管理者 | Teams 管理者 | 69091246-20e8-4a56-aa4d-066075b2a7a8
使用状況の概要のレポート閲覧者 | 使用状況の概要のレポート閲覧者 | 75934031-6c7e-415a-99d7-48dbd49e875e
User | 使用できないため、表示されません | a0b1b346-4d3e-4e8b-98f8-753987be4970
ユーザー管理者 | ユーザー管理者 | fe930be7-5e62-47db-91af-98c3a49a38b1
デバイスの社内参加 | 非推奨 | c34f683f-4d5a-4403-affd-6615e00e3a7f

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
デバイスの参加 | 非推奨 | [非推奨になったロールのドキュメント](permissions-reference.md#deprecated-roles)
デバイス マネージャー | 非推奨 | [非推奨になったロールのドキュメント](permissions-reference.md#deprecated-roles)
デバイス ユーザー | 非推奨 | [非推奨になったロールのドキュメント](permissions-reference.md#deprecated-roles)
ディレクトリ同期アカウント | 使用するべきではないため、表示されません | [ディレクトリ同期アカウントのドキュメント](permissions-reference.md#directory-synchronization-accounts)
ゲスト ユーザー | 使用できないため、表示されません  | NA
Partner Tier 1 サポート | 使用するべきではないため、表示されません | [Partner Tier 1 サポートのドキュメント](permissions-reference.md#partner-tier1-support)
Partner Tier 2 サポート | 使用するべきではないため、表示されません | [Partner Tier 2 サポートのドキュメント](permissions-reference.md#partner-tier2-support)
制限されたゲストユーザー | 使用できないため、表示されません | NA
User | 使用できないため、表示されません | NA
デバイスの社内参加 | 非推奨 | [非推奨になったロールのドキュメント](permissions-reference.md#deprecated-roles)

## <a name="password-reset-permissions"></a>パスワード リセットのアクセス許可

列見出しは、パスワードをリセットできるロールを表します。 テーブルの行には、パスワードをリセットできるロールが含まれます。

パスワードをリセットできる | 認証管理者 | ヘルプデスク管理者 | パスワード管理者 | [ユーザー管理者] | 特権認証管理者 | グローバル管理者
------ | ------ | ------ | ------ | ------ | ------ | ------
認証管理者 | :heavy_check_mark: | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
ディレクトリ リーダー | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
グローバル管理者 | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:\*
グループ管理者 | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
ゲスト | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
ゲスト招待元 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
ヘルプデスク管理者 | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
メッセージ センター閲覧者 | :heavy_check_mark: | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
パスワード管理者 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
特権認証管理者 | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
特権ロール管理者 | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
レポート閲覧者 | :heavy_check_mark: | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
制限付きゲスト | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
ユーザー (管理者ロールなし) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
[ユーザー管理者] | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
使用状況の概要のレポート閲覧者 | :heavy_check_mark: | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:

\* グローバル管理者は、自分のグローバル管理者の割り当てを削除することはできません。 これは、組織にグローバル管理者がいなくなる状況を防ぐためです。

## <a name="next-steps"></a>次のステップ

* Azure サブスクリプションの管理者としてユーザーを割り当てる方法の詳細については、[Azure のロール割り当て (Azure RBAC) の追加と削除](../../role-based-access-control/role-assignments-portal.md)に関するページを参照してください
* Microsoft Azure でリソース アクセスを制御する方法の詳細については、[さまざまなロールの概要](../../role-based-access-control/rbac-and-directory-admin-roles.md)に関するページを参照してください
* サブスクリプションと Azure AD テナントの関係の詳細、またはサブスクリプションの関連付けまたは追加を行う手順の詳細については、「[Azure サブスクリプションを Azure Active Directory テナントに関連付けるまたは追加する](../fundamentals/active-directory-how-subscriptions-associated-directory.md)」を参照してください。
