---
title: "Web アプリ リソースを他のリソース グループに移動する"
description: "Web アプリおよびアプリ サービスを一つのリソース グループから他のリソース グループに移動するシナリオについて記述しています。"
services: app-service
documentationcenter: 
author: ZainRizvi
manager: erikre
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: zarizvi
translationtype: Human Translation
ms.sourcegitcommit: 078833a21cd309087b842e21e3fc730695c254dd
ms.openlocfilehash: 89fdeca4ffc1a36e8aff829b00483f208fa03e0f
ms.lasthandoff: 12/21/2016


---
# <a name="supported-move-configurations"></a>サポートされている移動の構成
Azure Web アプリ リソースを [Resource Manager Move Resources API](../azure-resource-manager/resource-group-move-resources.md) を使用して移動できます。

現在、Azure の Web アプリでは次の移動のシナリオがサポートされています。

* リソース グループの内容全体 (Web アプリ、アプリのサービス プラン、および証明書) を別のリソース グループに移動します。 
   > [!Note]
   > このシナリオでは、変換先のリソース グループにすべての Microsoft.Web リソースを含めることはできません。

* 個々の Web アプリを異なるリソース グループに移動します。Web アプリは引き続き現在のアプリのサービス プランでホストされます (アプリのサービス プランは元のリソース グループにとどまります)。



