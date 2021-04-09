---
title: 'Azure AD Connect sync: 誤って削除されないように保護する | Microsoft Docs'
description: このトピックでは、Azure AD Connect の "誤って削除されないように保護する" 機能について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16d48cda87b8226ebc3bbab179c1034abf0a486f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90084611"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Azure AD Connect Sync: 誤って削除されないように保護する
このトピックでは、Azure AD Connect の "誤って削除されないように保護する" 機能について説明します。

Azure AD Connect のインストール中は、誤った削除操作を防止する機能が既定で有効になり、500 個を超える削除を行うエクスポートを許可しないように構成されます。 この機能は、構成を誤って変更することと、たくさんのユーザーや各種オブジェクトに影響を与える可能性があるオンプレミス ディレクトリを変更することを防ぐように設計されています。

## <a name="what-is-prevent-accidental-deletes"></a>誤って削除されないように保護する機能
たとえば、次のような大量の削除を行う状況でこの機能が作用します。

* [OU](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) 全体または[ドメイン](how-to-connect-sync-configure-filtering.md#domain-based-filtering)全体を除外していた[フィルター処理](how-to-connect-sync-configure-filtering.md)に変更を加えた場合。
* OU 内のすべてのオブジェクトを削除した場合。
* OU の名前を変更したことでその OU 内のすべてのオブジェクトが同期の対象外と見なされる場合。

既定値の 500 オブジェクトは、PowerShell の `Enable-ADSyncExportDeletionThreshold` を使って変更できます。このコマンドレットは、Azure Active Directory Connect でインストールされる AD Sync モジュールの一部です。 組織の規模に合わせてこの値を構成する必要があります。 同期スケジューラは 30 分おきに実行されるので、この値は 30 分以内に確認される削除数になります。

Azure AD にエクスポートするようにステージングされた削除の数が多すぎる場合は、エクスポートは停止され、次のような電子メールが送信されます。

![Prevent Accidental deletes email](./media/how-to-connect-sync-feature-prevent-accidental-deletes/email.png)

> *(技術担当者) 様。(時刻) に、ID 同期サービスは、削除の数が、(組織名) に対して構成されている削除のしきい値を超えたことを検出しました。この ID 同期の実行で、合計 (数) 個のオブジェクトが削除のために送信されました。これは、構成されている削除のしきい値である (数) 個のオブジェクトに達しているか、それを超えています。続行する前に、これらの削除を処理してよいかどうかを確認する必要があります。この電子メール メッセージに記載されているエラーの詳細情報については、「誤って削除されないように保護する」を参照してください。*
>
> 

また、**Synchronization Service Manager** UI でエクスポート プロファイルのステータスが `stopped-deletion-threshold-exceeded` となっていることを確認できます。
![誤って削除されないように保護する Sync Service Manager UI](./media/how-to-connect-sync-feature-prevent-accidental-deletes/syncservicemanager.png)

これを予想していなかった場合は、調査し、修正のアクションを実行します。 削除されようとしているオブジェクトを表示するには、次の操作を行います。

1. [スタート] メニューから **[同期サービス]** を起動します。
2. **[コネクタ]** に進みます。
3. 種類が「 **Azure Active Directory**」のコネクタを選択します。
4. 右にある **[アクション]** で、 **[コネクタの検索領域]** を選択します。
5. ポップアップ ウィンドウの **[Scope (範囲)]** で、 **[Disconnected Since (切断時刻)]** を選択し、過去の時間を選択します。 **[検索]** をクリックします。 このページには、削除されようとしているすべてのオブジェクトが表示されます。 各項目をクリックすると、そのオブジェクトに関する追加情報を取得できます。 また、 **[Column Setting (列設定)]** をクリックして、グリッドに表示する属性を追加することもできます。

![コネクタ スペースの検索](./media/how-to-connect-sync-feature-prevent-accidental-deletes/searchcs.png)

[!NOTE] すべての削除が望ましいかどうかわからず、安全な道を選択することがあります。 PowerShell コマンドレットの `Enable-ADSyncExportDeletionThreshold` を使用すると、望ましくない削除を許可する可能性があるしきい値を無効にするのではなく、新しいしきい値を設定できます。 

## <a name="if-all-deletes-are-desired"></a>すべて削除したい場合
すべての削除が望まれる場合、次のように操作します。

1. 現在の削除のしきい値を取得するには、PowerShell コマンドレット `Get-ADSyncExportDeletionThreshold` を実行します。 Azure AD グローバル管理者のアカウントとパスワードを入力します。 既定値は 500 です。
2. この保護を一時的に無効にし、それらの削除を行うには、PowerShell コマンドレットの `Disable-ADSyncExportDeletionThreshold`を実行します。 Azure AD グローバル管理者のアカウントとパスワードを入力します。
   ![Azure AD グローバル管理者のユーザー名とパスワードを入力するためのダイアログ ボックスを示すスクリーンショット。](./media/how-to-connect-sync-feature-prevent-accidental-deletes/credentials.png)
3. Azure Active Directory Connector が選択されている状態で、 **[実行]** アクションを選択し、 **[エクスポート]** を選択します。
4. 保護を再度有効にするには、PowerShell コマンドレットの `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`を実行します。 現在の削除のしきい値を取得する場合、500 を通知する値に置き換えます。 Azure AD グローバル管理者のアカウントとパスワードを入力します。

## <a name="next-steps"></a>次のステップ
**概要トピック**

* [Azure AD Connect 同期:同期を理解してカスタマイズする](how-to-connect-sync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)
