---
title: Azure Active Directory Identity Protection (更新版) で危険なユーザーとサインインを調査する方法 | Microsoft Docs
description: Azure Active Directory Identity Protection (更新版) で危険なユーザーとサインインを調査する方法について説明します。
services: active-directory
keywords: Azure Active Directory Identity Protection, Cloud App Discovery, アプリケーションの管理, セキュリティ, リスク, リスク レベル, 脆弱性, セキュリティ ポリシー
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.author: joflore
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.date: 01/25/2019
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a90195a2d0899b0a157cc67badd2f9873164987
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108965"
---
# <a name="how-to-investigate-risky-users-and-sign-ins"></a>方法:危険なユーザーとサインインの調査 


危険なサインイン レポートと危険なユーザー レポートを使用して、環境内のリスクを調査し、詳細を把握できます。 危険なサインインとユーザーをフィルター処理して並べ替えられるため、組織内の潜在的な侵入についてよりよく理解することができます。 


## <a name="risky-users-report"></a>危険なユーザー レポート

危険なユーザー レポートによって提供される情報を使用すると、次のような疑問への答えを得ることができます。

- 危険度が高いのはどのユーザーか。
- リスクの状態が修復されているのはどのユーザーか。



このレポートへの最初のエントリ ポイントは、セキュリティ ページの **[調査]** セクションです。

![危険なユーザー レポート](./media/howto-investigate-risky-users-signins/01.png)


危険なユーザー レポートの既定のビューには、以下が表示されます。

- Name

- リスクの状態

- リスク レベル

- リスクの詳細

- リスクの最終更新日時

- Type

- Status
 

![危険なユーザー レポート](./media/howto-investigate-risky-users-signins/03.png)


リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。

![危険なユーザー レポート](./media/howto-investigate-risky-users-signins/04.png)

列ダイアログでは、追加フィールドの表示や、既に表示されているフィールドの削除が可能です。

リスト ビュー内の項目をクリックすると、その項目に関する確認可能な詳細が横表示で表示されます。

![危険なユーザー レポート](./media/howto-investigate-risky-users-signins/05.png)


詳細ビューでは次のものが表示されます。

- 基本情報

- 最近の危険なサインイン

- サインインにリンクされていないリスク イベント

- リスクの履歴



さらに、次の操作が可能です。

![危険なユーザー レポート](./media/howto-investigate-risky-users-signins/08.png)

- すべてのサインイン ショートカットを表示して、該当するユーザーのサインイン レポートを確認します。

- すべての危険なサインインを表示して、危険としてフラグが設定された、該当するユーザーのすべてのサインインを確認します。

- ユーザーの ID が侵害されていると思われる場合は、ユーザーのパスワードをリセットします。

- ユーザーのアクティブなリスク イベントが誤検知であると思われる場合は、ユーザー リスクを無視します。 詳細については、[検出の精度を向上させる方法](howto-improve-detection-accuracy.md)に関するページを参照してください。



### <a name="filter-risky-users"></a>危険なユーザーをフィルター処理する

報告されるデータを有用なものだけに絞り込むために、次の既定のフィールドを使用して危険なユーザー データをフィルター処理できます。

- Name

- ユーザー名

- リスクの状態

- リスク レベル

- Type

- Status

![危険なユーザー レポート](./media/howto-investigate-risky-users-signins/06.png)



**[名前]** フィルターでは、確認したいユーザーの名前またはユーザー プリンシパル名 (UPN) を指定できます。


**[リスクの状態]** フィルターでは、次のいずれかを選択できます。

- 危険
- 修復された
- 無視


**[リスク レベル]** フィルターでは、次のいずれかを選択できます。

- 高
- Medium
- 低


**[種類]** フィルターでは、次のいずれかを選択できます。

- ゲスト
- メンバー

**[状態]** フィルターでは、次のいずれかを選択できます。

- Deleted
- アクティブ


### <a name="download-risky-users-data"></a>危険なユーザー データをダウンロードする

Azure portal の外部で操作する場合は、危険なユーザー データをダウンロードできます。 [ダウンロード] をクリックすると、最新の 2,500 レコードの CSV ファイルが作成されます。 

![危険なユーザー レポート](./media/howto-investigate-risky-users-signins/07.png)


