---
title: Azure Active Directory (Azure AD) 監査アクティビティ リファレンス | Microsoft Docs
description: Azure Active Directory (Azure AD) で監査ログに記録できる監査アクティビティの概要を示します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/24/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66dd017e8f78f1e93c96262b42dc084c165cdef7
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437682"
---
# <a name="azure-ad-audit-activity-reference"></a>Azure AD 監査アクティビティのリファレンス

環境の動作状況を判断するために必要な情報は、Azure Active Directory (Azure AD) レポートで入手できます。

Azure AD のレポート アーキテクチャは、次のコンポーネントで構成されます。

- **アクティビティ レポート** 
    - [サインイン](concept-sign-ins.md) – マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報を提供します。
    - [監査ログ](concept-audit-logs.md) - Azure AD 内のさまざまな機能によって行われたすべての変更についてログによる追跡可能性を提供します。 
    
- **セキュリティ レポート** 
    - [リスクの高いサインイン](concept-risky-sign-ins.md) - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。 
    - [リスクのフラグ付きユーザー](concept-user-at-risk.md) - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。 

この記事では、監査ログにログに記録できる監査アクティビティの一覧を示します。

## <a name="access-reviews"></a>アクセス レビュー

|監査のカテゴリ|アクティビティ|
|---|---|
|アクセス レビュー|終了したアクセス レビュー|
|アクセス レビュー|要求の承認に承認者を追加|
|アクセス レビュー|アクセス レビューにレビュー担当者を追加|
|アクセス レビュー|アクセス レビュー結果の適用|
|アクセス レビュー|アクセス レビューの作成|
|アクセス レビュー|プログラムの作成|
|アクセス レビュー|要求の承認の作成|
|アクセス レビュー|アクセス レビューの削除|
|アクセス レビュー|プログラムの削除|
|アクセス レビュー|プログラム コントロールのリンク|
|アクセス レビュー|Azure AD アクセス レビューへのオンボード|
|アクセス レビュー|アクセス レビューからレビュー担当者を削除|
|アクセス レビュー|レビューの停止を要求|
|アクセス レビュー|レビュー結果の適用の要求|
|アクセス レビュー|Rbac ロール メンバーシップをレビュー|
|アクセス レビュー|アプリの割り当てのレビュー|
|アクセス レビュー|グループ メンバーシップのレビュー|
|アクセス レビュー|要求の承認要求のレビュー|
|アクセス レビュー|プログラム コントロールのリンクの解除|
|アクセス レビュー|アクセス レビューの更新|
|アクセス レビュー|Azure AD アクセス レビューのオンボード状態の更新|
|アクセス レビュー|アクセス レビューのメール通知設定を更新|
|アクセス レビュー|アクセス レビューの繰り返し回数の設定の更新|
|アクセス レビュー|アクセス レビュー繰り返し期間 (日数) の設定の更新|
|アクセス レビュー|アクセス レビュー繰り返し終了の種類の設定の更新|
|アクセス レビュー|アクセス レビュー繰り返しの種類の設定の更新|
|アクセス レビュー|アクセス レビューのリマインダー設定を更新|
|アクセス レビュー|プログラムの更新|
|アクセス レビュー|要求の承認の更新|
|アクセス レビュー|無効なユーザー|

## <a name="account-provisioning"></a>アカウント プロビジョニング

|監査のカテゴリ|アクティビティ|
|---|---|
|アプリケーション管理|V2 アプリケーションのアクセス許可の付与の取得|
|アプリケーション管理|現在のテナントの V2 アプリケーション サービス プリンシパルの取得|
|アプリケーション管理|V1 アプリケーションの更新|
|アプリケーション管理|V2 アプリケーションの更新|
|アプリケーション管理|V2 アプリケーションのアクセス許可の付与の更新|
|アプリケーション管理|OAuth2PermissionGrant の追加|
|アプリケーション管理|サービス プリンシパルへのアプリ ロールの割り当ての追加|

## <a name="application-proxy"></a>アプリケーション プロキシ

