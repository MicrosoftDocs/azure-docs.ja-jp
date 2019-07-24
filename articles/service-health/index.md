---
title: Azure Service Health | Microsoft Docs
description: Azure Service Health は、Azure サービスの問題による影響を受けた場合にパーソナライズされたガイダンスとサポートを提供するエクスペリエンスのスイートです。
author: stephbaron
layout: LandingPage
ms.service: service-health
ms.topic: landing-page
ms.date: 03/4/2019
ms.author: stbaron
ms.openlocfilehash: cf7084b569e2c90337e475beb9f49563214941d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2019
ms.locfileid: "60506938"
---
# <a name="azure-service-health-documentation"></a>Azure Service Health のドキュメント

Azure では、現在および将来の問題 (サービスに影響を及ぼすイベントなど)、計画メンテナンス、その他可用性に影響する可能性のある変更も含め、クラウド リソースの正常性に関する最新情報を随時お届けするエクスペリエンスのスイートを提供しています。

お客様のなかには、**[「Azure の状態」ページ](https://status.azure.com)** をご利用の方もいらっしゃるかもしれません。このページは、Azure の全リージョンを対象にあらゆる Azure サービスの正常性を一覧で確認できるというものです。 状態ページは、影響が広範囲に及ぶインシデントの有無を調べる際には良い参考になります。しかし、Azure をご利用中のお客様には、Azure 関連のインシデントとメンテナンスに関する最新情報を随時入手できるよう、**Azure Service Health** の利用を強くお勧めします。

**[Azure Service Health](service-health-overview.md)** では、ご利用中の Azure サービスとリージョンの正常性について、個々のお客様に応じたビューを提供します。 サービスに影響を及ぼす類の停止に関するお知らせ、計画メンテナンス活動、その他正常性に関する勧告の有無を確認する場所としては、認証を経て利用するものであって、お客様が利用中のサービスとリソースに応じて表示が変化する Azure Service Health のエクスペリエンスが最適です。 Service Health の最善の利用方法は、Service Health アラートを設定し、サービスの問題、計画メンテナンス、その他の変更がご利用中の Azure サービスとリージョンに影響を及ぼすおそれがある場合に、お好きな通信チャネルを使って通知を受け取れるようにすることです。

**[Azure Resource Health](resource-health-overview.md)** では、特定の仮想マシン インスタンスなど、個々のクラウド リソースの正常性に関する情報を入手できます。 さらに、Azure Monitor を使えば、クラウド リソースの可用性が変動したときに通知するアラートを構成することもできます。 Azure Resource Health と Azure Monitor による通知を組み合わせれば、リソースの可用性を分単位で把握すると共に、問題がお客様側の原因によるものか、Azure プラットフォームのイベントに関連するものかをすばやく評価できるようになります。

ここに挙げたエクスペリエンスが一体となって、お客様にとって最も意味のある精度で Azure の正常性を包括的に確認できます。

<ul class="panelContent cardsFTitle">
    <li>
        <a href="/azure/service-health/azure-status-overview">
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="media/index/GlobeSuccess.svg" alt="" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Azure の状態に関する詳細情報</h3>
                    </div>
                </div>
            </div>
        </div>
        </a>
    </li>
    <li>
        <a href="/azure/service-health/service-health-overview">
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="media/index/resource-health.svg" alt="" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Service Health に関する詳細情報</h3>
                    </div>
                </div>
            </div>
        </div>
        </a>
    </li>
    <li>
        <a href="/azure/service-health/resource-health-overview">
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="media/index/ResourceDefault.svg" alt="" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Resource Health に関する詳細情報</h3>
                    </div>
                </div>
            </div>
        </div>
        </a>
    </li>
    <li>
        <a href="https://azure.microsoft.com/resources/videos/announcing-azure-service-health/">
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="media/index/video-library.svg" alt="" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Service Health に関する概要ビデオを見る</h3>
                    </div>
                </div>
            </div>
        </div>
        </a>
    </li>
    <li>
        <a href="/azure/monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications?toc=%2fazure%2fresource-health%2ftoc.json">
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="media/index/article.svg" alt="" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>影響のある Azure サービスの問題が次に発生したときに通知を受ける</h3>
                    </div>
                </div>
            </div>
        </div>
        </a>
    </li>
    <li>
        <a href="./resource-health-alert-arm-template-guide.md">
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="media/index/article.svg" alt="" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Azure リソースが次回使用不可になったときに通知を受ける</h3>
                    </div>
                </div>
            </div>
        </div>
        </a>
    </li>
</ul>

---