ツール バーの [列] をクリックすることで、リスト ビューをカスタマイズできます。
 
これで、追加のフィールドの表示または既に表示されているフィールドの削除ができます。
 
危険なユーザーの詳細については、[詳細] ドロワーをクリックして展開します

 



## <a name="risky-sign-ins-report"></a>リスクの高いサインイン レポート

危険なサインイン レポートによって提供される情報を使用すると、次のような疑問への答えを得ることができます。

- 先週、匿名 IP アドレスのリスク イベントが発生した正常なサインインの数はどれくらいか。

- 先月、侵害が確認されたのはどのユーザーか。

- Office 365 ポータルへの危険なサインインを行ったのはどのユーザーか。




このレポートへの最初のエントリ ポイントは、セキュリティ ページの **[調査]** セクションです。

![リスクの高いサインイン レポート](./media/howto-investigate-risky-users-signins/02.png)

危険なサインイン レポートの既定のビューには、以下が表示されます。

- Date

- User

- Application

- サインインの状態

- リスクの状態

- リスク レベル (集計)

- リスク レベル (リアルタイム)

- 条件付きアクセス

- MFA が必要  
 

![リスクの高いサインイン レポート](./media/howto-investigate-risky-users-signins/09.png)


リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。

![危険なユーザー レポート](./media/howto-investigate-risky-users-signins/11.png)

列ダイアログでは、追加フィールドの表示や、既に表示されているフィールドの削除が可能です。

リスト ビュー内の項目をクリックすると、その項目に関する確認可能な詳細が横表示で表示されます。

![危険なユーザー レポート](./media/howto-investigate-risky-users-signins/12.png)


詳細ビューでは次のものが表示されます。

- 基本情報

- デバイス情報

- 危険な情報

- MFA 情報

- 条件付きアクセス





さらに、次の操作が可能です。

![危険なユーザー レポート](./media/howto-investigate-risky-users-signins/13.png)

- 侵害ありと確認する 

- 安全と確認する

詳細については、[検出の精度を向上させる方法](howto-improve-detection-accuracy.md)に関するページを参照してください。




### <a name="filter-risky-sign-ins"></a>危険なサインインをフィルター処理する

報告されるデータを有用なものだけに絞り込むために、次の既定のフィールドを使用して危険なユーザー データをフィルター処理できます。

- User
- Application
- サインインの状態
- リスクの状態
- リスク レベル (集計)
- リスク レベル (リアルタイム)
- 条件付きアクセス
- Date
- リスク レベルの種類

![リスクの高いサインイン レポート](./media/howto-investigate-risky-users-signins/14.png)



**[名前]** フィルターでは、確認したいユーザーの名前またはユーザー プリンシパル名 (UPN) を指定できます。

**[アプリケーション]** フィルターでは、ユーザーがアクセスしようとしたクラウド アプリを指定することができます。

**[サインイン状態]** フィルターでは、次のいずれかを選択できます。

- All
- Success
- 失敗


**[リスクの状態]** フィルターでは、次のいずれかを選択できます。

- 危険
- セキュリティ侵害の確認済み
- 安全であるとの確認済み
- 無視
- 修復された


**[リスク レベル (集計)]** フィルターでは、次のいずれかを選択できます。

- 高
- Medium
- 低

**[リスク レベル (リアルタイム)]** フィルターでは、次のいずれかを選択できます。

- 高
- Medium
- 低


**[条件付きアクセス]** フィルターでは、次のいずれかを選択できます。

- All
- 適用されていません
- Success
- 失敗


**[日付]** フィルターでは、返されるデータの期間を定義できます。
次のいずれかの値になります。

- 過去 1 か月
- 過去 7 日間
- 過去 24 時間
- カスタム期間





### <a name="download-risky-sign-ins-data"></a>危険なサインイン データをダウンロードする

Azure portal の外部で操作する場合は、危険なサインイン データをダウンロードできます。 [ダウンロード] をクリックすると、最新の 2,500 レコードの CSV ファイルが作成されます。 

![危険なユーザー レポート](./media/howto-investigate-risky-users-signins/15.png)



## <a name="next-steps"></a>次の手順

Azure AD Identity Protection の概要を入手するには、[Azure AD Identity Protection の概要](overview-v2.md)に関するページを参照してください。
