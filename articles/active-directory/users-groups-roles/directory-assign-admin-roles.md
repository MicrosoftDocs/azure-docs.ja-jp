---
title: 管理者ロールの説明とアクセス許可 - Azure Active Directory | Microsoft Docs
description: 管理者ロールは、ユーザーの追加、管理者ロールの割り当て、ユーザー パスワードのリセット、ユーザー ライセンスの管理、ドメインの管理などを行うことができます。
services: active-directory
author: curtand
manager: mtillman
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 06/04/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73ca740a4dcca3bdbb1951e55df4061364a5c646
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083918"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Azure Active Directory での管理者ロールのアクセス許可

Azure Active Directory (Azure AD) を使用して、特権が低いロールで ID のタスクを管理する限定された管理者を指定できます。 ユーザーの追加または変更、管理ロールの割り当て、ユーザーのパスワードのリセット、ユーザーのライセンスの管理、ドメイン名の管理などの目的で管理者を割り当てることができます。 既定のユーザー アクセス許可は、Azure AD のユーザー設定のみで変更できます。

全体管理者は、すべての管理機能にアクセスできます。 既定では、Azure サブスクリプションにサインアップしたユーザーには、ディレクトリの全体管理者ロールが割り当てられます。 管理者ロールを委任できるのは全体管理者と特権ロール管理者だけです。 ビジネスに対するリスクを軽減するには、このロールを、社内の少数のユーザーにのみ割り当てることをお勧めします。


## <a name="assign-or-remove-administrator-roles"></a>管理者ロールの割り当てまたは削除

Azure Active Directory でユーザーに管理者ロールを割り当てる方法については、[Azure Active Directory での管理者ロールの表示と割り当て](directory-manage-roles-portal.md)に関するページを参照してください。

## <a name="available-roles"></a>使用可能なロール

次の管理者ロールを使用できます。