|監査のカテゴリ|アクティビティ|
|---|---|
|アプリケーション管理|[アプリケーションの追加]|
|アプリケーション管理|アプリケーションへの所有者の追加|
|アプリケーション管理|サービス プリンシパルへの所有者の追加|
|アプリケーション管理|サービス プリンシパルへのポリシーの追加|
|ディレクトリ管理|サービス プリンシパルの追加|
|ディレクトリ管理|サービス プリンシパルの資格情報の追加|
|ディレクトリ管理|アプリケーションへの同意|
|ディレクトリ管理|Delete application|
|ディレクトリ管理|アプリケーションの物理的な削除|
|ディレクトリ管理|OAuth2PermissionGrant の削除|
|ディレクトリ管理|サービス プリンシパルからのアプリ ロールの割り当ての削除|
|ディレクトリ管理|アプリケーションからの所有者の削除|
|Resource|サービス プリンシパルからの所有者の削除|
|Resource|サービス プリンシパルからのポリシーの削除|
|Resource|サービス プリンシパルの削除|


## <a name="automated-password-rollover"></a>自動パスワード ロールオーバー

|監査のカテゴリ|アクティビティ|
|---|---|
|アプリケーション管理|サービス プリンシパルの資格情報の削除|


## <a name="b2c"></a>B2C

