---
title: インクルード ファイル
description: インクルード ファイル
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: d1e7fa1ed1649508f0d4923db8817d17ad556ca1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181105"
---
リソースを Azure に展開する場合、展開するリソースの種類、展開先、設定方法を決定するときにとても柔軟に対応できます。 ただし、その柔軟性によって、組織内で許可するオプションよりも多くのオプションを使用できるようになる可能性があります。 Azure にリソースを展開することを検討する場合、次のような懸案事項があります。

* 特定の国/地域でのデータの主権に関する法的要件を満たすにはどうすればよいか
* コストを管理するにはどうすればよいか
* 重要なシステムが誤って変更されないようにするにはどうすればよいか
* リソース コストを追跡し、正確に請求するにはどうすればよいか

この記事ではこのような問題に対応します。 具体的には次のとおりです。

> [!div class="checklist"]
> * ユーザーをロールに割り当て、ロールを範囲に割り当てることで、想定したアクションのみを実行するアクセス許可がユーザーに付与されます。
> * サブスクリプション内のリソースの規則を規定するポリシーを適用します。
> * システムの重要なリソースをロックします。
> * リソースにタグを付けることで、組織にとって意味のある値で追跡することができます。

この記事では、ガバナンスの実装に必要なタスクを中心に説明します。 概念に関する幅広い議論については、「[Azure でのガバナンス](../articles/security/governance-in-azure.md)」を参照してください。 
