---
title: Azure AD Connect Health のバージョンの履歴
description: このドキュメントでは、Azure AD Connect Health のリリースと、それらのリリースに含まれる内容について説明します。
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/10/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7653f850edc910fc78b14a628b87dcb22aeb903
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "89279416"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: バージョンのリリース履歴
Azure Active Directory チームは、Azure AD Connect Health を新機能で定期的に更新しています。 この記事では、リリースされたバージョンと機能の一覧を示します。  

> [!NOTE]
> Connect Health エージェントは、新しいバージョンがリリースされると自動的に更新されます。 Azure portal で自動アップグレード設定が有効になっていることを確認してください。
>

Azure AD Connect Health for Sync は、Azure AD Connect のインストールと統合されます。 詳細については、[Azure AD Connect のリリース履歴](./reference-connect-version-history.md)に関する記事をご覧ください。機能のフィードバックについては、[Connnect Health User Voice チャネル](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)で投票してください

## <a name="april-2020"></a>2020 年 4 月
**エージェントの更新**

- AD FS 用の Azure AD Connect Health エージェント (バージョン 3.1.77.0)

   1.   アラートが誤って報告されていた "AD FS サービスのサービス プリンシパル名 (SPN) が無効" アラートのバグを修正しました。


## <a name="july-2019"></a>2019 年 7 月
**エージェントの更新**
* AD FS 用の Azure AD Connect Health エージェント (バージョン 3.1.59.0) 
   1. TestWindowsTransport でのテキスト変更
   2. AD FS RP アップロードに関する変更
   
* AD FS 用の Azure AD Connect Health エージェント (バージョン 3.1.56.0) 
   1. CheckOffice365Endpoints テストでの TestWindowsTransport のテストの追加と WsTrust エンドポイントのチェックの削除
   2. OS と .NET に関する情報のログ記録
   3. RP 構成メッセージのアップロード サイズの 1MB への増加。
   4. バグの修正
   
* AD DS 用の Azure AD Connect Health エージェント (バージョン 3.1.56.0) 
   1. OS と .NET に関する情報のログ記録 
   2. バグの修正

## <a name="may-2019"></a>2019 年 5 月
**エージェントの更新:** 
* AD FS 用の Azure AD Connect Health エージェント (バージョン 3.1.51.0) 
   1. 同じ client-request-id を共有する複数のサインインを区別するためのバグ修正。
   2. 言語がローカライズされたサーバーで不適切なユーザー名/パスワードのエラーを解析するためのバグ修正。   

## <a name="april-2019"></a>2019 年 4 月
**エージェントの更新:** 
* AD FS 用の Azure AD Connect Health エージェント (バージョン 3.1.46.0) 
   1. ADFS の重複する SPN アラート プロセスの修正確認

## <a name="march-2019"></a>2019 年 3 月
**エージェントの更新:** 
* AD DS 用の Azure AD Connect Health エージェント (バージョン 3.1.41.0)  
   1. .NET バージョンのコレクション
   2. 特定のカテゴリが不足している場合のパフォーマンス カウンター収集の機能強化。
   3. 複数の Monitoring Agent インスタンスの生成を防ぐためのバグ修正。

* AD FS 用の Azure AD Connect Health エージェント (バージョン 3.1.41.0) 
   1. ADFSToolBox を使用した AD FS テスト スクリプトの統合およびアップグレード。
   2. .NET バージョンのコレクションの実装。
   3. 特定のカテゴリが不足している場合のパフォーマンス カウンター収集の機能強化。
   4. 複数の Monitoring Agent インスタンスの生成を防ぐためのバグ修正。


## <a name="november-2018"></a>2018 年 11 月
**新しい GA 機能:** 
* Azure AD Connect Health for Sync - ポータルから重複する属性の同期エラーを診断して修復します。

**エージェントの更新:** 
* AD DS 用の Azure AD Connect Health エージェント (バージョン 3.1.24.0) 
   1. トランスポート層セキュリティ (TLS) プロトコル バージョン 1.2 の対応と適用
   2. グローバル カタログのアラート ノイズの削減
   3. 正常性エージェント登録のバグの修正

