---
title: Azure API for FHIR のクロスオリジン リソース共有の構成
description: この記事では、Azure API for FHIR でのクロスオリジン リソース共有 (CORS) の構成方法について説明します。
author: matjazl
ms.author: matjazl
ms.date: 3/11/2019
ms.topic: reference
ms.service: healthcare-apis
ms.openlocfilehash: 4976ce5b8f0216e1a0978c7ad2225bf94e6af3cb
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58408478"
---
# <a name="configure-cross-origin-resource-sharing"></a>クロスオリジン リソース共有の構成

Azure API for FHIR は[クロス オリジン リソース共有 (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing) をサポートしており、あるドメインのアプリケーション (オリジン) に別のドメインからアクセスできる (ドメイン間要求) ように、設定を構成することができます。

CORS は、ほとんどの場合、別のドメインへの RESTful API 呼び出しを呼び出す必要のあるシングル ページ アプリで使用されます。

Azure API for FHIR で CORS 設定を構成するには、CORS 設定に移動します。 

ここでは次の設定を指定できます。

**[オリジン] (Access-Control-Allow-Origin):** Azure API for FHIR へのクロスオリジン要求が許可されているドメインの一覧。 各ドメイン (オリジン) は別々の行に入力する必要があります。 '*' を入力すると、ドメインからの呼び出しを許可することができます (これはセキュリティ上のリスクを示すため、推奨されません)。

**[ヘッダー] (Access-Control-Allow-Headers):** オリジン要求に含められるヘッダーの一覧を指定できます。 '*' に設定すると、すべてのヘッダーを許可することができます。 

**[メソッド] \(Access-Control-Allow-Methods):** API 呼び出しで許可されている許可されたメソッド (PUT、GET、POST、...) を選択できます。 すべてのメソッドに対して **[すべて選択]** を選択することもできます。

**[最長有効期間] \(Access-Control-Max-Age):****Access-Control-Allow-Methods** および **Access-Control-Allow-Headers** に対するプリフライト要求の結果をキャッシュするための値 (秒数)。 

**[Allow Credentials](資格情報の許可) (Access-Control-Allow-Credentials):** CORS 要求には、[クロスサイト リクエスト フォージェリ (CSRF)](https://en.wikipedia.org/wiki/Cross-site_request_forgery) 攻撃を防ぐために、通常 Cookie は含まれていません。 選択した場合、Cookie などの資格情報を使用して要求が行われるか、要求に資格情報が含められます。 [オリジン] を '*' に設定した場合、この設定はできません

![](media/cors/cors.png)

別のドメインの [オリジン] に異なる設定を指定することはできません。 すべての設定 (ヘッダー、メソッド、最長有効期間、および資格情報の許可) は、[オリジン] の設定に指定されたすべてのオリジンに適用されます。
