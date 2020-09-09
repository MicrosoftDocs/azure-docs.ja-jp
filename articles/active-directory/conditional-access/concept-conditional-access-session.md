---
title: 条件付きアクセス ポリシーのセッション コントロール - Azure Active Directory
description: Azure AD 条件付きアクセス ポリシーのセッション コントロールとは
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1140b65cf56125b23ef3c616e597aafba989b197
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "83993858"
---
# <a name="conditional-access-session"></a>条件付きアクセス:Session

管理者は、条件付きアクセス ポリシー内でセッション コントロールを利用すると、特定のクラウド アプリケーション内でのエクスペリエンスを制限できるようになります。

![多要素認証を必要とする許可コントロールを使用した条件付きアクセス ポリシー](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>アプリケーションによって適用される制限

組織では、このコントロールを使用して、選択されたクラウド アプリにデバイス情報を渡すように Azure AD に要求できます。 クラウド アプリは、デバイス情報によって、接続が準拠またはドメイン参加済みデバイスから開始されているかどうかを認識できます。 このコントロールでは、選択されたクラウド アプリとして SharePoint Online と Exchange Online のみがサポートされます。 選択されたクラウド アプリは、デバイス情報を使用して、デバイスの状態に応じて制限付きまたは完全なエクスペリエンスをユーザーに提供します。

アプリによって適用される制限の使用と構成の詳細については、以下の記事を参照してください。

- [SharePoint Online での制限付きアクセスの有効化](/sharepoint/control-access-from-unmanaged-devices)
- [Exchange Online での制限付きアクセスの有効化](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>アプリケーションの条件付きアクセス制御

アプリの条件付きアクセス制御は、リバース プロキシ アーキテクチャを使用して Azure AD 条件付きアクセスと一意に統合されます。 Azure AD 条件付きアクセスによって、組織のアプリに対するアクセス制御を一定の条件に基づいて適用できます。 この条件では、条件付きアクセス ポリシーの適用先が、誰であるか (ユーザーまたはユーザーのグループ)、何であるか (どのクラウド アプリか)、どこであるか (どの場所およびネットワークか) が定義されます。 条件を決定したら、アクセス制御とセッション コントロールの適用によるアプリの条件付きアクセス制御を使用してデータを保護できる [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) にユーザーをルーティングできます。

アプリの条件付きアクセス制御を使用すると、アクセス ポリシーとセッション ポリシーに基づいて、ユーザー アプリのアクセスとセッションをリアルタイムで監視および制御できるようになります。 アクセス ポリシーとセッション ポリシーは、フィルターをさらに調整し、ユーザーに対して実行されるアクションを設定するため、 Cloud App Security ポータル内で使用されます。 アクセス ポリシーとセッション ポリシーにより、以下のことが可能です。

- データの流出を防止する:アンマネージド デバイスなどでの、機密性の高いドキュメントのダウンロード、切り取り、コピー、および印刷をブロックできます。
- ダウンロード時に保護する:機密性の高いドキュメントのダウンロードをブロックする代わりに、ドキュメントがラベル付けされ、Azure Information Protection で保護されるよう要求することができます。 このアクションにより、危険な可能性があるセッションで確実にドキュメントが保護され、ユーザー アクセスが制限されます。
- ラベル付けされていないファイルがアップロードされないようにする:機密性の高いファイルのアップロード、配布、他のユーザーによる使用が行われる前に、ファイルのラベルと保護が適切であると確認することが重要です。 ラベル付けされておらず機密性の高いコンテンツが含まれるファイルは、ユーザーがコンテンツを分類するまでアップロードがブロックされるようにできます。
- コンプライアンスのためにユーザー セッションを監視する:危険なユーザーは、アプリへのサインイン時に監視され、アクションはセッション内からログに記録されます。 ユーザーの行動を調査して分析すると、将来どこで、どのような条件のもとでセッション ポリシーを適用する必要があるかを理解できます。
- アクセスをブロックする:いくつかのリスク要因に応じて、特定のアプリやユーザーのアクセスをきめ細かくブロックできます。 たとえば、それらがクライアント証明書をデバイス管理方式として使用している場合にブロックできます。
- カスタム アクティビティをブロックする:アプリによっては、リスクを伴う固有のシナリオがあります。Microsoft Teams や Slack のようなアプリで、機密性の高いコンテンツを含むメッセージを送信するなどです。 これらの種類のシナリオでは、機密性の高いコンテンツがないかメッセージをスキャンし、リアルタイムでメッセージをブロックできます。

詳細については、「[おすすめアプリのためにアプリの条件付きアクセス制御をデプロイする](/cloud-app-security/proxy-deployment-aad)」を参照してください。

## <a name="sign-in-frequency"></a>サインインの頻度

サインインの頻度は、ユーザーがリソースにアクセスしようとしたときにサインインし直すように求められるまでの期間を定義します。

サインイン頻度設定は、標準に従って OAUTH2 または OIDC プロトコルを実装したアプリで動作します。 Windows、Mac、次の Web アプリケーションを含むモバイル用のほとんどの Microsoft ネイティブ アプリは、この設定に準拠します。

- Word、Excel、PowerPoint Online
- OneNote Online
- Office.com
- O365 管理ポータル
- Exchange Online
- SharePoint と OneDrive
- Teams Web クライアント
- Dynamics CRM Online
- Azure portal

詳細については、「[条件付きアクセスを使用して認証セッション管理を構成する](howto-conditional-access-session-lifetime.md#user-sign-in-frequency)」という記事を参照してください。

## <a name="persistent-browser-session"></a>永続的ブラウザー セッション

永続的なブラウザー セッションでは、ユーザーはブラウザー ウィンドウを閉じてから再度開いた後でもサインインした状態を維持できます。

詳細については、「[条件付きアクセスを使用して認証セッション管理を構成する](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions)」という記事を参照してください。

## <a name="next-steps"></a>次のステップ

- [Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

- [レポート専用モード](concept-conditional-access-report-only.md)
