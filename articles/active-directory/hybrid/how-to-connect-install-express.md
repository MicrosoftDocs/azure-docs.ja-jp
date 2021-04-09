---
title: 'Azure AD Connect: 簡単設定を使用した開始 | Microsoft Docs'
description: Azure AD Connect のセットアップ ウィザードをダウンロード、インストール、および実行する方法について説明します。
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.assetid: b6ce45fd-554d-4f4d-95d1-47996d561c9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a655f355bb77d937f4daff2f8987769416ebd8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279671"
---
# <a name="getting-started-with-azure-ad-connect-using-express-settings"></a>簡単設定を使用した Azure AD Connect の開始
Azure AD Connect の **簡単設定** は、トポロジのフォレストが 1 つであり、認証に [パスワード ハッシュ同期](how-to-connect-password-hash-synchronization.md)を使用する場合に使います。 **簡単設定** は既定のオプションであり、ごく一般的なデプロイ シナリオで使用されます。 数回クリックするだけで、オンプレミスのディレクトリをクラウドに拡張できます。

Azure AD Connect のインストールを始める前に、必ず [Azure AD Connect をダウンロード](https://go.microsoft.com/fwlink/?LinkId=615771)し、[Azure AD Connect のハードウェアと前提条件](how-to-connect-install-prerequisites.md)に関するページに記載されている前提条件の手順を完了してください。

簡単設定が対象のトポロジに適さない場合は、「 [関連ドキュメント](#related-documentation) 」でその他のシナリオを確認してください。

## <a name="express-installation-of-azure-ad-connect"></a>Azure AD Connect の高速インストール
「 [ビデオ](#videos) 」セクションでは次の手順を実際に確認できます。

1. Azure AD Connect をインストールするサーバーにローカル管理者としてサインインします。 この作業は、同期サーバーとなるサーバーに対して行う必要があります。
2. **AzureADConnect.msi** を検索し、ダブルクリックします。
3. [ようこそ] 画面で、ライセンス条項に同意するチェック ボックスをオンにし、 **[続行]** をクリックします。  
4. [簡単設定] 画面で、 **[簡単設定を使う]** をクリックします。  
   ![Azure AD Connect へようこそ](./media/how-to-connect-install-express/express.png)
5. [Azure AD に接続] 画面で、Azure AD のグローバル管理者のユーザー名とパスワードを入力します。 **[次へ]** をクリックします。  
   ![Azure への接続](./media/how-to-connect-install-express/connectaad.png)  
   接続に問題があり、エラーが発生する場合は、[接続の問題に対するトラブルシューティング](tshoot-connect-connectivity.md)についてのページを参照してください。
6. [AD DS に接続] 画面で、エンタープライズ管理者アカウントのユーザー名とパスワードを入力します。 ドメインの部分は NetBios または FQDN の形式で入力できます (FABRIKAM\administrator または fabrikam.com\administrator)。 **[次へ]** をクリックします。  
   ![AD DS に接続](./media/how-to-connect-install-express/connectad.png)
7. [**[Azure AD サインインの構成]**](plan-connect-user-signin.md#azure-ad-sign-in-configuration) ページは、[前提条件](how-to-connect-install-prerequisites.md)の [ドメインの検証](../fundamentals/add-custom-domain.md)が済んでいない場合にのみ表示されます。
   ![Unverified domains](./media/how-to-connect-install-express/unverifieddomain.png)  
   このページが表示されたら、**[Not Added (追加されていません)]** と **[Not Verified (検証されていません)]** のマークが付いたドメインをすべて確認します。 使用するドメインを Azure AD で検証済みにしてください。 対象のドメインを検証済みにしたら、更新シンボルをクリックします。
8. [構成の準備完了] 画面で、 **[インストール]** をクリックします。
   * 必要に応じて、[構成の準備完了] ページの **[Start the synchronization process as soon as configuration completes (構成が完了したらすぐに同期プロセスを開始する)]** チェック ボックスをオフにします。 続けて[フィルター処理](how-to-connect-sync-configure-filtering.md)などの構成作業を行う場合は、このチェック ボックスをオフにする必要があります。 このオプションをオフにした場合、同期の構成は実行されますが、スケジューラは無効のままとなります。 スケジューラを実行するには、[もう一度インストール ウィザードを実行](how-to-connect-installation-wizard.md)して手動で有効にする必要があります。
   * **[Start the synchronization process as soon as configuration completes]\(構成が完了したらすぐに同期プロセスを開始する\)** チェック ボックスをオンのままにすると、すべてのユーザー、グループ、連絡先について、直ちに Azure AD との完全同期がトリガーされます。
   * オンプレミスの Active Directory に Exchange がある場合、[**Exchange ハイブリッド デプロイ**](/exchange/exchange-hybrid)を有効にすることもできます。 クラウドとオンプレミスの両方に Exchange メールボックスを同時に配置する場合は、このオプションを有効にしてください。
     ![Ready to configure Azure AD Connect](./media/how-to-connect-install-express/readytoconfigure.png)
9. インストールが完了したら、 **[終了]** をクリックします。
10. インストールが完了した後、 Synchronization Service Manager または同期規則エディターを使用する前に、サインアウトし、もう一度サインインします。

## <a name="videos"></a>ビデオ
高速インストールの使用方法については、以下のビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Express-Settings/player]
>
>

## <a name="next-steps"></a>次のステップ
Azure AD Connect がインストールされたので、[インストールを確認し、ライセンスを割り当てる](how-to-connect-post-installation.md)ことができます。

インストールの結果有効になった機能について詳しくは、[自動アップグレード](how-to-connect-install-automatic-upgrade.md)、[誤った削除操作を防止する機能](how-to-connect-sync-feature-prevent-accidental-deletes.md)、[Azure AD Connect Health](how-to-connect-health-sync.md) に関する各ページを参照してください。

一般的なトピックについては、[スケジューラの使用と同期のトリガー方法](how-to-connect-sync-feature-scheduler.md)に関するページを参照してください。

「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。

## <a name="related-documentation"></a>関連ドキュメント

| トピック | Link |
| --- | --- |
| Azure AD Connect の概要 | [オンプレミスのディレクトリと Azure Active Directory の統合](whatis-hybrid-identity.md)
| カスタマイズした設定を使用したインストール | [Azure AD Connect のカスタム インストール](how-to-connect-install-custom.md) |
| DirSync からのアップグレード | [Azure AD 同期ツール (DirSync) からのアップグレード](how-to-dirsync-upgrade-get-started.md)|
| インストールで使用するアカウント | [Azure AD Connect の資格情報とアクセス許可の詳細](reference-connect-accounts-permissions.md) |