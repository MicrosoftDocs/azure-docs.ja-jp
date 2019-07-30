---
title: 管理タスク別の最小特権ロールを委任する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory で ID タスク用に委任するロール
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 05/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21136046bad8cb58432de367f0de06d1d0457eec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083784"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Azure Active Directory における管理タスク別の管理者ロール

この記事では、Azure Active Directory (Azure AD) で最小特権ロールを割り当てることによってユーザーの管理者アクセス許可を制限するうえで必要な情報を取り上げます。 機能領域ごとの管理者タスクと、各タスクを実行するために必要な最小特権ロールのほか、そのタスクを実行できる非グローバル管理者ロールも別途記載しています。

## <a name="application-proxy"></a>アプリケーション プロキシ

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
アプリケーション プロキシ アプリを構成する | アプリケーション管理者 | 
コネクタ グループのプロパティを構成する | アプリケーション管理者 | 
アプリケーションの登録を作成する (すべてのユーザーについて権限が無効になっている場合) | アプリケーション開発者 | クラウド アプリケーション管理者、アプリケーション管理者
コネクタ グループを作成する | アプリケーション管理者 | 
コネクタ グループを削除する | アプリケーション管理者 | 
アプリケーション プロキシの無効化 | アプリケーション管理者 | 
コネクタ サービスをダウンロードする | アプリケーション管理者 | 
すべての構成を読み取る | アプリケーション管理者 | 

## <a name="b2c"></a>B2C

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
Azure AD B2C のディレクトリを作成する | すべての非ゲスト ユーザー ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
B2C アプリケーションを作成する | グローバル管理者 | 
エンタープライズ アプリケーションを作成する | クラウド アプリケーション管理者 | アプリケーション管理者
B2C のポリシーの作成、読み取り、更新、削除を実行する | グローバル管理者 | 
ID プロバイダーの作成、読み取り、更新、削除を実行する | グローバル管理者 | 
パスワード リセット ユーザー フローの作成、読み取り、更新、削除を実行する | グローバル管理者 | 
プロファイル編集ユーザー フローの作成、読み取り、更新、削除を実行する | グローバル管理者 | 
サインイン ユーザー フローの作成、読み取り、更新、削除を実行する | グローバル管理者 | 
サインアップ ユーザー フローの作成、読み取り、更新、削除を実行する |グローバル管理者 | 
ユーザー属性の作成、読み取り、更新、削除を実行する | グローバル管理者 | 
ユーザーの作成、読み取り、更新、削除を実行する | グローバル管理者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs))
すべての構成を読み取る | グローバル管理者 | 
B2C 監査ログを読み取る | グローバル管理者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs)) | 

> [!NOTE]
> Azure AD B2C のグローバル管理者には、Azure AD のグローバル管理者と同じアクセス許可はありません。 Azure AD B2C のグローバル管理者権限がある場合は、Azure Active Directory ディレクトリではなく Azure AD B2C ディレクトリにいることを確認してください。

## <a name="company-branding"></a>会社のブランド

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
会社のブランドの構成 | グローバル管理者 | 
すべての構成を読み取る | ディレクトリ閲覧者 | 既定のユーザー ロール ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="company-properties"></a>会社のプロパティ

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
会社のプロパティを構成する | グローバル管理者 | 

## <a name="connect"></a>接続

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
パススルー認証 | グローバル管理者 | 
すべての構成を読み取る | グローバル管理者 | 
シームレス シングル サインオン | グローバル管理者 | 

## <a name="connect-health"></a>Connect Health

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
サービスを追加または削除する | 所有者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
同期エラーに対する修正プログラムを適用する | 共同作成者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Owner
通知の構成 | 共同作成者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Owner
設定を構成する | 所有者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
同期の通知を構成する | 共同作成者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Owner
ADFS セキュリティ レポートを読み取る | セキュリティ閲覧者 | 共同作成者、所有者
すべての構成を読み取る | 閲覧者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | 共同作成者、所有者
同期エラーを読み取る | 閲覧者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | 共同作成者、所有者
同期サービスを読み取る | 閲覧者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | 共同作成者、所有者
メトリックとアラートを表示する | 閲覧者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | 共同作成者、所有者
メトリックとアラートを表示する | 閲覧者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | 共同作成者、所有者
同期サービスのメトリックとアラートを表示する | 閲覧者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | 共同作成者、所有者


