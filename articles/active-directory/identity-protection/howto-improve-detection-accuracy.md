---
title: Azure Active Directory Identity Protection (更新版) での検出精度を向上させる方法 | Microsoft Docs
description: Azure Active Directory Identity Protection (更新版) での検出精度を向上させる方法。
services: active-directory
keywords: Azure Active Directory Identity Protection, Cloud App Discovery, アプリケーションの管理, セキュリティ, リスク, リスク レベル, 脆弱性, セキュリティ ポリシー
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7724d69a9294b420ca061d5ad26ad64826372203
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517676"
---
# <a name="how-to-improve-the-detection-accuracy"></a>方法:検出精度を向上させる 

Identity Protection では、Azure AD に環境内でのリスク検出についてフィードバックするメカニズムが提供されます。 フィードバックを提供するために、検出された危険なユーザーまたはサインイン イベントの状態を確認できます。 Microsoft ではこのフィードバックを使用して、現在のリスク検出に対してアクションを実行し、今後の検出の精度を向上させます。 


## <a name="what-is-detection"></a>検出とは

検出は、ユーザー アカウントを組み合わせて使用する疑わしいアクティビティを識別するプロセスです。 Azure AD で検出できる疑わしいアクティビティは、[リスク イベント](../reports-monitoring/concept-risk-events.md)と呼ばれます。 検出プロセスは、ユーザーの危険なイベントを検出するためのアダプティブ機械学習アルゴリズムとヒューリスティックに基づいています。

検出結果は、ユーザーとサインインが危険な状態かどうかを判断するために使用されます。 


## <a name="how-can-i-improve-the-detection-accuracy"></a>検出精度を向上させるにはどうすればよいか

検出は自動化されたプロセスであるため、Azure AD で誤検知が報告される可能性があります。 検出結果について、Azure AD にフィードバックを提供することで、検出精度を向上させることができます。

検出精度を向上させるための 3 つの方法 (侵害されたサインインの確認、安全なサインインの確認、ユーザー リスクの無視) があります。 これは次のレポートで行うことができます。

- **危険なサインイン レポート -** 危険なサインイン レポートでは、サインインが安全であるか、侵害されているかを確認できます

- **危険なユーザー レポート -** 危険なユーザー レポートでは、ユーザー リスクを無視できます 

お客様のフィードバックは、検出結果の精度を向上させるために Azure AD によって処理されます。 通常、フィードバックは、ユーザー リスクまたはサインイン リスクの調査の一部として提供します。 詳細については、[危険なユーザーとサインインの調査方法](howto-investigate-risky-users-signins.md)に関するページを参照してください。


## <a name="confirm-compromised"></a>侵害ありと確認する

サインイン イベントを侵害ありと確認することで、サインインが ID 所有者によって承認されなかったことを Azure AD に通知します。 "侵害ありと確認する" を選択した場合、Azure AD では次のことを行います。

- 影響を受けたユーザーのユーザー リスクを高に上げる。

- リスク イベントを検出する機械学習を最適化できるようにする
 
- 組織をさらに保護するその他の対策を実行する



侵害されたサインインを確認するには、次のようにします。

- **危険なサインイン レポート** - このオプションでは、1 つ以上のサインイン イベントについて、侵害されたサインインを確認できます。

    ![ユーザー リスクを無視する](./media/howto-improve-detection-accuracy/07.png)

- **危険なサインイン レポートの [詳細] ビュー** - このオプションでは、危険なサインイン レポートで選択されたサインイン イベントについて、侵害されたアカウントを確認することができます。 

    ![ユーザー リスクを無視する](./media/howto-improve-detection-accuracy/04.png)


 
## <a name="confirm-safe"></a>安全と確認する


サインイン イベントを安全と確認することで、サインインがそれぞれの ID 所有者によって承認**された**ことを Azure AD に通知します。 "安全と確認する" を選択した場合、Azure AD では次のことを行います。

- 選択されたサインインのユーザー リスクの影響を元に戻す

- 基になるリスク イベントを閉じる

- リスク イベントを検出する機械学習を最適化できるようにする

- 組織をさらに保護するその他の対策を実行する
 

安全なサインインを確認するには、次のようにします。

- **危険なサインイン レポート** - このオプションでは、1 つ以上のサインイン イベントについて、安全なサインインを確認できます。

    ![ユーザー リスクを無視する](./media/howto-improve-detection-accuracy/08.png)

- **危険なサインイン レポートの [詳細] ビュー** - このオプションでは、危険なサインイン レポートで選択されたサインイン イベントについて、安全なサインインを確認することができます。 

    ![ユーザー リスクを無視する](./media/howto-improve-detection-accuracy/05.png)




## <a name="dismiss-user-risk"></a>ユーザー リスクを無視する

既にリスク ユーザーの修復アクションを実行している場合や、誤って危険としてマークされたと思われる場合は、ユーザーのリスクを無視できます。 ユーザーのリスクを無視することで、ユーザーは危険ではない状態に復元されます。 選択されたユーザーの過去の危険なサインインとリスク イベントはすべて無視されます。


報告されたユーザー リスクは、次のようにして無視することができます。

- **危険なユーザー レポート** - このオプションでは、選択された 1 人以上のユーザーのユーザー リスクを無視できます。

    ![ユーザー リスクを無視する](./media/howto-improve-detection-accuracy/02.png)

- **[詳細] ビュー** - このオプションでは、ユーザー リスク レポートで選択されたユーザーのユーザー リスクを無視することができます。 

    ![ユーザー リスクを無視する](./media/howto-improve-detection-accuracy/01.png)


**知っておくべきこと**

- このアクションを元に戻すことはできません。

- このアクションが完了するまで数分かかることがあるため、要求を再送信しないでください。

- AD でユーザーの資格情報が管理されている場合にのみ、このアクションを実行できます。 



## <a name="best-practices"></a>ベスト プラクティス

ユーザー リスク ポリシーによってブロックされており、パスワードのリセットや MFA が有効になっていないために自分で修正できない場合に、ブロックを解除する 1 つの方法はユーザーのリスクを無視することです。 この場合、ユーザーがパスワードのリセットと MFA に登録し、今後のリスク イベントを自分で修正できるようにすることをお勧めします。


## <a name="next-steps"></a>次の手順

Azure AD Identity Protection の概要を入手するには、[Azure AD Identity Protection の概要](overview-v2.md)に関するページを参照してください。


