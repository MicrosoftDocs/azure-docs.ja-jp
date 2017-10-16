---
title: "Azure App Service の IP 制限 | Microsoft Docs"
description: "Azure App Service で IP 制限を使用する方法"
author: btardif
manager: stefsch
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/12/2017
ms.author: byvinyal
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 83cdc42d412f646ddf1ecd1b65bf9aa46983b26b
ms.contentlocale: ja-jp
ms.lasthandoff: 09/14/2017

---
# <a name="azure-app-service-static-ip-restrictions"></a>Azure App Service 静的 IP 制限 #

IP 制限を使用すると、アプリへのアクセスをブロックされている IP アドレスの一覧を定義できます。 ブロック一覧には、個々 の IP アドレスまたはサブネット マスクによって定義された IP アドレスの範囲を含めることができます。

アプリへの要求がクライアントから生成されるとき、ブロック一覧に対して IP アドレスが評価されます。 一覧と一致した場合、アプリは[HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) ステータス コードで応答します。

IP 制限は、アプリに割り当てられた同じ App Service プラン インスタンスで評価されます。

IP 制限の規則をアプリに追加するには、メニューを使って **[ネットワーク]**>**[IP 制限]** の順にクリックし、**[IP 制限の構成]** をクリックします。

![IP 制限] (media/app-service-ip-restrictions/ip-restrictions.png)

ここでは、アプリに対して定義されている IP 制限の一覧を確認できます。

![IP 制限を一覧表示する](media/app-service-ip-restrictions/browse-ip-restrictions.png)

**[[+] 追加]** をクリックすると、新しい IP 制限の規則を追加できます。

![IP 制限を追加する](media/app-service-ip-restrictions/add-ip-restrictions.png)

