---
title: "Azure AD Connect Health のバージョンの履歴"
description: "このドキュメントでは、Azure AD Connect Health のリリースと、それらのリリースに含まれる内容について説明します。"
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 2daa6c07191da7bd732cadf62b414f8027682071
ms.openlocfilehash: 35d7ac416c35c74d38f4370ee7e34a96eb18d000


---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: バージョンのリリース履歴
Azure Active Directory チームは、Azure AD Connect Health を新機能で定期的に更新しています。 この記事では、リリースされたバージョンと機能の一覧を示します。

## <a name="october-2016"></a>2016 年 10 月
**エージェントの更新:**

* AD FS 用 Azure AD Connect Health エージェント \(バージョン 2.6.408.0\)
  1. 認証要求でクライアント IP アドレスを検出する機能の強化
  2. アラートに関連するバグの修正
* AD DS 用 Azure AD Connect Health エージェント (バージョン 2.6.408.0)
  1. アラートに関連するバグの修正。
* Azure AD Connect バージョン 1.1.281.0 と共にリリースされた同期用 Azure AD Connect Health エージェント (バージョン 2.6.353.0)
  1. 同期エラー レポートに必要なデータを提供
  2. アラートに関連するバグの修正

**新しいプレビュー機能:**

* Azure AD Connect の同期エラー レポート

**新機能:**

* AD FS 用 Azure AD Connect Health - ユーザー名/パスワードが正しくない上位 50 名のユーザーについてのレポートで、[IP アドレス] フィールドを使用できます。

## <a name="july-2016"></a>2016 年 7 月
**新しいプレビュー機能:**

* [Azure AD Connect Health for AD DS](active-directory-aadconnect-health-adds.md)。

## <a name="january-2016"></a>2016 年 1 月
**エージェントの更新:**

* Azure AD Connect Health エージェント for AD FS (バージョン 2.6.91.1512)

**新機能:**

* [Azure AD Connect Health エージェントの接続テスト ツール](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>2015 年 11 月
**新機能:**

* [ロールベースのアクセス制御](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)

**新しいプレビュー機能:**

* [Azure AD Connect Health for Sync](active-directory-aadconnect-health-sync.md)。

**修正された問題:**

* エージェントの登録中に検出されたエラーのバグの修正。

## <a name="september-2015"></a>2015 年 9 月
**新機能:**

* AD FS の不正なユーザー名パスワード レポート
* 非認証 HTTP プロキシの構成のサポート
* Server コア上のエージェントの構成のサポート
* AD FS のアラートの改善
* Azure AD Connect Health エージェント for AD FS の接続とデータ アップロードの改善。

**修正された問題:**

* AD FS のエラーの種類に対する利用状況インサイトのバグ修正。

## <a name="june-2015"></a>2015 年 6 月
**Azure AD Connect Health for AD FS および AD FS プロキシの初期リリース。**

**新機能:**

* 電子メール通知による AD FS および AD FS プロキシ サーバーの監視のアラート。
* AD FS パフォーマンス カウンターでの AD FS トポロジおよびパターンへの簡単なアクセス。
* アプリケーション、認証方法、要求ネットワークの場所などによってグループ化された AD FS サーバーへの成功したトークン要求の傾向。
* アプリケーション、エラーの種類などによってグループ化された AD FS サーバーへの失敗した要求の傾向。
* Azure AD グローバル管理者の資格情報を使用した簡単なエージェントのデプロイメント。  

## <a name="next-steps"></a>次のステップ
「 [クラウド内のオンプレミスの ID インフラストラクチャと同期サービスの監視](active-directory-aadconnect-health.md)」を参照してください。




<!--HONumber=Feb17_HO1-->


