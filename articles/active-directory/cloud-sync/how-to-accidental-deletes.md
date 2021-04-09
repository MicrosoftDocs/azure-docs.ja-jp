---
title: Azure AD Connect クラウド同期の誤削除
description: このトピックでは、削除を防止する誤削除機能を使用する方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/25/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0da54bd28c1d9ea933e88b6c86cf6092c10d036a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785187"
---
# <a name="accidental-delete-prevention"></a>誤削除防止

次のドキュメントでは、Azure AD Connect クラウド同期の誤削除機能について説明します。誤削除機能は、構成の変更やオンプレミス ディレクトリの変更を誤って行うことで多くのユーザーやグループに影響を与えることがないように保護するために設計されています。  この機能では次のことができます。

- 誤って削除しないように自動的に保護する機能を構成します。 
- 構成が有効になるオブジェクト数 (しきい値) を設定します 
- 問題の同期ジョブが隔離されたら電子メールで通知を受け取ることができるように、このシナリオのために通知の電子メールアドレスを設定します 

この機能を使用するには、削除された場合に同期を停止する必要があるオブジェクト数のしきい値を設定します。  この数に達すると、同期が停止し、指定された電子メールに通知が送信されます。  この通知により、何が起こっているかを調べることができます。


## <a name="configure-accidental-delete-prevention"></a>誤削除防止の構成
新しい機能を使用するには、次の手順に従ってください。


1.  Azure Portal で、 **[Azure Active Directory]** を選びます。
2.  **[Azure AD Connect]** を選びます。
3.  **[Manage cloud sync]\(クラウド同期の管理\)** を選択します。
4. **[構成]** の下で、自分の構成を選択します。
5. **[設定]** で、次の項目を入力します。
    - **[電子メールの通知]** - 通知に使用する電子メール
    - **[Prevent accidental deletions]\(誤削除を防ぐ\)** - このチェックボックスをオンにして機能を有効にします
    - **[Accidental deletion threshold]\(誤削除のしきい値\)** -同期を停止して通知を送信するオブジェクト数を入力します

![不注意による削除](media/how-to-accidental-deletes/accident-1.png)

## <a name="recovering-from-an-accidental-delete-instance"></a>誤削除インスタンスからの復旧
誤削除が発生すると、プロビジョニング エージェントの構成の状態に表示されます。  **[Delete threshold exceeded]\(削除しきい値を超えました\)** と表示されます。
 
![誤削除の状態](media/how-to-accidental-deletes/delete-1.png)

**[Delete threshold exceeded]\(削除しきい値を超えました\)** をクリックすると、同期状態情報が表示されます。  これにより、追加の詳細情報が提供されます。 
 
 ![同期状態](media/how-to-accidental-deletes/delete-2.png)

省略記号を右クリックすると、次のオプションが表示されます。
 - プロビジョニング ログの表示
 - エージェントの表示
 - Allow deletes\(削除を許可する\)

 ![右クリックします。](media/how-to-accidental-deletes/delete-3.png)

**[プロビジョニング ログの表示]** を使用して、**StagedDelete** エントリを参照し、削除されたユーザーに提供されている情報を確認できます。
 
 ![プロビジョニング ログ](media/how-to-accidental-deletes/delete-7.png)

### <a name="allowing-deletes"></a>削除を許可する

**[Allow deletes]\(削除を許可する\)** アクションを使用して、誤削除のしきい値をトリガーしたオブジェクトを削除します。  削除を受け入れるには、次の手順に従います。  

1. 省略記号を右クリックし、 **[Allow deletes]\(削除を許可する\)** を選択します。
2. 確認の **[はい]** をクリックして削除を許可します。
 
 ![確認の [はい]](media/how-to-accidental-deletes/delete-4.png)

3. 削除が受け入れられたことを確認するメッセージが表示され、次のサイクルで状態が正常に戻ります。 
 
 ![削除を受け入れる](media/how-to-accidental-deletes/delete-8.png)

### <a name="rejecting-deletions"></a>削除の拒否

削除を許可しない場合は、次の操作を行う必要があります。
- 削除のソースを調査する
- 問題を修正する (たとえば、OU が誤ってスコープ外に移動され、スコープに再び追加された場合)
- エージェント構成で **同期の再起動** を実行する

## <a name="next-steps"></a>次のステップ 

- [Azure AD Connect クラウド同期のトラブルシューティング](how-to-troubleshoot.md)
- [Azure AD Connect クラウド同期のエラー コード](reference-error-codes.md)
 

