---
title: 新着情報のアーカイブ - Azure Active Directory | Microsoft Docs
description: このコンテンツ セットの概要セクションにある新着情報リリース ノートには、6 か月間のアクティビティが含まれています。 6 か月後、項目はメイン記事から削除され、このアーカイブ記事に配置されます。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6935d2a89968b55dec1626d23b3f290261cf66f
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655443"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Azure Active Directory の新着情報のアーカイブ

主な[新着情報リリース ノート](whats-new.md)記事には、最新の 6 か月間の情報が含まれますが、この記事にはより古い情報がすべて含まれています。

新着情報リリース ノートでは、以下に関する情報が提供されます。

- 最新のリリース
- 既知の問題
- バグの修正
- 非推奨の機能
- 変更の計画

---

## <a name="novemberdecember-2018"></a>2018 年 11 月/12 月

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>同期範囲から削除されたユーザーがクラウド専用アカウントに切り替わらない

**種類:** 固定  
**サービス カテゴリ:** [ユーザー管理]  
**製品の機能:** Directory

>[!Important]
>この修正についてご意見をいただき、ご迷惑をおかけしていることをよく理解しております。 このため、お客様の組織に変更を簡単に導入できるようになるまで、この変更は元に戻しました。

Active Directory Domain Services (AD DS) オブジェクトが同期範囲から除外され、後続の同期周期で Azure AD のごみ箱に移動されると、ユーザーの DirSyncEnabled フラグが間違って **False** に切り替わるバグを修正しました。 この修正の結果、ユーザーが同期範囲から除外され、その後、Azure AD のごみ箱から復元された場合、予想どおり、ユーザー アカウントはオンプレミス AD との同期を維持します。また、その権限ソース (SoA) がオンプレミス AD のままなので、クラウドで管理できません。

この修正の前は、DirSyncEnabled フラグが False に切り替わるという問題がありました。 これらのアカウントがクラウド専用オブジェクトに変換された、アカウントをクラウドで管理できるという間違った印象を与えました。 しかしながら、アカウントの SoA はオンプレミスのままであり、同期されるプロパティ (シャドウ属性) はすべてオンプレミス AD から届きます。 この状態は Azure AD とその他のクラウド ワークロードでさまざまな問題を引き起こしました。これらのアカウントは AD と同期しているものとして処理されるはずですが、クラウド専用アカウントのように動作しました。

現時点では、AD 同期アカウントをクラウド専用アカウントに実際に変換する唯一の方法はテナント レベルで DirSync を無効にし、SoA を転送するバックエンド操作をトリガーすることです。 この種の SoA 変更では、オンプレミス関連の属性 (LastDirSyncTime やシャドウ属性など) をすべて消去する必要があり、また、他のクラウド ワークロードに信号を送り、個々のオブジェクトもクラウド専用アカウントに変換させる必要があります (ただし、これらに限定されません)。

結果的に、今回の修正によって、一部のシナリオでは過去に必須でしたが、AD と同期しているユーザーの ImmutableID 属性を直接更新することが回避されます。 仕様では、Azure AD のオブジェクトの ImmutableID はその名前が示すように不変であることを意図しています。 Azure AD Connect Health と Azure AD Connect Synchronization クライアントで導入された新機能で次のようなシナリオに対処できます。

- **多数のユーザーを対象に大規模な ImmutableID 更新を段階的に行う**
  
  たとえば、時間のかかる AD DS フォレスト間移行を行う必要があります。 解決方法:Azure AD Connect を使用し、**ソース アンカーを構成**します。また、ユーザーが移行するとき、Azure AD からローカル AD DS ユーザーの新しいフォレストの ms-DS-Consistency-Guid 属性に既存の ImmutableID 値をコピーします。 詳細については、「[sourceAnchor としての ms-DS-ConsistencyGuid の使用](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor)」を参照してください。

- **多数のユーザーを対象に大規模な ImmutableID 更新を一度に行う**

  たとえば、Azure AD Connect の導入中、設定を間違え、SourceAnchor 属性を変更しなければなりません。 解決方法:テナント レベルで DirSync を無効にし、無効な ImmutableID 値をすべて消去します。 詳しくは、「[Office 365 のディレクトリ同期を無効にする](/office365/enterprise/turn-off-directory-synchronization)」をご覧ください。

- **オンプレミス ユーザーと Azure AD の既存ユーザーを再度組み合わせる** たとえば、AD DS でユーザーが再作成された場合、Azure AD の既存アカウントと再度組み合わされず、重複する Azure AD アカウントが生成されます (孤立オブジェクト)。 解決方法:Azure portal の Azure AD Connect Health を使用し、ソース アンカー/ImmutableID のマッピングをやり直します。 詳細については、「[孤立したオブジェクトのシナリオ](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario)」を参照してください。

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>重大な変更:Azure Monitor を通じた監査およびサインイン ログ スキーマの更新

**種類:** 変更された機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

現在、監査とサインインのログ ストリームはどちらも Azure Monitor を通じて公開していますので、お使いの SIEM ツールや Log Analytics とログ ファイルをシームレスに統合できます。 お客様のフィードバックに基づき、またこの機能の一般提供の発表に備えて、次の変更がスキーマに加えられます。 これらのスキーマ変更と関連ドキュメントの更新は 1 月の第 1 週までに行われる予定です。

#### <a name="new-fields-in-the-audit-schema"></a>監査スキーマの新しいフィールド
リソースに対して実行される操作の種類を提供するために、新しい **[操作の種類]** フィールドが追加されます。 たとえば、 **[追加]** 、 **[更新]** 、 **[削除]** などです。

#### <a name="changed-fields-in-the-audit-schema"></a>監査スキーマの変更されるフィールド
監査スキーマの次のフィールドが変更されます:

|フィールド名|変更箇所|古い値|新しい値|
|----------|------------|----------|----------|
|Category|以前は **[サービス名]** フィールドでした。 現在は **[監査カテゴリ]** フィールドです。 **[サービス名]** は **loggedByService** フィールドに名前が変更されました。|<ul><li>アカウント プロビジョニング</li><li>Core Directory (コア ディレクトリ)</li><li>セルフサービスのパスワード リセット</li></ul>|<ul><li>[ユーザー管理]</li><li>グループ管理</li><li>アプリの管理</li></ul>|
|targetResources|**TargetResourceType** を最上位レベルに含みます。|&nbsp;|<ul><li>ポリシー</li><li>アプリ</li><li>User</li><li>Group</li></ul>|
|loggedByService|監査ログを生成したサービスの名前を提供します。|Null|<ul><li>アカウント プロビジョニング</li><li>Core Directory (コア ディレクトリ)</li><li>セルフサービスのパスワード リセット</li></ul>|
|結果|監査ログの結果を提供します。 以前は、これは列挙されていましたが、実際の値を示すようになります。|<ul><li>0</li><li>1</li></ul>|<ul><li>Success</li><li>失敗</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>サインイン スキーマの変更されるフィールド
サインイン スキーマの次のフィールドが変更されます:

|フィールド名|変更箇所|古い値|新しい値|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|これは **conditionalaccessPolicies** フィールドでした。 現在は **appliedConditionalAccessPolicies** フィールドです。|変更なし|変更なし|
|conditionalAccessStatus|サインイン時点の条件付きアクセス ポリシー ステータスの結果を提供します。 以前は、これは列挙されていましたが、実際の値を示すようになります。|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>失敗</li><li>未適用</li><li>Disabled</li></ul>|
|appliedConditionalAccessPolicies: 結果|サインイン時点の個別の条件付きアクセス ポリシー ステータスの結果を提供します。 以前は、これは列挙されていましたが、実際の値を示すようになります。|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>失敗</li><li>未適用</li><li>Disabled</li></ul>|

スキーマの詳細については、「[Azure Monitor の Azure AD 監査ログ スキーマを解釈する (プレビュー)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)」を参照してください。

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Identity Protection の教師あり機械学習モデルとリスク スコア エンジンの機能強化

**種類:** 変更された機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** リスク スコア

Identity Protection に関連した、ユーザーおよびサインインのリスク評価エンジンの機能強化により、ユーザー リスクの精度とカバレッジを向上させることができます。 管理者にとっての注目点は、ユーザーのリスク レベルが特定の検出のリスク レベルに直接リンクされなくなったことと、リスクの高いサインイン イベントの数とレベルが増加していることです。

ユーザーのサインインと検出のパターンの追加機能を使用してユーザーのリスクを計算する、教師ありの機械学習モデルによってリスクの検出が評価されるようになりました。 ユーザーに関連付けられた検出のリスクが低または中の場合でも、このモデルに基づいて、管理者はリスク スコアの高いユーザーを見つけることができます。 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>管理者が Microsoft Authenticator アプリ (パブリック プレビュー) を使用して自分自身のパスワードをリセット可能

**種類:** 変更された機能  
**サービス カテゴリ:** セルフサービスによるパスワードのリセット  
**製品の機能:** ユーザー認証

Azure AD 管理者は、Microsoft Authenticator アプリの通知や、任意のモバイル認証アプリまたはハードウェア トークンのコードを使用して、自分自身のパスワードをリセットできるようになりました。 自分自身のパスワードをリセットするために、管理者は次の方法のうちの 2 つを使用できるようになります:

- Microsoft Authenticator アプリの通知

- その他のモバイル認証アプリ/ハードウェア トークンのコード

- Email

- 音声通話

- テキスト メッセージ

