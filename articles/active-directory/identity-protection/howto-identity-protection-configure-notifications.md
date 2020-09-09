---
title: Azure Active Directory Identity Protection の通知
description: 通知が調査作業にどのように役立つのかを説明します。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 291afbdd902c7563e47595132d56b354dab28a3a
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950393"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection の通知

Azure AD Identity Protection では、ユーザーのリスクとリスク検出の管理に役立つ 2 種類の自動通知電子メールが送信されます。

- 危険な状態のユーザーが検出された電子メール
- 週間ダイジェスト電子メール

この記事では、両方の通知メールの概要を説明します。

## <a name="users-at-risk-detected-email"></a>危険な状態のユーザーが検出された電子メール

危険な状態のアカウントが検出されると、Azure AD Identity Protection は **[Users at risk detected]\(危険な状態のユーザーが検出されました\)** という件名のアラート メールを生成します。 このメールには、 **[リスクのフラグ付きユーザー](./overview-identity-protection.md)** レポートへのリンクが含まれます。 ベスト プラクティスとして、危険な状態のユーザーをすぐに調べる必要があります。

このアラートを構成すると、アラートを生成するユーザー リスク レベルを指定できます。 ユーザーのリスク レベルが、指定されたものに達するとメールが生成されます。 たとえば、ユーザー リスクが中になったら警告を出すようにポリシーを設定していて、ユーザー John のユーザー リスクのスコアが、リアルタイム サインイン リスクが原因で中リスクに移行した場合は、危険な状態のユーザーが検出されたというメールを受信します。 このユーザーに対して後続のリスク検出が行われ、それによってユーザー リスク レベルの計算が、指定されたリスク レベル (またはそれ以上) になった場合は、ユーザー リスク スコアが再計算されたときに、危険な状態のユーザーが検出されたという追加のメールを受け取ります。 たとえば、ユーザーが 1 月 1 日に中リスクに移行すると、中リスクのときに警告を出すように設定されている場合は、メール通知を受け取ります。 次に、同じユーザーに対して 1 月 5 日に新たなリスク検出が行われて、それも中リスクであり、ユーザー リスク スコアが再計算され、それでもまだ中だった場合は、新たなメール通知を受け取ります。 

ただし、追加のメール通知が送信されるのは、(ユーザー リスク レベルの変更の原因となった) リスク検出の発生時刻が、最後のメールが送信されたときよりも最近である場合だけです。 たとえば、1 月 1 日の午前 5 時にユーザーがサインインし、リアルタイム リスクはありません (つまり、そのサインインが原因でメールが生成されることはありません)。 10 分後の午前 5:10 に、同じユーザーがもう一度サインインし、リアルタイム リスクが高くなり、それによってユーザー リスク レベルが高に移行し、メールが送信されます。 その後、午前 5:15 に、午前 5 時に行われた元のサインインのオフライン リスク スコアが、オフライン リスク処理により高リスクに変わります。 最初のサインインの時刻は、既にメール通知をトリガーした 2 回目のサインインより前だったので、ユーザーにリスクのフラグが立てられたという追加メールは送信されません。

メールのオーバーロードを防ぐために、危険な状態のユーザーが検出されたというメールを受信するのは、5 秒間の期間内に 1 通のみとなります。 つまり、同じ 5 秒間の期間中に複数のユーザーが、指定されたリスク レベルに移行した場合は、その全員に対するリスク レベルの変化を示す 1 通のメールを集計して送信します。

![危険な状態のユーザーが検出された電子メール](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>"リスクのあるユーザーが検出された警告" を構成する

管理者は以下を設定することができます。

- **このメールの生成をトリガーするユーザー リスク レベル** - 既定では、リスク レベルは "高" リスクに設定されます。
- **このメールの受信者** - 既定では、受信者にはすべてのグローバル管理者が含まれます。 グローバル管理者は、受信者として他のグローバル管理者、セキュリティ管理者、セキュリティ閲覧者を追加することもできます。
   - 必要に応じて、**アラート通知を受信する電子メールをさらに追加**することができます。この機能はプレビューであり、定義されているユーザーがリンク レポートを Azure portal で表示するには適切なアクセス許可が必要です。

"危険な状態のユーザー" メールは、**Azure portal** の **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[Identity Protection]**  >  **[リスクのあるユーザーが検出された警告]** で構成します。

## <a name="weekly-digest-email"></a>週間ダイジェスト電子メール

週間ダイジェスト電子メールには、新しいリスク検出の概要が含まれます。  
次の情報が含まれます。

- 新しい危険なユーザーが検出されました
- 新しい危険なサインインが検出されました (リアルタイムで)
- Identity Protection の関連するレポートへのリンク

![週間ダイジェスト電子メール](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

既定では、受信者にはすべてのグローバル管理者が含まれます。 グローバル管理者は、受信者として他のグローバル管理者、セキュリティ管理者、セキュリティ閲覧者を追加することもできます。

### <a name="configure-weekly-digest-email"></a>週刊ダイジェストの電子メールを構成する

管理者は、週刊ダイジェストの電子メールの送信を有効にしたり無効にしたりできるほか、メールの受信者として割り当てるユーザーを選択することができます。

週刊ダイジェストの電子メールは、**Azure portal** の **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[Identity Protection]**  >  **[週刊ダイジェスト]** で構成します。

## <a name="see-also"></a>関連項目

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)