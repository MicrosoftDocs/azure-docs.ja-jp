---
title: Microsoft ID プラットフォームと統合する方法
description: アプリケーションと Microsoft ID プラットフォームを統合する利点について学習し、簡略化されたサインイン、ID 管理、多要素認証、アクセスの制御などの機能についての情報を確認します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: c193742527cf69526d69549d3c10f5591f0d2f93
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115698"
---
# <a name="integrating-with-microsoft-identity-platform"></a>Microsoft ID プラットフォームとの統合

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

この記事では、アプリケーションと Microsoft ID プラットフォームを統合する利点について学習し、統合のための情報を確認します。 Microsoft ID プラットフォームと Azure Active Directory (AD) は、クラウド アプリケーションに関するエンタープライズ レベルの ID 管理を組織に提供します。 Microsoft ID プラットフォームの統合により、簡素化されたサインイン エクスペリエンスをユーザーに提供し、アプリケーションを IT ポリシーに準拠させることができます。

## <a name="how-to-integrate"></a>統合方法

アプリケーションを Microsoft ID プラットフォームと統合する方法は複数あります。 アプリケーションに適したシナリオを必要な数だけいくつでも利用できます。

### <a name="support-microsoft-identity-platform-as-a-way-to-sign-in-to-your-application"></a>アプリケーションへのサインイン方法として Microsoft ID プラットフォームをサポートする

**サインインの手間が減り、サポート コストが削減されます。** Microsoft ID プラットフォームを使用してアプリケーションにサインインすれば、ユーザーは名前とパスワードを 1 つ余計に覚える必要がなくなります。 開発者としては、保存して保護するパスワードが 1 つ減ります。 忘れたパスワードのリセットを処理する必要がないだけで、かなり手間が省けます。 Microsoft ID プラットフォームにより、Office 365 や Microsoft Azure などの世界的に人気の高いクラウド アプリケーションのサインインが容易になります。 数百万の組織の何億人ものユーザーが既に Microsoft ID プラットフォームにサインインしています。 詳細については、[Microsoft ID プラットフォーム サインインのサポートの追加](./authentication-vs-authorization.md)に関する記事を参照してください。

**アプリケーションへのサインアップが簡単になります。**  アプリケーションへのサインアップの間に、Microsoft ID プラットフォームからユーザーに関する基本的な情報が送信されるため、サインアップ フォームへの入力を事前に行ったり、完全に排除したりできます。 ユーザーは、ソーシャル メディアやモバイル アプリケーションと同様の使い慣れた同意エクスペリエンスから、Azure AD アカウントを使用してアプリケーションにサインアップできます。 すべてのユーザーが、IT スタッフの介入を必要とせずに、Microsoft ID プラットフォームと統合されているアプリケーションにサインアップおよびサインインできます。 [Azure AD アカウント ログイン用のアプリケーションのサインアップ](../../app-service/configure-authentication-provider-aad.md)の詳細を参照してください。

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>ユーザーの参照、ユーザーのプロビジョニングの管理、およびアプリケーションへのアクセスの制御

**ディレクトリのユーザーを参照します。**  他のユーザーを招待したりアクセスを許可するときは、Microsoft Graph API を使用して組織内のユーザーを検索して参照できます。メール アドレスを入力する必要はありません。 ユーザーは、組織の詳細な階層の表示も含めて、使い慣れたアドレス帳スタイルのインターフェイスを使用して参照できます。 [Microsoft Graph API](/graph/overview) の詳細についてご確認ください。

**顧客が既に管理している Active Directory のグループと配布リストを再利用します。**  Azure AD には、顧客が電子メールの配布やアクセスの管理に既に使用しているグループが含まれます。 Microsoft Graph API を使用して、これらのグループを再利用できます。顧客がアプリケーションで別のグループ セットを作成して管理する必要はありません。 グループの情報は、サインイン トークンでアプリケーションにも送信できます。 [Microsoft Graph API](/graph/overview) の詳細についてご確認ください。

**Microsoft ID プラットフォームを使用して、アプリケーションにアクセスできるユーザーを制御します。**  Azure AD の管理者およびアプリケーション所有者は、特定のユーザーおよびグループにアプリケーションへのアクセスを割り当てることができます。 Microsoft Graph API を使用すると、このリストを読み取ったり、リストを使用してアプリケーション内のリソースやアクセスのプロビジョニングとプロビジョニング解除を制御したりできます。

**ロールベースのアクセス制御に Microsoft ID プラットフォームを使用します。**  管理者とアプリケーション所有者は、アプリケーションを Microsoft ID プラットフォームに登録するときに定義するロールに、ユーザーやグループを割り当てることができます。 ロール情報はサインイン トークンでアプリケーションに送信され、Microsoft Graph API を使用して読み取ることができます。 詳細については、[Microsoft ID プラットフォームを使用した承認](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/)に関する記事をご覧ください。

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>ユーザーのプロファイル、予定表、電子メール、連絡先、ファイル、その他へのアクセスの取得

**Microsoft ID プラットフォームは、Office 365 やその他の Microsoft ビジネス サービスの承認サーバーです。**  アプリケーションへのサインイン方法として Microsoft ID プラットフォームをサポートしている場合、または OAuth 2.0 を使用して現在のユーザー アカウントを Azure AD ユーザー アカウントにリンクすることをサポートしている場合は、ユーザーのプロファイル、予定表、電子メール、連絡先、ファイル、その他の情報への読み書きアクセスを要求できます。 ユーザーの予定表にシームレスにイベントを書き込んだり、OneDrive のファイルを読み書きしたりできます。 [Office 365 API のアクセス](/previous-versions/office/office-365-api/)に関する詳細を参照してください。

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Azure および Office 365 マーケットプレースのアプリケーションの宣伝

