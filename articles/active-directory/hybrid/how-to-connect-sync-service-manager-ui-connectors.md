---
title: Azure AD の Synchronization Service Manager UI のコネクタ | Microsoft Docs
description: Azure AD Connect の Synchronization Service Manager の [コネクタ] タブについて
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: d215c2e200308664f24daa28a0054c8f1bcfc09c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91319879"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Azure AD Connect の Sync Service Manager でコネクタを使用する

![Sync Service Manager を示すスクリーンショット。](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

[コネクタ] タブを利用し、同期エンジンが接続されているすべてのシステムを管理します。

## <a name="connector-actions"></a>コネクタのアクション
| アクション | 解説 |
| --- | --- |
| 作成 |使用しないでください。 その他の AD フォレストに接続するには、インストール ウィザードを使用します。 |
| Properties |ドメインと OU フィルター処理に使用されます。 |
| [削除](#delete) |コネクタ スペースのデータを削除するか、フォレストへの接続を削除するために使用されます。 |
| [実行プロファイルの構成](#configure-run-profiles) |ドメイン フィルター処理を除き、ここで構成するものはありません。 このアクションは、既に構成されている実行プロファイルを確認する場合に使用します。 |
| ラン |プロファイルの 1 回限りの実行を開始するために使用されます。 |
| Stop |現在プロファイルを実行しているコネクタを停止します。 |
| コネクタのエクスポート |使用しないでください。 |
| コネクタのインポート |使用しないでください。 |
| コネクタの更新 |使用しないでください。 |
| スキーマの更新 |キャッシュされたスキーマを更新します。 このアクション代わりに、インストール ウィザードのオプションを使用することをお勧めします。インストール ウィザードのオプションであれば、同期規則も更新されます。 |
| [コネクタ スペースの検索](#search-connector-space) |オブジェクトを検索し、オブジェクトとそのデータをシステム全体でフォローするために使用されます。 |

### <a name="delete"></a>削除
削除アクションには次の 2 つがあります。  
![[Delete connector space only]\(コネクタ スペースのみを削除する\) が選択された [コネクタの削除] ウィンドウを示すスクリーンショット。](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

**[Delete connector space only (コネクタ スペースのみを削除する)]** オプションを選択すると、すべてのデータが削除されますが、構成は維持されます。

**[Delete Connector and connector space (コネクタとコネクタ スペースを削除する)]** オプションを選択すると、データと構成が削除されます。 このオプションは、フォレストに今後接続しない場合に使用します。

いずれのオプションでも、すべてのオブジェクトが同期され、メタバース オブジェクトが更新されます。 このアクションは、実行時間の長い操作となります。

### <a name="configure-run-profiles"></a>実行プロファイルの構成
このオプションを使用すると、コネクタ用に構成された実行プロファイルを表示できます。

![[Delta Import]\(差分インポート\) が選択された [Configure Run Profiles]\(実行プロファイルの構成\) ウィンドウを示すスクリーンショット。](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>コネクタ スペースの検索
コネクタ スペースの検索アクションは、オブジェクトを検索し、データ問題を解決するのに役立ちます。

![[Search Connector Space]\(コネクタ スペースの検索\) ウィンドウを示すスクリーンショット。](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

まず、 **[scope]** (範囲) を選択します。 データ (RDN、DN、アンカー、サブツリー) またはオブジェクトの状態 (その他すべてのオプション) に基づいて検索できます。  
![[Scope]\(スコープ\) ドロップダウン メニューを示すスクリーンショット。](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
たとえば、サブツリー検索を行うと、1 つの OU のすべてのオブジェクトが取得されます。  
![[Sub-Tree]\(サブツリー\) 検索の例を示すスクリーンショット。](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
このグリッドからオブジェクトを選択し、 **[プロパティ]** を選択して、ソース コネクタ スペースからメタバースを経てターゲット コネクタ スペースまで [フォロー](tshoot-connect-object-not-syncing.md)できます。

### <a name="changing-the-ad-ds-account-password"></a>AD DS アカウント パスワードの変更
アカウントのパスワードを変更すると、Synchronization Service でオンプレミスの AD に変更をインポートまたはエクスポートできなくなります。   次のように表示されます。

- AD コネクタのインポートまたはエクスポート手順が失敗し、"no-start-credentials (開始資格情報なし)" エラーが表示されます。
- Windows イベント ビューアーのアプリケーション イベント ログに、イベント ID 6000 のエラーと「資格情報が有効でないため、管理エージェント "contoso.com" は実行できませんでした。」というメッセージが表示されます。

この問題を解決するには、次の手順で AD DS ユーザー アカウントを更新します。


1. Synchronization Service Manager を起動します ([スタート]、[同期サービス] の順に移動します)。
</br>![Sync Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. **[コネクタ]** タブに移動します。
3. AD DS アカウントを使用して構成されている AD コネクタを選択します。
4. [アクション] の **[プロパティ]** を選択します。
5. ポップアップ ダイアログで、[Active Directory フォレストに接続] を選択します。
6. フォレスト名は、対応するオンプレミスの AD を示します。
7. ユーザー名は、同期に使用する AD DS アカウントを示します。
8. ![[Azure AD Connect Sync 暗号化キー ユーティリティ]](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png) というパスワード テキスト ボックスに、AD DS アカウントの新しいパスワードを入力します。
9. [OK] をクリックして、新しいパスワードを保存し、Synchronization Service を再起動して、メモリ キャッシュから古いパスワードを削除します。



## <a name="next-steps"></a>次のステップ
[Azure AD Connect Sync](how-to-connect-sync-whatis.md) の構成に関するページをご覧ください。

「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