## <a name="custom-domain-names"></a>カスタム ドメイン名

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
ドメインの管理 | グローバル管理者 | 
すべての構成を読み取る | ディレクトリ閲覧者 | 既定のユーザー ロール ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="domain-services"></a>ドメイン サービス

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
Azure AD Domain Services インスタンスを作成する | グローバル管理者 | 
Azure AD Domain Services の全タスクを実行する | Azure AD DC Administrators グループ ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-administer-domain#administrative-tasks-you-can-perform-on-a-managed-domain)) | 
すべての構成を読み取る | AD DS サービスを含む Azure サブスクリプションの閲覧者 | 

## <a name="devices"></a>デバイス

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
デバイスを無効にする | クラウド デバイス管理者 | 
デバイスを有効にする | クラウド デバイス管理者 | 
基本構成を読み取る | 既定のユーザー ロール ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
BitLocker キーを読み取る | セキュリティ閲覧者 | パスワード管理者、セキュリティ管理者

## <a name="enterprise-applications"></a>エンタープライズ アプリケーション

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
委任された任意のアクセス許可に同意する | クラウド アプリケーション管理者 | アプリケーション管理者
アプリケーションのアクセス許可に同意する (Microsoft Graph と Azure AD Graph を除く) | クラウド アプリケーション管理者 | アプリケーション管理者
Microsoft Graph または Azure AD Graph に対するアプリケーションのアクセス許可に同意する | グローバル管理者 | 
アプリケーションが自分のデータにアクセスすることに同意する | 既定のユーザー ロール ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
エンタープライズ アプリケーションを作成する | クラウド アプリケーション管理者 | アプリケーション管理者
アプリケーション プロキシを管理する | アプリケーション管理者 | 
ユーザー設定を管理する | グローバル管理者 | 
グループまたはアプリのアクセス レビューを読み取る | セキュリティ閲覧者 | セキュリティ管理者、ユーザー管理者
すべての構成を読み取る | 既定のユーザー ロール ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
エンタープライズ アプリケーションの割り当てを更新する | エンタープライズ アプリケーション所有者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | クラウド アプリケーション管理者、アプリケーション管理者
エンタープライズ アプリケーション所有者を更新する | エンタープライズ アプリケーション所有者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | クラウド アプリケーション管理者、アプリケーション管理者
エンタープライズ アプリケーションのプロパティを更新する | エンタープライズ アプリケーション所有者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | クラウド アプリケーション管理者、アプリケーション管理者
エンタープライズ アプリケーションのプロビジョニングを更新する | エンタープライズ アプリケーション所有者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | クラウド アプリケーション管理者、アプリケーション管理者
エンタープライズ アプリケーションのセルフ サービスを更新する | エンタープライズ アプリケーション所有者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | クラウド アプリケーション管理者、アプリケーション管理者
シングル サインオンのプロパティを更新する | エンタープライズ アプリケーション所有者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | クラウド アプリケーション管理者、アプリケーション管理者

## <a name="groups"></a>グループ

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
ライセンスの割り当て | ユーザー管理者 | 
グループを作成する | ユーザー管理者 | 
グループまたはアプリのアクセス レビューを作成、更新、削除する | ユーザー管理者 | 
グループの有効期限を管理する | ユーザー管理者 | 
グループ設定の管理 | グローバル管理者 | 
すべての構成を読み取る (非表示のメンバーシップを除く) | ディレクトリ閲覧者 | 既定のユーザー ロール ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
非表示のメンバーシップを読み取る | グループ メンバー | グループ所有者、パスワード管理者、Exchange 管理者、SharePoint 管理者、Teams 管理者、ユーザー管理者
非表示のメンバーシップを含むグループのメンバーシップを読み取る | ヘルプデスク管理者 | ユーザー管理者、Teams 管理者
ライセンスを取り消す | ライセンス管理者 | ユーザー管理者
グループ メンバーシップを更新する | グループ所有者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | ユーザー管理者
グループ所有者を更新する | グループ所有者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | ユーザー管理者
グループのプロパティを更新する | グループ所有者 ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | ユーザー管理者

## <a name="identity-protection"></a>Identity Protection

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
アラート通知を構成する| セキュリティ管理者 | 
MFA ポリシーを構成し、有効または無効にする| セキュリティ管理者 | 
サインイン リスク ポリシーを構成し、有効または無効にする| セキュリティ管理者 | 
ユーザー リスク ポリシーを構成し、有効または無効にする | セキュリティ管理者 | 
週刊ダイジェストを構成する | セキュリティ管理者| 
すべてのリスク イベントを閉じる | セキュリティ管理者 | 
脆弱性を修正または無視する | セキュリティ管理者 | 
すべての構成を読み取る | セキュリティ閲覧者 | 
すべてのリスク イベントを読み取る | セキュリティ閲覧者 | 
脆弱性を読み取る | セキュリティ閲覧者 | 

