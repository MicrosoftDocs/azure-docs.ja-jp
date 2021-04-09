---
title: Azure AD Connect:オブジェクト同期のトラブルシューティング | Microsoft Docs
description: このトピックでは、トラブルシューティング タスクを使用してオブジェクト同期の問題のトラブルシューティングを行う手順を示します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73d4239dd34f2a64aa7b3edbf88bad4348e01291
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85356204"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Azure AD Connect Sync を使用したオブジェクト同期のトラブルシューティング
この条項では、トラブルシューティングタスクを使用し、オブジェクト同期の問題のトラブルシューティングを行う手順を示します。 Azure Active Directory (Azure AD) Connect でどのようにトラブルシューティングが行われるか確認するには、[ このショートビデオ ](https://aka.ms/AADCTSVideo) をご覧ください。

## <a name="troubleshooting-task"></a>トラブルシューティング タスク
Azure AD Connect のバージョン 1.1.749.0 以上のデプロイについては、ウィザードのトラブルシューティング タスクを使用して、オブジェクト同期の問題のトラブルシューティングを行ってください。 以前のバージョンについては、[こちら](tshoot-connect-object-not-syncing.md)の説明に従って、手動でトラブルシューティングを行ってください。

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>ウィザードでトラブルシューティング タスクを実行する
トラブルシューティング タスクをウィザードで実行するには、次の手順を実行します。

1.  [管理者として実行] オプションを指定して、Azure AD Connect サーバーで新しい Windows PowerShell セッションを開きます。
2.  `Set-ExecutionPolicy RemoteSigned` または `Set-ExecutionPolicy Unrestricted` を実行します。
3.  Azure AD Connect ウィザードを開始します。
4.  [追加のタスク] ページに移動し、[トラブルシューティング] を選択して [次へ] をクリックします。
5.  トラブルシューティング ページで、[起動] をクリックして PowerShell でトラブルシューティング メニューを起動します。
6.  メイン メニューで、[オブジェクト同期のトラブルシューティング]\(Troubleshoot Object Synchronization\) を選択します。
![オブジェクト同期のトラブルシューティング](media/tshoot-connect-objectsync/objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>入力パラメーターのトラブルシューティング
トラブルシューティング タスクでは、次の入力パラメーターが必要です。
1.  **オブジェクトの識別名** – これは、トラブルシューティングが必要なオブジェクトの識別名です
2.  **AD コネクタ名** – これは、上記のオブジェクトが存在している AD フォレストの名前です。
3.  Azure AD テナント グローバル管理者の資格情報![グローバル管理者の資格情報](media/tshoot-connect-objectsync/objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>トラブルシューティング タスクの結果を理解する
トラブルシューティング タスクでは、次のチェックが実行されます。

1.  オブジェクトが Azure Active Directory に同期される場合は、UPN の不一致を検出します
2.  オブジェクトがドメイン フィルタリングによってフィルター処理されるかどうかを確認します
3.  オブジェクトが OU フィルタリングによってフィルター処理されるかどうかを確認します
4.  リンクされたメールボックスによってオブジェクトの同期がブロックされているかどうかを確認します
5. オブジェクトが同期されることになっていない動的配布グループかどうかを確認します

このセクションの残りの部分では、タスクによって返される具体的な結果について説明します。 各ケースでは、タスクによって分析が示され、その後、問題を解決するための推奨の操作が示されます。

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>オブジェクトが Azure Active Directory に同期される場合に、UPN の不一致を検出する
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>UPN サフィックスが Azure AD テナントで検証されない
UserPrincipalName (UPN)/代替ログイン ID サフィックスが Azure AD テナントで検証されない場合、Azure Active Directory では、UPN サフィックスが既定のドメイン名 "onmicrosoft.com" に置き換えられます。

![UPN が Azure AD に置き換えられる](media/tshoot-connect-objectsync/objsynch2.png)

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Azure AD テナントの DirSync 機能 'SynchronizeUpnForManagedUsers' が無効になっている
Azure AD テナントの DirSync 機能 'SynchronizeUpnForManagedUsers' が無効になっている場合、Azure Active Directory では、管理された認証を使用しているライセンス ユーザー アカウントについて、UserPrincipalName/代替ログイン ID への同期更新を行うことができません。

![SynchronizeUpnForManagedUsers](media/tshoot-connect-objectsync/objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>オブジェクトがドメイン フィルタリングによってフィルター処理されている
### <a name="domain-is-not-configured-to-sync"></a>ドメインが同期するように構成されていない
ドメインが構成されていないため、オブジェクトはスコープ外となります。 下記の例では、オブジェクトの属しているドメインが同期対象から除外されているため、オブジェクトが同期スコープ外となっています。

![ドメインが同期するように構成されていない](media/tshoot-connect-objectsync/objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>ドメインが同期用に構成されているが、実行プロファイル/実行スッテップがない
ドメインの実行プロファイル/実行スッテップがないため、オブジェクトはスコープ外となります。 下記の例では、オブジェクトの属しているドメインにフル インポート実行プロファイルの実行ステップがないため、オブジェクトが同期スコープ外となっています。
![不足している実行プロファイル](media/tshoot-connect-objectsync/objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>オブジェクトが OU フィルタリングによってフィルター処理されている
OU フィルタリング構成によって、オブジェクトは同期スコープ外となります。 下記の例では、オブジェクトは OU=NoSync,DC=bvtadwbackdc,DC=com に属しています。  この OU は同期スコープに含まれません。</br>

![OU](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>リンクされたメールボックスの問題
リンクされたメールボックスは、別の信頼できるアカウント フォレストにある外部マスター アカウントに関連付けられることが想定されています。 そのような外部マスター アカウントが存在しない場合、Azure AD Connect は、Exchange フォレスト内のリンクされたメールボックスに対応するユーザー アカウントを、Azure AD テナントに同期しません。</br>
![リンクされたメールボックス](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>動的配布グループの問題
オンプレミスの Active Directory と Azure Active Directory の間にはさまざまな相違があるため、Azure AD Connect は動的配布グループを Azure AD テナントに同期しません。

![動的配布グループ](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>HTML レポート
トラブルシューティング タスクでは、オブジェクトを分析するだけでなく、オブジェクトについてわかっているすべての情報を含んだ、HTML レポートを生成することもできます。 この HTML レポートを必要に応じてサポート チームと共有し、さらなるトラブルシューティングを行うこともできます。

![HTML レポート](media/tshoot-connect-objectsync/objsynch8.png)

## <a name="next-steps"></a>次のステップ
「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