Microsoft Authenticator アプリを使用したパスワードのリセットの詳細については、[Azure AD のセルフサービスでのパスワード リセット - モバイル アプリと SSPR (プレビュー)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview) に関するページを参照してください。

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>新しい Azure AD Cloud Device Administrator ロール (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** デバイスの登録と管理  
**製品の機能:** アクセス制御

クラウド デバイス管理タスクを実行するために、管理者はユーザーを新しい Cloud Device Administrator ロールに割り当てることができます。 Cloud Device Administrators ロールを割り当てられたユーザーは、Azure AD でデバイスを有効化、無効化、削除できることに加えて、Azure portal で Windows 10 の BitLocker キー (ある場合) を読み取ることができます。

ロールとアクセス許可の詳細については、「[Azure Active Directory の管理者ロールの割り当て](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)」を参照してください

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Azure AD で新しいアクティビティ タイムスタンプを使用してデバイスを管理する (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** デバイスの登録と管理  
**製品の機能:** デバイスのライフサイクル管理

Azure AD では、古くなったデバイスを環境から排除するために、定期的に組織のデバイスを更新したり、インベントリから削除したりする必要があります。 このプロセスを支援するために、Azure AD では、新しいアクティビティ タイムスタンプによってデバイスが更新されることで、デバイスのライフサイクルが管理しやすくなりました。

このタイムスタンプを取得および使用する方法については、「[方法:Azure AD で古いデバイスを管理する](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)」を参照してください

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>管理者が各デバイスで利用規約の承諾をユーザーに要求可能

**種類:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス
 
管理者は **[各デバイスでユーザーによる同意が必要]** オプションを有効にすることで、テナントでユーザーが使用しているすべてのデバイスで利用規約の承諾をユーザーに要求できるようになりました。

詳細については、[Azure Active Directory Terms of Use 機能の、デバイスごとの利用規約に関するセクション](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use)を参照してください。

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>管理者が定期的なスケジュールに基づいて利用規約の有効期限を設定可能

**種類:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス
 

管理者は **[期限切れの同意]** オプションを有効にすることで、すべてのユーザーを対象に、利用規約の有効期限を指定の定期スケジュールに基づいて設定できるようになりました。 年に 1 回、半年に 1 回、3 か月に 1 回、または月に 1 回のスケジュールを指定できます。 利用規約が期限切れになったら、ユーザーは再度受け入れる必要があります。

詳細については、[Azure Active Directory Terms of Use 機能の、利用規約の追加に関するセクション](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)を参照してください。

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>管理者が各ユーザーのスケジュールに基づいて利用規約の有効期限を設定可能

**種類:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス

管理者は、ユーザーが利用規約を再承諾しなければならない期間を指定できるようになりました。 たとえば、管理者は、ユーザーが 90 日おきに利用規約を再承諾しなければならないことを指定できます。

詳細については、[Azure Active Directory Terms of Use 機能の、利用規約の追加に関するセクション](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)を参照してください。
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Azure Active Directory のロールに関する新しい Azure AD Privileged Identity Management (PIM) の電子メール

**種類:** 新機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management
 
Azure AD Privileged Identity Management (PIM) を使用しているお客様は、過去 7 日間の次の情報を含むダイジェスト電子メールを毎週受信できるようになりました:

- 最上位の有資格かつ永続的なロール割り当ての概要

- ロールをアクティブ化しているユーザー数

- PIM でロールに割り当てられたユーザー数

- PIM 外でロールに割り当てられたユーザー数

- PIM で "永続化" されたユーザー数

PIM と利用可能な電子メール通知の詳細については、「[PIM での電子メール通知](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)」を参照してください。

---

### <a name="group-based-licensing-is-now-generally-available"></a>グループベースのライセンスの一般提供開始

**種類:** 変更された機能  
**サービス カテゴリ:** その他  
**製品の機能:** Directory

グループベースのライセンスのパブリック プレビューが終了し、一般提供が開始されました。 この一般向けリリースの一環として、この機能のスケーラビリティの向上に加えて、単一ユーザーへのグループベースのライセンス割り当てを再処理できるようになり、Office 365 E3/A3 ライセンスでグループベースのライセンスが使用できるようになりました。

グループベースのライセンスの詳細については、「[Azure Active Directory のグループベースのライセンスとは](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)」を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2018 年 11 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2018 年 11 月に、フェデレーションを使用した以下の 26 の新規アプリのサポートが、アプリ ギャラリーに追加されました:

[CoreStack](https://cloud.corestack.io/site/login)、[HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial)、[GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial)、[Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial)、[eHour](https://getehour.com/try-now)、[Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial)、[Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial)、[DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview)、[Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial)、[Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial)、[Alaya](https://alayagood.com/en/demo/)、[HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial)、[Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial)、[Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial)、[Zenegy for Business Central 365](https://accounting.zenegy.com/)、[Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial)、[IDEO](https://profile.ideo.com/users/sign_up)、[Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial)、[Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial)、[Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial)、[Plex Apps - Classic Test](https://test.plexonline.com/signon)、[Plex Apps – Classic](https://www.plexonline.com/signon)、[Plex Apps - UX Test](https://test.cloud.plex.com/sso)、[Plex Apps – UX](https://cloud.plex.com/sso)、[Plex Apps – IAM](https://accounts.plex.com/)、[CRAFTS - Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration) 

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

## <a name="october-2018"></a>2018 年 10 月

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD のログを Azure Log Analytics (パブリック プレビュー) と連携させることが可能になりました

**種類:** 新機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

Azure AD のログを Azure Log Analytics に転送できるようになりました。 要望の特に多かったこの機能が追加されたことで、ビジネス、運営、セキュリティに関する分析データへのアクセスがこれまで以上に改善され、インフラストラクチャの監視機能も強化されました。 詳しくは、関連のブログ記事 (「[Azure Active Directory Activity logs in Azure Log Analytics now available (Azure Active Directory のアクティビティ ログが Azure Log Analytics で利用できるようになりました)](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843)」) をご覧ください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2018 年 10 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2018 年 10 月に、フェデレーションを使用した以下の 14 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[My Award Points](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial)、[Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial)、ambyint、[MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial)、[BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial)、Dialpad、[ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial)、[RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial)、[Zscaler Three](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial)、[Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial)、[Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial)、[Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial)、[Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial)、[Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services の電子メール通知

**種類:** 新機能  
**サービス カテゴリ:** Azure AD Domain Services  
**製品の機能:** Azure AD Domain Services

Azure AD Domain Services では、マネージド ドメインの構成ミスや問題に関するアラートが、Azure portal を通じて提供されます。 これらのアラートにはステップ バイ ステップ ガイドが含まれているので、サポートへの連絡を必要とすることなく、問題の解決に着手できます。

10 月からは、マネージド ドメインの通知設定をカスタマイズできるようになるので、新しいアラートの発生時に、指定のユーザー グループへ電子メールを自動送信できるようになります。これにより、ポータルで更新プログラムを常時確認する必要がなくなります。

詳しくは、「[Azure AD Domain Services の通知設定](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications)」をご覧ください。

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD ポータルで、ForceDelete ドメイン API を使用してカスタム ドメインを削除できるようになりました 

**種類:** 変更された機能  
**サービス カテゴリ:** ディレクトリ管理  
**製品の機能:** Directory

ForceDelete ドメイン API を使用して、ユーザー、グループ、アプリなどの参照を、カスタム ドメイン名 (contoso.com) から既定の初期ドメイン名 (contoso.onmicrosoft.com) へと非同期的に名前変更することにより、カスタム ドメイン名を削除できるようになりました。

これにより、カスタム ドメイン名が使用されなくなった場合や、そのドメイン名を別の Azure AD で使用する必要が生じた場合に、ドメイン名をよりすばやく削除できるようになりました。

詳しくは、「[カスタム ドメイン名を削除する](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name)」をご覧ください。

---

## <a name="september-2018"></a>2018 年 9 月
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>動的グループの管理者ロールのアクセス許可が更新された

**種類:** 固定  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

特定の管理者ロールが、グループの所有者になる必要なく、動的メンバーシップ ルールの作成と更新を行えるように問題を修正しました。

これらのロールを次に示します。

- 全体管理者

- Intune 管理者

- ユーザー管理者

詳細については、「[動的グループの作成と状態チェックを行う](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)」を参照してください

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>一部のサード パーティ製アプリケーションのシングル サインオン (SSO) 構成設定を簡素化

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO

各アプリケーション構成の固有の性質が原因で、サービスとしてのソフトウェア (SaaS) アプリケーションに向けたシングル サインオン (SSO) の設定が困難であることは、Microsoft でも認識しています。 以下のサードパーティ製 SaaS アプリケーションの SSO 構成設定を自動入力するため、簡素化した構成エクスペリエンスを構築しました。

- Zendesk

- ArcGis Online

- Jamf Pro

このワンクリック エクスペリエンスの使用を開始するには、**Azure portal** > アプリケーションの **SSO 構成**ページにアクセスします。 詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)」を参照してください

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory のデータの保管場所に関するページ

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** GoLocal

**Azure Active Directory のデータの保管場所**に関するページから会社のリージョンを選択すると、Azure AD のすべてのサービスについて、どの Azure データ センターに Azure AD の保存データが格納されているかが表示されます。 この情報は、会社のリージョンに関して、特定の Azure AD サービスに基づいてフィルター処理できます。

この機能へのアクセス方法と機能の詳細については、[Azure Active Directory のデータの保管場所](https://aka.ms/AADDataMap)に関するページを参照してください。

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>[マイ アプリ] アクセス パネルで新しいデプロイ計画を使用可能

**種類:** 新機能  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** SSO

[マイ アプリ] アクセス パネルで使用できる新しいデプロイ計画をご確認ください (https://aka.ms/deploymentplans) 。
[マイ アプリ] アクセス パネルによって、ユーザーは 1 つの場所でアプリケーションの検索とアクセスを行えます。 このポータルでは、ユーザーはセルフ サービスで、アプリケーションとグループへのアクセスを要求したり、他のユーザーに代わってこれらのリソースへのアクセスを管理したりすることなどが可能です。

詳細については、「[マイ アプリ ポータルとは](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)」を参照してください

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Azure portal の [サインイン] ログ ページに [トラブルシューティングおよびサポート] タブを新設

**種類:** 新機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

Azure portal の **[サインイン]** ページの **[トラブルシューティングおよびサポート]** タブは、管理者やサポート エンジニアが、Azure AD のサインインに関連する問題のトラブルシューティングを行う助けとなるように設計されています。この新しいタブには、問題の解決に役立つように、エラー コード、エラー メッセージ、および修復の推奨事項 (ある場合) が表示されます。 問題を解決できない場合は、**クリップボードへのコピー**操作によってサポート チケットを作成する新しい方法も用意されています。この方法で、サポート チケット内のログ ファイルの**要求 ID** と**日付 (UTC)** のフィールドが設定されます。  

![新しいタブが表示されている サインイン ログ](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>動的メンバーシップ ルールを作成するために使用されるカスタム拡張機能プロパティのサポートの強化

**種類:** 変更された機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

この更新プログラムでは、動的ユーザー グループのルール ビルダーにある **[Get custom extension properties] (カスタム拡張機能プロパティを取得する)** リンクをクリックし、一意のアプリ ID を入力すると、ユーザーの動的メンバーシップ ルールの作成時に使用するカスタム拡張機能プロパティの完全な一覧が表示されるようになりました。 この一覧を最新の情報に更新して、そのアプリの新しいカスタム拡張機能プロパティを取得することもできます。

動的メンバーシップ ルール用のカスタム拡張機能プロパティの使用に関する詳細については、[拡張機能プロパティおよびカスタム拡張機能プロパティ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)についてのページを参照してください

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD アプリベースの条件付きアクセス向けの承認されたクライアント アプリの新規追加

**種類:** 変更の計画  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護

以下のアプリが、[承認されたクライアント アプリ](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement)の一覧に示されます。

- Microsoft To-Do

- Microsoft Stream

詳細については、次を参照してください。

- [Azure AD のアプリベースの条件付きアクセス](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Windows 7/8/8.1 のロック画面からのセルフ サービスによるパスワード リセットを新たにサポート

**種類:** 新機能  
**サービス カテゴリ:** SSPR  
**製品の機能:** ユーザー認証

この新しい機能を設定すると、Windows 7、Windows 8、または Windows 8.1 を実行しているデバイスの**ロック**画面から自分のパスワードをリセットするためのリンクがユーザーに表示されます。 そのリンクをクリックすると、ユーザーには、Web ブラウザーを介する場合と同じパスワードのリセット フローが示されます。

詳細については、[Windows 7、8、8.1 からパスワードのリセットを有効にする方法](https://aka.ms/ssprforwindows78)に関するページを参照してください

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>変更通知:承認コードを再利用できなくなる 

**種類:** 変更の計画  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証

2018 年 11 月 15 日以降、Azure AD では、以前使用されていた、アプリの認証コードの受け入れが停止されます。 このセキュリティの変更により、Azure AD と OAuth の仕様が一致するようになります。この変更は、v1 と v2 両方のエンドポイントに適用されます。

お使いのアプリで承認コードを再利用して複数のリソースに対するトークンを取得している場合は、コードを使用して更新トークンを取得した後、その更新トークンを使用して他のリソース用のトークンを追加取得することお勧めします。 承認コードは 1 回しか使用できませんが、更新トークンは複数のリソースで複数回使用できます。 OAuth コード フローの間にアプリで認証コードを再利用しようとすると、invalid_grant エラーが発生します。

このことと、その他のプロトコル関連の変更については、[認証についての新機能の完全な一覧](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes)を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2018 年 9 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2018 年 9 月に、フェデレーションを使用した以下の 16 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial)、[Comeet Recruiting Software](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial)、[Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial)、[ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial)、[Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial)、[JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial)、[Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial)、NavigoCloud、[Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial)、join.me、[ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial)、[Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial)、Riverbed Xirrus EasyPass、[Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial)、Enlyft SSO for Azure、SurveyMonkey、[Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial)、[dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="support-for-additional-claims-transformations-methods"></a>要求変換メソッドの追加サポート

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO

新しい要求変換メソッドの ToLower() と ToUpper() を導入しました。これらは、SAML ベースの **[シングル サインオンの構成]** ページから SAML トークンに適用できます。

詳細については、[Azure AD のエンタープライズ アプリケーションの SAML トークンで発行された要求をカスタマイズする方法](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)についてのページを参照してください

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>SAML ベースのアプリの構成 UI を更新 (プレビュー)

**種類:** 変更された機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO

SAML ベースのアプリの構成 UI が更新された一部として、以下が提供されます。

- SAML ベースのアプリを構成するためのチュートリアル エクスペリエンスの更新。

- 構成において不足している、または正しくないものに関する可視性の向上。

- 証明書の有効期限通知のために複数のメール アドレスを追加する機能。

- 新しい要求変換メソッドの ToLower() と ToUpper() など。

- エンタープライズ アプリのために独自のトークン署名証明書をアップロードする方法。

- SAML アプリの NameID 形式を設定する方法と、ディレクトリ拡張機能として NameID 値を設定する方法。

この更新されたビューをオンにするには、 **[シングル サインオン]** ページ上部の **[Try out our new experience] (新しいエクスペリエンスを試す)** リンクをクリックします。 詳細については、「[チュートリアル:Azure Active Directory による SAML ベースのシングル サインオンをアプリケーションに構成する](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal)」を参照してください。

---

## <a name="august-2018"></a>2018 年 8 月

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Azure Active Directory の IP アドレス範囲の変更

**種類:** 変更の計画  
**サービス カテゴリ:** その他  
**製品の機能:** プラットフォーム

Microsoft では、Azure AD に対してより大規模な IP 範囲の導入を進めています。つまり、ファイアウォール、ルーター、またはネットワーク セキュリティ グループの Azure AD IP アドレス範囲を構成している場合はそれらを更新する必要があります。 Microsoft では、Azure AD で新しいエンドポイントが追加されたときにファイアウォール、ルーター、またはネットワーク セキュリティ グループの IP アドレス範囲の構成を再び変更する必要がないように、この更新を行っています。 

ネットワーク トラフィックは、今後 2 か月にわたってこれらの新しい範囲に移行されます。 中断のないサービスを継続するには、2018 年 9 月 10 日までに、次に示す更新された値を IP アドレスに追加する必要があります。

- 20.190.128.0/18 

- 40.126.0.0/18 

すべてのネットワーク トラフィックが新しい範囲に移行されるまでは古い IP アドレス範囲を削除しないことを強くお勧めします。 移行に関する最新情報や古い範囲を削除できるタイミングについては、「[Office 365 URLs and IP address ranges (Office 365 の URL と IP アドレス範囲)](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)」を参照してください。

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>変更通知:承認コードを再利用できなくなる 

**種類:** 変更の計画  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証

2018 年 11 月 15 日以降、Azure AD では、以前使用されていた、アプリの認証コードの受け入れが停止されます。 このセキュリティの変更により、Azure AD と OAuth の仕様が一致するようになります。この変更は、v1 と v2 両方のエンドポイントに適用されます。

お使いのアプリで承認コードを再利用して複数のリソースに対するトークンを取得している場合は、コードを使用して更新トークンを取得した後、その更新トークンを使用して他のリソース用のトークンを追加取得することお勧めします。 承認コードは 1 回しか使用できませんが、更新トークンは複数のリソースで複数回使用できます。 OAuth コード フローの間にアプリで認証コードを再利用しようとすると、invalid_grant エラーが発生します。

このことと、その他のプロトコル関連の変更については、[認証についての新機能の完全な一覧](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes)を参照してください。
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>セルフサービスのパスワード (SSPR) と Multi-Factor Authentication (MFA) の集中型セキュリティ情報管理

**種類:** 新機能  
**サービス カテゴリ:** SSPR  
**製品の機能:** ユーザー認証
 
この新しい機能を使用すると、ユーザーは SSPR および MFA に対するセキュリティ情報 (電話番号、モバイル アプリなど) を 1 つの場所とエクスペリエンス管理できます。以前は、2 つの異なる場所で行われていました。

この集中型エクスペリエンスは、SSPR または MFA のどちらかを使用しているユーザーでも機能します。 さらに、MFA または SSPR の登録を強制していない組織の場合でも、ユーザーは、組織で許可されている MFA または SSPR のセキュリティ情報の方法に、マイ アプリ ポータルから登録できます。

これは、オプトイン パブリック プレビューです。 管理者は、新しいエクスペリエンスを (必要に応じて) テナント内の特定のグループまたはすべてのユーザーに有効にできます。 集中型エクスペリエンスについて詳しくは、[集中型エクスペリエンスのブログ](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)をご覧ください。

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Azure AD アプリケーション プロキシ アプリでの新しい HTTP 専用 Cookie の設定

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** Access Control

アプリケーション プロキシ アプリには、**HTTP 専用 Cookie** と呼ばれる新しい設定があります。 この設定では、アプリケーション プロキシ アクセス Cookie とセッション Cookie の両方に対する HTTP 応答ヘッダーに HTTPOnly フラグを追加して、クライアント側スクリプトから Cookie へのアクセスを停止し、さらに Cookie のコピーや変更などのアクションを防止することにより、セキュリティを強化します。 このフラグは以前は使用されていませんでしたが、Cookie は常に暗号化され、SSL 接続を使用して送信されることで、不適切な変更から保護されていました。

この設定は、リモート デスクトップなどの ActiveX コントロールを使用するアプリとの互換性はありません。 このような状況の場合は、この設定をオフにすることをお勧めします。

HTTP 専用 Cookie の設定について詳しくは、「[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal)」をご覧ください。

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Azure リソースに対する Privileged Identity Management (PIM) での管理グループのリソースの種類のサポート

**種類:** 新機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management
 
サブスクリプション、リソース グループ、リソース (VM、App Services など) に対して既に行われているのと同じように、管理グループのリソースの種類に対しても Just-In-Time アクティブ化と割り当ての設定を適用できるようになります。 さらに、管理グループに対して管理者アクセスできるロールを持つすべてのユーザーは、PIM でそのリソースを検出して管理できます。

PIM と Azure リソースについて詳しくは、「[Privileged Identity Management を使用した Azure リソースの探索と管理](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)」をご覧ください。
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>アプリケーション アクセス (プレビュー) では、Azure AD ポータルへのより高速なアクセスが提供される

**種類:** 新機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management
 
現在、PIM を使用してロールをアクティブ化すると、アクセス許可が有効になるまでに 10 分以上かかることがあります。 アプリケーション アクセス (現在パブリック プレビュー段階) を使用した場合、管理者はアクティブ化要求が完了するとすぐに Azure AD ポータルにアクセスすることができます。

現時点では、アプリケーション アクセスでサポートされているのは、Azure AD ポータル エクスペリエンスと Azure リソースのみです。 PIM とアプリケーション アクセスについて詳しくは、「[Azure AD Privileged Identity Management とは](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)」をご覧ください。
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2018 年 8 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2018年 8 月に、フェデレーションを使用した以下の 16 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial)、[Bridgeline Unbound](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial)、[Sauce Labs - Mobile and Web Testing](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial)、[Meta Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial)、[Way We Do](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial)、[Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial)、[ProMaster (by Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial)、SchoolBooking、[4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial)、[Dossier](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial)、[N2F - Expense reports](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial)、[Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial)、[SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial)、[ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial)、[eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial)、[Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial)。

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Tableau のネイティブ サポートが Azure AD アプリケーション プロキシで使用できるようになった

**種類:** 変更された機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** Access Control

事前認証プロトコルについての OpenID Connect から OAuth 2.0 Code Grant プロトコルへの更新により、アプリケーション プロキシで Tableau を使用するために追加構成を行う必要がなくなりました。 このプロトコルの変更により、JavaScript と HTML のタグで一般的にサポートされる HTTP のみのリダイレクトを使用することで、アプリケーション プロキシによる最新のアプリのサポートも向上しました。

Tableau のネイティブ サポートについて詳しくは、「[Azure AD Application Proxy now with native Tableau support](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support)」(Tableau をネイティブにサポートするようになった Azure AD アプリケーション プロキシ) をご覧ください。

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Azure Active Directory で B2B ゲスト ユーザーに対する ID プロバイダーとして Google を追加する新しいサポート (プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C

組織において Google とのフェデレーションを設定することにより、招待された Gmail ユーザーは既存の Google アカウントを使用して共有アプリおよびリソースにサインインできます。個人の Microsoft アカウント (MSA) または Azure AD アカウントを作成する必要はありません。

これは、オプトイン パブリック プレビューです。 Google のフェデレーションについて詳しくは、「[Add Google as an identity provider for B2B guest users](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)」(B2B ゲスト ユーザーの ID プロバイダーとして Google を追加する) をご覧ください。

---

## <a name="july-2018"></a>2018 年 7 月

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Azure Active Directory の電子メール通知の機能強化

**種類:** 変更された機能  
**サービス カテゴリ:** その他  
**製品の機能:** ID ライフサイクル管理
 
Azure Active Directory (Azure AD) の設計が更新され、以下のサービスから送信された場合、送信者メール アドレスと送信者表示名が変更されます。
 
- Azure AD アクセス レビュー
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- エンタープライズ アプリの証明書期限切れ通知
- エンタープライズ アプリのサービス プロビジョニング通知
 
メール通知は、次のメール アドレスと表示名から送信されます。

- メール アドレス: azure-noreply@microsoft.com
- 表示名:Microsoft Azure
 
新しい電子メール デザインのいくつかの例と詳細は、「[Azure AD PIM の電子メール通知](https://go.microsoft.com/fwlink/?linkid=2005832)」で参照してください。

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD アクティビティ ログは Azure Monitor から使用できるようになりました。

**種類:** 新機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

Azure AD アクティビティ ログは、Azure Monitor (Azure のプラットフォーム全体の監視サービス) のパブリック プレビューで利用可能になりました。 Azure Monitor は、これらの機能強化に加え、長期保有とシームレスな統合を提供します。

- ログ ファイルを Azure ストレージ アカウントにルーティングすることによる長期保有。

- シームレスな SIEM 統合により、カスタム スクリプトを作成または管理する必要がありません。

- お客様独自のカスタム ソリューション、分析ツール、またはインシデント管理ソリューションとシームレスに統合。

これらの新機能の詳細については、マイクロソフト公式ブログ「[Azure AD activity logs in Azure Monitor diagnostics is now in public preview](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/)」(Azure Monitor 診断の Azure AD アクティビティ ログは現在パブリック プレビュー中です) や [Azure Monitor の Azure Active Directory アクティビティ ログ (プレビュー)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview) に関するマイクロソフトのドキュメントを参照してください。

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Azure AD サインイン レポートに条件付きアクセス情報を追加

**種類:** 新機能  
**サービス カテゴリ:** レポート  
**製品の機能:** ID のセキュリティと保護
 
この更新により、ユーザーがサインインしたときにどのポリシーが評価されるか、また、そのポリシーの結果を確認できるようになりました。 さらに、ユーザーによって使用されるクライアント アプリの種類がレポートに含まれるようになったため、レガシ プロトコルのトラフィックを識別できます。 ユーザーに表示されるエラー メッセージに記載されている相関 ID を条件にレポート エントリ内を検索できるようにもなったため、対応するサインイン要求を特定してトラブルシューティングに利用できます。

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>サインイン アクティビティ ログでレガシ認証を表示

**種類:** 新機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート
 
サインイン アクティビティ ログに **[クライアント アプリ]** フィールドを導入することで、お客様がレガシ認証を使用しているユーザーを確認できるようになりました。 この情報にアクセスするには、Sign-ins MS Graph API を使用するか、Azure AD ポータルのサインイン アクティビティ ログで **[クライアント アプリ]** コントロールを使用してレガシー認証をフィルター処理します。 詳細については、ドキュメントをご覧ください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2018 年 7 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2018年 7 月に、フェデレーションを使用した以下の 16 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial)、[Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial)、[Certain Admin SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial)、PSUC Staging、[iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial)、[Screencast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial)、PowerSchool Unified Classroom、[Eli Onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial)、[Bomgar Remote Support](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial)、[Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial)、[Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial)、[Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial)、[SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial)、[Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial)、[SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial)、[Skills Base](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>新しいユーザー プロビジョニング SaaS アプリの統合 - 2018 年 7 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
Azure AD を使用すると、Dropbox、Salesforce、ServiceNow などの SaaS アプリケーションで、ユーザー ID の作成、保守、削除を自動化できます。 2018 年 7 月、Azure AD アプリ ギャラリーの次のアプリケーションに対して、ユーザー プロビジョニング サポートが追加されました。

- [Cisco Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-spark-provisioning-tutorial)

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

ユーザー プロビジョニングをサポートする Azure AD ギャラリーのすべてのアプリケーションの一覧については、[Azure Active Directory と SaaS アプリケーションの統合](https://aka.ms/appstutorial)に関する記事を参照してください。

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health for Sync - 孤立および重複した属性の同期エラーを修正する簡単な方法

**種類:** 新機能  
**サービス カテゴリ:** AD Connect  
**製品の機能:** 監視とレポート
 
Azure AD Connect Health は、同期エラーを特定し修正するためのセルフサービス修復機能を提供します。 この機能は、重複した属性同期エラーのトラブルシューティングを行い、Azure AD から孤立したオブジェクトを修正します。 この診断には、次のような利点があります。

- 重複した属性の同期エラーを絞り込み、特定の修正プログラムを提供します。

- 1 つの手順でエラーを解決する、Azure AD のシナリオ専用の修正プログラムを適用します。

- この機能を有効にするために、アップグレードや構成の必要はありません。

詳細については、「[重複する属性の同期エラーの診断](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)」を参照してください。

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Azure AD のビジュアルと MSA のサインイン エクスペリエンスを更新

**種類:** 変更された機能  
**サービス カテゴリ:** Azure AD  
**製品の機能:** ユーザー認証

Office 365 や Azure などの Microsoft のオンライン サービス用のサインイン エクスペリエンスを更新しました。 この変更により、画面がすっきりし見やすくなりました。 この変更の詳細については、ブログ 「[Azure AD のサインイン画面に関するアップデートのお知らせ](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/)」を参照してください。

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Azure AD Connect の新しいリリース - 2018 年 7 月

**種類:** 変更された機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理

Azure AD Connect の最新リリースは次のとおりです。 

- バグの修正とサポートの更新 

- Ping フェデレーション統合の一般公開

- 最新の SQL 2012 クライアントに更新 

この更新の詳細については、「[Azure AD Connect:バージョンのリリース履歴](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)」を参照してください

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>使用条件エンドユーザー UI の更新

**種類:** 変更された機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス

TOU エンドユーザー UI の同意の文字列を更新しています。

**現在のテキスト** [tenantName] リソースにアクセスするには、使用条件に同意する必要があります。<br>**新しいテキスト** [tenantName] リソースにアクセスするには、使用条件を読む必要があります。

**現在のテキスト:** 合意を選択すると上記の使用条件すべてに同意することを意味します。<br>**新しいテキスト:** 使用条件を読んで理解したことを確定するには、[同意する] をクリックしてください。

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>パススルー認証で、レガシのプロトコルとアプリケーションをサポート

**種類:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
パススルー認証で、レガシのプロトコルとアプリケーションをサポートできるようになりました。 次の制限事項は完全にサポートされています。

- レガシの Office クライアント アプリケーション、Office 2010、および Office 2013 へのユーザー サインイン (先進認証なし)。

- Office 2010 上でのみ、Exchange ハイブリッド環境で予定表の共有と空き時間情報にアクセスします。

- Skype for Business クライアント アプリケーションへのユーザー サインイン (先進認証なし)。

- PowerShell バージョン 1.0 へのユーザー サインイン。

- iOS 設定アシスタントを使用している Apple Device Enrollment Program (Apple DEP)。 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>セルフサービスのパスワード リセットと多要素認証の集中型セキュリティ情報管理

**種類:** 新機能  
**サービス カテゴリ:** SSPR  
**製品の機能:** ユーザー認証

この新しい機能を使用すれば、ユーザーはセキュリティ情報 (たとえば、電話番号、電子メール アドレス、モバイル アプリなど) のセルフサービス パスワード リセット (SSPR) と多要素認証 (MFA) を 1 つのエクスペリエンスで管理できます。 ユーザーが SSPR と MFA の 2 つの異なるエクスペリエンスのために同じセキュリティ情報を登録する必要がなくなります。 この新しいエクスペリエンスは、SSPR または MFA のいずれかを持つユーザーにも適用されます。

組織で MFA または SSPR 登録を使用していない場合は、ユーザーは **Myapps** ポータルを通してセキュリティ情報を登録できます。 そこから、ユーザーは MFA または SSPR に有効な任意の方法を登録できます。 

これは、オプトイン パブリック プレビューです。 管理者は、新しいエクスペリエンス (必要に応じて) を特定のユーザー グループまたはテナント内のすべてのユーザーに有効にできます。

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Microsoft Authenticator アプリを使用して、パスワードをリセットしたときに本人確認する

**種類:** 変更された機能  
**サービス カテゴリ:** SSPR  
**製品の機能:** ユーザー認証

この機能を使用すると、パスワードをリセットする際に、Microsoft Authenticator (または他の認証アプリ) からの通知またはコードを使用して、管理者以外のユーザーが本人確認できます。 管理者が、このセルフサービスによるパスワード リセット方法をオンにすると、aka.ms/mfasetup または aka.ms/setupsecurityinfo を通してモバイル アプリを登録したユーザーは、パスワードをリセットする際にモバイル アプリを本人確認の方法として使用できます。

モバイル アプリの通知は、パスワードのリセットに 2 つの認証方法を要求するポリシーの一部としてのみオンに設定できます。

---

## <a name="june-2018"></a>2018 年 6 月

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>変更通知:Azure AD アクティビティ ログ API を使用するアプリの委任された承認ワークフローに対するセキュリティの修正

**種類:** 変更の計画  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

セキュリティ強化のため、委任された承認ワークフローを使って [Azure AD アクティビティ ログ API](https://aka.ms/aadreportsapi) にアクセスするアプリのアクセス許可を変更する必要がありました。 この変更は、**2018 年 6 月 26日**までに行われます。

お使いのアプリに Azure AD アクティビティ ログ API を使っているものがある場合は、以下の手順に従って、変更後にアプリで問題が発生しないことを確認してください。

**アプリのアクセス許可を更新するには**

1. Azure Portal にサインインし、 **[Azure Active Directory]** を選択してから **[アプリの登録]** を選択します。
2. Azure AD アクティビティ ログ API を使用しているアプリを選択して **[設定]** を選択し、 **[必要なアクセス許可]** を選んでから、 **[Windows Azure Active Directory]** API を選択します。
3. **[アクセスの有効化]** ブレードの **[委任されたアクセス許可]** 領域で、 **[ディレクトリ データの読み取り]** チェック ボックスをオンにして、 **[保存]** を選択します。
4. **[アクセス許可の付与]** を選択し、 **[はい]** を選択します。
    
    >[!Note]
    >アプリへのアクセス許可の付与は、全体管理者が行う必要があります。

詳細については、「Azure AD レポート API にアクセスするための前提条件」記事の「[アクセス許可を付与する](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions)」セクションを参照してください。

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>PCI DSS コンプライアンスのために TLS 設定を構成して Azure AD サービスに接続する

**種類:** 新機能  
**サービス カテゴリ:** 該当なし  
**製品の機能:** プラットフォーム

トランスポート層セキュリティ (TLS) は、通信する 2 つのアプリケーション間にプライバシーとデータ整合性を提供するプロトコルであり、今日、最も広く展開され、使用されているセキュリティ プロトコルです。

[PCI Security Standards Council](https://www.pcisecuritystandards.org/) は、**2018 年 6 月 30 日**に開始されたコンプライアンスに沿って、新しいより安全なアプリ プロトコルを有効にして、初期バージョンの TLS および Secure Sockets Layer (SSL) を無効化する必要があることを決定しました。 この変更は、Azure AD サービスに接続し、PCI DSS コンプライアンスを必要とする場合には、TLS 1.0 を無効にしなければならないことを意味します。 複数のバージョンの TLS が使用可能ですが、TLS 1.2 は Azure Active Directory Services に利用できる最新バージョンです。 クライアント/サーバーおよびブラウザー/サーバーの両方の組み合わせにおいて、TLS 1.2 に直接移行することを強くお勧めします。

古いブラウザーでは、TLS 1.2 などの新しい TLS バージョンがサポートされない可能性があります。 お使いのブラウザーでどの TLS バージョンがサポートされるかを確認するには、[Qualys SSL Labs](https://www.ssllabs.com/) サイトへ移動して、 **[Test your browser]** をクリックします。 お使いの Web ブラウザーを最新バージョンにアップグレードして、可能であれば TLS 1.2 のみを有効にすることをお勧めします。

**TLS 1.2 を有効にするために、ブラウザーで実行する手順**

- **Microsoft Edge および Internet Explorer (いずれも、Internet Explorer を使用して設定される)**

    1. Internet Explorer を開き、 **[ツール]**  >  **[インターネット オプション]**  >  **[詳細]** の順に選択します。
    2. **[セキュリティ]** 領域で、 **[use TLS 1.2]\(TLS 1.2 を使用する\)** を選択して、 **[OK]** をクリックします。
    3. すべてのブラウザー ウィンドウを閉じて、Internet Explorer を再起動します。 

- **Google Chrome**

    1. Google Chrome を開き、「*chrome://settings/* 」とアドレス バーに入力して、**Enter** キーを押します。
    2. **[詳細]** オプションを展開し、 **[システム]** 領域へ移動して、 **[プロキシ セットアップを開く]** を選択します。
    3. **[インターネットのプロパティ]** ボックスで、 **[詳細]** タブを選択し、 **[セキュリティ]** 領域へ移動して、 **[use TLS 1.2]\(TLS 1.2 を使用する\)** を選択してから、 **[OK]** をクリックします。
    4. すべてのブラウザー ウィンドウを閉じて、Google Chrome を再起動します。

- **Mozilla Firefox**

    1. Firefox を開き、アドレス バーに「*about:config*」と入力してから、**Enter** キーを押します。
    2. *TLS* という語句を検索して、**security.tls.version.max** エントリを選択します。
    3. 値を **3** に設定して、ブラウザーでバージョン TLS 1.2 までを使用するように設定し、 **[OK]** をクリックします。

        >[!NOTE]
        >Firefox バージョン 60.0 では TLS 1.3 をサポートしているため、security.tls.version.max の値を **4** に設定することもできます。

    4. すべてのブラウザー ウィンドウを閉じて、Mozilla Firefox を再起動します。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2018 年 6 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2018年 6 月に、フェデレーションを使用した以下の 15 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial)、[Settling music](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial)、[SAML 1.1 Token enabled LOB App](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial)、[Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial)、[Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial)、[Endpoint Backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial)、[Skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial)、Smartway2、[TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial)、[Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial)、[Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial)、[SharePoint on-premises](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial)、[ForeSee CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial)、[Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial)、[ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)」を参照してください。 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>パブリック プレビューで使用可能な Azure AD Password Protection

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ユーザー認証

Azure AD Password Protection を使用して、簡単に推測できるパスワードをお使いの環境から排除できます。 これらのパスワードを排除すると、パスワード スプレー タイプの攻撃に侵害されるリスクが低下します。

具体的には、Azure AD Password Protection は次のような機能を提供します。

- Azure AD および Windows Server Active Directory (AD) の両方で組織のアカウントを保護する。 
- 最も一般的に使用されている 500 を超えるパスワード一覧のパスワードと、それらのパスワードの 100 万を超える代替文字のバリエーションを、ユーザーが使用しないようにする。
- Azure AD およびオンプレミスの Windows Server AD の両方に対して、Azure AD ポータルの単一の場所から Azure AD Password Protection を管理する。

Azure AD Password Protection の詳細については、「[組織内の不適切なパスワードを排除する](https://aka.ms/aadpasswordprotectiondocs)」を参照してください。

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>使用条件の作成中に作成された、新しい "すべてのゲスト" 条件付きアクセス ポリシー テンプレート

**種類:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス

使用条件の作成中に、"すべてのゲスト" と "すべてのアプリ" のための新しい条件付きアクセス ポリシー テンプレートも作成されます。 この新しいポリシー テンプレートでは、新しく作成された ToU を適用し、ゲストの作成および強制プロセスを合理化します。

詳しくは、「[Azure Active Directory Terms of Use 機能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)」を参照してください。

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>使用条件の作成中に作成された、新しい "カスタム" 条件付きアクセス ポリシー テンプレート

**種類:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス

使用条件の作成中に、新しい "カスタム" 条件付きアクセス ポリシー テンプレートも作成されます。 この新しいポリシー テンプレートでは、ToU を作成してから、すぐに条件付きアクセス ポリシーの作成ブレードに移動できます。ポータル経由で、手動で移動する必要はありません。

詳しくは、「[Azure Active Directory Terms of Use 機能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)」を参照してください。

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication のデプロイに関する新しい包括的なガイダンス

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** ID のセキュリティと保護
 
組織に Azure Multi-Factor Authentication (MFA) をデプロイする方法に関する新しいステップ バイ ステップ ガイドを公開しました。

MFA のデプロイ ガイドを表示するには、GitHub の [Identity Deployment Guides](https://aka.ms/DeploymentPlans) (ID デプロイ ガイド) リポジトリに移動します。 デプロイ ガイドに関するフィードバックを提出するには、[Deployment Plan Feedback (デプロイ プランに関するフィードバック) フォーム](https://aka.ms/deploymentplanfeedback)を使用してください。 デプロイ ガイドに関する質問がある場合は、[IDGitDeploy](mailto:idgitdeploy@microsoft.com) までお問い合わせください。

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>パブリック プレビューにある Azure AD の委任されたアプリ管理ロール

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** Access Control

管理者は、グローバル管理者ロールを割り当てずに、アプリ管理タスクを委任できるようになりました。 新しいロールと機能は次のとおりです。

- **新しい標準 Azure AD 管理者ロール:**

    - **アプリケーション管理者。** 登録、SSO 設定、アプリの割り当てとライセンス付与、アプリのプロキシ設定、および承認 (Azure AD リソースを除く) を含め、すべてのアプリの全側面を管理する権限が付与されます。

    - **クラウド アプリケーション管理者。** アプリ プロキシではオンプレミス アクセスを提供していないため、アプリ プロキシを除くすべてのアプリケーション管理者の権限が付与されます。

    - **アプリケーション開発者。** **[allow users to register apps]\(ユーザーによるアプリ登録を許可する\)** オプションが無効になっている場合でも、アプリ登録を作成する権限を付与します。

- **所有権 (アプリ登録ごと、および企業アプリごとに設定し、グループ所有権のプロセスとほぼ同じ):**
 
    - **アプリ登録の所有者。** アプリ マニフェストや別の所有者の追加など、所有するアプリ登録の全側面を管理する権限が付与されます。

    - **エンタープライズ アプリ所有者。** SSO 設定、アプリの割り当て、および承認 (Azure AD リソースを除く) を含め、所有するエンタープライズ アプリの多くの側面を管理する権限が付与されます。

パブリック プレビューの詳細については、ブログ「[Azure AD delegated application management roles are in public preview!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/)」(Azure AD の委任されたアプリケーション管理ロールをパブリック プレビューで確認しよう!) を参照してください。 ロールとアクセス許可の詳細については、「[Azure Active Directory の管理者ロールの割り当て](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)」を参照してください。

---

## <a name="may-2018"></a>2018 年 5 月

### <a name="expressroute-support-changes"></a>ExpressRoute のサポートの変更

**タイプ:** 変更の計画  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** プラットフォーム  

Azure Active Directory (Azure AD) などのサービスとしてのソフトウェア オファーは、インターネット経由で直接アクセスすることで最適に動作するように設計されており、ExpressRoute または他のプライベート VPN トンネルは必要ありません。 このため、**2018 年 8 月 1日**をもって、Azure パブリック ピアリングおよび Microsoft ピアリングの Azure コミュニティを使用する Azure AD サービスに対する ExpressRoute のサポートは停止します。 この変更によって影響を受けるサービスでは、Azure AD トラフィックが ExpressRoute からインターネットに段階的に移行します。

サポートは変更されますが、認証トラフィック用に専用回線セットを使用する必要がある状況がまだ存在することも認識しています。 このため、Azure AD は、"他の Office 365 Online サービス" コミュニティとの Microsoft ピアリングでまだ ExpressRoute とサービスを使用しているテナント IP 範囲ごとの制限を引き続きサポートします。 お使いのサービスが影響を受け、ExpressRoute が必要な場合は、次の操作を行う必要があります。

- **Azure パブリック ピアリングの場合。** Microsoft ピアリングに移行し、**その他の Office 365 Online サービス (12076:5100)** コミュニティにサインアップします。 Azure パブリック ピアリングから Microsoft ピアリングに移行する方法について詳しくは、「[パブリック ピアリングを Microsoft ピアリングに移行する](https://docs.microsoft.com/azure/expressroute/how-to-move-peering)」をご覧ください。

- **Microsoft ピアリングの場合。** **その他の Office 365 Online サービス (12076:5100)** コミュニティにサインアップします。 ルーティングの要件について詳しくは、「ExpressRoute のルーティングの要件」の「[BGP コミュニティのサポート](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp)」をご覧ください。

専用回線の使用を続ける必要がある場合は、Microsoft アカウント チームに連絡し、**その他の Office 365 Online サービス (12076:5100)** コミュニティを使用する承認を取得する方法を確認する必要があります。 MS Office 管理レビュー委員会は、回線が必要かどうかを検証し、回線を保持することによる技術的な影響をお客様が理解していることを確認します。 承認されていないサブスクリプションが Office 365 のルート フィルターを作成しようとすると、エラー メッセージが表示されます。 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>TOU の管理シナリオ用の Microsoft Graph API

**タイプ:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** 開発者エクスペリエンス
 
Azure AD Terms of Use の管理操作のための Microsoft Graph API が追加されました。 使用条件オブジェクトを作成、更新、削除できます。

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Azure AD のマルチ テナント エンドポイントを Azure AD B2C の ID プロバイダーとして追加

**タイプ:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 
カスタム ポリシーを使用して、Azure AD の共通エンドポイントを ID プロバイダーとして Azure AD B2C に追加できるようになりました。 これにより、アプリケーションにサインインするすべての Azure AD ユーザー用のエントリ ポイントを 1 つにまとめることができます。 詳細については、[Azure Active Directory B2C: カスタム ポリシーを使用して、ユーザーがマルチテナント Azure AD ID プロバイダーにサインインできるようにする](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom)に関するページを参照してください。

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>My Apps Sign-in Extension と Azure AD アプリケーション プロキシを使い、どこからでも内部 URL を使用してアプリにアクセス可能

**タイプ:** 新機能  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** SSO
 
ユーザーが企業ネットワークの外部にいる場合でも、Azure AD 向けの My Apps Sign-in Extension を使用して、内部 URL を使ってアプリケーションにアクセスできるようになりました。 これは、Azure AD アプリケーション プロキシを使用して発行されたすべてのアプリケーションで動作し、アクセス パネル ブラウザー拡張機能もインストールされているブラウザーで動作します。 URL リダイレクト機能は、ユーザーが拡張機能にログインすると自動的に有効になります。 この拡張機能は、[Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)、[Chrome](https://go.microsoft.com/fwlink/?linkid=866367)、[Firefox](https://go.microsoft.com/fwlink/?linkid=866366) でダウンロードして使用できます。

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory - ヨーロッパ内のデータ (ヨーロッパのお客様向け)

**タイプ:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** GoLocal

ヨーロッパのお客様は、プライバシー法とヨーロッパの法律を満たすために、各自のデータをヨーロッパ内にとどめる必要があり、ヨーロッパのデータセンターの外部にデータをレプリケートすることはできません。 こちらの[記事](https://go.microsoft.com/fwlink/?linkid=872328)に、ヨーロッパ内に保存される ID 情報の具体的詳細と、ヨーロッパのデータセンターの外部で保存される情報の詳細が説明されています。 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>新しいユーザー プロビジョニング SaaS アプリの統合 - 2018 年 5 月

**タイプ:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
Azure AD を使用すると、Dropbox、Salesforce、ServiceNow などの SaaS アプリケーションで、ユーザー ID の作成、保守、削除を自動化できます。 2018 年 5 月、Azure AD アプリ ギャラリーの次のアプリケーションに対して、ユーザー プロビジョニング サポートが追加されました。

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Cornerstone OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Azure AD ギャラリーにある、ユーザー プロビジョニングをサポートするすべてのアプリケーションの一覧については、[https://aka.ms/appstutorial](https://aka.ms/appstutorial) を参照してください。

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>グループとアプリの Azure AD アクセス レビューで定期レビューが可能

**タイプ:** 新機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** ガバナンス
 
グループとアプリのアクセス レビューが Azure AD Premium P2 の一部として一般公開されました。  管理者は、グループ メンバーシップとアプリケーション割り当てのアクセス レビューを、毎月または四半期などの定期的な間隔で自動的に繰り返すように構成できるようになります。

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Azure AD アクティビティ ログ (サインインと監査) を MS Graph から利用可能

**タイプ:** 新機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート
 
Azure AD アクティビティ ログ (サインイン ログと監査ログを含みます) を MS Graph から利用できるようになりました。 これらのログにアクセスするための MS Graph 経由の 2 つのエンドポイントが公開されています。 Azure AD Reporting API を開始するためのプログラムによるアクセスについては、こちらの[ドキュメント](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)を参照してください。 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>B2B のredemption エクスペリエンスと組織からの離脱

**タイプ:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C

**ジャスト イン タイム利用:** B2B API を使用してリソースをゲスト ユーザーと共有したら、特別な招待メールを送信する必要はありません。 ほとんどの場合、ゲスト ユーザーはリソースにアクセスでき、ジャスト イン タイムで利用エクスペリエンスを体験できます。 電子メールの見逃しによる影響は発生しなくなります。 ゲスト ユーザーに「システムが送信した利用リンクをクリックしましたか」と質問する必要はありません。 つまり、SPO が招待マネージャーを使用すると、利用状態に関係なく、すべてのユーザー (内部および外部) のクラウドの添付ファイルで同一の正規 URL を使用できます。

**最新の利用エクスペリエンス:** 画面分割された利用ランディング ページはなくなりました。 サードパーティのアプリと同じように、招待側組織のプライバシーに関する声明を含む最新の同意エクスペリエンスがユーザーに提供されます。

**ゲスト ユーザーは組織を離れることが可能:** ユーザーは、組織との関係が終了したら、その組織からの離脱を自分で実行できます。 招待側組織の管理者に連絡して "削除" を依頼する必要も、サポート チケットを提出する必要もありません。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2018 年 5 月

**タイプ:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合
 
2018年 5 月に、フェデレーションを使用した以下の 18 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial)、Infogix Data3Sixty Govern、[Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial)、[Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial)、[KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial)、[Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial)、[LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial)、[Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial)、[Montage Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial)、[まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial)、OpenReel、[Arc Publishing - SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial)、[PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial)、[iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial)、[Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial)、[Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial)、[Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial)、[Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。

アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)」を参照してください。

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Azure Active Directory のデプロイ手順に関する新しいガイダンス

**タイプ:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** Directory
 
Azure Active Directory (Azure AD) のデプロイ方法に関する新しいステップ バイ ステップ ガイド。セルフサービスによるパスワードのリセット (SSPR)、シングル サインオン (SSO)、条件付きアクセス (CA)、アプリ プロキシ、ユーザー プロビジョニング、パススルー認証 (PTA) に対する Active Directory フェデレーション サービス (ADFS)、およびパスワード ハッシュの同期 (PHS) に対する ADFS が含まれます。

デプロイ ガイドを表示するには、GitHub の [Identity Deployment Guides](https://aka.ms/DeploymentPlans) (ID デプロイ ガイド) リポジトリに移動します。 デプロイ ガイドに関するフィードバックを提出するには、[Deployment Plan Feedback (デプロイ プランに関するフィードバック) フォーム](https://aka.ms/deploymentplanfeedback)を使用してください。 デプロイ ガイドに関する質問がある場合は、[IDGitDeploy](mailto:idgitdeploy@microsoft.com) までお問い合わせください。

---

### <a name="enterprise-applications-search---load-more-apps"></a>エンタープライズ アプリケーション検索 - 読み込むアプリを増やす

**タイプ:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO
 
アプリケーション/サービス プリンシパルが見つかりませんか? すべてのアプリケーションが表示されるように、エンタープライズ アプリケーションにより多くのアプリケーションを読み込む機能が追加されました。 既定では、20 のアプリケーションが表示されます。 **[さらに読み込む]** をクリックすると、さらにアプリケーションが表示されるようになりました。 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>AADConnect の 5 月のリリースには、PingFederate との統合のパブリック プレビュー、重要なセキュリティ更新プログラム、多くのバグ修正、新しい優れたトラブルシューティング ツールが含まれています。 

**タイプ:** 変更された機能  
**サービス カテゴリ:** AD Connect  
**製品の機能:** ID ライフサイクル管理
 
AADConnect の 5 月のリリースには、PingFederate との統合のパブリック プレビュー、重要なセキュリティ更新プログラム、多くのバグ修正、新しい優れたトラブルシューティング ツールが含まれています。 [こちら](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190)でリリース ノートを確認できます。

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD アクセス レビュー: 自動適用

**タイプ:** 変更された機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** ガバナンス

グループとアプリのアクセス レビューが Azure AD Premium P2 の一部として一般公開されました。 管理者は、アクセス レビューの完了時にグループまたはアプリのレビュー担当者による変更が自動的に適用されるように構成できます。 さらに、管理者は、レビュー担当者が応答しなかった場合、アクセスが削除された場合、アクセスが維持された場合、またはシステムの推奨事項が採用された場合に、ユーザーの継続中のアクセスをどのように処理するかを指定できます。 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-responsemode-for-new-apps"></a>新しいアプリでは query response_mode を使用して ID トークンを返すことはできません。 

**種類:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
2018 年 4 月 25 日以降に作成されたアプリは、**query** response_mode を使用して **id_token** を要求することはできません。  これにより、OIDC 仕様による Azure AD のインラインが実現し、アプリの攻撃対象領域を削減できます。  2018 年 4 月 25 日より前に作成されたアプリによる **query** response_mode と **id_token** の response_type の使用はブロックされません。  AAD に id_token を要求した場合に返されるエラーは **AADSTS70007: ‘query’ is not a supported value of ‘response_mode’ when requesting a token** (AADSTS70007: トークン要求時の ‘query’ は ‘response_mode’ のサポートされている値ではありません) です。

**fragment** と **form_post** response_modes は引き続き動作します。新しいアプリケーション オブジェクトを作成する (たとえば、アプリ プロキシで使用する) 場合は、新しいアプリケーションが作成される前にいずれかの response_modes が使用されることを確認してください。  

---
 
## <a name="april-2018"></a>2018 年 4 月 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C アクセス トークンが GA に

**タイプ:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C 

Azure AD B2C によってセキュリティで保護されている Web API にアクセス トークンを使用してアクセスできるようになりました。 この機能はパブリック プレビューから GA に移行します。 Azure AD B2C アプリケーションと Web API を構成するための UI エクスペリエンスが強化され、他の小規模な改善が加えられました。
 
詳細については、「[Azure AD B2C: アクセス トークンの要求](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens)」を参照してください。

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>SAML ベース アプリケーションのシングル サインオン構成をテストする

**タイプ:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO

SAML ベースの SSO アプリケーションを構成するときに、構成ページで統合をテストできます。 サインイン時にエラーが発生した場合には、テスト エクスペリエンスにエラーを提供でき、特定の問題を解決するための解決手順が Azure AD から示されます。

詳細については、次を参照してください。

- [Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Azure AD Terms of Use にユーザーごとのレポートが設けられた

**種類:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** コンプライアンス
 
管理者が特定の使用条件 (ToU) を選択し、その ToU に同意したすべてのユーザーと、同意された日時を確認できるようになりました。

詳細については、[Azure AD Terms of Use 機能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)に関するページを参照してください。

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: AD FS エクストラネット ロックアウト保護の危険な IP 

**タイプ:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** 監視とレポート

Connect Health で、U/P ログインの失敗のしきい値を超える IP アドレスを時間単位または日単位で検出する機能がサポートされました。 この機能によって次のことが可能になります。

- カスタマイズ可能なしきい値に基づいて時間単位または日単位で生成される、失敗したログインの IP アドレスと回数を示す包括的なレポート。
- 特定の IP アドレスが U/P ログイン失敗のしきい値 (時間単位または日単位) をいつ超えたかを示す、電子メール ベースのアラート。
- データの詳しい分析を実行するためのダウンロード オプション

詳細については、[危険な IP のレポート](https://aka.ms/aadchriskyip)に関するページを参照してください。

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>メタデータ ファイルまたは URL を使用した簡単なアプリ構成

**タイプ:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO

管理者は [エンタープライズ アプリケーション] ページで、AAD ギャラリーに含まれるアプリケーションとギャラリーに含まれないアプリケーション向けに SAML ベースのサインインを構成するための SAML メタデータ ファイルをアップロードできます。

さらに、Azure AD アプリケーションのフェデレーション メタデータ URL を使用して、対象となるアプリケーションで SSO を構成できます。

詳細については、「[Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)」を参照してください。

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Azure AD Terms of Use が一般公開に

**タイプ:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** コンプライアンス
 

Azure AD Terms of Use がパブリック プレビューから一般公開に移動されました。

詳細については、[Azure AD Terms of Use 機能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)に関するページを参照してください。

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>B2B ユーザーに対する特定組織からの招待を許可またはブロックする

**タイプ:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C
 

Azure AD B2B コラボレーションで共有とコラボレーションを行う相手の取引先組織を指定できるようになりました。 そのために、特定の許可ドメインまたは拒否ドメインを指定したリストを作成できます。 これらの機能を使用してドメインをブロックすると、従業員はそのドメイン内のユーザーに招待を送信できなくなります。

これは、承認済みユーザーのエクスペリエンスを円滑に保ちながら、リソースに対するアクセスを制御するのに役立ちます。

この B2B コラボレーション機能はすべての Azure Active Directory ユーザーが利用でき、条件付きアクセスや ID 保護などの Azure AD Premium 機能と連携させて、外部のビジネス ユーザーによるサインインやアクセス取得のタイミングと方法をより細かく制御することができます。

詳細については、「[B2B ユーザーに対する特定組織からの招待を許可またはブロックする](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list)」を参照してください。

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ

**タイプ:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2018年 4 月に、フェデレーションを使用した以下の 13 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

Criterion HCM、[FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial)、[Secret Server (On-Premises)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial)、[Dynamic Signal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial)、[mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial)、[OrgChart Now](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial)、[Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial)、[AppNeta Performance Monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial)、[Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial)、[Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial)、[Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial)、Shelf、[SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。

アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)」を参照してください。

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>オンプレミスのアプリケーションに対するアクセスを Azure AD の B2B ユーザーに付与する (パブリック プレビュー)

**タイプ:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C

Azure Active Directory (Azure AD) B2B コラボレーション機能を使用して取引先組織のゲスト ユーザーを Azure AD に招待している組織が、これらの B2B ユーザーにオンプレミスのアプリケーションへのアクセスを提供できるようになりました。 これらのオンプレミスのアプリケーションでは、Kerberos の制約付き委任 (KCD) と共に SAML ベースの認証または統合 Windows 認証 (IWA) を使用できます。

詳細については、「[Azure AD の B2B ユーザーにオンプレミスのアプリケーションへのアクセスを許可する](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises)」を参照してください。

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Azure Marketplace から SSO 統合チュートリアルを入手する

**タイプ:** 変更された機能  
**サービス カテゴリ:** その他  
**製品の機能:** サード パーティ統合

[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) にリストされているアプリケーションが SAML ベースのシングル サインオンをサポートしている場合、 **[今すぐ入手する]** をクリックすると、そのアプリケーションに関連する統合チュートリアルが表示されます。 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>SaaS アプリケーションへの Azure AD 自動ユーザー プロビジョニングのパフォーマンスが向上

**タイプ:** 変更された機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
以前は、(Salesforce、ServiceNow、Box などの) SaaS アプリケーション向けの Azure Active Directory ユーザー プロビジョニング コネクタを使用するお客様は、Azure AD テナントに含まれるユーザーとグループが 100,000 を超え、プロビジョニングが必要なユーザーを判別するためにユーザーとグループの割り当てを使用している場合に、パフォーマンスが低下することがありました。

2018 年 4 月 2 日に Azure AD プロビジョニング サービスに非常に重要性の高いパフォーマンス強化がデプロイされ、Azure Active Directory とターゲット SaaS アプリケーションの間における初期同期の実行に必要な時間が大幅に短縮されました。

その結果、これまではアプリの初期同期に多くの日数を要したり、完了に至らなかったりした多くのお客様が、わずか数分または数時間でこれを完了できるようになりました。

詳細については、「[プロビジョニング中の動作](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning)」を参照してください。

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>ハイブリッド Azure AD 参加マシンの Windows 10 ロック画面からのセルフ サービスによるパスワードのリセット

**タイプ:** 変更された機能  
**サービス カテゴリ:** セルフサービスによるパスワードのリセット  
**製品の機能:** ユーザー認証
 
Windows 10 SSPR 機能が更新され、ハイブリッド Azure AD に参加しているマシンがサポートされるようになりました。 この機能は Windows 10 RS4 で利用でき、ユーザーは Windows 10 マシンのロック画面からパスワードをリセットできます。 この機能を利用できるのは、セルフ サービスのパスワード リセットを有効にして登録したユーザーです。

詳細については、「[ログイン画面からの Azure AD パスワード リセット](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows)」を参照してください。

---

## <a name="march-2018"></a>2018 年 3 月
 
### <a name="certificate-expire-notification"></a>証明書の期限切れ通知

**タイプ:** 固定  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO
 
ギャラリーのアプリケーションまたはギャラリー以外のアプリケーションの証明書の有効期限が近づくと、Azure AD は通知を送信します。 

SAML ベースのシングル サインオン対応に構成されたエンタープライズ アプリケーションを使う一部のユーザーは、通知を受け取りませんでした。 この問題が解決されました。 Azure AD は、証明書が期限切れになる 7 日前、30 日前、60 日前に通知を送信します。 このイベントは監査ログで確認できます。 

詳細については、次を参照してください。

- [Azure Active Directory でのフェデレーション シングル サインオンの証明書の管理](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Azure Active Directory ポータルの監査アクティビティ レポート](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Azure AD B2C での Twitter および GitHub の ID プロバイダー

**タイプ:** 新機能  
**サービス カテゴリ:** B2C - コンシューマー ID 管理  
**製品の機能:** B2B/B2C
 
Azure AD B2C の ID プロバイダーとして、Twitter または GitHub を追加できるようになりました。 Twitter はパブリック プレビューから GA に移行します。 GitHub はパブリック プレビューでリリースされます。

詳しくは、「[Azure AD B2B コラボレーションとは](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)」をご覧ください。
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>iOS および Android 用の、Azure AD アプリケーション ベースの条件付きアクセスを使用した、Intune Managed Browser によるブラウザー アクセスの制限

**種類:** 新機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護
 
**パブリック プレビュー中です。**

**Intune Managed Browser SSO:** 従業員が (Microsoft Outlook などの) ネイティブ クライアント全体でシングル サインオンを使用できます。また、Azure AD に接続されたすべてのアプリで Intune Managed Browser を使用できます。

**Intune Managed Browser の条件付きアクセスのサポート:** アプリケーションベースの条件付きアクセス ポリシーを使用して、従業員に Intune Managed Browser の使用を求めることができます。

詳細については、この[ブログ記事](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/)をご覧ください。

詳細については、次を参照してください。

- [アプリケーションベースの条件付きアクセスの設定](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Managed Browser ポリシーの構成](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>PowerShell GA モジュールでのアプリ プロキシ コマンドレット

**タイプ:** 新機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** Access Control
 
アプリケーション プロキシ コマンドレットのサポートが、PowerShell GA モジュールに組み込まれました。 この機能を使うには、PowerShell モジュールを最新の状態にしておく必要があります。1 年以上古いモジュールでは、一部のコマンドレットが動作しない可能性があります。 

詳しくは、「[AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0)」をご覧ください。
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Office 365 のネイティブ クライアントが、非対話型のプロトコルを使ってシームレス SSO によりサポートされる

**タイプ:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
Office 365 ネイティブ クライアント (バージョン 16.0.8730.xxxx 以降) を使うユーザーは、シームレス SSO によるサイレント サインオン エクスペリエンスを利用できます。 このサポートは、Azure AD への非対話型プロトコル (WS-Trust) の追加によって提供されます。

詳しくは、「[ネイティブ クライアントでのシームレス SSO によるサインインのしくみ](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)」をご覧ください。
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>アプリケーションがサインイン要求を Azure AD のテナント エンドポイントに送信する場合、シームレス SSO によるサイレント サインオン エクスペリエンスがユーザーに提供される

**タイプ:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
アプリケーション (`https://contoso.sharepoint.com` など) がサインイン要求を Azure AD の共通エンドポイント (`https://login.microsoftonline.com/common/<...>`) ではなく、Azure AD のテナント エンドポイント (つまり、`https://login.microsoftonline.com/contoso.com/<..>` または `https://login.microsoftonline.com/<tenant_ID>/<..>`) に送信する場合、シームレス SSO でサイレント サインオン エクスペリエンスがユーザーに提供されます。

詳しくは、「[Azure Active Directory シームレス シングル サインオン](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)」をご覧ください。 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>シームレス SSO をロールアウトするには、以前のように 2 つの URL ではなく、1 つの Azure AD URL だけをユーザーのイントラネット ゾーンの設定に追加する必要がある

**タイプ:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
シームレス SSO をユーザーにロールアウトするには、Active Directory のグループ ポリシーを使って、ユーザーのイントラネット ゾーンの設定にただ 1 つの Azure AD URL `https://autologon.microsoftazuread-sso.com` を追加する必要があります。 以前は、2 つの URL を追加する必要がありました。

詳しくは、「[Azure Active Directory シームレス シングル サインオン](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)」をご覧ください。 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ

**タイプ:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2018年 3 月に、フェデレーションを使用した以下の 15 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial)、[CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial)、Wrike、[SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial)、Assistant by FirstAgenda、[YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial)、Vtiger CRM、inwink、[Amplitude](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial)、[Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial)、[ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial)、[Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial)、[Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial)、[Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial)、[Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial)。
 
アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。

アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)」を参照してください。 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>Azure リソース向け PIM の一般公開

**タイプ:** 新機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management
 
ディレクトリ ロールに Azure AD Privileged Identity Management を使っている場合は、サブスクリプション、リソース グループ、仮想マシン、および Azure Resource Manager によってサポートされるその他のリソースなどの Azure リソース ロールに、PIM の期限付きアクセスと割り当ての機能を使うことができるようになりました。 Just-In-Time でロールをアクティブ化するときに多要素認証を適用し、承認済みの変更期間と連携してアクティブ化をスケジュールします。 さらに、このリリースでは、更新された UI、承認ワークフロー、まもなく期限切れになるロールの延長と期限が切れたロールの更新など、パブリック プレビュー期間中には使用できなかった機能強化が追加されています。

詳しくは、「[Azure リソース向けの PIM (プレビュー)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)」をご覧ください
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>アプリ トークンへのオプション要求の追加 (パブリック プレビュー)

**タイプ:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
Azure AD アプリでは、JWT トークンまたは SAML トークンでカスタム要求またはオプション要求を要求できるようになりました。  これらは、サイズまたは適用性の制約のために、既定ではトークンに含まれないユーザーまたはテナントに関する要求です。  この機能は現在、v1.0 および v2.0 エンドポイントの Azure AD アプリに対するパブリック プレビューです。  追加できる要求および要求を行うためのアプリケーション マニフェストの編集方法については、ドキュメントをご覧ください。  

詳しくは、「[Optional claims in Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)」(Azure AD でのオプションの要求) をご覧ください。
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>より安全な OAuth フローのための Azure AD による PKCE のサポート

**タイプ:** 新機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
Azure AD のドキュメントが更新されて、PKCE のサポートが記述されるようになりました。この機能は、OAuth 2.0 承認コード付与フローでのより安全な通信に対応します。  S256 とプレーンテキストの両方の code_challenge が、v1.0 と v2.0 のエンドポイントでサポートされます。 

詳しくは、「[承認コードを要求する](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code)」をご覧ください。 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-getworkers-api"></a>Workday Get_Workers API で使用可能なすべてのユーザー属性値のプロビジョニングのサポート

**タイプ:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
Workday から Active Directory と Azure AD への受信プロビジョニングのパブリック プレビューで、Workday Get_Workers API で使用可能なすべての属性値の抽出とプロビジョニングがサポートされるようになりました。 これにより、Workday 受信プロビジョニング コネクタの初期バージョンに付属するもの以外に、何百もの標準とカスタム属性のサポートが追加されます。

詳細については、次を参照してください。[Workday のユーザー属性リストをカスタマイズする](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>動的から静的または静的から動的への、グループ メンバーシップの変更

**タイプ:** 新機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション
 
グループのメンバーシップの管理方法を変更することができます。 これは、システムで同じグループの名前と ID を保持する場合に便利です。グループへの既存の参照は有効のままであるため、新しいグループを作成する場合にそれらの参照を更新する必要がありません。
Azure AD 管理センターが更新されて、この機能がサポートされるようになりました。 現在では、ユーザーは、動的なメンバーシップから割り当て済みのメンバーシップに、またはその逆に、既存のグループを変換できます。 既存の PowerShell コマンドレットも引き続き使用できます。

詳細については、「[Azure Active Directory の動的グループ メンバーシップ ルール](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)」を参照してください。

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>シームレス SSO でのサインアウト動作の向上

**タイプ:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
以前は、ユーザーは、Azure AD によってセキュリティ保護されたアプリケーションから明示的にサインアウトした場合であっても、ドメイン参加デバイスから企業ネットワーク内の Azure AD アプリケーションに再びアクセスしようとすると、シームレス SSO を使うサインインに自動的に戻りました。 この変更により、サインアウトがサポートされるようになります。  これにより、ユーザーは、シームレス SSO を使って自動的にサインインするのではなく、再度のサインインに使うものとして同じ Azure AD アカウントまたは異なる Azure AD アカウントを選ぶことができます。

詳しくは、「[Azure Active Directory シームレス シングル サインオン](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)」をご覧ください
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>アプリケーション プロキシ コネクタと バージョン 1.5.402.0 のリリース

**タイプ:** 変更された機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** ID のセキュリティ & 保護
 
このバージョンのコネクタは、11 月中に徐々にロールアウトされます。 この新しいバージョンのコネクタには、次の変更が含まれています。

- コネクタは、サブドメイン レベルではなくドメイン レベルで Cookie を設定するようになりました。 これにより、いっそう滑らかな SSO エクスペリエンスが提供され、冗長な認証プロンプトが表示されなくなります。
- チャンク エンコード要求のサポート
- 強化されたコネクタ正常性の監視 
- 複数のバグ修正と安定性の向上

詳しくは、「[Azure AD アプリケーション プロキシ コネクタを理解する](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)」をご覧ください。
 
---

## <a name="february-2018"></a>2018 年 2 月
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>ユーザーとグループの管理に関するナビゲーションの改善

**タイプ:** 変更の計画  
**サービス カテゴリ:** ディレクトリ管理  
**製品の機能:** Directory

ユーザーとグループを管理する際のナビゲーションの操作性が向上しています。 ディレクトリの概要から全ユーザーの一覧に直接移動できるようになりました。削除済みのユーザーの一覧にもアクセスしやすくなっています。 また、全グループの一覧にもディレクトリの概要から直接移動できるようになり、グループ管理の設定にアクセスしやすくなりました。 さらに、ディレクトリの概要ページから、ユーザーやグループ、エンタープライズ アプリケーション、アプリの登録を検索することもできます。 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>21Vianet (Azure China 21Vianet) が運用する Microsoft Azure でサインイン レポートと監査レポートが利用可能に

**タイプ:** 新機能  
**サービス カテゴリ:** Azure Stack  
**製品の機能:** 監視とレポート

21Vianet (Azure China 21Vianet) インスタンスで運用される Microsoft Azure で、Azure AD アクティビティ ログ レポートが利用できるようになりました。 次のログが対象となります。

- **サインイン アクティビティ ログ** - ご利用のテナントに関連付けられているすべてのサインイン ログが対象となります。

- **セルフサービス パスワード監査ログ** - すべての SSPR 監査ログが対象となります。

- **ディレクトリ管理監査ログ** - ディレクトリ管理に関連したすべての監査ログが対象となります (ユーザー管理、アプリ管理など)。

これらのログを通じて、自分が管理する環境がどのような状態にあるのかの分析情報を得ることができます。 レポートから得たデータによって次のことが可能となります。

- 管理下にあるユーザーがアプリとサービスをどのように利用しているかを明らかにする。

- ユーザーの作業を妨げている問題をトラブルシューティングする。

レポートの使い方について詳しくは、「[Azure Active Directory レポート](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)」をご覧ください。

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>"レポート リーダー" ロール (非管理者ロール) を使用して Azure AD アクティビティ レポートを閲覧する

**タイプ:** 新機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

Azure AD のアクティビティ ログに非管理者ロールでアクセスできるようにするというカスタマー フィードバックの一環として、"レポート リーダー" ロールのユーザーを対象に、Azure Portal 内のサインイン アクティビティや監査アクティビティにアクセスしたり、Graph API を使用したりする機能を用意しました。 

レポートの使い方について詳しくは、「[Azure Active Directory レポート](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)」をご覧ください。 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>EmployeeID の信頼性情報がユーザー属性やユーザー ID として利用可能に

**タイプ:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** SSO
 
SAML ベースのサインオンを使用したアプリケーションでは、メンバー ユーザーや B2B ゲストのユーザー識別子およびユーザー属性として **EmployeeID** をエンタープライズ アプリケーションの UI から構成することができます。

詳細については、「[Azure Active Directory のエンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)」を参照してください。

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Azure Active Directory アプリケーション プロキシでワイルドカードを使用してアプリケーション管理を単純化

**タイプ:** 新機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** ユーザー認証
 
アプリケーションをデプロイしやすくして管理オーバーヘッドを低減するために、ワイルドカードを使ってアプリケーションを発行する機能に対応しました。 ワイルドカード アプリケーションの発行は、標準的なアプリケーション発行フローに沿って行われますが、内部 URL と外部 URL にはワイルドカードが使用されます。

詳細については、「[Azure Active Directory アプリケーション プロキシのワイルドカード アプリケーション](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)」を参照してください。

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>アプリケーション プロキシの構成をサポートする新しいコマンドレット

**タイプ:** 新機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** プラットフォーム

AzureAD PowerShell Preview モジュールの最新リリースには、PowerShell を使ってユーザーがアプリケーション プロキシ アプリケーションを構成できる新しいコマンドレットが備わっています。

新たに追加されたコマンドレットは次のとおりです。 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>グループの構成をサポートする新しいコマンドレット

**タイプ:** 新機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** プラットフォーム

AzureAD PowerShell モジュールの最新リリースには、Azure AD 内のグループを管理するためのコマンドレットが備わっています。 これらのコマンドレットは、AzureADPreview モジュールで従来から提供されていましたが、新たに AzureAD モジュールにも追加されました。

新たに一般提供向けにリリースされた Group のコマンドレットは次のとおりです。 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Azure AD Connect の新しいリリースが利用可能に

**タイプ:** 新機能  
**サービス カテゴリ:** AD 同期  
**製品の機能:** プラットフォーム
 
Azure AD とオンプレミス データ ソース (Windows Server Active Directory や LDAP) との間でデータを同期するためのツールとしては Azure AD Connect が推奨されています。

>[!Important]
>このビルドでは、スキーマと同期ルールに変更が加えられています。 アップグレードの後、フル インポートの手順と完全同期の手順が Azure AD Connect 同期サービスによってトリガーされます。 この動作を変更する方法については、「[アップグレード後に完全な同期を保留にする方法](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade)」を参照してください。

このリリースで行われた更新や変更を以下に示します。

**修正された問題**

- 次のページに切り替えるときの、パーティション フィルター処理ページのバック グラウンド タスクにおけるタイミング ウィンドウの修正。

- ConfigDB カスタム アクションの実行中にアクセス違反を引き起こしていたバグを修正しました

- SQL 接続のタイムアウトから復旧できるようにバグを修正しました。

- SAN ワイルドカードを含む証明書で前提条件の確認に失敗するバグを修正しました。

- AAD コネクタのエクスポート中に miiserver.exe がクラッシュするバグを修正しました。

- 実行時に誤ったパスワードで DC へのログオンを試行すると、AAD 接続ウィザードの構成が変更されてしまうバグを修正しました。

**新機能と機能強化**
 
- アプリケーション テレメトリ - このデータ クラスのオン/オフは、管理者が切り替えることができます。

- Azure AD の正常性データ - 正常性の設定を制御するには、管理者が正常性ポータルにアクセスする必要があります。 サービス ポリシーが変更された場合、エージェントがこれを読み取って強制します。

- デバイスの書き戻し構成アクションと、ページの初期化の進行状況バーを追加しました。

- HTML レポートでの一般的な診断と、ZIP テキスト/HTML レポートの完全なデータ コレクションが向上しました。

- 自動アップグレードの信頼性が向上しました。また、サーバーの正常性を確認できるように、テレメトリを追加しました。

- AD コネクタ アカウントの特権アカウントで使用できるアクセス許可が制限されています。 新規インストールの場合、MSOL アカウントの作成後に特権アカウントが MSOL アカウントに対して持つアクセス許可がウィザードによって制限されます。 この変更は、自動作成アカウントでの高速インストールとカスタム インストールに影響します。

- AADConnect のクリーン インストールで SA 特権が求められないようにインストーラーを変更しました。

- 特定のオブジェクトの同期に関する問題のトラブルシューティングを行う新しいユーティリティを追加しました。 現時点では、このユーティリティは以下の内容を確認します。

    - 同期されたユーザー オブジェクトと Azure AD テナントのユーザー アカウントの間の UserPrincipalName の不一致。
  
    - ドメインのフィルター処理によって、オブジェクトが同期からフィルター処理されているかどうか
  
    - 組織単位 (OU) のフィルター処理によって、オブジェクトが同期からフィルター処理されているかどうか

- 特定のユーザー アカウントを対象に、オンプレミス Active Directory に格納されている現在のパスワード ハッシュを同期する新しいユーティリティを追加しました。 このユーティリティでは、パスワードの変更は必要ありません。 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Azure AD アプリケーション ベースの条件付きアクセスで使用できるアプリケーションに Intune App Protection ポリシー対応アプリケーションを追加

**種類:** 変更された機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティ & 保護

アプリケーション ベースの条件付きアクセスをサポートするアプリケーションが増えました。 今後は、これらの承認されたクライアント アプリを使用して、Office 365 などの Azure AD 接続クラウド アプリにアクセスすることができます。

2 月末までに次のアプリケーションが追加される予定です。

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

詳細については、次を参照してください。

- [承認されたクライアント アプリケーションの要件](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD のアプリベースの条件付きアクセス](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>使用条件のモバイル エクスペリエンスへの更新 

**種類:** 変更された機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** コンプライアンス

使用条件が表示されたときに **[表示に問題がある場合は、ここをクリックします]** をクリックできるようになりました。 このリンクをクリックすると、お使いのデバイスでネイティブに使用条件が表示されます。 ドキュメントのフォント サイズやデバイスの画面サイズに関係なく、必要に応じてズームしてドキュメントを読むことができます。 

---
 
## <a name="january-2018"></a>2018 年 1 月
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ 

**タイプ:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2018 年 1 月に、フェデレーションをサポートする次の新しいアプリがアプリ ギャラリーに追加されました。

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698)、[OneTrust Privacy Management Software](https://go.microsoft.com/fwlink/?linkid=861660)、[Dealpath](https://go.microsoft.com/fwlink/?linkid=863526)、IriusRisk Federated Directory、[Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701)。

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。

アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)」を参照してください。 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>追加のリスクが検出されたサインイン

**タイプ:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティ & 保護

検出されたリスク イベントに対して取得する洞察は、Azure AD サブスクリプションに関連付けられています。 Azure AD Premium P2 エディションでは、基になるすべての検出に関する最も詳細な情報を取得できます。

Azure AD Premium P1 エディションでは、ライセンスに含まれない検出は、追加のリスクが検出されたサインインというリスク イベントとして表示されます。

詳細については、「[Azure Active Directory リスク イベント](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)」を参照してください。
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>エンド ユーザーのアクセス パネルでの Office 365 アプリケーションの非表示

**タイプ:** 新機能  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** SSO

新しいユーザー設定を使用することで、ユーザーのアクセス パネルで Office 365 アプリケーションを表示する方法が管理しやすくなりました。 このオプションは、Office ポータルでのみ Office アプリを表示する場合に、ユーザーのアクセス パネル内のアプリ数を減らすのに役立ちます。 この設定は **[ユーザー設定]** 内にあり、 **[ユーザーは Office 365 ポータルでのみ Office 365 アプリを表示できる]** というラベルが付いています。

詳細については、「[Azure Active Directory でユーザーのエクスペリエンスからアプリケーションを非表示にする](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)」をご覧ください。

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>アプリ URL からの直接のパスワード SSO が有効なアプリへのシームレスなサインイン 

**タイプ:** 新機能  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** SSO

マイ アプリのブラウザー拡張機能は、マイ アプリのシングル サインオン機能をブラウザーでショートカットとして使用できる、便利なツール上で利用できます。 インストール後、ユーザーのブラウザーにワッフル アイコンが表示され、そこからアプリにクイック アクセスできるようになります。 ユーザーは次のメリットを得ることができます。

- アプリのログイン ページから直接、パスワード SSO ベースのアプリにサインインできます
- クイック検索機能を使用して、すべてのアプリを起動できます
- 拡張機能で最近使用したアプリにショートカットできます
- この拡張機能は、Microsoft Edge、Chrome、Firefox で利用できます。
 
詳細については、「[マイ アプリによるセキュリティで保護されたサインイン拡張機能](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)」をご覧ください。

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure クラシック ポータルでの Azure AD 管理エクスペリエンスの廃止

**タイプ:** 非推奨   
**サービス カテゴリ:** Azure AD  
**製品の機能:** Directory

2018 年 1 月 8 日に、Azure クラシック ポータルでの Azure AD 管理エクスペリエンスは廃止されました。 これは Azure クラシック ポータル自体の廃止と合わせて行われました。 今後は、Azure AD のポータル ベースの管理についてはすべて [Azure AD 管理センター](https://aad.portal.azure.com)をご利用ください。
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>PhoneFactor Web ポータルは廃止されました

**タイプ:** 非推奨  
**サービス カテゴリ:** Azure AD  
**製品の機能:** Directory
 
2018 年 1 月 8日に、PhoneFactor Web ポータルは廃止されました。 このポータルは、MFA サーバーの管理に使用されていましたが、これらの機能は portal.azure.com の Azure Portal に移行しました。 

MFA の構成は、 **[Azure Active Directory] \> [MFA サーバー]** にあります。
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Azure AD レポートの廃止

**タイプ:** 非推奨  
**サービス カテゴリ:** レポート  
**製品の機能:** ID ライフサイクル管理  


新しい Azure Active Directory 管理コンソールと、アクティビティおよびセキュリティ レポートに使用できる新しい API の一般公開に伴い、"/reports" エンドポイントにあるレポート API は 2017 年 12 月 31 日に廃止されました。

**利用可能な機能**

新しい管理コンソールへの移行の一環として、Azure AD アクティビティ ログの取得に使用できる新しい 2 つの API が作成されました。 この新しい API セットでは監査およびサインイン アクティビティが追加されているだけでなく、より充実したフィルター機能と並べ替え機能も備わっています。 以前はセキュリティ レポートを通じて提供されていたデータに、Microsoft Graph の Identity Protection リスク イベント API を通じてアクセスできます。

詳細については、次を参照してください。

- [Azure Active Directory Reporting API の概要](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Azure Active Directory Identity Protection と Microsoft Graph の基本](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>2017 年 12 月

### <a name="terms-of-use-in-the-access-panel"></a>アクセス パネルでの使用条件

**タイプ:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** コンプライアンス
 
アクセス パネルに移動して、以前承認した使用条件を確認できます。

次の手順に従います。

1. [MyApps ポータル](https://myapps.microsoft.com)に移動し、サインインします。

2. 右上隅に表示されるご自身の名前をクリックし、一覧から **[プロファイル]** を選択します。 

3. **[プロファイル]** で、 **[使用条件の確認]** を選択します。 

4. 承認した使用条件を確認できます。 

詳細については、「[Azure Active Directory Terms of Use 機能 (プレビュー)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)」をご覧ください。
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>新しい Azure AD サインイン エクスペリエンス

**タイプ:** 新機能  
**サービス カテゴリ:** Azure AD  
**製品の機能:** ユーザー認証
 
Azure AD と Microsoft アカウント ID システムの UI は、外観と操作性が一貫したものになるよう再設計されました。 また、Azure AD サインイン ページは最初にユーザー名を収集し、2 番目の画面で資格情報を収集します。

詳細については、「[The new Azure AD sign-in experience is now in public preview (サインイン エクスペリエンスのパブリック プレビューを開始)](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)」をご覧ください。
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>回数が減ったサインイン プロンプト: Azure AD サインインの新しい "サインインしたままにする" エクスペリエンス

**タイプ:** 新機能  
**サービス カテゴリ:** Azure AD  
**製品の機能:** ユーザー認証
 
Azure AD サインイン ページの **[サインインしたままにする]** チェック ボックスは、お客様が正常に認証された後に表示される新しいプロンプトに置き換えられました。 

このプロンプトに **[はい]** と応答した場合、サービスにより永続的な更新トークンが付与されます。 この動作は、以前のエクスペリエンスで **[サインインしたままにする]** チェック ボックスをオンにした場合と同じになります。 フェデレーション テナントの場合、お客様がフェデレーション サービスで正常に認証された後にこのプロンプトが表示されます。

詳細については、「[Fewer sign-in prompts: The new "keep me signed in" experience for Azure AD is in preview」 (回数が減ったサインイン プロンプト: Azure AD の新しい "サインインしたままにする" エクスペリエンスはプレビュー段階)](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/)」を参照してください。 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>同意前に使用条件の展開を要求する構成の追加

**タイプ:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** コンプライアンス
 
条項に同意する前に使用条件を展開することをお客様のユーザーに要求する、管理者向けのオプションです。

[ユーザーは使用条件を展開する必要があります] を **[オン]** にするか **[オフ]** にするかを選択します。 **[オン]** に設定すると、同意前に使用条件を承認するようユーザーに要求します。

詳細については、「[Azure Active Directory Terms of Use 機能 (プレビュー)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)」をご覧ください。
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>対象となるロール割り当ての範囲指定されたアクティブ化

**タイプ:** 新機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management
 
範囲指定されたアクティブ化により、元の割り当ての既定値よりも低い自律性で、対象となる Azure リソースのロール割り当てをアクティブ化できます。 たとえば、ご利用のテナントでお客様がサブスクリプションの所有者として割り当てられているとします。 範囲指定されたアクティブ化では、サブスクリプションに含まれる最大 5 つのリソース (リソース グループや仮想マシンなど) に対して所有者のロールをアクティブ化できます。 アクティブ化を範囲指定すると、重要な Azure リソースに対して望ましくない変更が実行される可能性を低減できる場合があります。

詳しくは、「[Azure AD Privileged Identity Management とは](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)」をご覧ください。
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Azure AD アプリ ギャラリーの新しいフェデレーション アプリ

**タイプ:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリケーション  
**製品の機能:** サード パーティ統合

2017 年 12 月に、フェデレーションを使用した以下の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523)、Adobe Experience Manager、[EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685)、[Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe、[FactSet](https://go.microsoft.com/fwlink/?linkid=863525)、[IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517)、[MOBI](https://go.microsoft.com/fwlink/?linkid=863521)、[MobileIron Azure AD integration](https://go.microsoft.com/fwlink/?linkid=858027)、[Reflektive](https://go.microsoft.com/fwlink/?linkid=863518)、[SAML SSO for Bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520)、[SAML SSO for Bitbucket by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519)、[Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522)、WebHR、Zenegy Azure AD Integration。

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。

アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)」を参照してください。 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Azure AD ディレクトリ ロールに対する承認ワークフロー

**タイプ:** 変更された機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management
 
Azure AD ディレクトリ ロールの承認ワークフローが一般公開されました。

承認ワークフローでは、特権ロール管理者は特権ロール使用前に、対象となるロール メンバーに対してロールのアクティブ化を要求するよう要求できます。 複数のユーザーとグループに承認責任を委任できます。 対象となるロール メンバーは、承認が完了して各自のロールがアクティブ化されたときに通知を受け取ります。

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>パススルー認証: Skype for Business のサポート

**タイプ:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証

パススルー認証では、先進認証 (オンライン トポロジとハイブリッド トポロジを含む) をサポートする Skype for Business クライアント アプリケーションへのユーザー サインインがサポートされるようになりました。 

詳細については、「[先進認証でサポートされる Skype for Business トポロジ](https://technet.microsoft.com/library/mt803262.aspx)」をご覧ください。
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Azure RBAC 用の Azure AD Privileged Identity Management の更新 (プレビュー)

**タイプ:** 変更された機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management
 
Azure ロールベースのアクセス制御 (RBAC) 用の Azure AD Privileged Identity Management (PIM) のパブリック プレビュー更新により、次のことが可能になりました。

* Just Enough Administration を使用する。
* リソース ロールのアクティブ化の前に承認を要求する。
* Azure AD と Azure RBAC の両方のロールの承認を要求するロールの、将来のアクティブ化をスケジュールする。
 
詳細については、「[Azure リソース向けの PIM (プレビュー)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)」をご覧ください。

---
 
## <a name="november-2017"></a>2017 年 11 月
 
### <a name="access-control-service-retirement"></a>Access Control Service の廃止

**タイプ:** 変更の計画  
**サービス カテゴリ:** Access Control Service  
**製品の機能:** Access Control Service 

Azure Active Directory Access Control (Access Control Service とも呼ばれます) は 2018 年後半に廃止されます。 詳しいスケジュールや大まかな移行ガイダンスなど、詳細については数週間以内にお知らせします。 Access Control Service に関する質問をこのページにコメントとして残していただければ、チーム メンバーがお答えします。

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>ブラウザー アクセスを Intune Managed Browser に限定 

**タイプ:** 変更の計画  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護

Intune Managed Browser を承認されたアプリとして使用することで、Office 365 などの Azure AD 接続クラウド アプリへのブラウザー アクセスを制限できるようになります。 

アプリケーションベースの条件付きアクセスで以下の条件を構成できるようになります。

**クライアント アプリ:** Browser

**この変更の影響**

現在、この条件を使用する場合、アクセスはブロックされます。 このプレビューが利用可能になると、すべてのアクセスで Managed Browser アプリケーションの使用が必須になります。 

この機能の詳細については、今後のブログとリリース ノートをお待ちください。 

詳細については、[Azure AD の条件付きアクセス](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)に関するページを参照してください。
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD アプリベースの条件付きアクセス向けの承認されたクライアント アプリの新規追加

**種類:** 変更の計画  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護

以下のアプリが、[承認されたクライアント アプリ](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)の一覧に示されます。

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

詳細については、次を参照してください。

- [承認されたクライアント アプリケーションの要件](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD のアプリベースの条件付きアクセス](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>使用条件での多言語のサポート

**タイプ:** 新機能    
**サービス カテゴリ:** 使用条件  
**製品の機能:** コンプライアンス

管理者は、PDF ドキュメントを複数含めた新しい使用条件を作成できるようになりました。 これらの PDF ドキュメントは、対応する言語にタグ付けできます。 PDF はユーザー設定に一致する言語で表示されます。 一致する言語がない場合は、既定の言語が表示されます。

---
 
### <a name="real-time-password-writeback-client-status"></a>パスワード ライトバック クライアントのリアルタイムの状態

**タイプ:** 新機能  
**サービス カテゴリ:** セルフサービスのパスワード リセット  
**製品の機能:** ユーザー認証

オンプレミスのパスワード ライトバック クライアントの状態を確認できるようになりました。 このオプションは、[[パスワードのリセット]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) ページの **[オンプレミスの統合]** セクションで使用できます。 

オンプレミスのライトバック クライアントへの接続で問題が生じた場合、次の内容のエラー メッセージが表示されます。

- オンプレミスのライトバック クライアントへ接続できない理由の詳細
- 問題の解決に役立つドキュメントへのリンク 

詳細については、「[オンプレミスの統合](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration)」をご覧ください。

---

### <a name="azure-ad-app-based-conditional-access"></a>Azure AD のアプリベースの条件付きアクセス 
 
**種類:** 新機能  
**サービス カテゴリ:** Azure AD  
**製品の機能:** ID のセキュリティと保護

[Azure AD のアプリベースの条件付きアクセス](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)を使用することで、Office 365 などの Azure AD 接続クラウド アプリへのアクセスを、Intune App Protection ポリシーがサポートされる[承認されたクライアント アプリ](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)に限定できるようになりました。 Intune App Protection ポリシーは、これらのクライアント アプリケーション上にある企業データの構成と保護に使用されます。

[アプリベース](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)の条件付きアクセス ポリシーを[デバイスベース](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications)の条件付きアクセス ポリシーと組み合わせることで、個人および会社のデバイスのデータを柔軟に保護できます。

現在、アプリベースの条件付きアクセスでは以下の条件とコントロールを使用できます。

**サポートされるプラットフォームの条件**

- iOS
- Android

**クライアント アプリの条件**

- モバイル アプリとデスクトップ クライアント

**アクセス制御**

- 承認済みクライアント アプリを必須にする

詳細については、[Azure AD のアプリベースの条件付きアクセス](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)に関するページを参照してください。
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Azure Portal での Azure AD デバイスの管理

**タイプ:** 新機能  
**サービス カテゴリ:** デバイスの登録と管理  
**製品の機能:** ID のセキュリティと保護

Azure AD に接続されているすべてのデバイスと、それらのデバイスに関連したアクティビティを 1 か所で確認できるようになりました。 Azure Portal にすべてのデバイスの ID と設定を管理できる新しい管理機能が追加されました。 このリリースでは、次の操作を行うことができます。

- Azure AD の条件付きアクセスで使用可能なすべてのデバイスを表示する。
- プロパティを表示する (ハイブリッド Azure AD 参加済みデバイスも含む)。
- Azure AD 参加済みデバイスの BitLocker キーを確認し、Intune などでデバイスを管理する。
- Azure AD デバイス関連の設定を管理する。

詳細については、[Azure Portal によるデバイスの管理](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal)に関するページをご覧ください。

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Azure AD の条件付きアクセス用デバイス プラットフォームとしての macOS のサポート 

**種類:** 新機能    
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護 

Azure AD 条件付きアクセス ポリシーのデバイス プラットフォームの条件に macOS を追加 (または除外) できるようになりました。 サポートされるデバイス プラットフォームに macOS が追加されたことで、以下の操作が可能になりました。

- **Intune を使用して macOS デバイスを登録、管理する。** iOS や Android などのプラットフォームと同様に、macOS でも登録を統合するポータル サイト アプリケーションを使用できるようになりました。 macOS 用の新しいポータル サイト アプリを使用すれば、Intune と Azure AD にデバイスを登録できます。
- **Intune で定義したご所属の組織のコンプライアンス ポリシーを厳守するよう macOS デバイスを設定する。** Azure Portal の Intune では、macOS デバイス用のコンプライアンス ポリシーを設定できます。 
- **Azure AD のアプリケーションにアクセスできるデバイスを、ポリシーに準拠した macOS デバイスのみに制限する。** 条件付きアクセス ポリシーを作成する際、別のデバイス プラットフォーム オプションとして macOS を選択できます。 Azure で設定済みのターゲット アプリケーション向けに macOS 専用の条件付きアクセス ポリシーを作成できます。

詳細については、次を参照してください。

- [Intune で macOS デバイス用のデバイス コンプライアンス ポリシーを作成する](https://aka.ms/macoscompliancepolicy)
- [Azure AD の条件付きアクセス](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Azure Multi-factor Authentication の ネットワーク ポリシー サーバー拡張機能 

**タイプ:** 新機能    
**サービス カテゴリ:** 多要素認証  
**製品の機能:** ユーザー認証

Azure Multi-Factor Authentication のネットワーク ポリシー サーバー拡張機能は、既存のサーバーを使用してクラウド ベースの多要素認証機能をご利用の認証インフラストラクチャに追加します。 ネットワーク ポリシー サーバー拡張機能を使用すると、電話、テキスト メッセージ、またはモバイル アプリによる検証を、ご利用の既存の認証フローに追加できます。 新しいサーバーをインストール、構成、メンテナンスする必要はありません。 

この拡張機能は、Azure Multi-Factor Authentication Server をデプロイせずに仮想プライベート ネットワーク接続を保護する必要がある組織を対象に作成されました。 ネットワーク ポリシー サーバー拡張機能は、RADIUS とクラウド ベース Azure Multi-Factor Authentication の間のアダプターとして機能し、認証の 2 番目の要素をフェデレーション ユーザーまたは同期済みユーザーに提供します。

詳細については、「[Azure Multi-Factor Authentication と既存の NPS インフラストラクチャの統合](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension)」をご覧ください。
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>削除済みユーザーの復元または完全な削除

**タイプ:** 新機能    
**サービス カテゴリ:** [ユーザー管理]  
**製品の機能:** Directory 

Azure AD 管理センターで以下の操作が可能になりました。

- 削除済みユーザーを復元する。 
- ユーザーを完全に削除する。

**実行方法:**

1. Azure AD 管理センターの **[管理]** セクションで [[すべてのユーザー]](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) を選択します。 

2. **[表示]** リストで **[最近削除されたユーザー]** を選択します。 

3. 最近削除されたユーザーを 1 名または複数名選択して、復元するか完全に削除します。
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD アプリベースの条件付きアクセス向けの承認されたクライアント アプリの新規追加
 
**種類:** 変更された機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護

以下のアプリが、[承認されたクライアント アプリ](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)の一覧に追加されました。

- Microsoft Planner
- Azure Information Protection 

詳細については、次を参照してください。

- [承認されたクライアント アプリケーションの要件](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD のアプリベースの条件付きアクセス](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>条件付きアクセス ポリシーのコントロール間での "OR" の使用 

**種類:** 変更された機能    
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護
 
条件付きアクセス コントロールで (選択したコントロールのいずれかを必須にする) "OR" を使用できるようになりました。 この機能を使用すると、アクセス コントロール間を "OR" でつないでポリシーを作成できます。 たとえば、この機能を使用して、多要素認証を使用したサインイン "または" 準拠デバイスの使用をユーザーに求めるポリシーを作成できます。

詳細については、[Azure AD の条件付きアクセスのコントロール](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls)に関するページを参照してください。
 
---

### <a name="aggregation-of-real-time-risk-events"></a>リアルタイムのリスク イベントの集計

**タイプ:** 変更された機能    
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティと保護

Azure AD Identity Protection では、指定日に同一の IP アドレスで発生したすべてのリアルタイム リスク イベントが、リスク イベントの種類ごとに集計されるようになりました。 この変更により表示されるリスク イベントの量が制限されますが、ユーザーのセキュリティが変更されることはありません。

根本となるリアルタイム検出は、ユーザーのサインインのたびに行われます。 多要素認証またはアクセスのブロックを行うサインインのリスク セキュリティ ポリシーを設定している場合、このポリシーは引き続きリスクのあるサインインのたびにトリガーされます。
 
---
 
## <a name="october-2017"></a>2017 年 10 月

### <a name="deprecate-azure-ad-reports"></a>Azure AD レポートの廃止

**タイプ:** 変更の計画  
**サービス カテゴリ:** レポート  
**製品の機能:** ID ライフサイクル管理  

Azure Portal では次の機能が提供されています。

- 新しい Azure AD 管理コンソール。
- アクティビティ レポートとセキュリティ レポートのための新しい API。
 
これらの新機能の導入に伴い、/reports エンドポイントのレポート API は 2017 年 12 月 10 日に廃止されました。 

---

### <a name="automatic-sign-in-field-detection"></a>サインイン フィールドの自動検出

**タイプ:** 固定   
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** シングル サインオン  

Azure AD は、HTML ユーザー名とパスワード フィールドをレンダリングするアプリケーションのサインイン フィールドの自動検出をサポートしています。 この手順は「[アプリケーションのサインイン フィールドを自動的にキャプチャする方法](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app)」に示されています。 この機能は、[Azure Portal](https://aad.portal.azure.com) の **[エンタープライズ アプリケーション]** ページで*ギャラリー以外のアプリケーション*を追加することで表示されます。 さらに、Web URL を入力してページを保存することにより、この新しいアプリケーションの**シングル サインオン** モードを**パスワード ベースのシングル サインオン**を実行するように構成できます。
 
サービスの問題により、この機能は一時的に無効化されていました。 問題が解決されたため、サインイン フィールドの自動検出が再び利用可能になりました。

---

### <a name="new-multi-factor-authentication-features"></a>新しい多要素認証機能

**タイプ:** 新機能  
**サービス カテゴリ:** 多要素認証  
**製品の機能:** ID のセキュリティと保護  

多要素認証 (MFA) は、組織を保護する上で欠かせない要素です。 資格情報の適応性を高めて多要素認証をよりシームレスに利用できるようにするため、以下の機能が追加されました。 

- MFA 結果へのプログラムによるアクセスなどを含め、多要素認証結果が Azure AD サインイン レポートに直接統合されました。
- MFA 構成 が Azure Portal の Azure AD 構成エクスペリエンスに、より緊密に統合されました。

このパブリック プレビューでは、MFA の管理およびレポートが、コアとなる Azure AD の構成エクスペリエンスに組み込まれました。 Azure AD を使用しながら MFA の管理ポータル機能を管理できます。

詳細については、「[Azure Portal の多要素認証レポートのリファレンス](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa)」をご覧ください。 

---

### <a name="terms-of-use"></a>使用条件

**タイプ:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** コンプライアンス  

Azure AD の使用条件を利用して、ユーザーに対して法律またはコンプライアンスの要件に関する免責事項を表示できます。

Azure AD Terms of Use は、次のシナリオで使用できます。

- ご所属の組織内のすべてのユーザーに対する一般的な使用条件
- ユーザーの属性 (たとえば、動的グループによって行われる、医師と看護師や、国内の従業員と国外の従業員などの区別) に基づいた特定の使用条件
- ビジネスへの影響が大きいアプリ (Salesforce など) へのアクセスのための特定の使用条件

詳細については、[Azure AD の使用条件](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)に関するページをご覧ください。

---

### <a name="enhancements-to-privileged-identity-management"></a>Privileged Identity Management の機能強化

**タイプ:** 新機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management  

Azure AD Privileged Identity Management で、組織内の以下の Azure リソース (プレビュー) へのアクセスを管理、制御、監視できるようになりました。

- サブスクリプション
- リソース グループ
- 仮想マシン 

Azure RBAC 機能を利用する Azure Portal 内のすべてのリソースで、Azure AD Privileged Identity Management から提供されるセキュリティおよびライフサイクルの管理機能を使用できます。

詳細については、[Azure リソース向けの Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac) に関するページをご覧ください。

---

### <a name="access-reviews"></a>アクセス レビュー

**タイプ:** 新機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** コンプライアンス  

組織はアクセス レビュー (プレビュー) を使用して、グループ メンバーシップの管理とエンタープライズ アプリケーションへのアクセスを効率的に行うことができます。 

- アプリケーションへのアクセスおよびグループのメンバーシップのアクセス レビューを使うと、ゲスト ユーザーによるアクセスを認定できます。 レビュー担当者は、アクセス レビューによって提供される分析情報に基づき、ゲストが引き続きアクセスすることを許可するかどうかを効率的に決定できます。
- アクセス レビューを使って従業員のアプリケーション アクセスとグループ メンバーシップを認定できます。

アクセス レビューの制御を組織に関連するプログラムに集めて、コンプライアンスまたはリスクを受けやすいアプリケーションのレビューを追跡できます。

詳細については、「[Azure AD アクセス レビュー](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview)」をご覧ください。

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>マイ アプリおよび Office 365 アプリ ランチャーでのサード パーティ製アプリケーションの非表示

**タイプ:** 新機能  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** シングル サインオン  

新しい**アプリの非表示**プロパティにより、ご利用のユーザー ポータルに表示されるアプリを管理しやすくなりました。 アプリの非表示は、バックエンド サービス用のアプリ タイルが表示されていたり、タイルが重複してユーザーのアプリ ランチャーが整理されていなかったりする場合に役立ちます。 切り替えボタンはサード パーティ製アプリの **[プロパティ]** セクションにあり、 **[Visible to user?]\(ユーザーに表示しますか?\)** というラベルが付いています。 PowerShell を介してプログラムでアプリを非表示にすることもできます。 

詳細については、[Azure AD でユーザーのエクスペリエンスからサードパーティ製アプリケーションを非表示にする](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)方法に関するページをご覧ください。 


**利用可能な機能**

 新しい管理コンソールへの移行の一環として、Azure AD のアクティビティ ログを取得する新しい 2 つの API が利用可能になりました。 この新しい API セットでは監査およびサインイン アクティビティが追加されているだけでなく、より充実したフィルター機能と並べ替え機能も備わっています。 以前はセキュリティ レポートで提供されていたデータに、Microsoft Graph の Identity Protection リスク イベント API を使用してアクセスできます。


## <a name="september-2017"></a>2017 年 9 月

### <a name="hotfix-for-identity-manager"></a>Identity Manager の修正プログラム

**タイプ:** 変更された機能  
**サービス カテゴリ:** Identity Manager  
**製品の機能:** ID ライフサイクル管理  

2017 年 9 月 25 日に、Identity Manager 2016 Service Pack 1 の修正プログラム ロールアップ パッケージ (ビルド 4.4.1642.0) の提供が開始されました。 このロールアップ パッケージの説明は以下のとおりです。

- 問題を解決し、機能を強化します。
- Identity Manager 2016 のビルド 4.4.1459.0 までのすべての Identity Manager 2016 Service Pack 1 更新プログラムに代わる、累積的な更新プログラムです。 
- ご利用のコンピューターに Identity Manager 2016 ビルド 4.4.1302.0 がインストールされている必要があります。 

詳細については、[Identity Manager 2016 Service Pack 1 の修正プログラム ロールアップ パッケージ (ビルド 4.4.1642.0)](https://support.microsoft.com/help/4021562) に関するページをご覧ください。 

---