* **[アプリケーション管理者](#application-administrator)** :このロールのユーザーは、エンタープライズ アプリケーション、アプリケーション登録、アプリケーション プロキシの設定の全側面を作成して管理できます。 さらに、このロールは、委任されたアクセス許可とアプリケーション アクセス許可 (Microsoft Graph と Azure AD Graph を除く) に同意する権限を付与します。 このロールに割り当てられたユーザーは、新しいアプリケーション登録またはエンタープライズ アプリケーションを作成する際に、所有者として追加されません。

  <b>重要</b>:このロールは、アプリケーションの資格情報を管理する権限を付与します。 このロールが割り当てられたユーザーは、アプリケーションに資格情報を追加し、その資格情報を使用してアプリケーションの ID を偽装することができます。 アプリケーションの ID に Azure Active Directory へのアクセス権 (ユーザーやその他のオブジェクトの作成や更新など) が付与されている場合、このロールが割り当てられたユーザーは、アプリケーションを偽装している間にこのようなアクションを実行することができます。 アプリケーションの ID を偽装するこの機能は、ユーザーが Azure AD のロール割り当てを使用して実行できることを越えた特権の昇格になる可能性があります。 ユーザーにアプリケーション管理者ロールを割り当てると、そのユーザーがアプリケーションの ID を偽装できるようになることを理解することが重要です。

* **[アプリケーション開発者](#application-developer)** :このロールのユーザーは、[ユーザーはアプリケーションを登録できる] 設定が [いいえ] に設定されている場合に、アプリケーション登録を作成できます。 さらにこのロールでは、[ユーザーはアプリが自身の代わりに会社のデータにアクセスすることを許可できます] 設定が [いいえ] に設定されている場合に、代わりに同意する権限を付与します。 このロールに割り当てられたユーザーは、新しいアプリケーション登録またはエンタープライズ アプリケーションを作成する際に、所有者として追加されます。

* **[認証管理者](#authentication-administrator)** :このロールのユーザーは、パスワード以外の資格情報の設定とリセットを行うことができます。 認証管理者は、パスワード以外の既存の資格情報 (たとえば、MFA、FIDO) に対する再登録をユーザーに要求し、"**このデバイスに MFA を記憶する**" 機能を取り消すことができます。これによって、管理者でないユーザーや次のロールのみが割り当てられているユーザーが次回サインインするときに MFA の入力を求めることができます。
  * 認証管理者
  * ディレクトリ リーダー
  * ゲスト招待元
  * メッセージ センター閲覧者
  * レポート閲覧者

  認証管理者のロールは現在パブリック プレビュー段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

  <b>重要</b>:このロールのユーザーは、機密情報や個人情報または Azure Active Directory の内外の重要な構成にアクセスできるユーザーのパスワードを変更できます。 ユーザーの資格情報を変更するということは、そのユーザーの ID とアクセス許可を引き受けることができるということを意味します。 例:

  * 所有しているアプリの資格情報を管理できる、アプリケーションの登録とエンタープライズ アプリケーションの所有者。 これらのアプリには、認証管理者に付与されていないAzure AD およびその他の場所への特権アクセス許可がある場合があります。 認証管理者は、このパスからアプリケーション所有者の ID を引き受け、さらにそのアプリケーションの資格情報を更新して特権アプリケーションの ID を引き受けることができる場合があります。
  * 機密情報や個人情報または Azure の重要な構成にアクセスできる Azure サブスクリプション所有者。
  * グループ メンバーシップを管理できるセキュリティ グループと Office 365 グループの所有者。 これらのグループは、機密情報や個人情報または Azure AD や別の場所の重要な構成へのアクセス権を付与される場合があります。
  * Exchange Online、Office Security and Compliance Center、人事システムのような Azure AD 以外のサービスの管理者。
  * 機密情報や個人情報にアクセスできる場合がある役員、弁護士、人事担当者のような非管理者。

* **[Azure Information Protection 管理者](#azure-information-protection-administrator)** :このロールが割り当てられたユーザーは、Azure Information Protection サービスのすべてのアクセス許可を持ちます。 このロールでは、Azure Information Protection ポリシーのラベルの構成、保護テンプレートの管理、保護のアクティブ化を行うことができます。 このロールでは、Identity Protection Center、Privileged Identity Management、Office 365 Service Health の監視、および Office 365 のセキュリティ/コンプライアンス センターのアクセス許可は付与されません。

* **[B2C ユーザー フロー管理者](#b2c-user-flow-administrator)** :このロールが割り当てられたユーザーは、Azure Portal で B2C ユーザー フロー ("組み込み" ポリシーとも呼ばれます) を作成および管理することができます。 これらのユーザーは、ユーザー フローを作成または編集することで、ユーザー エクスペリエンスの html/CSS/javascript コンテンツの変更、ユーザー フローごとの MFA 要件の変更、トークンの要求の変更、テナント内のすべてのポリシー用のセッション設定の調整を行うことができます。 その一方で、このロールには、ユーザーのデータを確認したり、テナント スキーマに含まれている属性を変更したりする機能は含まれていません。 Identity Experience Framework (カスタムとも呼ばれます) ポリシーの変更もこのロールの範囲外です。

* **[B2C ユーザー フロー属性管理者](#b2c-user-flow-attribute-administrator)** :このロールが割り当てられたユーザーは、テナント内のすべてのユーザー フローに使用可能なカスタム属性を追加または削除できます。 そのため、このロールが割り当てられたユーザーは、エンド ユーザー スキーマを変更、またはエンド ユーザー スキーマに新しい要素を追加して、すべてのユーザー フローの動作に影響を及ぼしたり、エンド ユーザーが求め、最終的には要求としてアプリケーションに送信されるデータを間接的に変更したりできます。 このロールでユーザー フローを編集することはできません。

* **[B2C IEF キーセット管理者](#b2c-ief-keyset-administrator)** :  ユーザーは、トークンの暗号化、トークンの署名、および要求の暗号化/暗号化解除のために、ポリシー キーとシークレットを作成して管理できます。 新しいキーを既存のキー コンテナーに追加すると、この制限付き管理者は、既存のアプリケーションに影響を与えることなく、必要に応じてシークレットをロールオーバーできます。 このユーザーは、これらのシークレットとその有効期限の完全な内容を、シークレットを作成した後でも確認できます。
    
  <b>重要:</b>  これは機密性の高いロールです。 キーセット管理者ロールは、慎重に監査し、運用前環境と運用環境では、慎重に割り当てる必要があります。

* **[B2C IEF ポリシー管理者](#b2c-ief-policy-administrator)** :このロールが割り当てられたユーザーは、Azure AD B2C のすべてのカスタム ポリシーを作成、読み取り、更新、および削除することができます。そのため、関連する Azure AD B2C テナント内の Identity Experience Framework を完全に制御できます。 このユーザーは、ポリシーを編集することで、外部の ID プロバイダーとの直接フェデレーションの確立、ディレクトリ スキーマの変更、ユーザー向けのすべてのコンテンツ (HTML、CSS、JavaScript) の変更、認証を完了するための要件の変更、新しいユーザーの作成、ユーザー データの外部システムへの送信 (完全な移行を含む)、パスワードや電話番号などの機密フィールドを含むすべてのユーザー情報の編集を行うことができます。 逆に、このロールでは、暗号化キーを変更したり、テナント内のフェデレーションに使用されているシークレットを編集したりすることはできません。

  <b>重要:</b>B2 IEF ポリシー管理者は、非常に機密性の高いロールであるため、運用環境ではごく限られたテナントに割り当てる必要があります。 これらのユーザーによるアクティビティは、とりわけ運用環境のテナントに対しては、注意深く監査する必要があります。

* **[課金管理者](#billing-administrator)** :購入、サブスクリプションの管理、サポート チケットの管理、サービスの正常性の監視を行います。

* **[クラウド アプリケーション管理者](#cloud-application-administrator)** :このロールのユーザーは、(アプリケーション プロキシを管理する権限を除き) アプリケーション管理者ロールと同じアクセス許可を持ちます。 このロールは、エンタープライズ アプリケーションとアプリケーション登録の全側面を作成して管理する権限を付与します。 さらに、このロールは、委任されたアクセス許可とアプリケーション アクセス許可 (Microsoft Graph と Azure AD Graph を除く) に同意する権限を付与します。 このロールに割り当てられたユーザーは、新しいアプリケーション登録またはエンタープライズ アプリケーションを作成する際に、所有者として追加されません。

  <b>重要</b>:このロールは、アプリケーションの資格情報を管理する権限を付与します。 このロールが割り当てられたユーザーは、アプリケーションに資格情報を追加し、その資格情報を使用してアプリケーションの ID を偽装することができます。 アプリケーションの ID に Azure Active Directory へのアクセス権 (ユーザーやその他のオブジェクトの作成や更新など) が付与されている場合、このロールが割り当てられたユーザーは、アプリケーションを偽装している間にこのようなアクションを実行することができます。 アプリケーションの ID を偽装するこの機能は、ユーザーが Azure AD のロール割り当てを使用して実行できることを越えた特権の昇格になる可能性があります。 ユーザーにクラウド アプリケーション管理者ロールを割り当てると、そのユーザーがアプリケーションの ID を偽装できるようになることを理解することが重要です。

* **[クラウド デバイス管理者](#cloud-device-administrator)** :このロールのユーザーは、Azure AD でデバイスを有効化、無効化、および削除することができ、Azure portal で Windows 10 の BitLocker キーを読み取る (ある場合) ことができます。 このロールでは、デバイス上の他のプロパティを管理するアクセス許可の付与は行いません。

* **[コンプライアンス管理者](#compliance-administrator)** :このロールのユーザーには、Microsoft 365 コンプライアンス センター、Microsoft 365 管理センター、Azure、および Office 365 セキュリティ/コンプライアンス センターのコンプライアンス関連の機能を管理する権限があります。 また、ユーザーは、Exchange 管理センター、Teams および Skype for Business の管理センター内のすべての機能を管理したり、Azure および Microsoft 365 のサポート チケットを作成したりすることもできます。 詳しくは、「[Office 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。

  イン | できること
  ----- | ----------
  [Microsoft 365 コンプライアンス センター](https://protection.office.com) | Microsoft 365 サービス全体での組織のデータの保護および管理<br>コンプライアンス アラートの管理
  [コンプライアンス マネージャー](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 組織の法令遵守活動の追跡、割り当て、確認
  [Office 365 セキュリティ/コンプライアンス センター](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | データ ガバナンスの管理<br>法律およびデータ調査の実行<br>データ主体の要求の管理
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Intune のすべての監査データの表示
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 読み取り専用アクセス許可があり、アラートを管理できる<br>ファイル ポリシーの作成と変更、ファイル ガバナンス アクションの許可<br> データ管理下のすべての組み込みレポートの表示

* **[コンプライアンス データ管理者](#compliance-data-administrator)** :このロールのユーザーには、Microsoft 365 コンプライアンス センター、Microsoft 365 管理センター、および Azure のデータを保護および追跡するための権限があります。 また、ユーザーは、Exchange 管理センター、Compliance Manager 、Teams および Skype for Business の管理センター内のすべての機能を管理したり、Azure および Microsoft 365 のサポート チケットを作成したりすることもできます。

  イン | できること
  ----- | ----------
  [Microsoft 365 コンプライアンス センター](https://protection.office.com) | Microsoft 365 サービス全体のコンプライアンス関連ポリシーの監視<br>コンプライアンス アラートの管理
  [コンプライアンス マネージャー](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 組織の法令遵守活動の追跡、割り当て、確認
  [Office 365 セキュリティ/コンプライアンス センター](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | データ ガバナンスの管理<br>法律およびデータ調査の実行<br>データ主体の要求の管理
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Intune のすべての監査データの表示
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 読み取り専用アクセス許可があり、アラートを管理できる<br>ファイル ポリシーの作成と変更、ファイル ガバナンス アクションの許可<br> データ管理下のすべての組み込みレポートの表示

* **[条件付きアクセス管理者](#conditional-access-administrator)** :このロールのユーザーは、Azure Active Directory の条件付きアクセスの設定を管理できます。
  > [!NOTE]
  > Azure で Exchange ActiveSync の条件付きアクセス ポリシーをデプロイするには、ユーザーは、グローバル管理者である必要もあります。
  
* **[カスタマー ロックボックスのアクセス承認者](#customer-lockbox-access-approver)** : 組織内の[カスタマー ロックボックス要求](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests)を管理します。 彼らはカスタマー ロックボックス要求の電子メール通知を受信し、Microsoft 365 管理センターから要求の承認と拒否を行うことができます。 カスタマー ロックボックス機能を有効または無効にすることもできます。 グローバル管理者のみが、このロールに割り当てられているユーザーのパスワードをリセットできます。
  <!--  This was announced in August of 2018. https://techcommunity.microsoft.com/t5/Security-Privacy-and-Compliance/Customer-Lockbox-Approver-Role-Now-Available/ba-p/223393-->

* **[デスクトップ Analytics 管理者](#desktop-analytics-administrator)** :このロールのユーザーは、デスクトップ Analytics および Office のカスタマイズとポリシーのサービスを管理できます。 デスクトップ Analytics の場合、これには、資産インベントリの表示、デプロイ プランの作成、デプロイと正常性の状態の表示に対する権限が含まれます。 Office のカスタマイズとポリシーのサービスの場合、このロールによりユーザーは Office のポリシーを管理することができます。

* **[デバイス管理者](#device-administrators)** :この役割は、[デバイス設定](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)の追加のローカル管理者としてのみ割り当て可能です。 このロールのユーザーは、Azure Active Directory に参加しているすべての Windows 10 デバイスのローカル マシン管理者になります。 Azure Active Directory 内のデバイス オブジェクトを管理することはできません。 

* **[ディレクトリ閲覧者](#directory-readers)** :これは、[同意フレームワーク](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)をサポートしていない従来のアプリケーションだけに割り当てられるロールです。 ユーザーには、割り当てないでください。

* **[ディレクトリ同期アカウント](#directory-synchronization-accounts)** :使用しないでください。 このロールは、自動的に Azure AD Connect サービスに割り当てられます。他の用途に使用するためのものではなく、他の用途ではサポートされていません。

* **[ディレクトリ ライター](#directory-writers)** :これは、[同意フレームワーク](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)をサポートしていないアプリケーションに割り当てられる従来のロールです。 このロールをユーザーに割り当てることはできません。

* **[Dynamics 365 管理者/CRM 管理者](#crm-service-administrator)** :このロールが割り当てられたユーザーは、Microsoft Dynamics 365 Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、サポート チケットを管理し、サービス正常性を監視できます。 詳細については、「[service admin ロールを使用してテナントを管理する](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)」を参照してください。
  > [!NOTE] 
  > Microsoft Graph API、Azure AD Graph API、Azure AD PowerShell では、このロールは "Dynamics 365 サービス管理者" として識別されます。 [Azure portal](https://portal.azure.com) では、"Dynamics 365 管理者" になります。

* **[Exchange 管理者](#exchange-service-administrator)** :このロールが割り当てられたユーザーは、Microsoft Exchange Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。 また、すべての Office 365 グループの作成および管理、サポート チケットの管理、サービスの正常性の監視を行うこともできます。 詳細については、「 [Office 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。
  > [!NOTE]
  > Microsoft Graph API、Azure AD Graph API、Azure AD PowerShell では、このロールは "Exchange サービス管理者" として識別されます。 [Azure portal](https://portal.azure.com) では、"Exchange 管理者" になります。 [Exchange 管理センター](https://go.microsoft.com/fwlink/p/?LinkID=529144)では "Exchange Online 管理者" になります。 

* **[外部 ID プロバイダー管理者](#external-identity-provider-administrator)** :この管理者は、Azure Active Directory テナントと外部 ID プロバイダー間のフェデレーションを管理します。 このロールが割り当てられたユーザーは、新しい ID プロバイダーを追加したり、使用可能なすべての設定 (認証パス、サービス ID、割り当てられたキー コンテナーなど) を構成したりできます。 このユーザーは、テナントで外部 ID プロバイダーからの認証の信頼を有効にすることができます。 その結果としてエンド ユーザー エクスペリエンスに及ぼす影響は、テナントの種類によって異なります。
  * 従業員とパートナー向けの Azure Active Directory テナント: (たとえば Gmail との) フェデレーションの追加は、まだ招待に応じていないすべてのゲストの招待にすぐに影響します。 「[Google を B2B ゲスト ユーザーの ID プロバイダーとして追加する](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)」を参照してください。
  * Azure Active Directory B2C テナント:(たとえば Facebook、または別の Azure Active Directory との) フェデレーションの追加は、ユーザー フロー (別名、組み込みのポリシー) で ID プロバイダーがオプションとして追加されるまで、エンド ユーザー フローにすぐに影響することはありません。 例については、[ID プロバイダーとしての Microsoft アカウントの構成](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app)に関するページを参照してください。 ユーザー フローを変更するには、"B2C ユーザー フロー管理者" の制限されたロールが必要です。

* **[全体管理者/会社の管理者](#company-administrator)** :このロールが割り当てられたユーザーは、Azure Active Directory のすべての管理機能と、Azure Active Directory の ID を使用するサービス (Microsoft 365 セキュリティ センター、Microsoft 365 security center コンプライアンス センター、Exchange Online、SharePoint Online、Skype for Business Online など) にアクセスできます。 Azure Active Directory テナントにサインアップしたユーザーが全体管理者になります。 他の管理者ロールを割り当てることができるのは全体管理者だけです。 会社に複数の全体管理者が存在してかまいません。 すべてのユーザーと他のすべての管理者のパスワードをリセットできます。

  > [!NOTE]
  > Microsoft Graph API、Azure AD Graph API、Azure AD PowerShell では、このロールは "会社の管理者" として識別されます。 [Azure Portal](https://portal.azure.com) では、"全体管理者" になります。
  >
  >

* **[ゲスト招待元](#guest-inviter)** :このロールが割り当てられたユーザーは、 **[メンバーは招待ができる]** ユーザー設定が [いいえ] に設定されている場合に、Azure Active Directory B2B ゲスト ユーザーの招待を管理できます。 B2B コラボレーションの詳細については、「[Azure AD B2B コラボレーションとは](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)」をご覧ください。 その他の権限は含まれません。

* **[Intune 管理者](#intune-service-administrator)** :このロールが割り当てられたユーザーは、Microsoft Intune Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。 さらに、このロールはポリシーを関連付けるためにユーザーとデバイスを管理することができ、グループを作成および管理することもできます。 詳細については、「[Microsoft Intune でのロール ベースの管理制御 (RBAC)](https://docs.microsoft.com/intune/role-based-access-control)」を参照してください。
  > [!NOTE]
  > Microsoft Graph API、Azure AD Graph API、Azure AD PowerShell では、このロールは "Intune サービス管理者" として識別されます。 [Azure portal](https://portal.azure.com) では、"Intune 管理者" になります。
  
 * **[Kaizala 管理者](#kaizala-administrator)** :このロールが割り当てられたユーザーは、Microsoft Kaizala 内で設定を管理するグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、サポート チケットを管理し、サービス正常性を監視できます。
さらに、このユーザーは、組織のメンバーによる Kaizala の導入と使用法に関連したレポート、および Kaizala アクションを使用して生成されるビジネス レポートにもアクセスできます。 

* **[ライセンス管理者](#license-administrator)** :このロールのユーザーは、ユーザーに対するライセンス割り当ての追加、削除、更新、グループに対する (グループベースのライセンスを使用した) ライセンス割り当ての追加、削除、更新に加え、ユーザーに対する利用場所の管理を行うことができます。 このロールでは、サブスクリプションの購入と管理、グループの作成と管理を行う権限は与えられません。また、利用場所を超える範囲でのユーザーの作成と管理を行う権限も与えられません。 このロールには、サポート チケットの表示、作成、管理のためのアクセス権がありません。

* **[メッセージ センターのプライバシー閲覧者](#message-center-privacy-reader)** :このロールのユーザーは、データのプライバシー メッセージを含め、メッセージ センター内のすべての通知を監視できます。 メッセージ センターのプライバシー閲覧者は、データのプライバシーに関連したものも含めてメール通知を受け取り、メッセージ センターの設定を使用して登録を解除することができます。 データのプライバシー メッセージを読み取ることができるのは、グローバル管理者とメッセージ センターのプライバシー閲覧者のみになります。 さらに、このロールには、グループ、ドメイン、サブスクリプションを表示する権限が含まれています。 このロールには、サービス要求を表示、作成、または管理するアクセス許可はありません。

* **[メッセージ センター閲覧者](#message-center-reader)** :このロールのユーザーは、自分の組織の Exchange、Intune、Microsoft Teams などのサービス構成に対する [Office 365 メッセージ センター](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093)の通知や、正常性に関して注意を促す更新情報を監視できます。 メッセージ センター閲覧者は、投稿の毎週のメール ダイジェストを受け取り、Office 365 でメッセージ センターの投稿を共有できます。 Azure AD では、このロールに割り当てられているユーザーはユーザーやグループなどの読み取り専用アクセスのみを持ちます。 このロールには、サポート チケットの表示、作成、管理のためのアクセス権がありません。

* **[パートナー レベル 1 のサポート](#partner-tier1-support)** :使用しないでください。 このロールは非推奨となっており、将来的に Azure AD から削除されます。 このロールは少数の Microsoft 再販パートナーを対象としており、一般的な使用を目的としたものではありません。

* **[パートナー レベル 2 のサポート](#partner-tier2-support)** :使用しないでください。 このロールは非推奨となっており、将来的に Azure AD から削除されます。 このロールは少数の Microsoft 再販パートナーを対象としており、一般的な使用を目的としたものではありません。

* **[ヘルプデスク (パスワード) 管理者](#helpdesk-administrator)** : このロールが割り当てられたユーザーは、パスワードの変更、更新トークンの無効化、サービス要求の管理、サービス正常性の監視を行うことができます。 更新トークンを無効にすると、ユーザーは再度サインインすることを強制されます。 ヘルプデスク管理者は、管理者ではない他のユーザーまたは次のロールのみが割り当てられているユーザーについて、パスワードをリセットし、更新トークンを無効にすることができます。
  * ディレクトリ リーダー
  * ゲスト招待元
  * ヘルプデスク管理者
  * メッセージ センター閲覧者
  * レポート閲覧者
  
  <b>重要</b>:このロールを持つユーザーは、機密情報や個人情報または Azure Active Directory の内外の重要な構成にアクセスできるユーザーのパスワードを変更できます。 ユーザーのパスワードを変更するということは、そのユーザーの ID およびアクセス許可を取得できるということを意味します。 例:
  * 所有しているアプリの資格情報を管理できる、アプリケーションの登録とエンタープライズ アプリケーションの所有者。 これらのアプリには、Azure AD およびヘルプデスク管理者に付与されていない場所への特権アクセス許可がある場合があります。 ヘルプデスク管理者は、このパスからアプリケーション所有者の ID を取得し、さらにそのアプリケーションの資格情報を更新して特権アプリケーションの ID を取得できる場合があります。
  * 機密情報や個人情報または Azure の重要な構成にアクセスできる Azure サブスクリプション所有者。
  * グループ メンバーシップを管理できるセキュリティ グループと Office 365 グループの所有者。 これらのグループは、機密情報や個人情報または Azure AD や別の場所の重要な構成へのアクセス権を付与される場合があります。
  * Exchange Online、Office Security and Compliance Center、人事システムのような Azure AD 以外のサービスの管理者。
  * 機密情報や個人情報にアクセスできる場合がある役員、弁護士、人事担当者のような非管理者。


  > [!NOTE]
  > ユーザーのサブセットに管理アクセス許可を委任する、およびポリシーを適用するには、[管理単位 (プレビュー)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units) を使用します。


  > [!NOTE]
  > このロールは、[Azure portal](https://portal.azure.com/) で以前は "パスワード管理者" と呼ばれていました。 Azure AD PowerShell、Azure AD Graph API、および Microsoft Graph API での名前に一致するように、この名前は "ヘルプデスク管理者" に変更される予定です。 Azure portal では、"ヘルプデスク管理者" に名前が変更されるまでの短い期間、"ヘルプデスク (パスワード) 管理者" に変更されます。


* **[Power BI 管理者](#power-bi-service-administrator)** :このロールが割り当てられたユーザーは、Microsoft Power BI 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、サポート チケットを管理し、サービス正常性を監視できます。 詳細については、「[Power BI 管理者の役割について](https://docs.microsoft.com/power-bi/service-admin-role)」を参照してください。
  > [!NOTE]
  > Microsoft Graph API、Azure AD Graph API、Azure AD PowerShell では、このロールは "Power BI サービス管理者" として識別されます。 [Azure portal](https://portal.azure.com) では、"Power BI 管理者" になります。

* **[特権認証管理者](#privileged-authentication-administrator)** :このロールのユーザーは、グローバル管理者を含むすべてのユーザーに対して、パスワード以外の資格情報の設定やリセットを実行できます。 特権認証管理者は、パスワード以外の既存の資格情報 (たとえば、MFA、FIDO) に対する再登録をユーザーに強制することができます。また、"このデバイスに MFA を記憶する" 機能を取り消して、すべてのユーザーが次回ログインするときに MFA の入力を求めることができます。 特権認証管理者ができることは次のとおりです。
  * パスワード以外の既存の資格情報 (例: MFA、FIDO) に対する再登録をユーザーに強制する
  * "このデバイスに MFA を記憶する" 機能を取り消し、次回ログイン時に MFA の入力を求める

* **[特権ロール管理者](#privileged-role-administrator)** :このロールが割り当てられたユーザーは、Azure Active Directory と Azure AD Privileged Identity Management 内でロールの割り当てを管理できます。 さらに、このロールは、Privileged Identity Management と管理単位のすべての側面を管理できます。

  <b>重要</b>:このロールは、全体管理者ロールを含むすべての Azure AD ロールの割り当てを管理する権限を付与します。 このロールには、Azure AD でユーザーの作成や更新などの特権付きの機能が含まれていません。 ただし、このロールが割り当てられたユーザーは、追加のロールを割り当てることで、自分または他のユーザーの追加の特権を付与できます。

* **[レポート閲覧者](#reports-reader)** :このロールが割り当てられたユーザーは、Microsoft 365 管理センターで使用状況のレポート データとレポート ダッシュボードを表示できます。また、Power BI で導入コンテキスト パックを表示できます。 さらに、Azure AD のサインイン レポートとアクティビティ、および Microsoft Graph レポート API から返されるデータにもアクセスできます。 レポート閲覧者ロールが割り当てられたユーザーは、関連する使用状況と導入メトリックにのみアクセスできます。 製品固有の管理センター (Exchange など) の設定を構成したり、アクセスしたりする管理者アクセス許可はありません。 このロールには、サポート チケットの表示、作成、管理のためのアクセス権がありません。

* **[Search 管理者](#search-administrator)** :このロールのユーザーには、Microsoft 365 管理センター内のすべての Microsoft Search 管理機能へのフル アクセスがあります。 Search 管理者は、Search 管理者ロールと Search エディター ロールをユーザーに委任し、ブックマーク、Q&A、場所のようなコンテンツを作成および管理できます。 さらに、これらのユーザーは、メッセージ センターを表示し、サービス正常性を監視し、サービス要求を作成することができます。

* **[Search エディター](#search-editor)** :このロールのユーザーは、ブックマーク、Q&A、および場所を含め、Microsoft 365 管理センターで Microsoft Search のコンテンツを作成、管理、および削除できます。

* **[セキュリティ管理者](#security-administrator)** :このロールが割り当てられたユーザーは、Microsoft 365 セキュリティ センター、Azure Active Directory Identity Protection、Azure Information Protection、Office 365 セキュリティ/コンプライアンス センターのセキュリティ関連機能を管理するアクセス許可を持ちます。 Office 365 のアクセス許可の詳細については、「[Office 365 セキュリティ/コンプライアンス センターでのアクセス許可](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)」をご覧ください。
  
  どこで | できること
  --- | ---
  [Microsoft 365 セキュリティ センター](https://protection.office.com) | Microsoft 365 サービス全体のセキュリティ関連ポリシーの監視<br>セキュリティの脅威とアラートの管理<br>レポートを表示する
  Identity Protection Center | セキュリティ閲覧者ロールのすべてのアクセス許可<br>さらに、パスワードのリセットを除く Identity Protection センターのすべての操作を行う権限
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | セキュリティ閲覧者ロールのすべてのアクセス許可<br>Azure AD ロールの割り当てまたは設定を管理することは**できません**
  [Office 365 セキュリティ/コンプライアンス センター](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | セキュリティ ポリシーの管理<br>セキュリティの脅威の表示、調査、対応<br>レポートを表示する
  Azure Advanced Threat Protection | 疑わしいセキュリティ アクティビティの監視と対応
  Windows Defender ATP および EDR | ロールを割り当てる<br>コンピューター グループを管理する<br>エンドポイントの脅威の検出と自動修復の構成<br>アラートの表示、調査、対応
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | ユーザー、デバイス、登録、構成、アプリケーション情報の表示<br>Intune に変更を加えることはできません
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 管理者の追加、ポリシーと設定の追加、ログのアップロード、ガバナンス アクションの実行
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | セキュリティ ポリシーの表示、セキュリティ状態の表示、セキュリティ ポリシーの編集、アラートと推奨事項の表示、アラートと推奨事項の却下を行うことができます
  [Office 365 サービス正常性](https://docs.microsoft.com/office365/enterprise/view-service-health) | Office 365 サービスの正常性の表示

* **[セキュリティ オペレーター](#security-operator)** :このロールのユーザーはアラートを管理できるほか、Microsoft 365 セキュリティ センター、Azure Active Directory、Identity Protection、Privileged Identity Management、および Office 365 セキュリティ/コンプライアンス センター におけるすべての情報を含むセキュリティ関連機能への読み取り専用グローバル アクセス権があります。 Office 365 のアクセス許可の詳細については、「[Office 365 セキュリティ/コンプライアンス センターでのアクセス許可](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center)」をご覧ください。

  どこで | できること
  --- | ---
  [Microsoft 365 セキュリティ センター](https://protection.office.com) | セキュリティ閲覧者ロールのすべてのアクセス許可<br>セキュリティの脅威アラートの表示、調査、対応
  Identity Protection Center | セキュリティ閲覧者ロールのすべてのアクセス許可<br>さらに、パスワードのリセットを除く Identity Protection センターのすべての操作を行う権限
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | セキュリティ閲覧者ロールのすべてのアクセス許可
  [Office 365 セキュリティ/コンプライアンス センター](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | セキュリティ閲覧者ロールのすべてのアクセス許可<br>セキュリティ アラートの表示、調査、対応
  Windows Defender ATP および EDR | セキュリティ閲覧者ロールのすべてのアクセス許可<br>セキュリティ アラートの表示、調査、対応
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | セキュリティ閲覧者ロールのすべてのアクセス許可
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | セキュリティ閲覧者ロールのすべてのアクセス許可
  [Office 365 サービス正常性](https://docs.microsoft.com/office365/enterprise/view-service-health) | Office 365 サービスの正常性の表示
<!--* **[Security Operator](#security-operator)**: Users with this role can manage alerts and have global read-only access on security-related feature, including all information in Microsoft 365 security center, Azure Active Directory, Identity Protection, Privileged Identity Management.-->

* **[セキュリティ閲覧者](#security-reader)** :このロールのユーザーには、Microsoft 365 セキュリティ センター、Azure Active Directory、Identity Protection、Privileged Identity Management におけるすべての情報を含むセキュリティ関連機能への読み取り専用グローバル アクセス権に加えて、Azure Active Directory サインイン レポートと監査ログ、および Office 365 セキュリティ/コンプライアンス センターで閲覧する権限があります。 Office 365 のアクセス許可の詳細については、「[Office 365 セキュリティ/コンプライアンス センターでのアクセス許可](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)」をご覧ください。

  どこで | できること
  --- | ---
  [Microsoft 365 セキュリティ センター](https://protection.office.com) | Microsoft 365 サービス全体のセキュリティ関連ポリシーの表示<br>セキュリティの脅威とアラートの表示<br>レポートを表示する
  Identity Protection Center | 各セキュリティ機能の全セキュリティ レポートと設定情報の閲覧<br><ul><li>スパム対策<li>暗号化<li>データ損失の防止<li>マルウェア対策<li>高度な脅威保護<li>フィッシング詐欺対策<li>メールフロー ルール
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Azure AD PIM に表示される、Azure AD ロール割り当てに関するポリシーとレポート、セキュリティ レビューのすべての情報に対する読み取り専用アクセス権を持ちます。また、将来的には、Azure AD ロール割り当て以外のシナリオのポリシー データとレポートに対する読み取りアクセス権も付与される予定です。<br>Azure AD PIM へのサインアップおよび Azure AD PIM の変更を行うことは**できません**。 このロールのユーザーは、追加のロール (グローバル管理者や特権ロール管理者など) の資格がある場合、PIM ポータルまたは PowerShell からそれらのロールを有効化することができます。
  [Office 365 セキュリティ/コンプライアンス センター](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | セキュリティ ポリシーの表示<br>セキュリティの脅威の表示および調査<br>レポートを表示する
  Windows Defender ATP および EDR | アラートの表示と調査
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | ユーザー、デバイス、登録、構成、アプリケーション情報の表示。 Intune に変更を加えることはできません。
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 読み取り専用アクセス許可があり、アラートを管理できる
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | 推奨事項とアラート、セキュリティ ポリシー、セキュリティの状態を表示することはできますが、変更することはできません
  [Office 365 サービス正常性](https://docs.microsoft.com/office365/enterprise/view-service-health) | Office 365 サービスの正常性の表示

* **[サービス サポート管理者](#service-support-administrator)** :このロールが割り当てられたユーザーは、Azure サービスと Office 365 サービスについて Microsoft へのサポート要求を開くことができます。また、[Azure portal](https://portal.azure.com) と [Microsoft 365 管理センター](https://admin.microsoft.com)で、サービス ダッシュボードとメッセージ センターを表示できます。 詳細については、「 [Office 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。
  > [!NOTE]
  > Microsoft Graph API、Azure AD Graph API、Azure AD PowerShell では、このロールは "サービス サポート管理者" として識別されます。 [Azure portal](https://portal.azure.com)、[Microsoft 365 管理センター](https://admin.microsoft.com)、Intune ポータルでは "サービス管理者" になります。

* **[SharePoint 管理者](#sharepoint-service-administrator)** :このロールが割り当てられたユーザーは、Microsoft SharePoint Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、すべての Office 365 グループを作成および管理し、サポート チケットを管理し、サービス正常性を監視できます。 詳細については、「 [Office 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。
  > [!NOTE]
  > Microsoft Graph API、Azure AD Graph API、Azure AD PowerShell では、このロールは "SharePoint サービス管理者" として識別されます。 [Azure portal](https://portal.azure.com) では、"SharePoint 管理者" になります。

* **[Skype for Business/Lync 管理者](#lync-service-administrator)** :このロールが割り当てられたユーザーは、Microsoft Skype for Business 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、Azure Active Directory で Skype 固有のユーザー属性を管理します。 さらに、このロールはサポート チケットを管理し、サービス正常性を監視して、Teams と Skype for Business の管理センターにアクセスすることができます。 アカウントには、Teams のライセンスが付与されている必要もあります。そうでないと、Teams の PowerShell コマンドレットを実行できません。 詳細については、[Skype for Business の管理者ロール](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5)に関するページを参照してください。Teams のライセンス情報については、[Skype for Business と Microsoft Teams アドオンのライセンス](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)に関するページを参照してください

  > [!NOTE]
  > Microsoft Graph API、Azure AD Graph API、Azure AD PowerShell では、このロールは "Lync サービス管理者" として識別されます。 [Azure portal](https://portal.azure.com/) では、"Skype for Business 管理者" になります。

* **[Teams 管理者](#teams-service-administrator)** :このロールのユーザーは、Microsoft Teams および Skype for Business の管理センターと、対応する PowerShell モジュールを使用して、Microsoft Teams のワークロードの全側面を管理できます。 これにはその他の領域の、テレフォニー、メッセージング、会議、およびチーム自体に関連するすべての管理ツールが含まれます。 このロールはさらに、すべての Office 365 グループの作成および管理、サポート チケットの管理、サービスの正常性の監視を行うこともできます。
  > [!NOTE]
  > Microsoft Graph API、Azure AD Graph API、Azure AD PowerShell では、このロールは "Teams サービス管理者" として識別されます。 [Azure portal](https://portal.azure.com) では、"Teams 管理者" になります。

* **[Teams 通信管理者](#teams-communications-administrator)** :このロールのユーザーは、音声とテレフォニーに関連する Microsoft Teams のワークロードの各側面を管理できます。 これには、電話番号の割り当て、音声と会議のポリシー、および通話分析ツールセットへのフル アクセスのための管理ツールが含まれます。

* **[Teams 通信サポート エンジニア](#teams-communications-support-engineer)** :このロールのユーザーは、Microsoft Teams と Skype for Business の管理センターでユーザー通話のトラブルシューティング ツールを使用して、Microsoft Teams と Skype for Business での通信に関する問題をトラブルシューティングできます。 このロールのユーザーは、関係するすべての参加者の完全な通話記録情報を表示できます。 このロールには、サポート チケットの表示、作成、管理のためのアクセス権がありません。

* **[Teams 通信サポート スペシャリスト](#teams-communications-support-specialist)** :このロールのユーザーは、Microsoft Teams と Skype for Business の管理センターでユーザー通話のトラブルシューティング ツールを使用して、Microsoft Teams と Skype for Business での通信に関する問題をトラブルシューティングできます。 このロールのユーザーが表示できるのは、調査した特定ユーザーの通話における詳細のみです。 このロールには、サポート チケットの表示、作成、管理のためのアクセス権がありません。

* **[ユーザー管理者](#user-administrator)** :このロールが割り当てられたユーザーは、ユーザーを作成し、いくつか制限はありますがユーザーのすべての側面を管理できる (後述) ほか、パスワードの有効期限ポリシーを更新できます。 また、このロールのユーザーは、グループを作成し、すべてのグループを管理することができます。 このロールでは、ユーザー ビューを作成および管理、サポート チケットの管理、サービスの正常性の監視を行うこともできます。

  | | |
  | --- | --- |
  |一般的なアクセス許可|<p>ユーザーとグループを作成する</p><p>ユーザー ビューの作成と管理</p><p>Office サポート チケットの管理<p>パスワードの有効期限ポリシーを更新する|
  |<p>すべての管理者を含むすべてのユーザーに対して</p>|<p>ライセンスを管理する</p><p>ユーザー プリンシパル名を除くすべてのユーザーのプロパティを管理する</p>
  |管理者以外のユーザー、または次の制限付き管理者ロールのいずれかに対してのみ:<ul><li>ディレクトリ リーダー<li>ゲスト招待元<li>ヘルプデスク管理者<li>メッセージ センター閲覧者<li>レポート閲覧者<li>ユーザー管理者|<p>削除と復元</p><p>無効化と有効化</p><p>更新トークンを無効にする</p><p>ユーザー プリンシパル名を含むすべてのユーザーのプロパティを管理する</p><p>[パスワードのリセット]</p><p>(FIDO) デバイス キーを更新する</p>
  
  <b>重要</b>:このロールを持つユーザーは、機密情報や個人情報または Azure Active Directory の内外の重要な構成にアクセスできるユーザーのパスワードを変更できます。 ユーザーのパスワードを変更するということは、そのユーザーの ID およびアクセス許可を取得できるということを意味します。 例:
  * 所有しているアプリの資格情報を管理できる、アプリケーションの登録とエンタープライズ アプリケーションの所有者。 これらのアプリには、Azure AD およびユーザー管理者に付与されていない場所への特権アクセス許可がある場合があります。 ユーザー管理者は、このパスからアプリケーション所有者の ID を取得し、さらにそのアプリケーションの資格情報を更新して特権アプリケーションの ID を取得できる場合があります。
  * 機密情報や個人情報または Azure の重要な構成にアクセスできる Azure サブスクリプション所有者。
  * グループ メンバーシップを管理できるセキュリティ グループと Office 365 グループの所有者。 これらのグループは、機密情報や個人情報または Azure AD や別の場所の重要な構成へのアクセス権を付与される場合があります。
  * Exchange Online、Office Security and Compliance Center、人事システムのような Azure AD 以外のサービスの管理者。
  * 機密情報や個人情報にアクセスできる場合がある役員、弁護士、人事担当者のような非管理者。

## <a name="role-permissions"></a>ロールのアクセス許可
以降の表は、各ロールに割り当てられている Azure Active Directory の具体的なアクセス許可の説明です。 一部のロールは、Azure Active Directory 以外の Microsoft サービスにおけるその他のアクセス許可を持っている場合があります。

### <a name="application-administrator"></a>アプリケーション管理者
アプリ登録とエンタープライズ アプリのすべての側面を作成して管理できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Azure Active Directory での applications.audience property プロパティの更新。 |
| microsoft.aad.directory/applications/authentication/update | Azure Active Directory での applications.authentication プロパティの更新。 |
| microsoft.aad.directory/applications/basic/update | Azure Active Directory での applications の基本プロパティの更新。 |
| microsoft.aad.directory/applications/create | Azure Active Directory での applications の作成。 |
| microsoft.aad.directory/applications/credentials/update | Azure Active Directory での applications.credentials プロパティの更新。 |
| microsoft.aad.directory/applications/delete | Azure Active Directory での applications の削除。 |
| microsoft.aad.directory/applications/owners/update | Azure Active Directory での applications.owners プロパティの更新。 |
| microsoft.aad.directory/applications/permissions/update | Azure Active Directory での applications.permissions プロパティの更新。 |
| microsoft.aad.directory/applications/policies/update | Azure Active Directory での applications.policies プロパティの更新。 |
| microsoft.aad.directory/appRoleAssignments/create | Azure Active Directory での appRoleAssignments の作成。 |
| microsoft.aad.directory/appRoleAssignments/read | Azure Active Directory での appRoleAssignments の読み取り。 |
| microsoft.aad.directory/appRoleAssignments/update | Azure Active Directory での appRoleAssignments の更新。 |
| microsoft.aad.directory/appRoleAssignments/delete | Azure Active Directory での appRoleAssignments の削除。 |
| microsoft.aad.directory/auditLogs/allProperties/read | Azure Active Directory での auditLogs 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Azure Active Directory での policies.applicationConfiguration プロパティの読み取り。 |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Azure Active Directory での policies.applicationConfiguration プロパティの更新。 |
| microsoft.aad.directory/policies/applicationConfiguration/create | Azure Active Directory での policies の作成。 |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Azure Active Directory での policies の削除。 |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Azure Active Directory での policies.applicationConfiguration プロパティの読み取り。 |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Azure Active Directory での policies.applicationConfiguration プロパティの更新。 |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Azure Active Directory での policies.applicationConfiguration プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Azure Active Directory での servicePrincipals.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/audience/update | Azure Active Directory での servicePrincipals.audience プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/authentication/update | Azure Active Directory での servicePrincipals.authentication プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/basic/update | Azure Active Directory での servicePrincipals の基本プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/create | Azure Active Directory での servicePrincipals の作成。 |
| microsoft.aad.directory/servicePrincipals/credentials/update | Azure Active Directory での servicePrincipals.credentials プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/delete | Azure Active Directory での servicePrincipals の削除。 |
| microsoft.aad.directory/servicePrincipals/owners/update | Azure Active Directory での servicePrincipals.owners プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/permissions/update | Azure Active Directory での servicePrincipals.permissions プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/policies/update | Azure Active Directory での servicePrincipals.policies プロパティの更新。 |
| microsoft.aad.directory/signInReports/allProperties/read | Azure Active Directory での signInReports 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="application-developer"></a>アプリケーション開発者
[ユーザーはアプリケーションを登録できる] の設定とは無関係にアプリケーション登録を作成できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Azure Active Directory での applications の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Azure Active Directory での appRoleAssignments の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Azure Active Directory での oAuth2PermissionGrants の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Azure Active Directory での servicePrincipals の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |

### <a name="authentication-administrator"></a>認証管理者
管理者以外のユーザーの認証方法の情報を表示、設定、リセットできます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.aad.directory/users/strongAuthentication/update | MFA 資格情報などの強力な認証プロパティを更新します。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="azure-information-protection-administrator"></a>Azure Information Protection 管理者
Azure Information Protection サービスのすべての側面を管理できます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection の全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="b2c-user-flow-administrator"></a>B2C ユーザー フロー管理者
ユーザー フローのすべての側面の作成と管理。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Azure Active Directory B2C でのユーザー フローの読み取りと構成。 |

### <a name="b2c-user-flow-attribute-administrator"></a>B2C ユーザー フロー属性管理者
すべてのユーザー フローに使用可能な属性スキーマの作成と管理。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Azure Active Directory B2C でユーザー属性の読み取りと構成。 |

### <a name="b2c-ief-keyset-administrator"></a>B2C IEF キーセット管理者
Identity Experience Framework でのフェデレーションのシークレットと暗号化の管理。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Azure Active Directory B2C でのキー セットの読み取りと構成。 |

### <a name="b2c-ief-policy-administrator"></a>B2C IEF ポリシー管理者
Identity Experience Framework での信頼フレームワーク ポリシーの作成と管理。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Azure Active Directory B2C でのカスタム ポリシーの読み取りと構成。 |

### <a name="billing-administrator"></a>課金管理者
支払情報の更新など、よく利用する課金関連タスクを実行できます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/organization/basic/update | Azure Active Directory での organization の基本プロパティの更新。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.commerce.billing/allEntities/allTasks | Office 365 課金の全側面の管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |


### <a name="cloud-application-administrator"></a>クラウド アプリケーション管理者
アプリ登録とエンタープライズ アプリのすべての側面 (アプリ プロキシを除く) を作成して管理できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Azure Active Directory での applications.audience property プロパティの更新。 |
| microsoft.aad.directory/applications/authentication/update | Azure Active Directory での applications.authentication プロパティの更新。 |
| microsoft.aad.directory/applications/basic/update | Azure Active Directory での applications の基本プロパティの更新。 |
| microsoft.aad.directory/applications/create | Azure Active Directory での applications の作成。 |
| microsoft.aad.directory/applications/credentials/update | Azure Active Directory での applications.credentials プロパティの更新。 |
| microsoft.aad.directory/applications/delete | Azure Active Directory での applications の削除。 |
| microsoft.aad.directory/applications/owners/update | Azure Active Directory での applications.owners プロパティの更新。 |
| microsoft.aad.directory/applications/permissions/update | Azure Active Directory での applications.permissions プロパティの更新。 |
| microsoft.aad.directory/applications/policies/update | Azure Active Directory での applications.policies プロパティの更新。 |
| microsoft.aad.directory/appRoleAssignments/create | Azure Active Directory での appRoleAssignments の作成。 |
| microsoft.aad.directory/appRoleAssignments/update | Azure Active Directory での appRoleAssignments の更新。 |
| microsoft.aad.directory/appRoleAssignments/delete | Azure Active Directory での appRoleAssignments の削除。 |
| microsoft.aad.directory/auditLogs/allProperties/read | Azure Active Directory での auditLogs 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.aad.directory/policies/applicationConfiguration/create | Azure Active Directory での policies の作成。 |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Azure Active Directory での policies.applicationConfiguration プロパティの読み取り。 |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Azure Active Directory での policies.applicationConfiguration プロパティの更新。 |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Azure Active Directory での policies の削除。 |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Azure Active Directory での policies.applicationConfiguration プロパティの読み取り。 |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Azure Active Directory での policies.applicationConfiguration プロパティの更新。 |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Azure Active Directory での policies.applicationConfiguration プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Azure Active Directory での servicePrincipals.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/audience/update | Azure Active Directory での servicePrincipals.audience プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/authentication/update | Azure Active Directory での servicePrincipals.authentication プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/basic/update | Azure Active Directory での servicePrincipals の基本プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/create | Azure Active Directory での servicePrincipals の作成。 |
| microsoft.aad.directory/servicePrincipals/credentials/update | Azure Active Directory での servicePrincipals.credentials プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/delete | Azure Active Directory での servicePrincipals の削除。 |
| microsoft.aad.directory/servicePrincipals/owners/update | Azure Active Directory での servicePrincipals.owners プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/permissions/update | Azure Active Directory での servicePrincipals.permissions プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/policies/update | Azure Active Directory での servicePrincipals.policies プロパティの更新。 |
| microsoft.aad.directory/signInReports/allProperties/read | Azure Active Directory での signInReports 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="cloud-device-administrator"></a>クラウド デバイス管理者
Azure AD でデバイスを管理するためのフル アクセス。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Azure Active Directory での auditLogs 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Azure Active Directory での devices.bitLockerRecoveryKeys プロパティの読み取り。 |
| microsoft.aad.directory/devices/delete | Azure Active Directory での devices の削除。 |
| microsoft.aad.directory/devices/disable | Azure Active Directory での devices の無効化。 |
| microsoft.aad.directory/devices/enable | Azure Active Directory での devices の有効化。 |
| microsoft.aad.directory/signInReports/allProperties/read | Azure Active Directory での signInReports 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |

### <a name="company-administrator"></a>会社の管理者
Azure AD のすべての側面と、Azure AD の ID が使用される Microsoft サービスを管理できます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | microsoft.aad.cloudAppSecurity でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Azure Active Directory での administrativeUnits の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/applications/allProperties/allTasks | Azure Active Directory での applications の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Azure Active Directory での appRoleAssignments の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/auditLogs/allProperties/read | Azure Active Directory での auditLogs 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.aad.directory/contacts/allProperties/allTasks | Azure Active Directory での contacts の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/contracts/allProperties/allTasks | Azure Active Directory での contracts の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/devices/allProperties/allTasks | Azure Active Directory での devices の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Azure Active Directory での directoryRoles の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Azure Active Directory での directoryRoleTemplates の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/domains/allProperties/allTasks | Azure Active Directory での domains の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/groups/allProperties/allTasks | Azure Active Directory での groups の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Azure Active Directory での groupSettings の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Azure Active Directory での groupSettingTemplates の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Azure Active Directory での loginTenantBranding の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Azure Active Directory での oAuth2PermissionGrants の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/organization/allProperties/allTasks | Azure Active Directory での organization の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/policies/allProperties/allTasks | Azure Active Directory での policies の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Azure Active Directory での roleAssignments の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Azure Active Directory での roleDefinitions の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Azure Active Directory での scopedRoleMemberships の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/serviceAction/activateService | Azure Active Directory で Activateservice サービス アクションを実行可能 |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Azure Active Directory で Disabledirectoryfeature サービス アクションを実行可能 |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Azure Active Directory で Enabledirectoryfeature サービス アクションを実行可能 |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Azure Active Directory で Getavailableextentionproperties サービス アクションを実行可能 |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Azure Active Directory での servicePrincipals の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/signInReports/allProperties/read | Azure Active Directory での signInReports 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Azure Active Directory での subscribedSkus の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/users/allProperties/allTasks | Azure Active Directory での users の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directorySync/allEntities/allTasks | Azure AD Connect でのすべてのアクションの実行。 |
| microsoft.aad.identityProtection/allEntities/allTasks | microsoft.aad.identityProtection でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | microsoft.aad.privilegedIdentityManagement でのすべてのリソースの読み取り。 |
| microsoft.azure.advancedThreatProtection/allEntities/read | microsoft.azure.advancedThreatProtection でのすべてのリソースの読み取り。 |
| microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection の全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.commerce.billing/allEntities/allTasks | Office 365 課金の全側面の管理。 |
| microsoft.intune/allEntities/allTasks | Intune の全側面の管理。 |
| microsoft.office365.complianceManager/allEntities/allTasks | Office 365 コンプライアンス マネージャーの全側面の管理 |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | デスクトップ Analytics のすべての側面を管理します。 |
| microsoft.office365.exchange/allEntities/allTasks | Exchange Online の全側面の管理。 |
| microsoft.office365.lockbox/allEntities/allTasks | Office 365 カスタマ― ロックボックスの全側面の管理 |
| microsoft.office365.messageCenter/messages/read | Microsoft.office365.messageCenter でメッセージを読み取ります。 |
| microsoft.office365.messageCenter/securityMessages/read | Microsoft.office365.messageCenter でセキュリティ メッセージを読み取ります。 |
| microsoft.office365.protectionCenter/allEntities/allTasks | Office 365 プロテクション センターの全側面の管理。 |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | microsoft.office365.securityComplianceCenter でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.sharepoint/allEntities/allTasks | microsoft.office365.sharepoint でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Skype for Business Online の全側面の管理。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.usageReports/allEntities/read | Office 365 の使用状況レポートの読み取り。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Dynamics 365 の全側面の管理。 |
| microsoft.powerApps.powerBI/allEntities/allTasks | Power BI の全側面の管理。 |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | microsoft.windows.defenderAdvancedThreatProtection でのすべてのリソースの読み取り。 |

### <a name="compliance-administrator"></a>コンプライアンス管理者
Azure AD および Office 365 のコンプライアンスの構成とレポートを読み取り、管理できます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.complianceManager/allEntities/allTasks | Office 365 コンプライアンス マネージャーの全側面の管理 |
| microsoft.office365.exchange/allEntities/allTasks | Exchange Online の全側面の管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.sharepoint/allEntities/allTasks | microsoft.office365.sharepoint でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Skype for Business Online の全側面の管理。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="compliance-data-administrator"></a>コンプライアンス データ管理者
コンプライアンス コンテンツを作成、管理します。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Microsoft Cloud App Security の読み取りと構成。 |
| microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection の全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.complianceManager/allEntities/allTasks | Office 365 コンプライアンス マネージャーの全側面の管理 |
| microsoft.office365.exchange/allEntities/allTasks | Exchange Online の全側面の管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.sharepoint/allEntities/allTasks | microsoft.office365.sharepoint でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Skype for Business Online の全側面の管理。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="conditional-access-administrator"></a>条件付きアクセス管理者
条件付きアクセスの機能を管理できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | Azure Active Directory での policies.conditionalAccess プロパティの読み取り。 |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | Azure Active Directory での policies.conditionalAccess プロパティの更新。 |
| microsoft.aad.directory/policies/conditionalAccess/create | Azure Active Directory での policies の作成。 |
| microsoft.aad.directory/policies/conditionalAccess/delete | Azure Active Directory での policies の削除。 |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Azure Active Directory での policies.conditionalAccess プロパティの読み取り。 |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Azure Active Directory での policies.conditionalAccess プロパティの更新。 |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Azure Active Directory での policies.conditionalAccess プロパティの読み取り。 |
| microsoft.aad.directory/policies/conditionalAccess/tenantDefault/update | Azure Active Directory での policies.conditionalAccess プロパティの更新。 |

### <a name="crm-service-administrator"></a>CRM サービス管理者
Dynamics 365 製品のすべての側面を管理できます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Dynamics 365 の全側面の管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="customer-lockbox-access-approver"></a>カスタマー ロックボックスのアクセス承認者
Microsoft サポートがお客様の組織データにアクセスする要求を承認することができます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.lockbox/allEntities/allTasks | Office 365 カスタマ― ロックボックスの全側面の管理 |

### <a name="desktop-analytics-administrator"></a>デスクトップ Analytics 管理者
デスクトップ Analytics および Office のカスタマイズとポリシーのサービスを管理できます。 デスクトップ Analytics の場合、これには、資産インベントリの表示、デプロイ プランの作成、デプロイと正常性の状態の表示に対する権限が含まれます。 Office のカスタマイズとポリシーのサービスの場合、このロールによりユーザーは Office のポリシーを管理することができます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | デスクトップ Analytics のすべての側面を管理します。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="device-administrators"></a>デバイス管理者
このロールに割り当てられたユーザーは、Azure AD 参加済みデバイスのローカル管理者グループに追加されます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | Azure Active Directory での groupSettings の基本プロパティの読み取り。 |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Azure Active Directory での groupSettingTemplates の基本プロパティの読み取り。 |

### <a name="directory-readers"></a>ディレクトリ リーダー
基本的なディレクトリ情報を読み取ることができます ユーザーではなく、アプリケーションへのアクセスを許可する場合。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | Azure Active Directory での administrativeUnits の基本プロパティの読み取り。 |
| microsoft.aad.directory/administrativeUnits/members/read | Azure Active Directory での administrativeUnits.members プロパティの読み取り。 |
| microsoft.aad.directory/applications/basic/read | Azure Active Directory での applications の基本プロパティの読み取り。 |
| microsoft.aad.directory/applications/owners/read | Azure Active Directory での applications.owners プロパティの読み取り。 |
| microsoft.aad.directory/applications/policies/read | Azure Active Directory での applications.policies プロパティの読み取り。 |
| microsoft.aad.directory/contacts/basic/read | Azure Active Directory での contacts の基本プロパティの読み取り。 |
| microsoft.aad.directory/contacts/memberOf/read | Azure Active Directory での contacts.memberOf プロパティの読み取り。 |
| microsoft.aad.directory/contracts/basic/read | Azure Active Directory での contracts の基本プロパティの読み取り。 |
| microsoft.aad.directory/devices/basic/read | Azure Active Directory での devices の基本プロパティの読み取り。 |
| microsoft.aad.directory/devices/memberOf/read | Azure Active Directory での devices.memberOf プロパティの読み取り。 |
| microsoft.aad.directory/devices/registeredOwners/read | Azure Active Directory での devices.registeredOwners プロパティの読み取り。 |
| microsoft.aad.directory/devices/registeredUsers/read | Azure Active Directory での devices.registeredUsers プロパティの読み取り。 |
| microsoft.aad.directory/directoryRoles/basic/read | Azure Active Directory での directoryRoles の基本プロパティの読み取り。 |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Azure Active Directory での directoryRoles.eligibleMembers プロパティの読み取り。 |
| microsoft.aad.directory/directoryRoles/members/read | Azure Active Directory での directoryRoles.members プロパティの読み取り。 |
| microsoft.aad.directory/domains/basic/read | Azure Active Directory での domains の基本プロパティの読み取り。 |
| microsoft.aad.directory/groups/appRoleAssignments/read | Azure Active Directory での groups.appRoleAssignments プロパティの読み取り。 |
| microsoft.aad.directory/groups/basic/read | Azure Active Directory での groups の基本プロパティの読み取り。 |
| microsoft.aad.directory/groups/memberOf/read | Azure Active Directory での groups.memberOf プロパティの読み取り。 |
| microsoft.aad.directory/groups/members/read | Azure Active Directory での groups.members プロパティの読み取り。 |
| microsoft.aad.directory/groups/owners/read | Azure Active Directory での groups.owners プロパティの読み取り。 |
| microsoft.aad.directory/groups/settings/read | Azure Active Directory での groups.settings プロパティの読み取り。 |
| microsoft.aad.directory/groupSettings/basic/read | Azure Active Directory での groupSettings の基本プロパティの読み取り。 |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Azure Active Directory での groupSettingTemplates の基本プロパティの読み取り。 |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | Azure Active Directory での oAuth2PermissionGrants の基本プロパティの読み取り。 |
| microsoft.aad.directory/organization/basic/read | Azure Active Directory での organization の基本プロパティの読み取り。 |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Azure Active Directory での organization.trustedCAsForPasswordlessAuth プロパティの読み取り。 |
| microsoft.aad.directory/roleAssignments/basic/read | Azure Active Directory での roleAssignments の基本プロパティの読み取り。 |
| microsoft.aad.directory/roleDefinitions/basic/read | Azure Active Directory での roleDefinitions の基本プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Azure Active Directory での servicePrincipals.appRoleAssignments プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/basic/read | Azure Active Directory での servicePrincipals の基本プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Azure Active Directory での servicePrincipals.memberOf プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Azure Active Directory での servicePrincipals.oAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Azure Active Directory での servicePrincipals.ownedObjects プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/owners/read | Azure Active Directory での servicePrincipals.owners プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/policies/read | Azure Active Directory での servicePrincipals.policies プロパティの読み取り。 |
| microsoft.aad.directory/subscribedSkus/basic/read | Azure Active Directory での subscribedSkus の基本プロパティの読み取り。 |
| microsoft.aad.directory/users/appRoleAssignments/read | Azure Active Directory での users.appRoleAssignments プロパティの読み取り。 |
| microsoft.aad.directory/users/basic/read | Azure Active Directory での users の基本プロパティの読み取り。 |
| microsoft.aad.directory/users/directReports/read | Azure Active Directory での users.directReports プロパティの読み取り。 |
| microsoft.aad.directory/users/manager/read | Azure Active Directory での users.manager プロパティの読み取り。 |
| microsoft.aad.directory/users/memberOf/read | Azure Active Directory での users.memberOf プロパティの読み取り。 |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Azure Active Directory での users.oAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.aad.directory/users/ownedDevices/read | Azure Active Directory での users.ownedDevices プロパティの読み取り。 |
| microsoft.aad.directory/users/ownedObjects/read | Azure Active Directory での users.ownedObjects プロパティの読み取り。 |
| microsoft.aad.directory/users/registeredDevices/read | Azure Active Directory での users.registeredDevices プロパティの読み取り。 |

### <a name="directory-synchronization-accounts"></a>ディレクトリ同期アカウント
Azure AD Connect サービスでのみ使用されます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Azure Active Directory での organization.dirSync プロパティの更新。 |
| microsoft.aad.directory/policies/create | Azure Active Directory での policies の作成。 |
| microsoft.aad.directory/policies/delete | Azure Active Directory での policies の削除。 |
| microsoft.aad.directory/policies/basic/read | Azure Active Directory での policies の基本プロパティの読み取り。 |
| microsoft.aad.directory/policies/basic/update | Azure Active Directory での policies の基本プロパティの更新。 |
| microsoft.aad.directory/policies/owners/read | Azure Active Directory での policies.owners プロパティの読み取り。 |
| microsoft.aad.directory/policies/owners/update | Azure Active Directory での policies.owners プロパティの更新。 |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Azure Active Directory での policies.policiesAppliedTo プロパティの読み取り。 |
| microsoft.aad.directory/policies/tenantDefault/update | Azure Active Directory での policies.tenantDefault プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Azure Active Directory での servicePrincipals.appRoleAssignments プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Azure Active Directory での servicePrincipals.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/audience/update | Azure Active Directory での servicePrincipals.audience プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/authentication/update | Azure Active Directory での servicePrincipals.authentication プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/basic/read | Azure Active Directory での servicePrincipals の基本プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/basic/update | Azure Active Directory での servicePrincipals の基本プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/create | Azure Active Directory での servicePrincipals の作成。 |
| microsoft.aad.directory/servicePrincipals/credentials/update | Azure Active Directory での servicePrincipals.credentials プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Azure Active Directory での servicePrincipals.memberOf プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Azure Active Directory での servicePrincipals.oAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/owners/read | Azure Active Directory での servicePrincipals.owners プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/owners/update | Azure Active Directory での servicePrincipals.owners プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Azure Active Directory での servicePrincipals.ownedObjects プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/permissions/update | Azure Active Directory での servicePrincipals.permissions プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/policies/read | Azure Active Directory での servicePrincipals.policies プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/policies/update | Azure Active Directory での servicePrincipals.policies プロパティの更新。 |
| microsoft.aad.directorySync/allEntities/allTasks | Azure AD Connect でのすべてのアクションの実行。 |

### <a name="directory-writers"></a>ディレクトリ ライター
基本的なディレクトリ情報の読み取りと書き込みを実行できます ユーザーではなく、アプリケーションへのアクセスを許可する場合。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/groups/create | Azure Active Directory での groups の作成。 |
| microsoft.aad.directory/groups/createAsOwner | Azure Active Directory での groups の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/groups/appRoleAssignments/update | Azure Active Directory での groups.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/groups/basic/update | Azure Active Directory での groups の基本プロパティの更新。 |
| microsoft.aad.directory/groups/members/update | Azure Active Directory での groups.members プロパティの更新。 |
| microsoft.aad.directory/groups/owners/update | Azure Active Directory での groups.owners プロパティの更新。 |
| microsoft.aad.directory/groups/settings/update | Azure Active Directory での groups.settings プロパティの更新。 |
| microsoft.aad.directory/groupSettings/basic/update | Azure Active Directory での groupSettings の基本プロパティの更新。 |
| microsoft.aad.directory/groupSettings/create | Azure Active Directory での groupSettings の作成。 |
| microsoft.aad.directory/groupSettings/delete | Azure Active Directory での groupSettings の削除。 |
| microsoft.aad.directory/users/appRoleAssignments/update | Azure Active Directory での users.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/users/assignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.aad.directory/users/basic/update | Azure Active Directory での users の基本プロパティの更新。 |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.aad.directory/users/manager/update | Azure Active Directory での users.manager プロパティの更新。 |
| microsoft.aad.directory/users/userPrincipalName/update | Azure Active Directory での users.userPrincipalName プロパティの更新。 |

### <a name="exchange-service-administrator"></a>Exchange サービス管理者
Exchange 製品のすべての側面を管理できます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Azure Active Directory 内での groups.unified プロパティの更新。 |
| microsoft.aad.directory/groups/unified/basic/update | Office 365 グループの基本プロパティを更新します。 |
| microsoft.aad.directory/groups/unified/create | Office 365 グループを作成します。 |
| microsoft.aad.directory/groups/unified/delete | Office 365 グループを削除します。 |
| microsoft.aad.directory/groups/unified/members/update | Office 365 グループのメンバーシップを更新します。 |
| microsoft.aad.directory/groups/unified/owners/update | Office 365 グループの所有権を更新します。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.exchange/allEntities/allTasks | Exchange Online の全側面の管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="external-identity-provider-administrator"></a>外部 ID プロバイダー管理者
直接フェデレーションで使用するための ID プロバイダーの構成。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Azure Active Directory B2C での ID プロバイダーの読み取りと構成。 |

### <a name="guest-inviter"></a>ゲスト招待元
[メンバーがゲストを招待できる] の設定とは無関係にゲスト ユーザーを招待できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Azure Active Directory での users.appRoleAssignments プロパティの読み取り。 |
| microsoft.aad.directory/users/basic/read | Azure Active Directory での users の基本プロパティの読み取り。 |
| microsoft.aad.directory/users/directReports/read | Azure Active Directory での users.directReports プロパティの読み取り。 |
| microsoft.aad.directory/users/inviteGuest | Azure Active Directory でのゲスト ユーザーの招待。 |
| microsoft.aad.directory/users/manager/read | Azure Active Directory での users.manager プロパティの読み取り。 |
| microsoft.aad.directory/users/memberOf/read | Azure Active Directory での users.memberOf プロパティの読み取り。 |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Azure Active Directory での users.oAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.aad.directory/users/ownedDevices/read | Azure Active Directory での users.ownedDevices プロパティの読み取り。 |
| microsoft.aad.directory/users/ownedObjects/read | Azure Active Directory での users.ownedObjects プロパティの読み取り。 |
| microsoft.aad.directory/users/registeredDevices/read | Azure Active Directory での users.registeredDevices プロパティの読み取り。 |

### <a name="helpdesk-administrator"></a>ヘルプデスク管理者
管理者以外のユーザーとヘルプデスク管理者のパスワードをリセットできます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Azure Active Directory での devices.bitLockerRecoveryKeys プロパティの読み取り。 |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.aad.directory/users/password/update | Azure Active Directory でのすべてのユーザーのパスワードの更新。 詳細については、オンライン ドキュメントを参照してください。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="intune-service-administrator"></a>Intune サービス管理者
Intune 製品のすべての側面を管理できます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Azure Active Directory での contacts の基本プロパティの更新。 |
| microsoft.aad.directory/contacts/create | Azure Active Directory での contacts の作成。 |
| microsoft.aad.directory/contacts/delete | Azure Active Directory での contacts の削除。 |
| microsoft.aad.directory/devices/basic/update | Azure Active Directory での devices の基本プロパティの更新。 |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Azure Active Directory での devices.bitLockerRecoveryKeys プロパティの読み取り。 |
| microsoft.aad.directory/devices/create | Azure Active Directory での devices の作成。 |
| microsoft.aad.directory/devices/delete | Azure Active Directory での devices の削除。 |
| microsoft.aad.directory/devices/registeredOwners/update | Azure Active Directory での devices.registeredOwners プロパティの更新。 |
| microsoft.aad.directory/devices/registeredUsers/update | Azure Active Directory での devices.registeredUsers プロパティの更新。 |
| microsoft.aad.directory/groups/appRoleAssignments/update | Azure Active Directory での groups.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/groups/basic/update | Azure Active Directory での groups の基本プロパティの更新。 |
| microsoft.aad.directory/groups/create | Azure Active Directory での groups の作成。 |
| microsoft.aad.directory/groups/createAsOwner | Azure Active Directory での groups の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/groups/delete | Azure Active Directory での groups の削除。 |
| microsoft.aad.directory/groups/hiddenMembers/read | Azure Active Directory での groups.hiddenMembers プロパティの読み取り。 |
| microsoft.aad.directory/groups/members/update | Azure Active Directory での groups.members プロパティの更新。 |
| microsoft.aad.directory/groups/owners/update | Azure Active Directory での groups.owners プロパティの更新。 |
| microsoft.aad.directory/groups/restore | Azure Active Directory での groups の復元。 |
| microsoft.aad.directory/groups/settings/update | Azure Active Directory での groups.settings プロパティの更新。 |
| microsoft.aad.directory/users/appRoleAssignments/update | Azure Active Directory での users.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/users/basic/update | Azure Active Directory での users の基本プロパティの更新。 |
| microsoft.aad.directory/users/manager/update | Azure Active Directory での users.manager プロパティの更新。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.intune/allEntities/allTasks | Intune の全側面の管理。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |

### <a name="kaizala-administrator"></a>Kaizala 管理者
Microsoft Kaizala の設定を管理できます。  

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >  
  
| **アクション** | **説明** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | Office 365 管理センターの読み取り。 |

### <a name="license-administrator"></a>ライセンス管理者
ユーザーおよびグループの製品ライセンスを管理できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.aad.directory/users/usageLocation/update | Azure Active Directory での users.usageLocation プロパティの更新。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |

### <a name="lync-service-administrator"></a>Lync サービス管理者
Skype for Business 製品のすべての側面を管理できます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Skype for Business Online の全側面の管理。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="message-center-privacy-reader"></a>メッセージ センターのプライバシー閲覧者
メッセージ センターの投稿、データのプライバシー メッセージ、グループ、ドメイン、およびサブスクリプションを読み取ることができます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.messageCenter/messages/read | Microsoft.office365.messageCenter でメッセージを読み取ります。 |
| microsoft.office365.messageCenter/securityMessages/read | Microsoft.office365.messageCenter でセキュリティ メッセージを読み取ります。 |

### <a name="message-center-reader"></a>メッセージ センター閲覧者
Office 365 メッセージ センター内でのみ自分の組織のメッセージと更新情報を閲覧することができます。 

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.messageCenter/messages/read | Microsoft.office365.messageCenter でメッセージを読み取ります。 |

### <a name="partner-tier1-support"></a>パートナー レベル 1 のサポート
使用しないでください。一般的な使用は想定されていません。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Azure Active Directory での contacts の基本プロパティの更新。 |
| microsoft.aad.directory/contacts/create | Azure Active Directory での contacts の作成。 |
| microsoft.aad.directory/contacts/delete | Azure Active Directory での contacts の削除。 |
| microsoft.aad.directory/groups/create | Azure Active Directory での groups の作成。 |
| microsoft.aad.directory/groups/createAsOwner | Azure Active Directory での groups の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/groups/members/update | Azure Active Directory での groups.members プロパティの更新。 |
| microsoft.aad.directory/groups/owners/update | Azure Active Directory での groups.owners プロパティの更新。 |
| microsoft.aad.directory/users/appRoleAssignments/update | Azure Active Directory での users.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/users/assignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.aad.directory/users/basic/update | Azure Active Directory での users の基本プロパティの更新。 |
| microsoft.aad.directory/users/delete | Azure Active Directory での users の削除。 |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.aad.directory/users/manager/update | Azure Active Directory での users.manager プロパティの更新。 |
| microsoft.aad.directory/users/password/update | Azure Active Directory でのすべてのユーザーのパスワードの更新。 詳細については、オンライン ドキュメントを参照してください。 |
| microsoft.aad.directory/users/restore | Azure Active Directory での削除済みユーザーの復元。 |
| microsoft.aad.directory/users/userPrincipalName/update | Azure Active Directory での users.userPrincipalName プロパティの更新。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="partner-tier2-support"></a>パートナー レベル 2 のサポート
使用しないでください。一般的な使用は想定されていません。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Azure Active Directory での contacts の基本プロパティの更新。 |
| microsoft.aad.directory/contacts/create | Azure Active Directory での contacts の作成。 |
| microsoft.aad.directory/contacts/delete | Azure Active Directory での contacts の削除。 |
| microsoft.aad.directory/domains/allTasks | Azure Active Directory での domains の作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.directory/groups/create | Azure Active Directory での groups の作成。 |
| microsoft.aad.directory/groups/delete | Azure Active Directory での groups の削除。 |
| microsoft.aad.directory/groups/members/update | Azure Active Directory での groups.members プロパティの更新。 |
| microsoft.aad.directory/groups/restore | Azure Active Directory での groups の復元。 |
| microsoft.aad.directory/organization/basic/update | Azure Active Directory での organization の基本プロパティの更新。 |
| microsoft.aad.directory/users/appRoleAssignments/update | Azure Active Directory での users.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/users/assignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.aad.directory/users/basic/update | Azure Active Directory での users の基本プロパティの更新。 |
| microsoft.aad.directory/users/delete | Azure Active Directory での users の削除。 |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.aad.directory/users/manager/update | Azure Active Directory での users.manager プロパティの更新。 |
| microsoft.aad.directory/users/password/update | Azure Active Directory でのすべてのユーザーのパスワードの更新。 詳細については、オンライン ドキュメントを参照してください。 |
| microsoft.aad.directory/users/restore | Azure Active Directory での削除済みユーザーの復元。 |
| microsoft.aad.directory/users/userPrincipalName/update | Azure Active Directory での users.userPrincipalName プロパティの更新。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="power-bi-service-administrator"></a>Power BI サービス管理者
Power BI 製品のすべての側面を管理できます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.powerApps.powerBI/allEntities/allTasks | Power BI の全側面の管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="privileged-authentication-administrator"></a>特権認証管理者
任意のユーザー (管理者または管理者以外) の認証方法の情報を表示、設定、リセットできます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.aad.directory/users/strongAuthentication/update | MFA 資格情報などの強力な認証プロパティを更新します。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="privileged-role-administrator"></a>特権ロール管理者
Azure AD でのロールの割り当てと、Privileged Identity Management のすべての側面を管理できます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | microsoft.aad.privilegedIdentityManagement でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/allTasks | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの読み取りと構成。 |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Azure Active Directory での servicePrincipals.oAuth2PermissionGrants プロパティの読み取りと構成。 |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | 管理単位 (メンバーを含む) の作成と管理。 |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | ロールの割り当ての作成と管理。 |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | ロール定義の作成と管理。 |

### <a name="reports-reader"></a>レポート閲覧者
サインインと監査のレポートを読み取ることができます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Azure Active Directory での auditLogs 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.aad.directory/signInReports/allProperties/read | Azure Active Directory での signInReports 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.usageReports/allEntities/read | Office 365 の使用状況レポートの読み取り。 |

### <a name="search-administrator"></a>Search 管理者
Microsoft Search 設定のすべての側面を作成および管理できます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Microsoft.office365.messageCenter でメッセージを読み取ります。 |
| microsoft.office365.search/allEntities/allProperties/allTasks | microsoft.Office365.search でのすべてのリソースの作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.usageReports/allEntities/read | Office 365 の使用状況レポートの読み取り。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |

### <a name="search-editor"></a>Search エディター
ブックマーク、Q&A、場所、フロアプランなどの編集コンテンツを作成および管理することができます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Microsoft.office365.messageCenter でメッセージを読み取ります。 |
| microsoft.office365.search/content/allProperties/allTasks | microsoft.Office365.search でのコンテンツの作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.office365.usageReports/allEntities/read | Office 365 の使用状況レポートの読み取り。 |

### <a name="security-administrator"></a>セキュリティ管理者
セキュリティ情報とレポートを読み取り、Azure AD と Office 365 で構成を管理することができます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | Azure Active Directory での applications.policies プロパティの更新。 |
| microsoft.aad.directory/auditLogs/allProperties/read | Azure Active Directory での auditLogs 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Azure Active Directory での devices.bitLockerRecoveryKeys プロパティの読み取り。 |
| microsoft.aad.directory/policies/basic/update | Azure Active Directory での policies の基本プロパティの更新。 |
| microsoft.aad.directory/policies/create | Azure Active Directory での policies の作成。 |
| microsoft.aad.directory/policies/delete | Azure Active Directory での policies の削除。 |
| microsoft.aad.directory/policies/owners/update | Azure Active Directory での policies.owners プロパティの更新。 |
| microsoft.aad.directory/policies/tenantDefault/update | Azure Active Directory での policies.tenantDefault プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/policies/update | Azure Active Directory での servicePrincipals.policies プロパティの更新。 |
| microsoft.aad.directory/signInReports/allProperties/read | Azure Active Directory での signInReports 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.aad.identityProtection/allEntities/read | microsoft.aad.identityProtection でのすべてのリソースの読み取り。 |
| microsoft.aad.identityProtection/allEntities/update | microsoft.aad.identityProtection でのすべてのリソースの更新。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | microsoft.aad.privilegedIdentityManagement でのすべてのリソースの読み取り。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.protectionCenter/allEntities/read | Office 365 プロテクション センターの全側面の読み取り。 |
| microsoft.office365.protectionCenter/allEntities/update | microsoft.office365.protectionCenter でのすべてのリソースの更新。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |

### <a name="security-operator"></a>セキュリティ オペレーター
セキュリティ イベントを作成、管理します。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Microsoft Cloud App Security の読み取りと構成。 |
| microsoft.aad.identityProtection/allEntities/read | microsoft.aad.identityProtection でのすべてのリソースの読み取り。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | microsoft.aad.privilegedIdentityManagement でのすべてのリソースの読み取り。 |
| microsoft.azure.advancedThreatProtection/allEntities/read | Azure AD Advanced Threat Protection の読み取りと構成。 |
| microsoft.intune/allEntities/allTasks | Intune の全側面の管理。 |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | セキュリティ/コンプライアンス センターの読み取りと構成。 |
| microsoft.office365.usageReports/allEntities/read | Office 365 の使用状況レポートの読み取り。 |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Windows Defender Advanced Threat Protection の読み取りと構成。 |

### <a name="security-reader"></a>セキュリティ閲覧者
Azure AD と Office 365 のセキュリティ情報とレポートを読み取ることができます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Azure Active Directory での auditLogs 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Azure Active Directory での devices.bitLockerRecoveryKeys プロパティの読み取り。 |
| microsoft.aad.directory/signInReports/allProperties/read | Azure Active Directory での signInReports 上のすべてのプロパティ (特権プロパティを含む) の読み取り。 |
| microsoft.aad.identityProtection/allEntities/read | microsoft.aad.identityProtection でのすべてのリソースの読み取り。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | microsoft.aad.privilegedIdentityManagement でのすべてのリソースの読み取り。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.protectionCenter/allEntities/read | Office 365 プロテクション センターの全側面の読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |

### <a name="service-support-administrator"></a>サービス サポート管理者
サービス正常性に関する情報を読み取り、サポート チケットを管理することができます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="sharepoint-service-administrator"></a>SharePoint サービス管理者
SharePoint サービスのすべての側面を管理できます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Azure Active Directory 内での groups.unified プロパティの更新。 |
| microsoft.aad.directory/groups/unified/basic/update | Office 365 グループの基本プロパティを更新します。 |
| microsoft.aad.directory/groups/unified/create | Office 365 グループを作成します。 |
| microsoft.aad.directory/groups/unified/delete | Office 365 グループを削除します。 |
| microsoft.aad.directory/groups/unified/members/update | Office 365 グループのメンバーシップを更新します。 |
| microsoft.aad.directory/groups/unified/owners/update | Office 365 グループの所有権を更新します。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.sharepoint/allEntities/allTasks | microsoft.office365.sharepoint でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="teams-communications-administrator"></a>Teams 通信管理者
Microsoft Teams サービス内での通話と会議の機能を管理できます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.usageReports/allEntities/read | Office 365 の使用状況レポートの読み取り。 |

### <a name="teams-communications-support-engineer"></a>Teams 通信サポート エンジニア
高度なツールを使用して、Teams 内の通信の問題のトラブルシューティングを行えます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |

### <a name="teams-communications-support-specialist"></a>Teams 通信サポート スペシャリスト
基本的なツールを使用して、Teams 内の通信の問題のトラブルシューティングを行えます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |

### <a name="teams-service-administrator"></a>Teams サービス管理者
Microsoft Teams サービスを管理できます。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Azure Active Directory での groups.hiddenMembers プロパティの読み取り。 |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Azure Active Directory 内での groups.unified プロパティの更新。 |
| microsoft.aad.directory/groups/unified/basic/update | Office 365 グループの基本プロパティを更新します。 |
| microsoft.aad.directory/groups/unified/create | Office 365 グループを作成します。 |
| microsoft.aad.directory/groups/unified/delete | Office 365 グループを削除します。 |
| microsoft.aad.directory/groups/unified/members/update | Office 365 グループのメンバーシップを更新します。 |
| microsoft.aad.directory/groups/unified/owners/update | Office 365 グループの所有権を更新します。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.office365.usageReports/allEntities/read | Office 365 の使用状況レポートの読み取り。 |

### <a name="user-administrator"></a>ユーザー管理者
ユーザーとグループのすべての側面を、制限付きの管理者のパスワードをリセットすることも含めて、管理できます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Azure Active Directory での appRoleAssignments の作成。 |
| microsoft.aad.directory/appRoleAssignments/delete | Azure Active Directory での appRoleAssignments の削除。 |
| microsoft.aad.directory/appRoleAssignments/update | Azure Active Directory での appRoleAssignments の更新。 |
| microsoft.aad.directory/contacts/basic/update | Azure Active Directory での contacts の基本プロパティの更新。 |
| microsoft.aad.directory/contacts/create | Azure Active Directory での contacts の作成。 |
| microsoft.aad.directory/contacts/delete | Azure Active Directory での contacts の削除。 |
| microsoft.aad.directory/groups/appRoleAssignments/update | Azure Active Directory での groups.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/groups/basic/update | Azure Active Directory での groups の基本プロパティの更新。 |
| microsoft.aad.directory/groups/create | Azure Active Directory での groups の作成。 |
| microsoft.aad.directory/groups/createAsOwner | Azure Active Directory での groups の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/groups/delete | Azure Active Directory での groups の削除。 |
| microsoft.aad.directory/groups/hiddenMembers/read | Azure Active Directory での groups.hiddenMembers プロパティの読み取り。 |
| microsoft.aad.directory/groups/members/update | Azure Active Directory での groups.members プロパティの更新。 |
| microsoft.aad.directory/groups/owners/update | Azure Active Directory での groups.owners プロパティの更新。 |
| microsoft.aad.directory/groups/restore | Azure Active Directory での groups の復元。 |
| microsoft.aad.directory/groups/settings/update | Azure Active Directory での groups.settings プロパティの更新。 |
| microsoft.aad.directory/users/appRoleAssignments/update | Azure Active Directory での users.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/users/assignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.aad.directory/users/basic/update | Azure Active Directory での users の基本プロパティの更新。 |
| microsoft.aad.directory/users/create | Azure Active Directory での users の作成。 |
| microsoft.aad.directory/users/delete | Azure Active Directory での users の削除。 |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.aad.directory/users/manager/update | Azure Active Directory での users.manager プロパティの更新。 |
| microsoft.aad.directory/users/password/update | Azure Active Directory でのすべてのユーザーのパスワードの更新。 詳細については、オンライン ドキュメントを参照してください。 |
| microsoft.aad.directory/users/restore | Azure Active Directory での削除済みユーザーの復元。 |
| microsoft.aad.directory/users/userPrincipalName/update | Azure Active Directory での users.userPrincipalName プロパティの更新。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal のすべてのリソースの基本的なプロパティの読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

## <a name="role-template-ids"></a>ロール テンプレート ID

ロール テンプレート ID は、Graph API または PowerShell のユーザーによって主に使用されます。

グラフの表示名 | Azure portal の表示名 | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
アプリケーション管理者 | アプリケーション管理者 | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
アプリケーション開発者 | アプリケーション開発者 | CF1C38E5-3621-4004-A7CB-879624DCED7C
認証管理者 | 認証管理者 | c4e39bd9-1100-46d3-8c65-fb160da0071f
Azure Information Protection 管理者 | Azure Information Protection 管理者 | 7495fdc4-34c4-4d15-a289-98788ce399fd
B2C ユーザー フロー管理者 | B2C ユーザー フロー管理者 | 6e591065-9bad-43ed-90f3-e9424366d2f0
B2C ユーザー フロー属性管理者 | B2C ユーザー フロー属性管理者 | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
B2C IEF キーセット管理者 | B2C IEF キーセット管理者 | aaf43236-0c0d-4d5f-883a-6955382ac081
B2C IEF ポリシー管理者 | B2C IEF ポリシー管理者 | 3edaf663-341e-4475-9f94-5c398ef6c070
課金管理者 | 課金管理者 | b0f54661-2d74-4c50-afa3-1ec803f12efe
クラウド アプリケーション管理者 | クラウド アプリケーション管理者 | 158c047a-c907-4556-b7ef-446551a6b5f7
クラウド デバイス管理者 | クラウド デバイス管理者 | 7698a772-787b-4ac8-901f-60d6b08affd2
会社の管理者 | 全体管理者 | 62e90394-69f5-4237-9190-012177145e10
コンプライアンス管理者 | コンプライアンス管理者 | 17315797-102d-40b4-93e0-432062caca18
コンプライアンス データ管理者 | コンプライアンス データ管理者 | e6d1a23a-da11-4be4-9570-befc86d067a7
条件付きアクセス管理者 | 条件付きアクセス管理者 | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
CRM サービス管理者 | Dynamics 365 管理者 | 44367163-eba1-44c3-98af-f5787879f96a
カスタマー ロックボックスのアクセス承認者 | カスタマー ロックボックスのアクセス承認者 | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
デスクトップ Analytics 管理者 | デスクトップ Analytics 管理者 | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
デバイス管理者 | デバイス管理者 | 9f06204d-73c1-4d4c-880a-6edb90606fd8
デバイスの参加 | デバイスの参加 | 9c094953-4995-41c8-84c8-3ebb9b32c93f
デバイス マネージャー | デバイス マネージャー | 2b499bcd-da44-4968-8aec-78e1674fa64d
デバイス ユーザー | デバイス ユーザー | d405c6df-0af8-4e3b-95e4-4d06e542189e
ディレクトリ リーダー | ディレクトリ閲覧者 | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
ディレクトリ同期アカウント | ディレクトリ同期アカウント | d29b2b05-8046-44ba-8758-1e26182fcf32
ディレクトリ ライター | ディレクトリ ライター | 9360feb5-f418-4baa-8175-e2a00bac4301
Exchange サービス管理者 | Exchange 管理者 | 29232cdf-9323-42fd-ade2-1d097af3e4de
外部 ID プロバイダー管理者 | 外部 ID プロバイダー管理者 | be2f45a1-457d-42af-a067-6ec1fa63bc45
ゲスト招待元 | ゲスト招待元 | 95e79109-95c0-4d8e-aee3-d01accf2d47b
ヘルプデスク管理者 | パスワード管理者 | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Intune サービス管理者 | Intune 管理者 | 3a2c62db-5318-420d-8d74-23affee5d9d5
Kaizala 管理者 | Kaizala 管理者 | 74ef975b-6605-40af-a5d2-b9539d836353
ライセンス管理者 | ライセンス管理者 | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Lync サービス管理者 | Skype for Business 管理者 | 75941009-915a-4869-abe7-691bff18279e
メッセージ センターのプライバシー閲覧者 | メッセージ センターのプライバシー閲覧者 | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
メッセージ センター閲覧者 | メッセージ センター閲覧者 | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
パートナー レベル 1 のサポート | パートナー レベル 1 のサポート | 4ba39ca4-527c-499a-b93d-d9b492c50246
パートナー レベル 2 のサポート | パートナー レベル 2 のサポート | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Power BI サービス管理者 | Power BI 管理者 | a9ea8996-122f-4c74-9520-8edcd192826c
特権認証管理者 | 特権認証管理者 | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
特権ロール管理者 | 特権ロール管理者 | e8611ab8-c189-46e8-94e1-60213ab1f814
レポート閲覧者 | レポート閲覧者 | 4a5d8f65-41da-4de4-8968-e035b65339cf
Search 管理者 | Search 管理者 | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Search エディター | Search エディター | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
セキュリティ管理者 | セキュリティ管理者 | 194ae4cb-b126-40b2-bd5b-6091b380977d
セキュリティ オペレーター | セキュリティ オペレーター | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
セキュリティ閲覧者 | セキュリティ閲覧者 | 5d6b6bb7-de71-4623-b4af-96380a352509
サービス サポート管理者 | サービス管理者 | f023fd81-a637-4b56-95fd-791ac0226033
SharePoint サービス管理者 | SharePoint 管理者 | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Teams 通信管理者 | Teams 通信管理者 | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Teams 通信サポート エンジニア | Teams 通信サポート エンジニア | f70938a0-fc10-4177-9e90-2178f8765737
Teams 通信サポート スペシャリスト | Teams 通信サポート スペシャリスト | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Teams サービス管理者 | Teams サービス管理者 | 69091246-20e8-4a56-aa4d-066075b2a7a8
User | User | a0b1b346-4d3e-4e8b-98f8-753987be4970
ユーザー アカウント管理者 | ユーザー管理者 | fe930be7-5e62-47db-91af-98c3a49a38b1
デバイスの社内参加 | デバイスの社内参加 | c34f683f-4d5a-4403-affd-6615e00e3a7f


## <a name="deprecated-roles"></a>非推奨のロール

次のロールは使用しないでください。 これらは非推奨となっており、将来的に Azure AD から削除されます。

* アドホック ライセンス管理者
* デバイスの参加
* デバイス マネージャー
* デバイス ユーザー
* メールで確認済みのユーザー作成者
* メールボックス管理者
* デバイスの社内参加

## <a name="next-steps"></a>次の手順

* Azure サブスクリプションの管理者としてユーザーを割り当てる方法の詳細については、[RBAC と Azure portal を使用したアクセスの管理](../../role-based-access-control/role-assignments-portal.md)に関するページを参照してください
* Microsoft Azure でリソース アクセスを制御する方法の詳細については、「 [Azure でのリソース アクセスについて](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Azure Active Directory と Azure サブスクリプションの関係の詳細については、「 [Azure サブスクリプションを Azure Active Directory に関連付ける方法](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