## <a name="licenses"></a>ライセンス

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
ライセンスの割り当て | ライセンス管理者 | ユーザー管理者
すべての構成を読み取る | ディレクトリ閲覧者 | 既定のユーザー ロール ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
ライセンスを取り消す | ライセンス管理者 | ユーザー管理者
サブスクリプションを試用または購入する | 課金管理者 | 


## <a name="monitoring---audit-logs"></a>監視 - 監査ログ

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
監査ログを読み取る | レポート閲覧者 | セキュリティ閲覧者、セキュリティ管理者

## <a name="monitoring---sign-ins"></a>監視 - サインイン

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
サインイン ログを読み取る | レポート閲覧者 | セキュリティ閲覧者、セキュリティ管理者

## <a name="multi-factor-authentication"></a>多要素認証

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
選択したユーザーによって生成されたすべての既存のアプリケーション パスワードを削除する | グローバル管理者 | 
MFA を無効にする | グローバル管理者 | 
MFA の有効化 | グローバル管理者 | 
MFA サービスの設定を管理する | グローバル管理者 | 
選択したユーザーについて連絡方法の再指定を必須にする | 認証管理者 | 
記憶されているすべてのデバイスで多要素認証を復元する  | 認証管理者 | 

## <a name="mfa-server"></a>MFA サーバー

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
ユーザーのブロック/ブロック解除 | グローバル管理者 | 
アカウント ロックアウトを構成する | グローバル管理者 | 
キャッシュ規則を構成する | グローバル管理者 | 
不正アクセスのアラートを構成する | グローバル管理者
通知の構成 | グローバル管理者 | 
ワンタイム バイパスを構成する | グローバル管理者 | 
電話の設定を構成する | グローバル管理者 | 
プロバイダーを構成する | グローバル管理者 | 
サーバー設定の構成 | グローバル管理者 | 
アクティビティ レポートを読み取る | グローバル管理者 | 
すべての構成を読み取る | グローバル管理者 | 
サーバーの状態を読み取る | グローバル管理者 |  

## <a name="organizational-relationships"></a>組織の関係

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
ID プロバイダーを管理する | グローバル管理者 | 
設定の管理 | グローバル管理者 | 
利用規約を管理する | グローバル管理者 | 
すべての構成を読み取る | グローバル管理者 | 

## <a name="password-reset"></a>パスワード リセット

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
認証方法を構成する | グローバル管理者 |
カスタマイズを構成する | グローバル管理者 |
通知を構成する | グローバル管理者 |
オンプレミスの統合を構成する | グローバル管理者 |
パスワードのリセット プロパティを構成する | ユーザー管理者 | グローバル管理者
登録を構成する | グローバル管理者 |
すべての構成を読み取る | セキュリティ管理者 | ユーザー管理者 |

## <a name="privileged-identity-management"></a>Privileged Identity Management

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
ユーザーをロールに割り当てる | 特権ロール管理者 | 
ロール設定を構成する | 特権ロール管理者 | 
監査アクティビティを表示する | セキュリティ閲覧者 | 
ロールのメンバーシップを表示する | セキュリティ閲覧者 | 

## <a name="roles-and-administrators"></a>ロールと管理者

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
ロールの割り当てを管理する | 特権ロール管理者 | 
Azure AD ロールのアクセス レビューを読み取る  | セキュリティ閲覧者 | セキュリティ管理者、特権ロール管理者
すべての構成を読み取る | 既定のユーザー ロール ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 

## <a name="security---authentication-methods"></a>セキュリティ - 認証方法

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
認証方法を構成する | グローバル管理者 | 
すべての構成を読み取る | グローバル管理者 | 

