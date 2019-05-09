---
title: ギャラリー以外のアプリケーションの追加時に関する問題 |Microsoft Docs
description: ギャラリー以外のカスタム アプリケーションを追加するときに直面する一般的な問題について説明します
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: c53675c27276fdcf403ca5a6add5d86fc38e410a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842460"
---
# <a name="problem-adding-a-non-gallery-application"></a>ギャラリー以外のアプリケーションの追加時に関する問題

この記事は、**ギャラリー以外のカスタム アプリケーション**を追加するときにユーザーが直面する一般的な問題と、それらを解決するために実行できる内容を理解するのに役立ちます。 

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>[追加] ボタンをクリックしたがアプリケーションが表示されるまでに長い時間がかかる

状況によって、アプリケーションがディレクトリに追加されてから表示されるまで、1 ～ 2 分 (またはそれ以上) かかる場合があります。 これは、通常期待されるパフォーマンスではありませんが、[Azure portal](https://portal.azure.com/) の右上隅にある **[通知]** アイコン (ビープ音) をクリックし、**[進行中]** または **[アプリケーションを作成]** というラベルが付いた **[完了]** 通知を検索して、アプリケーションの追加が進行中であることを確認できます。

アプリケーションが追加されていない場合、または **[追加]** ボタンをクリックしたときにエラーが発生した場合は、**[エラー]** 状態に **[通知]** と表示されます。 エラーに関する詳細を確認したり、サポート エンジニアと共有したりする場合は、「[ポータルの通知の詳細を確認する方法](#how-to-see-the-details-of-a-portal-notification)」セクションの手順に従って、エラーに関する詳細を確認できます。

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>[追加] ボタンをクリックしたがアプリケーションが表示されない

一時的な問題、ネットワークの問題、または、バグによって、アプリケーションの追加に失敗することがあります。 このような状況が発生すると、Azure portal の右上隅にある **[通知]** アイコン (ビープ音) をクリックしたときに、**[アプリケーションの作成]** 通知の横に赤い (!) アイコンが表示されます。 これは、アプリケーションを作成するときにエラーが発生したことを示します。

エラーが発生すると、**[追加]** ボタンをクリックしたときに **[エラー]** 状態に **[通知]** と表示されます。 エラーに関する詳細を確認したり、サポート エンジニアと共有したりする場合は、「[ポータルの通知の詳細を確認する方法](#how-to-see-the-details-of-a-portal-notification)」セクションの手順に従って、エラーに関する詳細を確認できます。

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>アプリケーションを追加した後に設定する方法がわからない

カスタム アプリケーションについて学習する必要がある場合、 [Azure AD アプリケーションのドキュメント ライブラリ](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index)で Azure AD を使用したシングル サインオンおよびそのしくみの詳細について確認できます。

## <a name="how-to-see-the-details-of-a-portal-notification"></a>ポータル通知の詳細を確認する方法

ポータルの通知の詳細を確認するには、次の手順に従います。

1. Azure Portal の右上隅にある **[通知]** アイコン (ベル) をクリックします。

2. **エラー**状態のいずれかの通知 (横に赤い (!) が表示されているもの) を選択します。

   >[!NOTE]
   >**[成功]** または **[進行中]** 状態の通知をクリックすることはできません。
   >
   >

4. **[通知の詳細]** の情報を使用して、問題に関する詳細を把握します。

5. サポートが必要な場合は、この情報をサポート エンジニアや製品グループと共有して支援を受けることもできます。

6. **[コピー エラー]** ボックスの右にある **[コピー] アイコン**をクリックしてすべての通知の詳細をコピーし、サポート エンジニアまたは製品グループの技術者と共有することができます。

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>通知の詳細をサポート エンジニアに送信して支援を受ける方法

支援が必要な場合は、**以下に記載された詳細をすべて**サポート エンジニアと共有することが重要です。これにより、すばやい解決が実現します。 **スクリーンショットを取ったり**、**[エラーのコピー]** ボックスの右にある **[エラーのコピー] アイコン**をクリックすると、簡単に実行できます。

## <a name="notification-details-explained"></a>通知の詳細についての説明

通知の詳細については、次の説明を参照してください。

### <a name="essential-notification-items"></a>重要な通知項目

- **タイトル** – 通知のわかりやすいタイトル
  *  例 – **アプリケーション プロキシの設定**

- **説明** – 操作の結果として発生した内容についての説明

  *  例 – **入力された内部 URL は、既に別のアプリケーションで使用されています**

- **通知 ID** – 通知の一意の ID

  *  例 – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

- **クライアント要求 ID** – ブラウザーで作成された特定の要求 ID

  *  例 – **302fd775-3329-4670-a9f3-bea37004f0bc**

- **タイム スタンプ UTC** – 通知発生時のタイムスタンプ (UTC)

  *  例 – **2017-03-23T19:50:43.7583681Z**

- **内部トランザクション ID** – システムでエラーを検索するために使用できる内部 ID

  *  例 – **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN** – 操作を実行したユーザー

  *  例 - **tperkins\@f128.info**

- **テナント ID** – 操作を実行したユーザーがメンバーであったテナントの一意の ID

  *  例 – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

- **ユーザー オブジェクト ID** – 操作を実行したユーザーの一意の ID

  *  例 – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>詳細な通知項目

- **表示名** – **(空白でも可)** エラーの詳細な表示名

  *  例 - **アプリケーションのプロキシ設定**

- **状態** – 通知の特定の状態

  *  例 – **失敗**

- **オブジェクト ID** – **(空でも可)** 実行された操作の対象であるオブジェクト ID

  *  例 – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

- **詳細** – 操作の結果として発生した内容についての詳細な説明

  *  例 - **内部 URL `https://bing.com/` は既に使用中のため無効です**

- **エラーのコピー** – **[エラーのコピー]** ボックスの右にある**コピー アイコン**をクリックしてすべての通知の詳細をコピーし、サポートまたは製品グループと共有します 
- エンジニア

  *  例 ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```




