---
title: "Azure Application Gateway のリダイレクトの概要 | Microsoft Docs"
description: "Azure Application Gateway のリダイレクト機能について説明します"
services: application-gateway
documentationcenter: na
author: amsriva
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2017
ms.author: amsriva
ms.openlocfilehash: ea9ae8373ff67bf9557b06bbc8a4b0d82a03e2d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="application-gateway-redirect-overview"></a>Application Gateway のリダイレクトの概要

HTTP を HTTPS に自動的にリダイレクトして、アプリケーションとユーザーの間のすべての通信が暗号化されたパスで行われるようにすることは、多くの Web アプリケーションでよくあるシナリオです。 これまでは、HTTP で受信した要求を HTTPS にリダイレクトすることが唯一の目的である専用のバックエンド プールの作成といった手法が使われていました。  Application Gateway は、Application Gateway 上でトラフィックをリダイレクトする機能をサポートするようになりました。 これにより、アプリケーションの構成が簡単になり、リソースの使用が最適化され、グローバルなリダイレクトやパスに基づくリダイレクトなどの新しいリダイレクト シナリオがサポートされるようになります。 Application Gateway のリダイレクトのサポートは、HTTP から HTTPS へのリダイレクトだけではありません。 これは汎用的なリダイレクト メカニズムであり、Application Gateway のあるリスナーで受信したトラフィックを別のリスナーにリダイレクトできます。 同様に、外部サイトへのリダイレクトもサポートします。 Application Gateway のリダイレクトのサポートでは、次の機能が提供されます。

1. Gateway 上のあるリスナーから別のリスナーへのグローバルなリダイレクト。 これにより、サイトでの HTTP から HTTPS へのリダイレクトが可能になります。
2. パスに基づくリダイレクト。 このタイプのリダイレクトを使うと、特定のサイト領域でのみ HTTP から HTTPS へのリダイレクトが可能になります (たとえば、/cart/* で示されたショッピング カート領域など)。
3. 外部サイトへのリダイレクト。

![リダイレクト](./media/application-gateway-redirect-overview/redirect.png)

この変更により、お客様は、リダイレクト先のターゲット リスナーまたは外部サイトを指定する新しいリダイレクト構成オブジェクトを作成する必要があります。 構成要素は、リダイレクトされる URL に URI パスとクエリ文字列を追加できるオプションもサポートしています。 お客様は、リダイレクトが一時的か (HTTP 状態コード 302) または永続的なリダイレクトか (HTTP 状態コード 301) を選ぶこともできます。 作成したこのリダイレクト構成は、新しいルールによってソース リスナーに関連付けられます。 基本的なルールを使うと、リダイレクト構成はソース リスナーに関連付けられて、グローバル リダイレクトになります。 パスに基づくルールを使うと、リダイレクト構成は URL パス マップ上で定義され、したがってサイトの特定のパス領域にのみ適用されます。

### <a name="next-steps"></a>次のステップ

[アプリケーション ゲートウェイに URL リダイレクトを構成する](application-gateway-configure-redirect-powershell.md)
