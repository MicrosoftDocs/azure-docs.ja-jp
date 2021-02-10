---
title: Azure Active Directory へのアプリケーションの追加または削除に関する一般的な問題のトラブルシューティング
description: Azure Active Directory にアプリを追加または削除するときに直面する一般的な問題のトラブルシューティングを行います。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/11/2018
ms.author: kenwith
ms.openlocfilehash: dbfc90f01bdd9cc0a831160d06efa1c3952884a2
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257671"
---
# <a name="troubleshoot-common-problem-adding-or-removing-an-application-to-azure-active-directory"></a>Azure Active Directory へのアプリケーションの追加または削除に関する一般的な問題のトラブルシューティング
この記事は、Azure Active Directory にアプリを追加または削除するときに直面する一般的な問題について理解するのに役立ちます。

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>[追加] ボタンをクリックしたがアプリケーションが表示されるまでに長い時間がかかる
状況によって、アプリケーションがディレクトリに追加されてから表示されるまで、1 ～ 2 分 (またはそれ以上) かかる場合があります。 これは、通常期待されるパフォーマンスではありませんが、[Azure portal](https://portal.azure.com/) の右上隅にある **[通知]** アイコン (ビープ音) をクリックし、**[進行中]** または **[アプリケーションの追加]** というラベルが付いた **[完了]** 通知を検索して、アプリケーションの追加が進行中であることを確認できます。

アプリケーションが追加されていない場合、または **[追加]** ボタンをクリックしたときにエラーが発生した場合は、**[エラー]** 状態に **[通知]** と表示されます。 エラーに関する詳細を確認したり、サポート エンジニアと共有したりする場合は、「[ポータルの通知の詳細を確認する方法](#how-to-see-the-details-of-a-portal-notification)」セクションの手順に従って、エラーに関する詳細を確認できます。

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>[追加] ボタンをクリックしたがアプリケーションが表示されない
一時的な問題、ネットワークの問題、または、バグによって、アプリケーションの追加に失敗することがあります。 このような状況が発生すると、Azure portal の右上隅にある **[通知]** アイコン (ベル) をクリックしたときに、**[アプリケーションの追加]** 通知の横に赤い (!) アイコンが表示されます。 これは、アプリケーションを作成するときにエラーが発生したことを示します。

エラーが発生すると、**[追加]** ボタンをクリックしたときに **[エラー]** 状態に **[通知]** と表示されます。 エラーに関する詳細を確認したり、サポート エンジニアと共有したりする場合は、「[ポータルの通知の詳細を確認する方法](#how-to-see-the-details-of-a-portal-notification)」セクションの手順に従って、エラーに関する詳細を確認できます。

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>アプリケーションを追加した後に設定する方法がわからない
アプリケーションの学習について支援が必要な場合は、[SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)に関する記事から始めることをお勧めします。

さらに、[Azure AD アプリケーションのドキュメント ライブラリ](./what-is-application-management.md)では、Azure AD を使用したシングル サインオンおよびそのしくみの詳細について確認できます。

## <a name="i-want-to-delete-an-application-but-the-delete-button-is-disabled"></a>アプリケーションを削除しようとしたら、削除ボタンが無効になっていた

次のシナリオでは、削除ボタンが無効になります。

- エンタープライズ アプリケーションの配下のアプリケーションの場合、次のいずれのロールも持っていないとき:グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。

- Microsoft のアプリケーションの場合は、ロールに関係なく UI から削除することはできません。

- マネージ ID に対応する ServicePrincipal の場合。 マネージド ID のサービス プリンシパルは、エンタープライズ アプリ ブレードでは削除できません。 それを管理するには、Azure リソースにアクセスする必要があります。 [マネージド ID に関する詳細情報](../managed-identities-azure-resources/overview.md)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>ポータルの通知の詳細を確認する方法
ポータルの通知の詳細を確認するには、次の手順に従います。
1.  Azure portal の右上隅にある **[通知]** アイコン (ベル) を選択します
2.  **エラー** 状態のいずれかの通知 (横に赤い (!) が表示されているもの) を選択します。
    >[!NOTE]
    >**[成功]** または **[進行中]** 状態の通知をクリックすることはできません。
4.  **[通知の詳細]** の情報を使用して、問題に関する詳細を把握します。
5.  サポートが必要な場合は、この情報をサポート エンジニアや製品グループと共有して支援を受けることもできます。
6.  すべての通知の詳細をコピーしてサポートまたは製品グループのエンジニアと共有するには、 **[エラーのコピー]** テキスト ボックスの右にある **コピー アイコン** をクリックします。

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>通知の詳細をサポート エンジニアに送信して支援を受ける方法
支援が必要な場合は、**以下に記載された詳細をすべて** サポート エンジニアと共有することが重要です。これにより、すばやい解決が実現します。 **スクリーンショットを取る** か、または **[エラーのコピー]** テキスト ボックスの右にある **[エラーのコピー] アイコン** を選択します。

## <a name="notification-details-explained"></a>通知の詳細についての説明
通知の詳細については、次の説明を参照してください。

### <a name="essential-notification-items"></a>重要な通知項目
- **タイトル** – 通知のわかりやすいタイトル
  * 例 – **アプリケーション プロキシの設定**
- **説明** – 操作の結果として発生した内容についての説明
  -   例 – **入力された内部 URL は、既に別のアプリケーションで使用されています**
- **通知 ID** – 通知の一意の ID
  -   例 – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**
- **クライアント要求 ID** – ブラウザーで作成された特定の要求 ID
  -   例 – **302fd775-3329-4670-a9f3-bea37004f0bc**
- **タイム スタンプ UTC** – 通知発生時のタイムスタンプ (UTC)
  -   例 – **2017-03-23T19:50:43.7583681Z**
- **内部トランザクション ID** – システムでエラーを検索するために使用できる内部 ID
  -   例 – **71a2f329-ca29-402f-aa72-bc00a7aca603**
- **UPN** – 操作を実行したユーザー
  -   例 - **tperkins\@f128.info**
- **テナント ID** – 操作を実行したユーザーがメンバーであったテナントの一意の ID
  -   例 – **7918d4b5-0442-4a97-be2d-36f9f9962ece**
- **ユーザー オブジェクト ID** – 操作を実行したユーザーの一意の ID
  -   例 – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>詳細な通知項目
-   **表示名** – **(空白でも可)** エラーの詳細な表示名
    -   例 – **アプリケーション プロキシの設定**
-   **状態** – 通知の特定の状態
    -   例 – **失敗**
-   **オブジェクト ID** – **(空でも可)** 実行された操作の対象であるオブジェクト ID
    -   例 – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**
-   **詳細** – 操作の結果として発生した内容についての詳細な説明
    -   例 - **内部 URL `https://bing.com/` は既に使用中のため無効です**
-   **エラーのコピー** – **[エラーのコピー]** テキスト ボックスの右にある **コピー アイコン** を選択してすべての通知の詳細をコピーし、サポートまたは製品グループと共有します 
-   エンジニア
    -   例 ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```
