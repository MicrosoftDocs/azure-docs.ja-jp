---
title: "アプリケーション プロキシ アプリケーションでアプリケーション ページが正しく表示されない | Microsoft Docs"
description: "Azure AD と統合されたアプリケーション プロキシ アプリケーションで、ページが正しく表示されない場合のガイダンス"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: d0b0ddba5ad9f8c584cd35c6f20edf76883b425d
ms.contentlocale: ja-jp
ms.lasthandoff: 04/18/2017

---

# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>アプリケーション プロキシ アプリケーションでアプリケーション ページが正しく表示されない

この記事は、ページに移動したときにページの一部が正しく表示されない場合に、Azure Active Directory アプリケーション プロキシ アプリケーションに関する問題をトラブルシューティングするのに役立ちます。

## <a name="overview"></a>概要
アプリケーション プロキシ アプリを発行する場合、アプリケーションにアクセスするときに、ルートの下にあるページのみがアクセス可能です。 ページが正しく表示されない場合、アプリケーションで使用するルートの内部 URL で、ページ リソースの一部が見つからない可能性があります。 この問題を解決するには、ページの*すべて*のリソースがアプリケーションの一部として発行されていることを確認します。

ネットワーク トラッカー (Fiddler や Internet Explorer/Edge で F12 キーを押すなどのツール) を開き、ページを読み込み、404 エラーを見つけることにより、この問題を確認できます。 これは、現在見つけることができず、引き続き発行する必要があるページを示します。

この場合の例として、内部 URL <http://myapps/expenses> を使用して経費アプリケーションを発行したが、アプリは、スタイル シート <http://myapps/style.css> を使用していると仮定します。 この場合、スタイル シートがアプリケーションでは発行されていないため、経費アプリケーションが style.css を読み込もうとするときに 404 エラーをスローします。 この例では、内部 URL <http://myapp/> を代わり使用してアプリケーションを発行することにより、問題が解決される場合があります。

## <a name="problems-with-publishing-as-one-application"></a>1 つのアプリケーションとしての発行による問題

同じアプリケーション内のすべてのリソースを公開しない場合は、複数のアプリケーションを発行し、それらのアプリケーション間のリンクを有効にする必要があります。

その場合は、[カスタム ドメイン](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) ソリューションを使用することをお勧めします。 ただし、このソリューションでは、ドメイン用の証明書を所有し、アプリケーションが完全修飾ドメイン名 (FQQN) を使用する必要があります。 その他のオプションについては、「[壊れたリンクのトラブルシューティングに関するドキュメント](https://microsoft-my.sharepoint.com/personal/harshja_microsoft_com/_layouts/15/guestaccess.aspx?guestaccesstoken=IxuG3mFVbnPWI3Yn4Qi7wCNi8VIfHS5mwPt5quh8DMw%3d&docid=2_14558cd6ddea34c1c9887dc640feb5831&rev=1)」を参照してください。

## <a name="next-steps"></a>次のステップ
[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](application-proxy-publish-azure-portal.md)

