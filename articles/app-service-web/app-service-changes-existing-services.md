---
title: "Azure App Service と、それが既存の Azure サービスに与える影響"
description: "新しい Azure App Service とその機能が Azure の既存のサービスにどのように影響するかを説明します。"
services: app-service
documentationcenter: 
author: yochay
manager: nirma
editor: 
ms.assetid: 86c6a292-3c33-49f4-890c-89cc0321b397
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2016
ms.author: yochaykk
translationtype: Human Translation
ms.sourcegitcommit: 75d42f41e6218d83cda00f2ef8926d6ca1f0aacd
ms.openlocfilehash: 8f458884d0ea649dffa5f92fd459bbaaa9f5e4cf


---
# <a name="azure-app-service-and-existing-azure-services"></a>Azure App Service と既存の Azure サービス
この記事では、複数の Azure サービスを新しい統合型サービスの [Azure App Service](https://azure.microsoft.com/services/app-service/)に統合するための変更の一環である、既存の Azure サービスへの変更について概要を説明します。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="overview"></a>概要
[Azure App Service](https://azure.microsoft.com/services/app-service/) は新しくユニークなクラウド サービスであり、開発者がこのサービスを利用することで、すべてのプラットフォームおよびデバイス向けに、Web アプリやモバイル アプリを作成できるようになります。 App Service は、繰り返しのコーディング機能の効率化、エンタープライズ システムと SaaS システムの統合、ビジネス プロセスの自動化などを実現しながら、安全性、信頼性、拡張性のニーズに応えられるよう設計された、統合型ソリューションです。

App Service では、[Websites](https://azure.microsoft.com/services/websites/)、[Mobile Services](https://azure.microsoft.com/services/mobile-services/)、[Biztalk Services](https://azure.microsoft.com/services/biztalk-services/) などの既存の Azure サービスを 1 つの複合サービスに統合すると同時に、パワフルな新機能も追加しています。  App Service では、次の種類のアプリをホストすることができます。

* Web Apps
* Mobile Apps
* API Apps
* Logic Apps

次の表では、既存の Azure サービスが、App Service とその中で利用できるアプリの種類に対して、どのような位置付けになっているかを説明します。

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">既存の Azure サービス</th>
<th align="left", style="width:10%">Azure App Service</th>
<th align="left", style="width:80%">変更箇所</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Azure Websites</td>
<td align="left">Web Apps</td>
<td align="left"><li>Azure Websites の場合は、App Service による変更は Websites から Web Apps への名前の変更のみに厳密に制限されています。
<p><li>Websites のすべての既存インスタンスが、現在は App Service 内の Web Apps になっています。</p>
<p><li><a href="http://go.microsoft.com/fwlink/?LinkId=529715">Azure Portal</a> から既存の Web サイトにアクセスすることができます。すべての既存サイトがこのポータルの <em>Web Apps</em> の下に表示されます。</p>
<p><li><em>Web ホスティング プラン</em>は現在の "<em>App Service プラン</em>" にあたります。 "<em>App Service プラン</em>" では、Web、Mobile、Logic、API など、App Service のすべての種類のアプリをホストできます。</p>
<p><li>Azure App Service Web Apps は、一般向けに提供されています。</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/web/">Web Apps の詳細についてはこちら</a>を参照してください。</p></td>
</tr>
<tr class="even">
<td align="left">Azure Mobile Services</td>
<td align="left">Mobile Apps</td>
<td align="left"><p><li>Mobile Services はスタンドアロン サービスとして引き続き利用でき、現在も完全なサポートが受けられます。</p>
<p><li>Mobile Apps は App Service のアプリの種類の 1 つで、Mobile Services のすべての機能を含む、多数の機能を統合しています。</p>
<p><li><a href="http://go.microsoft.com/fwlink/?LinkID=724279&clcid=0x409">Mobile Services から Mobile Apps への移行</a>は簡単です。</p>
<p><li>App Service の一部として、Mobile Apps には、オンプレミス システムと SaaS システムとの統合、ステージング スロット、Web ジョブ、充実したスケール オプションなど、Mobile Services を超える新しい機能が追加されました。</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/mobile/">Mobile Apps の詳細についてはこちら</a>を参照してください。</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">API Apps</td>
<td align="left">
<p><li>API Apps は App Service での新しいアプリの種類で、これを利用すると、クラウドで簡単に API の構築や使用ができます。</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/api/">API Apps の詳細についてはこちら</a>を参照してください。</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Logic Apps</td>
<td align="left">
<p><li>Logic Apps は App Service での新しいアプリの種類で、これを利用すると、ビジネス プロセスが簡単に自動化できます。</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/logic/">Logic Apps の詳細についてはこちら</a>を参照してください。</p></td>
</tr>
<tr class="odd">
<td align="left">Azure BizTalk Services</td>
<td align="left">BizTalk API Apps</td>
<td align="left">
<li><p>BizTalk Services はスタンドアロン サービスとして引き続き利用でき、現在も完全なサポートが受けられます。</p>
<li><p>BizTalk Services のすべての機能は、API Apps として App Service に統合されています。これを利用することで、ユーザーは App Service 内のすべての種類のアプリによる企業アプリケーション統合および B2B 統合のシナリオが実現できるようになります。</p>
<li><p>Logic Apps では、視覚的デザイン機能を使用してワークフローを作成し、ビジネス プロセスを自動化できるようになりました。</p></td>
</tr>
</tbody>
</table>

詳細については、「 [App Service のドキュメント](https://azure.microsoft.com/documentation/services/app-service/)」を参照してください。




<!--HONumber=Dec16_HO1-->