|監査のカテゴリ|アクティビティ|
|---|---|
|アプリケーション管理|アプリケーションの復元|
|アプリケーション管理|同意の取り消し|
|アプリケーション管理|アプリケーションを更新する|
|アプリケーション管理|外部シークレットを更新します。|
|アプリケーション管理|サービス プリンシパルの更新|
|アプリケーション管理|アプリケーションへのアクセス トークンの発行|
|アプリケーション管理|アプリケーションへの認証コードの発行|
|アプリケーション管理|アプリケーションへの id_token の発行|
|アプリケーション管理|ローカル アカウントの資格情報の検証|
|アプリケーション管理|ユーザー認証の検証|
|アプリケーション管理|V2 アプリケーションのアクセス許可の追加|
|アプリケーション管理|CPIM キー コンテナーへの ASCII シークレットに基づくキーの追加|
|アプリケーション管理|CPIM キー コンテナーへのキーの追加|
|アプリケーション管理|AdminPolicyDatas-SetResources|
|アプリケーション管理|AdminUserJourneys-GetResources|
|アプリケーション管理|AdminUserJourneys-RemoveResources|
|Authentication|AdminUserJourneys-SetResources|
|Authentication|IdentityProvider の作成|
|Authentication|V1 アプリケーションの作成|
|Authentication|V2 アプリケーションの作成|
|Authentication|テナント内のカスタム ドメインの作成|
|Authorization|新しい AdminUserJourney の作成|
|Authorization|ローカライズされたリソース json の作成|
|Authorization|新しいカスタム IDP の作成|
|Authorization|新しい IDP の作成|
|Authorization|B2C ディレクトリ リソースの作成または更新|
|Authorization|ポリシーの作成|
|Authorization|trustFramework ポリシーの作成|
|Authorization|構成可能なプレフィックスを持つ trustFramework ポリシーの作成|
|Authorization|ユーザー属性の作成|
|Authorization|CreateTrustFrameworkPolicy|
|Authorization|新しい AdminUserJourney の作成または更新|
|Authorization|IDP の削除|
|Authorization|IdentityProvider の削除|
|Authorization|V1 アプリケーションの削除|
|Authorization|V2 アプリケーションの削除|
|Authorization|V2 アプリケーションのアクセス許可の付与の削除|
|Authorization|B2C ディレクトリ リソースの削除|
|Authorization|CPIM キー コンテナーの削除|
|Authorization|trustFramework ポリシーの削除|
|Authorization|ユーザー属性の削除|
|Authorization|B2C 機能の有効化|
|Authorization|サブスクリプション内の B2C ディレクトリ リソースの取得|
|Authorization|カスタム IDP の取得|
|Authorization|IDP の取得|
|Authorization|V1 および V2 アプリケーションの取得|
|Authorization|V1 アプリケーションの取得|
|Authorization|V1 アプリケーションの取得|
|Authorization|V2 アプリケーションの取得|
|Authorization|V2 アプリケーションの取得|
|Authorization|B2C ディレクトリ リソースの取得|
|Authorization|テナント内のカスタム ドメインの一覧の取得|
|Authorization|ユーザー体験の取得|
|Authorization|ユーザー体験で許可されているアプリケーション要求の取得|
|Authorization|ユーザー体験で許可されているセルフアサート要求の取得|
|Authorization|ポリシーの許可されているセルフアサート要求の取得|
|Authorization|使用可能な出力要求の一覧の取得|
|Authorization|ユーザー体験のコンテンツ定義の取得|
|Authorization|固有の管理フローの IDP の取得|
|Authorization|JWK のキー コンテナー アクティブ キー メタデータの取得|
|Authorization|すべての管理フローの一覧の取得|
|Authorization|すべてのユーザーのすべての管理フローに対するタグの一覧の取得|
|Authorization|ユーザーのテナントの一覧の取得|
|Authorization|ローカル アカウントのセルフアサート要求の取得|
|Authorization|ローカライズされたリソース json の取得|
|Authorization|Microsoft.AzureActiveDirectory リソース プロバイダーの操作の取得|
|Authorization|ポリシーの取得|
|Authorization|ポリシーの取得|
|Authorization|テナントのリソース プロパティの取得|
|Authorization|サポートされている IDP の一覧の取得|
|Authorization|ユーザー体験でサポートされている IDP の一覧の取得|
|Authorization|テナント情報の取得|
|Authorization|テナントで許可されている機能の取得|
|Authorization|テナントで定義されているカスタム IDP の一覧の取得|
|Authorization|テナントで定義されている IDP の一覧の取得|
|Authorization|テナントで定義されているローカル IDP の一覧の取得|
|Authorization|リソース作成のためのユーザーに関するテナントの詳細の取得|
|Authorization|テナントの一覧の取得|
|Authorization|tenantDomains の取得|
|Authorization|CPIM のサポートされている既定のカルチャの取得|
|Authorization|管理フローの詳細の取得|
|Authorization|このテナントの UserJourneys の一覧の取得|
|Authorization|CPIM の使用可能なサポートされるカルチャのセットの取得|
|Authorization|trustFramework ポリシーの取得|
|Authorization|trustFramework ポリシーの xml としての取得|
|Authorization|ユーザー属性の取得|
|Authorization|ユーザー属性の取得|
|Authorization|ユーザー体験の一覧の取得|
|Authorization|GetIEFPolicies|
|Authorization|GetIdentityProviders|
|Authorization|GetTrustFrameworkPolicy|
|Authorization|jwk 形式での CPIM キー コンテナーの取得|
|Authorization|テナント内のキー コンテナーの一覧の取得|
|Authorization|テナントの種類の取得|
|Authorization|MigrateTenantMetadata|
|Authorization|IdentityProvider のパッチ|
|Authorization|PutTrustFrameworkPolicy|
|Authorization|PutTrustFrameworkpolicy|
|Authorization|ユーザー体験の削除|
|Authorization|CPIM キー コンテナー バックアップの復元|
|Authorization|V2 アプリケーションのアクセス許可の付与の取得|
|Authorization|現在のテナントの V2 アプリケーション サービス プリンシパルの取得|
|Authorization|カスタム IDP の更新|
|Authorization|IDP の更新|
|Authorization|ローカル IDP の更新|
|Authorization|V1 アプリケーションの更新|
|Authorization|V2 アプリケーションの更新|
|Authorization|V2 アプリケーションのアクセス許可の付与の更新|
|Authorization|ポリシーの更新|
|Authorization|ユーザー属性の更新|
|Authorization|CPIM 暗号化キーのアップロード|
|Authorization|ユーザーの承認:テナント機能セットに対して API が無効化された|
|Authorization|ユーザーの承認:'テナント管理者' としてのアクセスを付与されているユーザー|
|Authorization|ユーザーの承認:ユーザーが '認証されたユーザー' アクセス権を付与された|
|Authorization|B2C 機能が有効になっているかどうかの確認|
|Authorization|機能が有効になっているかどうかの確認|
|Authorization|プログラムの作成|
|Authorization|プログラムの削除|
|Authorization|プログラム コントロールのリンク|
|Authorization|Azure AD アクセス レビューへのオンボード|
|Authorization|プログラム コントロールのリンクの解除|
|Authorization|プログラムの更新|
|Authorization|デスクトップ SSO の無効化|
|Authorization|特定のドメインに対するデスクトップ SSO の無効化|
|Authorization|アプリケーション プロキシの無効化|
|Authorization|パススルー認証の無効化|
|Authorization|デスクトップ SSO の有効化|
|ディレクトリ管理|特定のドメインに対するデスクトップ SSO の有効化|
|ディレクトリ管理|アプリケーション プロキシの有効化|
|ディレクトリ管理|パススルー認証の有効化|
|ディレクトリ管理|テナント内のカスタム ドメインの作成|
|ディレクトリ管理|B2C 機能の有効化|
|ディレクトリ管理|テナント内のカスタム ドメインの一覧の取得|
|ディレクトリ管理|テナントのリソース プロパティの取得|
|ディレクトリ管理|テナント情報の取得|
|ディレクトリ管理|テナントで許可されている機能の取得|
|ディレクトリ管理|tenantDomains の取得|
|キー|テナントの種類の取得|
|キー|B2C 機能が有効になっているかどうかの確認|
|キー|機能が有効になっているかどうかの確認|
|キー|会社へのパートナーの追加|
|キー|未確認ドメインの追加|
|キー|確認済みドメインの追加|
|キー|会社の作成|
|キー|会社の設定を作成します。|
|キー|会社の設定を削除します。|
|キー|パートナーの降格|
|キー|ディレクトリが削除された|
|その他|ディレクトリが完全に削除された|
|その他|ディレクトリの削除が予定された|
|Resource|パートナーへの会社の昇格|
|Resource|権限管理プロパティの消去|
|Resource|会社からのパートナーの削除|
|Resource|未確認ドメインの削除|
|Resource|確認済みドメインの削除|
|Resource|会社情報の設定|
|Resource|DirSync 機能の設定|
|Resource|DirSyncEnabled フラグの設定|
|Resource|パートナーシップの設定|
|Resource|誤削除のしきい値の設定|
|Resource|会社で許可されるデータの場所の設定|
|Resource|会社の多国対応機能の有効化の設定|
|Resource|テナントでのディレクトリ機能の設定|
|Resource|ドメインの認証の設定|
|Resource|ドメインのフェデレーションの設定|
|Resource|パスワード ポリシーの設定|
|Resource|権限管理プロパティの設定|
|Resource|会社の更新|
|Resource|会社の設定の更新|
|Resource|ドメインの更新|
|Resource|ドメインの検証|
|Resource|電子メール検証済みドメインの検証|
|Resource|オンボード|
|Resource|アラート設定の更新|
|Resource|週間ダイジェストの設定の更新|
|Resource|ディレクトリのパスワード ライトバックの無効化|
|Resource|ディレクトリのパスワード ライトバックの有効化|
|Resource|グループへのアプリ ロールの割り当ての追加|
|Resource|グループの追加|
|Resource|グループへのメンバーの追加|
|Resource|グループへの所有者の追加|
|Resource|グループ設定の作成|
|Resource|グループの削除|
|Resource|グループ設定の削除|
|Resource|ユーザーに対するグループ ベースのライセンスの適用の終了|
|Resource|グループの物理的な削除|
|Resource|グループからのアプリ ロールの割り当ての削除|
|Resource|グループからのメンバーの削除|
|Resource|グループからの所有者の削除|
|Resource|グループの復元|
|Resource|グループ ライセンスの設定|
|Resource|ユーザーが管理するグループが設定されました。|
|Resource|ユーザーに対するグループ ベースのライセンスの適用の開始|
|Resource|グループ ライセンスの再計算のトリガー|
|Resource|グループの更新|
|Resource|グループ設定の更新|
|Resource|メンバーの追加|
|Resource|グループの作成|
|Resource|グループの削除|
|Resource|メンバーの削除|
|Resource|グループの更新|
|Resource|保留になっているグループへの参加要求の承認|
|Resource|保留になっているグループへの参加要求の取り消し|
|Resource|ライフサイクル管理ポリシーの作成|
|Resource|保留になっているグループへの参加要求の削除|
|Resource|保留になっているグループへの参加要求の拒否|
|Resource|グループの更新|
|Resource|グループへの参加要求|
|Resource|動的グループのプロパティの設定|
|Resource|ライフサイクル管理ポリシーの更新|
|Resource|CPIM キー コンテナーへの ASCII シークレットに基づくキーの追加|
|Resource|CPIM キー コンテナーへのキーの追加|
|Resource|CPIM キー コンテナーの削除|
|Resource|キー コンテナーの削除|
|Resource|JWK のキー コンテナー アクティブ キー メタデータの取得|
|Resource|キー コンテナー メタデータの取得|
|Resource|jwk 形式での CPIM キー コンテナーの取得|
|Resource|テナント内のキー コンテナーの一覧の取得|
|Resource|CPIM キー コンテナー バックアップの復元|
|Resource|キー コンテナーの保存|
|Resource|CPIM 暗号化キーのアップロード|
|Resource|アプリケーションへの認証コードの発行|
|Resource|アプリケーションへの id_token の発行|