* AD FS 用の Azure AD Connect Health エージェント (バージョン 3.1.24.0)  
   1. トランスポート層セキュリティ (TLS) プロトコル バージョン 1.2 の対応と適用
   2. ローカライズされたオペレーティング システムに対する Test-ADFSRequestToken のサポート
   3. 診断エージェント EventHandler のロックの問題の解決
   4. 正常性エージェント登録のバグの修正

## <a name="august-2018"></a>2018 年 8 月 
*  Azure AD Connect バージョン 1.1.880.0 と共にリリースされた同期用 Azure AD Connect Health エージェント (バージョン 3.1.7.0)。    
   1. [.NET Framework KB リリースを使用してエージェントを監視する場合に CPU が高くなる問題](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)の修正プログラム

## <a name="june-2018"></a>2018 年 6 月 
**新しいプレビュー機能:** 
* Azure AD Connect Health for Sync - ポータルから重複する属性の同期エラーを診断して修復します。 

**エージェントの更新:** 
* AD DS 用の Azure AD Connect Health エージェント (バージョン 3.1.7.0)    
  1. [.NET Framework KB リリースを使用してエージェントを監視する場合に CPU が高くなる問題](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)の修正プログラム
   
* AD FS 用の Azure AD Connect Health エージェント (バージョン 3.1.7.0)  
  1. [.NET Framework KB リリースを使用してエージェントを監視する場合に CPU が高くなる問題](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)の修正プログラム
  2. ADFS Server 2016 セカンダリ サーバーのテスト結果の修正
   
* AD FS 用の Azure AD Connect Health エージェント (バージョン 3.1.2.0)  
  1. バージョン 3.0.244.0 のエージェントのメモリ管理の修正プログラムと関連する警告


## <a name="may-2018"></a>2018 年 5 月
**エージェントの更新:**
* AD DS 用の Azure AD Connect Health エージェント (バージョン 3.0.244.0)
  1. エージェントのプライバシーの向上  
  2. バグの修正と一般的な機能強化

* AD FS 用 Azure AD Connect Health エージェント (バージョン 3.0.244.0)
  1. エージェントの診断サービスと関連する PowerShell モジュールの機能強化
  2. エージェントのプライバシーの向上  
  3. バグの修正と一般的な機能強化

* Azure AD Connect バージョン 1.1.819.0 と共にリリースされた同期用 Azure AD Connect Health エージェント (バージョン 3.0.164.0) 
  1. エージェントのプライバシーの向上  
  2. バグの修正と一般的な機能強化


## <a name="march-2018"></a>2018 年 3 月
**新しいプレビュー機能:**
* AD FS 用の Azure AD Connect Health - 危険な IP に関するレポートおよびアラート。

**エージェントの更新:**

* AD DS 用 Azure AD Connect Health エージェント (バージョン 3.0.176.0)
  1. エージェントの可用性の向上 
  2. バグの修正と一般的な機能強化
* AD FS 用 Azure AD Connect Health エージェント (バージョン 3.0.176.0)
  1. エージェントの可用性の向上 
  2. バグの修正と一般的な機能強化
* Azure AD Connect バージョン 1.1.750.0 と共にリリースされた同期用 Azure AD Connect Health エージェント (バージョン 3.0.129.0)  
  1. エージェントの可用性の向上 
  2. バグの修正と一般的な機能強化

## <a name="december-2017"></a>2017 年 12 月
**エージェントの更新:**

* AD DS 用 Azure AD Connect Health エージェント (バージョン 3.0.145.0)
  1. エージェントの可用性の向上 
  2. 新しいエージェントのトラブルシューティング コマンドの追加
  3. バグの修正と一般的な機能強化
* AD FS 用 Azure AD Connect Health エージェント (バージョン 3.0.145.0)
  1. 新しいエージェントのトラブルシューティング コマンドの追加
  2. エージェントの可用性の向上 
  3. バグの修正と一般的な機能強化
  
