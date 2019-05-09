---
title: Azure Application Gateway のリダイレクトの概要 | Microsoft Docs
description: Azure Application Gateway のリダイレクト機能について説明します
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2017
ms.author: amsriva
ms.openlocfilehash: d05d509b67fd26c958e0e2fa2bbd877db26e6521
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2019
ms.locfileid: "54232211"
---
# <a name="application-gateway-redirect-overview"></a>Application Gateway のリダイレクトの概要

HTTP を HTTPS に自動的にリダイレクトして、アプリケーションとユーザーの間のすべての通信が暗号化されたパスで行われるようにすることは、多くの Web アプリケーションでよくあるシナリオです。 これまでは、HTTP で受信した要求を HTTPS にリダイレクトすることが唯一の目的である専用のバックエンド プールの作成といった手法が使われていました。  Application Gateway は、Application Gateway 上でトラフィックをリダイレクトする機能をサポートするようになりました。 これにより、アプリケーションの構成が簡単になり、リソースの使用が最適化され、グローバルなリダイレクトやパスに基づくリダイレクトなどの新しいリダイレクト シナリオがサポートされるようになります。 Application Gateway のリダイレクトのサポートは、HTTP から HTTPS へのリダイレクトだけではありません。 これは汎用的なリダイレクト メカニズムであり、Application Gateway のあるリスナーで受信したトラフィックを別のリスナーにリダイレクトできます。 同様に、外部サイトへのリダイレクトもサポートします。 Application Gateway のリダイレクトのサポートでは、次の機能が提供されます。

1. Gateway 上のあるリスナーから別のリスナーへのグローバルなリダイレクト。 これにより、サイトでの HTTP から HTTPS へのリダイレクトが可能になります。
2. パスに基づくリダイレクト。 このタイプのリダイレクトを使うと、特定のサイト領域でのみ HTTP から HTTPS へのリダイレクトが可能になります (たとえば、/cart/* で示されたショッピング カート領域など)。
3. 外部サイトへのリダイレクト。

![リダイレクト](./media/application-gateway-redirect-overview/redirect.png)

この変更により、お客様は、リダイレクト先のターゲット リスナーまたは外部サイトを指定する新しいリダイレクト構成オブジェクトを作成する必要があります。 構成要素は、リダイレクトされる URL に URI パスとクエリ文字列を追加できるオプションもサポートしています。 お客様は、リダイレクトが一時的か (HTTP 状態コード 302) または永続的なリダイレクトか (HTTP 状態コード 301) を選ぶこともできます。 作成したこのリダイレクト構成は、新しいルールによってソース リスナーに関連付けられます。 基本的なルールを使うと、リダイレクト構成はソース リスナーに関連付けられて、グローバル リダイレクトになります。 パスに基づくルールを使うと、リダイレクト構成は URL パス マップ上で定義され、したがってサイトの特定のパス領域にのみ適用されます。

### <a name="next-steps"></a>次の手順

[アプリケーション ゲートウェイで HTTP から HTTPS へのリダイレクトを構成する](redirect-http-to-https-portal.md)