## <a name="core-directory"></a>コア ディレクトリ

|監査のカテゴリ|アクティビティ|
|---|---|
|管理単位管理|単一のリスク イベントの種類のダウンロード|
|管理単位管理|管理者と、週間ダイジェスト オプトインの状態をダウンロード|
|管理単位管理|すべてのリスク イベントの種類のダウンロード|
|管理単位管理|無料のユーザー リスク イベントのダウンロード|
|管理単位管理|リスクのフラグ付きユーザーのダウンロード|
|アプリケーション管理|バッチ招待が処理された|
|アプリケーション管理|バッチ招待がアップロードされた|
|アプリケーション管理|ポリシーへの所有者の追加|
|アプリケーション管理|ポリシーの追加|
|アプリケーション管理|ポリシーの削除|
|アプリケーション管理|ポリシー資格情報の削除|
|アプリケーション管理|ポリシーの更新|
|アプリケーション管理|MFA 登録ポリシーの設定|
|アプリケーション管理|サインン リスク ポリシーの設定|
|アプリケーション管理|ユーザー リスク ポリシーの設定|
|アプリケーション管理|使用条件の同意|
|アプリケーション管理|使用条件の作成|
|アプリケーション管理|使用条件の拒否|
|アプリケーション管理|使用条件の削除|
|アプリケーション管理|使用条件の編集|
|アプリケーション管理|使用条件の公開|
|アプリケーション管理|使用条件の公開の取り消し|
|アプリケーション管理|アプリケーション SSL 証明書の追加|
|アプリケーション管理|SSL バインディングの削除|
|アプリケーション管理|コネクタの登録|
|アプリケーション管理|AdminPolicyDatas-RemoveResources|
|アプリケーション管理|AdminPolicyDatas-SetResources|
|アプリケーション管理|AdminUserJourneys-GetResources|
|ディレクトリ管理|AdminUserJourneys-RemoveResources|
|ディレクトリ管理|AdminUserJourneys-SetResources|
|ディレクトリ管理|IdentityProvider の作成|
|ディレクトリ管理|新しい AdminUserJourney の作成|
|ディレクトリ管理|ローカライズされたリソース json の作成|
|ディレクトリ管理|新しいカスタム IDP の作成|
|ディレクトリ管理|新しい IDP の作成|
|ディレクトリ管理|B2C ディレクトリ リソースの作成または更新|
|ディレクトリ管理|ポリシーの作成|
|ディレクトリ管理|trustFramework ポリシーの作成|
|ディレクトリ管理|構成可能なプレフィックスを持つ trustFramework ポリシーの作成|
|ディレクトリ管理|ユーザー属性の作成|
|ディレクトリ管理|CreateTrustFrameworkPolicy|
|ディレクトリ管理|IDP の削除|
|ディレクトリ管理|IdentityProvider の削除|
|ディレクトリ管理|B2C ディレクトリ リソースの削除|
|ディレクトリ管理|trustFramework ポリシーの削除|
|ディレクトリ管理|ユーザー属性の削除|
|ディレクトリ管理|リソース グループ内の B2C ディレクトリ リソースの取得|
|ディレクトリ管理|サブスクリプション内の B2C ディレクトリ リソースの取得|
|ディレクトリ管理|カスタム IDP の取得|
|ディレクトリ管理|IDP の取得|
|ディレクトリ管理|B2C ディレクトリ リソースの取得|
|ディレクトリ管理|ユーザー体験の取得|
|ディレクトリ管理|ユーザー体験で許可されているアプリケーション要求の取得|
|ディレクトリ管理|ユーザー体験で許可されているセルフアサート要求の取得|
|ディレクトリ管理|ポリシーの許可されているセルフアサート要求の取得|
|ディレクトリ管理|使用可能な出力要求の一覧の取得|
|ディレクトリ管理|ユーザー体験のコンテンツ定義の取得|
|ディレクトリ管理|固有の管理フローの IDP の取得|
|ディレクトリ管理|すべての管理フローの一覧の取得|
|ディレクトリ管理|すべてのユーザーのすべての管理フローに対するタグの一覧の取得|
|グループ管理|ユーザーのテナントの一覧の取得|
|グループ管理|ローカル アカウントのセルフアサート要求の取得|
|グループ管理|ローカライズされたリソース json の取得|
|グループ管理|Microsoft.AzureActiveDirectory リソース プロバイダーの操作の取得|
|グループ管理|ポリシーの取得|
|グループ管理|ポリシーの取得|
|グループ管理|サポートされている IDP の一覧の取得|
|グループ管理|ユーザー体験でサポートされている IDP の一覧の取得|
|グループ管理|テナントで定義されているカスタム IDP の一覧の取得|
|グループ管理|テナントで定義されている IDP の一覧の取得|
|グループ管理|テナントで定義されているローカル IDP の一覧の取得|
|グループ管理|リソース作成のためのユーザーに関するテナントの詳細の取得|
|グループ管理|CPIM のサポートされている既定のカルチャの取得|
|グループ管理|管理フローの詳細の取得|
|グループ管理|このテナントの UserJourneys の一覧の取得|
|グループ管理|CPIM の使用可能なサポートされるカルチャのセットの取得|
|グループ管理|trustFramework ポリシーの取得|
|グループ管理|trustFramework ポリシーの xml としての取得|
|グループ管理|ユーザー属性の取得|
|ポリシー管理|ユーザー属性の取得|
|ポリシー管理|ユーザー体験の一覧の取得|
|ポリシー管理|GetIEFPolicies|
|ポリシー管理|GetIdentityProviders|
|ポリシー管理|GetTrustFrameworkPolicy|
|Resource|MigrateTenantMetadata|
|Resource|リソースの移動|
|Resource|IdentityProvider のパッチ|
|Resource|PutTrustFrameworkPolicy|
|Resource|PutTrustFrameworkpolicy|
|Resource|ユーザー体験の削除|
|Resource|カスタム IDP の更新|
|Resource|IDP の更新|
|Resource|ローカル IDP の更新|
|Resource|B2C ディレクトリ リソースの更新|
|Resource|ポリシーの更新|
|Resource|サブスクリプションの状態の更新|
|ロール管理|ユーザー属性の更新|
|ロール管理|リソースの移動の検証|
|ロール管理|デバイスの追加|
|ロール管理|デバイス構成の追加|
|ロール管理|デバイスへの登録済み所有者の追加|
|ロール管理|デバイスへの登録済みユーザーの追加|
|ロール管理|デバイスの削除|
|ロール管理|デバイスの構成の削除|
|ロール管理|デバイスが準拠していなくなった|
|ロール管理|デバイスが管理されなくなった|
|[ユーザー管理]|デバイスからの登録済み所有者の削除|
|[ユーザー管理]|デバイスからの登録済みユーザーの削除|
|[ユーザー管理]|デバイスの更新|
|[ユーザー管理]|デバイス構成の更新|
|[ユーザー管理]|ロールへの有資格メンバーの追加|
|[ユーザー管理]|ロールへのメンバーの追加|
|[ユーザー管理]|ロール定義へのロール割り当ての追加|
|[ユーザー管理]|テンプレートからのロールの追加|
|[ユーザー管理]|ロールへのスコープを持つメンバーの追加|
|[ユーザー管理]|ロールからの有資格メンバーの削除|
|[ユーザー管理]|ロールからのメンバーの削除|
|[ユーザー管理]|ロール定義からのロール割り当ての削除|
|[ユーザー管理]|ロールからのスコープを持つメンバーの削除|
|[ユーザー管理]|ロールの更新|
|[ユーザー管理]|AccessReview_Review|
|[ユーザー管理]|AccessReview_Update|
|[ユーザー管理]|ActivationAborted|
|[ユーザー管理]|ActivationApproved|
|[ユーザー管理]|ActivationCanceled|
|[ユーザー管理]|ActivationRequested|
|[ユーザー管理]|追加済み|
|[ユーザー管理]|割り当て|


