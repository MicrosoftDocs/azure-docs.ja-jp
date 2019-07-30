---
title: 認証方法の使用状況と分析情報に関するレポート (プレビュー) - Azure Active Directory
description: Azure AD のパスワード リセットのセルフサービスと Multi-Factor Authentication の認証方法の使用状況に関するレポート
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0f6a74308f1bc4a7b77576fb9f39f965de0a4f8
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561025"
---
# <a name="authentication-methods-usage--insights-preview"></a>認証方法の使用状況と分析情報 (プレビュー)

使用状況と分析情報を使うと、Azure Multi-Factor Authentication やパスワード リセットのセルフサービスなどの機能の認証方法が組織内でどのように機能しているかについて理解を深めることができます。 このレポート機能は、組織が登録中の方法や各方法の使用状況を確認するための手段となるものです。

## <a name="permissions-and-licenses"></a>アクセス許可とライセンス

使用状況と分析情報にアクセスできるロールは次のとおりです。

- グローバル管理者
- セキュリティ閲覧者
- セキュリティ管理者
- レポート閲覧者

使用状況と分析情報にアクセスするうえで追加のライセンスは必要ありません。 Azure Multi-Factor Authentication とパスワード リセットのセルフサービス (SSPR) のライセンス情報は、[Azure Active Directory の価格に関するサイト](https://azure.microsoft.com/pricing/details/active-directory/)を参照してください。

## <a name="how-it-works"></a>動作のしくみ

認証方法の使用状況と分析情報にアクセスする方法は次のとおりです。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
1. **Azure Active Directory** >  **[パスワード リセット]**  >  **[Usage & insights]\(使用状況と分析情報\)** の順に移動します。
1. **[登録]** または **[使用状況]** の概要から、ニーズに応じて事前に情報が絞り込まれたレポートを開くことができます。

![使用状況と分析情報の概要](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

使用状況と分析情報に直接アクセスするには、[https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) に移動します。 このリンクを使うと、登録の概要に移動できます。

[登録済みユーザー]、[有効なユーザー]、[対応ユーザー] のタイルには、ユーザーについて次の登録データが表示されます。

- 登録済み:ユーザー (または管理者) が SSPR または Multi-Factor Authentication に関する組織のポリシーを満たすうえで十分な認証方法を登録していると、"登録済み" であると判定されます。
- 有効:SSPR のポリシーの対象範囲に含まれるユーザーは、"有効" であると判定されます。 あるグループに対して SSPR が有効になっていると、そのグループに所属しているユーザーも "有効" であると判定されます。 ユーザー全員を対象に SSPR が有効になっていると、そのテナントに含まれるユーザー全員 (ゲスト除く) が "有効" であると判定されます。
- 対応:ユーザーが "登録済み" と "有効" のどちらにも該当する場合には、"対応" であると判定されます。 この状態は、そのユーザーが必要に応じて SSPR をいつでも実行できることを意味しています。

これらのタイルまたはその中に表示されている分析情報のいずれかをクリックすると、事前に情報が絞り込まれた、登録の詳細の一覧に移動できます。

**[登録]** タブにある **[登録]** グラフには、認証方法の登録の成功数と失敗数が、認証方法別に表示されます。 **[使用状況]** タブにある **[リセット]** グラフには、パスワード リセット フローの最中に認証が成功した回数と失敗した回数が、認証方法別に表示されます。

いずれかのグラフをクリックすると、事前に情報が絞り込まれた、登録またはリセット イベントの一覧に移動できます。

右上隅にあるコントロールを使うと、登録とリセットのグラフに表示されている監査データの日付範囲を 24 時間、7 日間、または 30 日間に変更できます。

からの登録データ 

### <a name="registration-details"></a>登録の詳細

**[登録済みユーザー]** 、 **[有効なユーザー]** 、または **[対応ユーザー]** のタイルまたは分析情報をクリックすると、登録の詳細に移動します。

登録の詳細レポートには、ユーザーごとに次の情報が表示されます。

- Name
- ユーザー名
- 登録の状態 ([すべて]、[登録済み]、[未登録])
- 有効化の状態 ([すべて]、[有効]、[無効])
- 対応の状態 ([すべて]、[対応]、[非対応])
- 方法 ([アプリ通知]、[アプリ コード]、[音声通話]、[SMS]、[メール]、[セキュリティの質問])

一覧の一番上にあるコントロールを使うと、特定のユーザーを検索したり、表示されている列に基づいてユーザーの一覧を絞り込んだりできます。

### <a name="reset-details"></a>リセットの詳細

登録またはリセットのグラフをクリックすると、リセットの詳細に移動します。

リセットの詳細レポートには、直近 30 日間に発生した登録とリセットのイベントについて、次のような情報が表示されます。

- Name
- ユーザー名
- 機能 ([すべて]、[登録]、[リセット])
- 認証方法 ([アプリ通知]、[アプリ コード]、[音声通話]、[Office call]\(会社の電話\)、[SMS]、[メール]、[セキュリティの質問])
- 状態 ([すべて]、[成功]、[失敗])

一覧の一番上にあるコントロールを使うと、特定のユーザーを検索したり、表示されている列に基づいてユーザーの一覧を絞り込んだりできます。

## <a name="limitations"></a>制限事項

これらのレポートに表示されるデータには、最大 60 分のタイム ラグが存在します。 データがどの程度新しいものであるかを確認できるように、Azure portal には [最終更新日時] フィールドが用意されています。

使用状況と分析情報のデータは、Azure Multi-Factor Authentication のアクティビティ レポートや、Azure AD のサインイン レポートに含まれる情報の代わりとなるものではありません。

## <a name="next-steps"></a>次の手順

- [認証方法の使用状況レポート API を使用する](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [組織の認証方法を選択する](concept-authentication-methods.md)
- [統合された登録エクスペリエンス](concept-registration-mfa-sspr-combined.md)
