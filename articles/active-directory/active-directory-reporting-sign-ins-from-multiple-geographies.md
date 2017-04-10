---
title: "複数の地域からのサインイン"
description: "2 回のサインインが異なるリージョンから実行されたと思われ、サインイン間の時間からユーザーがそれらのリージョン間を移動することは不可能と判断される場合に、そのユーザーを示すレポート。"
services: active-directory
documentationcenter: 
author: SSalahAhmed
manager: gchander
editor: 
ms.assetid: 79259c8a-2388-4747-b41e-c07434ea9a02
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/04/2016
ms.author: saah;kenhoff
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1de57f64692ade442f9ef8d1e3b587ffee35d7cf


---
# <a name="sign-ins-from-multiple-geographies"></a>複数の地域からのサインイン
このレポートには、1 人のユーザーの成功したサインインで、2 回のサインインが異なる地域と時間で行われ、サインインとサインインの間にユーザーが地域から地域へ移動することは不可能であると思われるサインインが含まれます。 考えられる原因は次のとおりです。

* ユーザーが他のユーザーとパスワードを共有している
* ユーザーがリモート デスクトップを使用してサインイン用の Web ブラウザーを起動している
* ハッカーが別の国からユーザーのアカウントにサインインした
* ユーザーが VPN またはプロキシを使用している
* ユーザーが同時に複数のデバイス (デスクトップと携帯電話など) からサインインしており、携帯電話の IP アドレスが異常である

このレポートの結果では、成功したサインイン イベントが、サインインの間隔、サインインが開始されたと思われる地域、および地域間の推定移動時間とともに示されます。 示される移動時間は単なる推定値であり、各場所間の実際の移動時間とは異なる可能性があります。

![複数の地域からのサインイン](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)




<!--HONumber=Nov16_HO3-->