## <a name="identity-protection"></a>Identity Protection

|監査のカテゴリ|アクティビティ|
|---|---|
|ディレクトリ管理|昇格|
|ディレクトリ管理|削除済み|
|ディレクトリ管理|ロール設定の変更|
|その他|ScanAlertsNow|
|その他|サインアップ|
|その他|降格|
|その他|UpdateAlertSettings|
|その他|UpdateCurrentState|
|ポリシー管理|終了したアクセス レビュー|
|ポリシー管理|要求の承認に承認者を追加|
|ポリシー管理|アクセス レビューにレビュー担当者を追加|
|[ユーザー管理]|アクセス レビュー結果の適用|
|[ユーザー管理]|アクセス レビューの作成|


## <a name="invited-users"></a>招待されたユーザー

|監査のカテゴリ|アクティビティ|
|---|---|
|その他|要求の承認の作成|
|その他|アクセス レビューの削除|
|[ユーザー管理]|アクセス レビューからレビュー担当者を削除|
|[ユーザー管理]|レビュー結果の適用の要求|
|[ユーザー管理]|レビューの停止を要求|
|[ユーザー管理]|アプリの割り当てのレビュー|
|[ユーザー管理]|グループ メンバーシップのレビュー|
|[ユーザー管理]|Rbac ロール メンバーシップをレビュー|


