---
title: Azure Active Directory と統合する方法 | Microsoft Docs
description: アプリケーションと Azure Active Directory を統合する利点について学習し、簡略化されたサインイン、ID 管理、多要素認証、アクセスの制御などの機能用のリソースを取得します。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4af8cdc1072678de7e34458fc4b54d52aae9eff1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111609"
---
# <a name="integrating-with-azure-active-directory"></a>Azure Active Directory との統合

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

この記事では、アプリケーションと Azure Active Directory (Azure AD) を統合する利点について学習し、統合に使用するリソースを取得します。 Azure AD では、クラウド アプリケーションに関するエンタープライズ レベルの ID 管理を組織に提供します。 Azure AD の統合は、簡素化されたサインイン エクスペリエンスをユーザーに提供し、アプリケーションが IT ポリシーに準拠できるようにします。

## <a name="how-to-integrate"></a>統合方法

アプリケーションを Azure AD と統合するにはいくつかの方法があります。 アプリケーションに適したシナリオを必要な数だけいくつでも利用できます。

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>アプリケーションにサインインする手段としての Azure AD のサポート

**サインインの手間が減り、サポート コストが削減されます。** Azure AD を使用してアプリケーションにサインインすることで、ユーザーは名前とパスワードを 1 つ余計に覚える必要がなくなります。 開発者としては、保存して保護するパスワードが 1 つ減ります。 忘れたパスワードのリセットを処理する必要がないだけで、かなり手間が省けます。 Azure AD により、Office 365 や Microsoft Azure などの世界で最も人気のある一部のクラウド アプリケーションのサインインが容易になります。 数百万の組織の何億人ものユーザーが、既に Azure AD にサインインしています。 [Azure AD サインインのサポートの追加](authentication-scenarios.md)に関する詳細を参照してください。

**アプリケーションへのサインアップが簡単になります。**  アプリケーションへのサインアップの間に、Azure AD はユーザーに関する基本的な情報を送信するので、サインアップ フォームに事前に入力したり、入力を完全に排除したりできます。 ユーザーは、ソーシャル メディアやモバイル アプリケーションと同様の使い慣れた同意エクスペリエンスから、Azure AD アカウントを使用してアプリケーションにサインアップできます。 すべてのユーザーが、IT スタッフの介入を必要とせずに、Azure AD と統合されているアプリケーションにサインアップおよびサインインできます。 [Azure AD アカウント ログイン用のアプリケーションのサインアップ](../../app-service/configure-authentication-provider-aad.md)の詳細を参照してください。

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>ユーザーの参照、ユーザーのプロビジョニングの管理、およびアプリケーションへのアクセスの制御

**ディレクトリのユーザーを参照します。**  他のユーザーを招待したりアクセスを許可するときは、Graph API を使用して組織内のユーザーを検索して参照できます。電子メール アドレスを入力する必要はありません。 ユーザーは、組織の詳細な階層の表示も含めて、使い慣れたアドレス帳スタイルのインターフェイスを使用して参照できます。 [Graph API](active-directory-graph-api.md) に関する詳細を参照してください。

**顧客が既に管理している Active Directory のグループと配布リストを再利用します。**  Azure AD には、顧客が電子メールの配布やアクセスの管理に既に使用しているグループが含まれます。 Graph API を使用して、これらのグループを再利用できます。顧客がアプリケーションで別のグループ セットを作成して管理する必要はありません。 グループの情報は、サインイン トークンでアプリケーションにも送信できます。 [Graph API](active-directory-graph-api.md) に関する詳細を参照してください。

**Azure AD を使用して、アプリケーションにアクセスできるユーザーを制御します。**  Azure AD の管理者およびアプリケーション所有者は、特定のユーザーおよびグループにアプリケーションへのアクセスを割り当てることができます。 Graph API を使用すると、このリストを読み取ったり、リストを使用してアプリケーション内のリソースやアクセスのプロビジョニングとプロビジョニング解除を制御したりできます。

**ロール ベースの Access Control に Azure AD を使用します。**  管理者とアプリケーション所有者は、アプリケーションを Azure AD に登録するときに定義したロールに、ユーザーやグループを割り当てることができます。 ロール情報はサインイン トークンでアプリケーションに送信され、Graph API を使用して読み取ることができます。 [承認への Azure AD の使用](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/)に関する詳細を参照してください。

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>ユーザーのプロファイル、予定表、電子メール、連絡先、ファイル、その他へのアクセスの取得