## <a name="security---conditional-access"></a>セキュリティ - 条件付きアクセス

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
MFA の信頼できる IP アドレスを構成する | 条件付きアクセス管理者 | 
カスタム コントロールを作成する | 条件付きアクセス管理者 | セキュリティ管理者
名前付きの場所を作成する | 条件付きアクセス管理者 | セキュリティ管理者
ポリシーの作成 | 条件付きアクセス管理者 | セキュリティ管理者
利用規約を作成する | 条件付きアクセス管理者 | セキュリティ管理者
VPN 接続の証明書を作成する | 条件付きアクセス管理者 | セキュリティ管理者
クラシック ポリシーを削除する | 条件付きアクセス管理者 | セキュリティ管理者
利用規約を削除する | 条件付きアクセス管理者 | セキュリティ管理者
VPN 接続の証明書を削除する | 条件付きアクセス管理者 | セキュリティ管理者
クラシック ポリシーを無効にする | 条件付きアクセス管理者 | セキュリティ管理者
カスタム コントロールを管理する | 条件付きアクセス管理者 | セキュリティ管理者
名前付きの場所を管理する | 条件付きアクセス管理者 | セキュリティ管理者
利用規約を管理する | 条件付きアクセス管理者 | セキュリティ管理者
すべての構成を読み取る | セキュリティ閲覧者 | セキュリティ管理者
名前付きの場所を読み取る | セキュリティ閲覧者 | 条件付きアクセス管理者、セキュリティ管理者

## <a name="security---identity-security-score"></a>セキュリティ - ID セキュリティ スコア

タスク | 最小特権ロール | その他のロール | 
---- | --------------------- | ----------------
すべての構成を読み取る | セキュリティ閲覧者 | セキュリティ管理者
セキュリティ スコアを読み取る | セキュリティ閲覧者 | セキュリティ管理者
イベントの状態を更新する | セキュリティ管理者 | 

## <a name="security---risky-sign-ins"></a>セキュリティ - 危険なサインイン

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
すべての構成を読み取る | セキュリティ閲覧者 | 
危険なサインインを読み取る | セキュリティ閲覧者 | 

## <a name="security---users-flagged-for-risk"></a>セキュリティ - リスクのフラグ付きユーザー

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
すべてのイベントを閉じる | セキュリティ管理者 | 
すべての構成を読み取る | セキュリティ閲覧者 | 
リスクのフラグ付きユーザーを読み取る | セキュリティ閲覧者 | 

## <a name="users"></a>ユーザー

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
ディレクトリ ロールにユーザーを追加する | 特権ロール管理者 | 
ユーザーをグループに追加する | ユーザー管理者 | 
ライセンスの割り当て | ライセンス管理者 | ユーザー管理者
ゲスト ユーザーを作成する | ゲスト招待元 | ユーザー管理者
ユーザーの作成 | ユーザー管理者 | 
ユーザーを削除する | ユーザー管理者 | 
制限付き管理者の更新トークンを無効にする (ドキュメントを参照) | ユーザー管理者 | 
非管理者の更新トークンを無効にする (ドキュメントを参照) | パスワード管理者 | ユーザー管理者
特権管理者の更新トークンを無効にする (ドキュメントを参照) | グローバル管理者 | 
基本構成を読み取る | 既定のユーザー ロール ([ドキュメントを参照](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
制限付き管理者のパスワードをリセットする (ドキュメントを参照) | ユーザー管理者 | 
非管理者のパスワードをリセットする (ドキュメントを参照) | パスワード管理者 | ユーザー管理者
特権管理者のパスワードをリセットする | グローバル管理者 | 
ライセンスを取り消す | ライセンス管理者 | ユーザー管理者
ユーザー プリンシパル名を除くすべてのプロパティを更新する | ユーザー管理者 | 
制限付き管理者のユーザー プリンシパル名を更新する (ドキュメントを参照) | ユーザー管理者 | 
特権管理者のユーザー プリンシパル名プロパティを更新する (ドキュメントを参照) | グローバル管理者 | 
ユーザー設定を更新する | グローバル管理者 | 


## <a name="support"></a>サポート

タスク | 最小特権ロール | その他のロール
---- | --------------------- | ----------------
サポート チケットを送信する | サービス管理者 | アプリケーション管理者、Azure Information Protection 管理者、課金管理者、クラウド アプリケーション管理者、コンプライアンス管理者、Dynamics 365 管理者、Desktop Analytics 管理者、Exchange 管理者、パスワード管理者、Intune 管理者、Skype for Business 管理者、Power BI 管理者、特権認証管理者、SharePoint 管理者、Teams 通信管理者、Teams 管理者、ユーザー管理者、Workplace Analytics 管理者

## <a name="next-steps"></a>次の手順

* [Azure AD 管理者ロールの割り当てと削除の方法](directory-manage-roles-portal.md)
* [Azure AD 管理者ロールのリファレンス](directory-assign-admin-roles.md)