## <a name="microsoft-identity-manager-mim"></a>Microsoft Identity Manager (MIM)

|監査のカテゴリ|アクティビティ|
|---|---|
|グループ管理|要求の承認要求のレビュー|
|グループ管理|アクセス レビューの更新|
|グループ管理|アクセス レビューのメール通知設定を更新|
|グループ管理|アクセス レビューの繰り返し回数の設定の更新|
|グループ管理|アクセス レビュー繰り返し期間 (日数) の設定の更新|
|[ユーザー管理]|アクセス レビュー繰り返し終了の種類の設定の更新|
|[ユーザー管理]|アクセス レビュー繰り返しの種類の設定の更新|



## <a name="privileged-identity-management"></a>Privileged Identity Management

|監査のカテゴリ|アクティビティ|
|---|---|
|PIM|ActivationAborted|
|PIM|ActivationApproved|
|PIM|ActivationCanceled|
|PIM|ActivationDenied|
|PIM|ActivationRequested|
|PIM|追加済み|
|PIM|AddedOutsidePIM|
|PIM|割り当て|
|PIM|DismissAlert|
|PIM|昇格|
|PIM|ReactivateAlert|
|PIM|削除済み|
|PIM|RemovedOutsidePIM|
|PIM|レビューの停止を要求|
|PIM|ロール設定の変更|
|PIM|ScanAlertsNow|
|PIM|サインアップ|
|PIM|割り当て解除|
|PIM|降格|
|PIM|UpdateAlertSettings|
|PIM|UpdateCurrentState|