**Azure AD は、Office 365 やその他の Microsoft ビジネス サービスに対する承認サーバーです。**  アプリケーションへのサインイン用に Azure AD をサポートしている場合、または OAuth 2.0 による Azure AD ユーザー アカウントへの現在のユーザー アカウントのリンクをサポートしている場合は、ユーザーのプロファイル、予定表、電子メール、連絡先、ファイル、その他の情報への読み書きアクセスを要求できます。 ユーザーの予定表にシームレスにイベントを書き込んだり、OneDrive のファイルを読み書きしたりできます。 [Office 365 API のアクセス](https://msdn.microsoft.com/office/office365/howto/platform-development-overview)に関する詳細を参照してください。

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Azure および Office 365 マーケットプレースのアプリケーションの宣伝

**既に Azure AD を使用している数百万の組織にアプリケーションを宣伝します。**  マーケットプレースを検索および参照しているユーザーは既にクラウド サービスを使用しており、クラウド サービスの顧客として認定されています。 [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/)のアプリケーションの宣伝に関する詳細を参照してください。

**ユーザーがアプリケーションにサインアップすると、アプリケーションは Azure AD アクセス パネルと Office 365 アプリケーション ランチャーに表示されるようになります。**  ユーザーは後で迅速かつ簡単にアプリケーションに戻ることができ、ユーザーの結びつきが強くなります。 [Azure AD アクセス パネル](../user-help/active-directory-saas-access-panel-introduction.md)に関する詳細を参照してください。

### <a name="secure-device-to-service-and-service-to-service-communication"></a>デバイスとサービスの間およびサービスとサービスの間の安全な通信

**サービスおよびデバイスの ID 管理に Azure AD を使用すると、記述する必要があるコードが減り、IT 部門はアクセスを管理できるようになります。**  サービスおよびデバイスは、OAuth を使用して Azure AD からトークンを取得し、そのトークンを使用して Web API にアクセスできます。 Azure AD を使用すると、複雑な認証コードを作成しなくて済みます。 サービスおよびデバイスの ID は Azure AD に格納されるため、IT 部門はキーと失効を 1 か所で管理できます。アプリケーションで個別に行う必要はありません。

## <a name="benefits-of-integration"></a>統合の利点

Azure AD との統合には、追加コードを記述する必要がないという利点があります。

### <a name="integration-with-enterprise-identity-management"></a>エンタープライズ ID 管理との統合

**アプリケーションを IT ポリシーに準拠させるのに役立ちます。**  組織はエンタープライズ ID 管理システムと Azure AD を統合しているので、ユーザーが組織からいなくなると、IT 部門が余分な手順を実行しなくても、ユーザーは自動的にアプリケーションにアクセスできなくなります。 IT 部門はアプリケーションにアクセスできるユーザーを管理し、必要なアクセス ポリシーを決定できるので (多要素認証など)、複雑な企業ポリシーに準拠するコードを記述する必要性が減ります。 Azure AD はアプリケーションにサインインしたユーザーに関する詳細な監査ログを管理者に提供するので、IT 部門は使用状況を追跡できます。

**Azure AD は Active Directory をクラウドに拡張し、アプリケーションが AD と統合できるようにします。**  世界中の多くの組織は、サインインおよび ID 管理の主要なシステムとして Active Directory を使用しており、アプリケーションに AD との連携を求めます。 Azure AD と統合すると、アプリが Active Directory と統合されます。

### <a name="advanced-security-features"></a>高度なセキュリティ機能

**多要素認証。**  Azure AD は、ネイティブな多要素認証機能を提供します。 IT 管理者はアプリケーションへのアクセスに多要素認証を要求でき、このサポートを自分でコーディングする必要はありません。 [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)に関する詳細を参照してください。

**異常なサインインの検出。**  Azure AD は 1 日に 10 億以上のサインインを処理しながら、機械学習アルゴリズムを使用して疑わしいアクティビティを検出し、可能性のある問題を IT 管理者に通知します。 Azure AD のサインインをサポートすることにより、アプリケーションでこの保護を利用できます。 [Azure Active Directory アクセス レポートの表示](../active-directory-view-access-usage-reports.md)に関する詳細を参照してください。

**条件付きアクセス。**  多要素認証に加えて、管理者はアプリケーションにサインインするユーザーに特定の条件を満たすことを要求できます。 設定可能な条件としては、クライアント デバイスの IP アドレスの範囲、指定したグループにおけるメンバーシップ、アクセスに使用されるデバイスの状態などがあります。 [Azure Active Directory の条件付きアクセス](../active-directory-conditional-access-azure-portal.md)に関する詳細を参照してください。

### <a name="easy-development"></a>簡単な開発

**業界標準のプロトコル。**  Microsoft は業界標準のサポートに取り組んでいます。 Microsoft ID プラットフォームでは、業界標準のプロトコルである OAuth 2.0 と OpenID Connect 1.0 がサポートされています。 詳細については、[Microsoft ID プラットフォームの認証プロトコル](active-directory-v2-protocols.md)に関するページを参照してください。

**オープン ソース ライブラリ。**  Microsoft は、開発時間短縮のため、一般的な言語とプラットフォームの完全にサポートされたオープン ソース ライブラリを提供します。 ソース コードは Apache 2.0 でライセンスされており、自由にフォークしてプロジェクトに役立てることができます。 詳細については、[Microsoft 認証ライブラリ (MSAL)](reference-v2-libraries.md) に関するページを参照してください。

### <a name="worldwide-presence-and-high-availability"></a>世界的なプレゼンスと高可用性

**Azure AD は、世界各地のデータセンターにデプロイされ、常に管理および監視されています。**  Azure AD は Microsoft Azure および Office 365 用の ID 管理システムであり、世界各地にある 28 のデータセンターにデプロイされています。 ディレクトリ データは、3 つ以上のデータセンターにレプリケートされることが保証されます。 グローバルなロード バランサーが、ユーザーが自分のデータを含む最も近い Azure AD のコピーにアクセスすることを保証し、問題が検出された場合は自動的に他のデータセンターに要求を再ルーティングします。

## <a name="next-steps"></a>次の手順

[コード作成の開始](v2-overview.md#getting-started)

[Microsoft ID プラットフォームを使用してユーザーをサインインさせる](authentication-scenarios.md)

