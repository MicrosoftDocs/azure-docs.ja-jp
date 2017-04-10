---
title: "複数のエラー後のサインイン"
description: "サインイン試行に何度も連続して失敗した後に正常にサインインしたユーザーを示すレポート。"
services: active-directory
documentationcenter: 
author: SSalahAhmed
manager: femila
editor: 
ms.assetid: e4ec1a39-9c20-418f-8a75-6497d0117176
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/04/2016
ms.author: saah;kenhoff
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e55e0145adbdb1f41a8b8753d5555f20e96bf161


---
# <a name="sign-ins-after-multiple-failures"></a>複数のエラー後のサインイン
このレポートは、サインイン試行に何度も連続して失敗した後に、正常にサインインしたユーザーを示します。 考えられる原因は次のとおりです。

* ユーザーが自分のパスワードを忘れた</li><li>ユーザーが、正しいパスワードを推測するブルート フォース攻撃の対象になっている

このレポートの結果では、正常なサインインよりも前に行われたサインイン試行の連続失敗回数と、最初に成功したサインインに関連するタイムスタンプを確認できます。

**レポートの設定**: レポートに表示されるまでに行われる必要がある、サインイン試行の連続失敗回数の最小値を構成できます。 この設定を変更する場合、その変更は、既存のレポートに現在表示されている失敗した既存のサインインには適用されないことに注意してください。 変更は、すべての今後のサインインに適用されます。 このレポートの変更は、ライセンスを付与された管理者がのみ行うことができます。

![複数のエラー後のサインイン](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)




<!--HONumber=Nov16_HO3-->


