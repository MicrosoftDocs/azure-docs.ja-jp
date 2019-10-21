---
title: 検疫のアプリケーション プロビジョニング状態 | Microsoft Docs
description: 自動ユーザー プロビジョニング用にアプリケーションを構成したら、検疫のプロビジョニング状態とクリアする方法について説明します。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 704e217cd7ddea988b6a9812627aba8c8468fb73
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2019
ms.locfileid: "71955415"
---
# <a name="application-provisioning-in-quarantine-status"></a>検疫状態のアプリケーションのプロビジョニング

Azure AD プロビジョニング サービスは、構成の正常性を監視し、異常なアプリを "検疫" 状態にします。 ターゲット システムに対する呼び出しのほとんどまたはすべてが、管理者の資格情報が無効であるなどの理由で常にエラーで失敗する場合、プロビジョニング ジョブは検疫状態になります。

検疫状態の間、増分サイクルの頻度は徐々に減少し、最終的には 1 日 1 回になります。 すべてのエラーが修正されると、プロビジョニング ジョブは検疫から削除され、次の同期サイクルが開始します。 プロビジョニング ジョブが検疫にとどまっている期間が 4 週間を超えると、そのプロビジョニング ジョブは無効 (実行が停止) になります。

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>アプリケーションが検疫されているかどうかを確認するにはどうすればよいですか。

アプリケーションが検疫中かどうかを確認するには、次の 3 つの方法があります。
  
- Azure portal で、 **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]**  > &lt; *[アプリケーション名]* &gt; >  **[プロビジョニング]** の順に移動し、下部にある進行状況バーにスクロールします。  

  ![検疫状態を示すプロビジョニング ステータス バー](media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Microsoft Graph 要求の [Get synchronizationJob](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) を使用して、プロビジョニング ジョブの状態をプログラムで取得します。

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- メールを確認します。 アプリケーションが検疫されると、1 回限りの通知メールが送信されます。 検疫の理由が変化した場合、新しい検疫理由を示す更新されたメールが送信されます。 メールが送信されない場合は、次を確認します。

  - アプリケーションのプロビジョニング構成でしているしている**通知メール**が有効であることを確認します。
  - 通知メールの受信トレイにスパム フィルターが適用されていないことを確認します。
  - メールの登録を解除していないことを確認します。

## <a name="why-is-my-application-in-quarantine"></a>アプリケーションが検疫されているのはなぜですか。

プロビジョニング ジョブの状態を取得するための Microsoft Graph 要求では、次の検疫理由が示されます。

- `EncounteredQuarantineException` は、無効な資格情報が指定されたことを示します。 プロビジョニング サービスは、ソース システムとターゲット システム間の接続を確立できません。

- `EncounteredEscrowProportionThreshold` は、プロビジョニングがエスクローのしきい値を超えたことを示します。 この状態は、60% 以上のプロビジョニング イベントが失敗すると発生します。

- `QuarantineOnDemand` は、アプリケーションの問題を検出し、検疫するよう手動で設定したことを意味します。

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>アプリケーションを検疫しないよう設定するにはどうすればよいですか。

まず、アプリケーションが検疫される原因となった問題を解決します。

- アプリケーションのプロビジョニング設定を調べ、[有効な管理者の資格情報](configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning)が入力されていることを確認してください。 Azure AD は、ターゲット アプリケーションとの信頼関係を確立できる必要があります。 有効な資格情報を入力し、アカウントに必要なアクセス許可があることを確認します。

- [プロビジョニング ログ](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)を確認して、検疫の原因となっているエラーを詳しく調査し、解決します。 Azure portal で、 **[Azure Active Directory]** &gt; **[エンタープライズ アプリ]** &gt; **[プロビジョニング ログ (プレビュー)]** ( **[アクティビティ]** セクション内) を順に選択して、プロビジョニング ログにアクセスします。

問題を解決したら、プロビジョニング ジョブを再開します。 属性マッピングやスコープフィルターなど、アプリケーションのプロビジョニング設定に特定の変更を加えると、自動的にプロビジョニングが再開されます。 アプリケーションの **[プロビジョニング]** ページの進行状況バーは、プロビジョニングが最後に開始された日時を示します。 プロビジョニング ジョブを手動で再起動する必要がある場合は、次のいずれかの方法を使用します。  

- Azure portal を使用して、プロビジョニング ジョブを再起動します。 **[設定]** のアプリケーションの **[プロビジョニング]** ページで、 **[Clear state and restart synchronization]\(状態を消去して同期を再開する\)** を選択し、 **[プロビジョニングの状態]** を **[オン]** に設定します。 この操作により、プロビジョニング サービスが完全に再起動されます (しばらく時間がかかることがあります)。 すべての初回サイクルが再度実行されます。これにより、エスクローがクリアされ、アプリが検疫から削除され、透かしがクリアされます。

- Microsoft Graph を使用して、[プロビジョニング ジョブを再起動します](https://docs.microsoft.com/en-us/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)。 再起動する対象は完全に制御できます。 エスクローをクリアするか (検疫状態と判定されるためのエスクロー カウンターを再起動するため)、検疫をオフにするか (検疫からアプリケーションを削除するため)、または透かしをクリアするかを選択できます。 次の要求を使用します。
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`