## <a name="self-service-group-management"></a>セルフサービスのグループ管理

|監査のカテゴリ|アクティビティ|
|---|---|
|グループ管理|ユーザー パスワードのリセット|
|グループ管理|ユーザーの復元|
|グループ管理|ユーザー パスワードの強制変更の設定|
|グループ管理|ユーザー管理の設定|
|グループ管理|ユーザーの oath トークン メタデータの有効化の設定|
|グループ管理|StsRefreshTokenValidFrom タイムスタンプの更新|
|グループ管理|外部シークレットを更新します。|
|グループ管理|ユーザーの更新|
|グループ管理|管理者が一時パスワードを生成する|


## <a name="self-service-password-management"></a>セルフサービスによるパスワード管理

|監査のカテゴリ|アクティビティ|
|---|---|
|ディレクトリ管理|管理者がユーザーにパスワードのリセットを要求する|
|ディレクトリ管理|アプリケーションへの外部ユーザーの割り当て|
|[ユーザー管理]|電子メールが送信されず、ユーザーがサブスクリプションを解除した|
|[ユーザー管理]|外部ユーザーの招待|
|[ユーザー管理]|外部ユーザーの招待の利用|
|[ユーザー管理]|バイラル テナントの作成|
|[ユーザー管理]|バイラル ユーザーの作成|
|[ユーザー管理]|ユーザー パスワードの登録|
|[ユーザー管理]|ユーザー パスワードのリセット|
|[ユーザー管理]|セルフサービスによるパスワード リセットのブロック|


## <a name="terms-of-use"></a>使用条件

|監査のカテゴリ|アクティビティ|
|---|---|
|使用条件|使用条件の同意|
|使用条件|使用条件の作成|
|使用条件|使用条件の拒否|
|使用条件|同意の削除|
|使用条件|使用条件の削除|
|使用条件|使用条件の編集|
|使用条件|使用条件の期限切れ|
|使用条件|使用条件の物理的な削除|
|使用条件|使用条件の公開|
|使用条件|使用条件の公開の取り消し|


## <a name="next-steps"></a>次の手順

- [Azure AD レポートの概要](overview-reports.md)
- [監査ログ レポート](concept-audit-logs.md) 
- [Azure AD レポートへのプログラムによるアクセス](concept-reporting-api.md)