## <a name="october-2017"></a>2017 年 10 月
**エージェントの更新:**

 * Azure AD Connect バージョン 1.1.649.0 と共にリリースされた同期用 Azure AD Connect Health エージェント (バージョン 3.0.129.0)
<br></br> Azure AD Connect と同期用 Azure AD Connect Health エージェント間の、バージョンの互換性に関する問題が修正されました。この問題は、Azure AD Connect のバージョン 1.1.647.0 へのインプレース アップグレードを実行していて、現在の Health Agent のバージョンが 3.0.127.0 である場合に影響があります。 アップグレード後に、Health Agent は Azure AD Connect 同期サービスについての正常性データを Azure AD ヘルス サービスに送信できなくなります。 この修正により、Azure AD Connect のインプレース アップグレード中に Health Agent バージョン 3.0.129.0 がインストールされます。 Health Agent バージョン 3.0.129.0 には、Azure AD Connect バージョン 1.1.649.0 との互換性の問題はありません。

## <a name="july-2017"></a>2017 年 7 月
**エージェントの更新:**

* AD DS 用 Azure AD Connect Health エージェント (バージョン 3.0.68.0)
  1. バグの修正と一般的な機能強化
  2. ソブリン クラウドのサポート
* AD FS 用 Azure AD Connect Health エージェント (バージョン 3.0.68.0)
  1. バグの修正と一般的な機能強化
  2. ソブリン クラウドのサポート
* Azure AD Connect バージョン 1.1.614.0 と共にリリースされた同期用 Azure AD Connect Health エージェント (バージョン 3.0.68.0)
  1. Microsoft Azure Government Cloud と Microsoft Cloud Germany のサポート

## <a name="april-2017"></a>2017 年 4 月      
**エージェントの更新:**

* Azure AD Connect Health エージェント for AD FS (バージョン 3.0.12.0)
  1. バグの修正と一般的な機能強化
* AD DS 用 Azure AD Connect Health エージェント (バージョン 3.0.12.0)
  1. パフォーマンス カウンター アップロードの強化
  2. バグの修正と一般的な機能強化

## <a name="october-2016"></a>2016 年 10 月
**エージェントの更新:**

* AD FS 用 Azure AD Connect Health エージェント (バージョン 2.6.408.0)
* 認証要求でクライアント IP アドレスを検出する機能の強化
* アラートに関連するバグの修正
* AD DS 用 Azure AD Connect Health エージェント (バージョン 2.6.408.0)
* アラートに関連するバグの修正。
* Azure AD Connect バージョン 1.1.281.0 と共にリリースされた同期用 Azure AD Connect Health エージェント (バージョン 2.6.353.0)
* 同期エラー レポートに必要なデータを提供
* アラートに関連するバグの修正

**新しいプレビュー機能:**

* Azure AD Connect の同期エラー レポート

**新機能:**

* AD FS 用 Azure AD Connect Health - ユーザー名/パスワードが正しくない上位 50 名のユーザーについてのレポートで、[IP アドレス] フィールドを使用できます。

## <a name="july-2016"></a>2016 年 7 月
**新しいプレビュー機能:**

* [Azure AD Connect Health for AD DS](how-to-connect-health-adds.md)。

## <a name="january-2016"></a>2016 年 1 月
**エージェントの更新:**

* Azure AD Connect Health エージェント for AD FS (バージョン 2.6.91.1512)

**新機能:**

* [Azure AD Connect Health エージェントの接続テスト ツール](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>2015 年 11 月
**新機能:**

* [Azure ロールベースのアクセス制御 (Azure RBAC)](how-to-connect-health-operations.md#manage-access-with-azure-rbac) のサポート

**新しいプレビュー機能:**

* [Azure AD Connect Health for Sync](how-to-connect-health-sync.md)。

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
「 [クラウド内のオンプレミスの ID インフラストラクチャと同期サービスの監視](./whatis-azure-ad-connect.md)」を参照してください。