**既に Azure AD を使用している数百万の組織にアプリケーションを宣伝します。**  マーケットプレースを検索および参照しているユーザーは既にクラウド サービスを使用しており、クラウド サービスの顧客として認定されています。 [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/)のアプリケーションの宣伝に関する詳細を参照してください。

**ユーザーがアプリケーションにサインアップすると、アプリケーションは Azure AD アクセス パネルと Office 365 アプリケーション ランチャーに表示されるようになります。**  ユーザーは後で迅速かつ簡単にアプリケーションに戻ることができ、ユーザーの結びつきが強くなります。 [Azure AD アクセス パネル](../user-help/my-apps-portal-end-user-access.md)に関する詳細を参照してください。

### <a name="secure-device-to-service-and-service-to-service-communication"></a>デバイスとサービスの間およびサービスとサービスの間の安全な通信

**サービスおよびデバイスの ID 管理に Microsoft ID プラットフォームを使用すると、記述する必要があるコードが減り、IT 部門でアクセスを管理できるようになります。**  サービスおよびデバイスは、OAuth を使用して Microsoft ID プラットフォームからトークンを取得し、そのトークンを使用して Web API にアクセスできます。 Microsoft ID プラットフォームを使用すると、複雑な認証コードを作成しなくて済みます。 サービスおよびデバイスの ID は Azure AD に格納されるため、IT 部門はキーと失効を 1 か所で管理できます。アプリケーションで個別に行う必要はありません。

## <a name="benefits-of-integration"></a>統合の利点

Microsoft ID プラットフォームとの統合には、追加コードを記述する必要がないという利点があります。

### <a name="integration-with-enterprise-identity-management"></a>エンタープライズ ID 管理との統合

**アプリケーションを IT ポリシーに準拠させるのに役立ちます。**  エンタープライズ ID 管理システムと Microsoft ID プラットフォームを統合すると、ユーザーが組織から離れたときに、IT 部門が特別な手順を実行しなくても、そのユーザーは自動的にアプリケーションにアクセスできなくなります。 IT 部門はアプリケーションにアクセスできるユーザーを管理し、必要なアクセス ポリシーを決定できるので (多要素認証など)、複雑な企業ポリシーに準拠するコードを記述する必要性が減ります。 Azure AD はアプリケーションにサインインしたユーザーに関する詳細な監査ログを管理者に提供するので、IT 部門は使用状況を追跡できます。

**Azure AD は Active Directory をクラウドに拡張し、アプリケーションが AD と統合できるようにします。**  世界中の多くの組織は、サインインおよび ID 管理の主要なシステムとして Active Directory を使用しており、アプリケーションに AD との連携を求めます。 Azure AD と統合すると、アプリが Active Directory と統合されます。

### <a name="advanced-security-features"></a>高度なセキュリティ機能

**多要素認証。**  Microsoft ID プラットフォームは、ネイティブな多要素認証機能を提供します。 IT 管理者はアプリケーションへのアクセスに多要素認証を要求でき、このサポートを自分でコーディングする必要はありません。 [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)に関する詳細を参照してください。

**異常なサインインの検出。**  Microsoft ID プラットフォームは 1 日に 10 億以上のサインインを処理しながら、機械学習アルゴリズムを使用して疑わしいアクティビティを検出し、起こり得る問題を IT 管理者に通知します。 Microsoft ID プラットフォームのサインインをサポートすることにより、この保護をアプリケーションで利用できます。 [Azure Active Directory アクセス レポートの表示](../reports-monitoring/overview-reports.md)に関する詳細を参照してください。

**条件付きアクセス。**  多要素認証に加えて、管理者はアプリケーションにサインインするユーザーに特定の条件を満たすことを要求できます。 設定可能な条件としては、クライアント デバイスの IP アドレスの範囲、指定したグループにおけるメンバーシップ、アクセスに使用されるデバイスの状態などがあります。 [Azure Active Directory の条件付きアクセス](../conditional-access/overview.md)に関する詳細を参照してください。

### <a name="easy-development"></a>簡単な開発

**業界標準のプロトコル。**  Microsoft は業界標準のサポートに取り組んでいます。 Microsoft ID プラットフォームでは、業界標準のプロトコルである OAuth 2.0 と OpenID Connect 1.0 がサポートされています。 詳細については、[Microsoft ID プラットフォームの認証プロトコル](active-directory-v2-protocols.md)に関するページを参照してください。

**オープン ソース ライブラリ。**  Microsoft は、開発時間短縮のため、一般的な言語とプラットフォームの完全にサポートされたオープン ソース ライブラリを提供します。 ソース コードは Apache 2.0 でライセンスされており、自由にフォークしてプロジェクトに役立てることができます。 詳細については、[Microsoft 認証ライブラリ (MSAL)](reference-v2-libraries.md) に関するページを参照してください。

### <a name="worldwide-presence-and-high-availability"></a>世界的なプレゼンスと高可用性

**Azure AD は、世界各地のデータセンターにデプロイされ、常に管理および監視されています。**  Azure AD は Microsoft Azure および Office 365 用の ID 管理システムであり、世界各地にある 28 のデータセンターにデプロイされています。 ディレクトリ データは、3 つ以上のデータセンターにレプリケートされることが保証されます。 グローバルなロード バランサーが、ユーザーが自分のデータを含む最も近い Azure AD のコピーにアクセスすることを保証し、問題が検出された場合は自動的に他のデータセンターに要求を再ルーティングします。

## <a name="next-steps"></a>次のステップ

[コード作成の開始](v2-overview.md#getting-started)

[Microsoft ID プラットフォームを使用してユーザーをサインインさせる](./authentication-vs-authorization.md)