---
title: 検疫のアプリケーション プロビジョニング状態 | Microsoft Docs
description: 自動ユーザー プロビジョニング用にアプリケーションを構成したら、検疫のプロビジョニング状態とクリアする方法について説明します。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/28/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 54d02b3189825d08716b73b7250efd4e3f334aa0
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88234741"
---
# <a name="application-provisioning-in-quarantine-status"></a>検疫状態のアプリケーションのプロビジョニング

Azure AD プロビジョニング サービスは、構成の正常性を監視し、異常なアプリを "検疫" 状態にします。 ターゲット システムに対する呼び出しのほとんどまたはすべてが、管理者の資格情報が無効であるなどの理由で常にエラーで失敗する場合、プロビジョニング ジョブは検疫状態になります。

検疫状態の間、増分サイクルの頻度は徐々に減少し、最終的には 1 日 1 回になります。 すべてのエラーが修正されると、プロビジョニング ジョブは検疫から削除され、次の同期サイクルが開始します。 プロビジョニング ジョブが検疫にとどまっている期間が 4 週間を超えると、そのプロビジョニング ジョブは無効 (実行が停止) になります。

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>アプリケーションが検疫されているかどうかを確認するにはどうすればよいですか。

アプリケーションが検疫中かどうかを確認するには、次の 3 つの方法があります。
  
- Azure portal で、 **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]**  > &lt;*アプリケーション名*&gt; >  **[プロビジョニング]** の順に移動し、検疫メッセージの進行状況バーを確認します。   

  ![検疫状態を示すプロビジョニング ステータス バー](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Azure portal で **[Azure Active Directory]**  >  **[監査ログ]** の順に移動し、 **[アクティビティ: 検疫]** フィルターをオンにして、検疫履歴を確認します。 プロビジョニングが現在検疫中であるかどうかは前述した進行状況バーのビューに表示され、アプリケーションの検疫履歴は監査ログで確認できます。 

- Microsoft Graph 要求の [Get synchronizationJob](/graph/api/synchronization-synchronizationjob-get?tabs=http&view=graph-rest-beta) を使用して、プロビジョニング ジョブの状態をプログラムで取得します。

```microsoft-graph
        GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

- メールを確認します。 アプリケーションが検疫されると、1 回限りの通知メールが送信されます。 検疫の理由が変化した場合、新しい検疫理由を示す更新されたメールが送信されます。 メールが送信されない場合は、次を確認します。

  - アプリケーションのプロビジョニング構成でしているしている**通知メール**が有効であることを確認します。
  - 通知メールの受信トレイにスパム フィルターが適用されていないことを確認します。
  - メールの登録を解除していないことを確認します。

## <a name="why-is-my-application-in-quarantine"></a>アプリケーションが検疫されているのはなぜですか。

|説明|推奨される操作|
|---|---|
|**SCIM へのコンプライアンスの問題:** 予期される HTTP/200 OK 応答ではなく、HTTP/404 Not Found 応答が返されました。 この場合、Azure AD プロビジョニング サービスによってターゲット アプリケーションへの要求が行われ、予期しない応答を受け取りました。|管理者資格情報のセクションを調べて、アプリケーションでテナントの URL を指定する必要があるかどうか、および URL が正しいことを確認します。 問題が見つからない場合は、アプリケーションの開発者に連絡し、サービスが SCIM に準拠していることを確認してください。 https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**無効な資格情報:** ターゲット アプリケーションへのアクセスの承認を試みたとき、指定された資格情報が無効であることを示す応答をターゲット アプリケーションから受け取りました。|プロビジョニング構成 UI の管理者資格情報のセクションに移動し、有効な資格情報で再度アクセスを承認してください。 アプリケーションがギャラリーにある場合は、アプリケーション構成のチュートリアルで必要な追加の手順を確認してください。|
|**重複したロール:** Salesforce や Zendesk などの特定のアプリケーションからインポートされるロールは、一意である必要があります。 |Azure portal でアプリケーションの[マニフェスト](../develop/reference-app-manifest.md)に移動し、重複するロールを削除します。|

 プロビジョニング ジョブの状態を取得するための Microsoft Graph 要求では、次の検疫理由が示されます。

- `EncounteredQuarantineException` は、無効な資格情報が指定されたことを示します。 プロビジョニング サービスは、ソース システムとターゲット システム間の接続を確立できません。

- `EncounteredEscrowProportionThreshold` は、プロビジョニングがエスクローのしきい値を超えたことを示します。 この状態は、60% 以上のプロビジョニング イベントが失敗すると発生します。

- `QuarantineOnDemand` は、アプリケーションの問題を検出し、検疫するよう手動で設定したことを意味します。

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>アプリケーションを検疫しないよう設定するにはどうすればよいですか。

まず、アプリケーションが検疫される原因となった問題を解決します。

- アプリケーションのプロビジョニング設定を調べ、[有効な管理者の資格情報](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning)が入力されていることを確認してください。 Azure AD は、ターゲット アプリケーションとの信頼関係を確立できる必要があります。 有効な資格情報を入力し、アカウントに必要なアクセス許可があることを確認します。

- [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を確認して、検疫の原因となっているエラーを詳しく調査し、解決します。 Azure portal で、 **[Azure Active Directory]** &gt; **[エンタープライズ アプリ]** &gt; **[プロビジョニング ログ (プレビュー)]** ( **[アクティビティ]** セクション内) を順に選択して、プロビジョニング ログにアクセスします。

問題を解決したら、プロビジョニング ジョブを再開します。 属性マッピングやスコープフィルターなど、アプリケーションのプロビジョニング設定に特定の変更を加えると、自動的にプロビジョニングが再開されます。 アプリケーションの **[プロビジョニング]** ページの進行状況バーは、プロビジョニングが最後に開始された日時を示します。 プロビジョニング ジョブを手動で再起動する必要がある場合は、次のいずれかの方法を使用します。  

- Azure portal を使用して、プロビジョニング ジョブを再起動します。 **[設定]** のアプリケーションの **[プロビジョニング]** ページで、 **[Clear state and restart synchronization]\(状態を消去して同期を再開する\)** を選択し、 **[プロビジョニングの状態]** を **[オン]** に設定します。 この操作により、プロビジョニング サービスが完全に再起動されます (しばらく時間がかかることがあります)。 すべての初回サイクルが再度実行されます。これにより、エスクローがクリアされ、アプリが検疫から削除され、透かしがクリアされます。

- Microsoft Graph を使用して、[プロビジョニング ジョブを再起動します](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta)。 再起動する対象は完全に制御できます。 エスクローをクリアするか (検疫状態と判定されるためのエスクロー カウンターを再起動するため)、検疫をオフにするか (検疫からアプリケーションを削除するため)、または透かしをクリアするかを選択できます。 次の要求を使用します。
 
```microsoft-graph
        POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

"{Id}" をアプリケーション ID の値に置き換え、"{jobId}" を[同期ジョブの ID](/graph/api/resources/synchronization-configure-with-directory-extension-attributes?tabs=http&view=graph-rest-beta#list-synchronization-jobs-in-the-context-of-the-service-principal) に置き換えます。