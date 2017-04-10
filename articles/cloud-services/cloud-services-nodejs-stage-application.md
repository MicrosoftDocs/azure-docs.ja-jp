---
title: "クラウド サービス デプロイのステージング (Node.js) | Microsoft Docs"
description: "Azure アプリケーションをステージング環境にデプロイしてから、仮想 IP (VIP) スワップを使用して運用環境にデプロイする方法について説明します。"
services: cloud-services
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: d65d26a6-b424-49cd-a88c-7ef46bb112a8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: a015c4a2f5dccb8cae49b739e5d8c342daec54cf


---
# <a name="staging-an-application-in-azure"></a>Azure アプリケーションのステージング
パッケージ アプリケーションは、インターネット上でアクセスできる運用環境に移行する前に、Azure でステージング環境にデプロイしてテストすることができます。 ステージング環境は運用環境とほぼ同じです。唯一の違いは、ステージングされたアプリケーションには、Azure で生成された難読化済みの URL を使用しなければアクセスできない点です。 アプリケーションが正常に動作することを確認したら、仮想 IP (VIP) スワップを実行して、運用環境にデプロイできます。

> [!NOTE]
> この記事で説明する手順は、Azure クラウド サービスとしてホストされるノード アプリケーションにのみ適用されます。
> 
> 

## <a name="step-1-stage-an-application"></a>手順 1. アプリケーションをステージングする
このタスクでは、 **Microsoft Azure PowerShell**を使用してアプリケーションをステージングする方法を説明します。

1. サービスを発行するときに、**Publish-AzureServiceProject** コマンドレットに **-Slot** パラメーターを渡します。
   
   ```powershell
   Publish-AzureServiceProject -Slot staging
   ```
2. [Azure クラシック ポータル] にログオンし、 **[クラウド サービス]**を選択します。 クラウド サービスを作成して、**[ステージング]** 列の状態が **[実行中]** に更新されたら、サービス名をクリックします。
   
   ![実行中のサービスを表示するポータル][cloud-service]
3. **[ダッシュボード]** を選択し、**[ステージング]** を選択します。
   
   ![クラウド サービス ダッシュボード][cloud-service-dashboard]
4. 右側の **[サイトの URL]** エントリの値をメモに記録します。 DNS 名は、Azure で生成されたわかりにくい内部 ID です。
   
    ![[サイトの URL]][cloud-service-staging-url]

これで、ステージング サイトの URL を使用して、アプリケーションがステージング環境で正常に動作していることを確認できます。

## <a name="step-2-upgrade-an-application-in-production-by-swapping-vips"></a>手順 2. VIP スワップにより運用環境でアプリケーションをアップグレードする
ステージング環境でアップグレードされたバージョンのアプリケーションを検証したら、ステージング環境と運用環境の仮想 IP (VIP) をスワップするだけで、そのアプリケーションを運用環境で使用可能にすることができます。

> [!NOTE]
> この手順では、アプリケーションを運用環境にデプロイ済みで、そのアプリケーションのアップグレードされたバージョンをステージング済みであると想定しています。
> 
> 

1. [Azure クラシック ポータル]にログインし、 **[クラウド サービス]** をクリックして、サービス名を選択します。
2. **[ダッシュボード]** で、**[ステージング]** を選択して、ページの下部にある **[スワップ]** をクリックします。 これにより、[VIP のスワップ] ダイアログが開きます。
   
   ![VIP のスワップ ダイアログ][vip-swap-dialog]
3. 情報を確認し、 **[OK]**をクリックします。 ステージング環境のデプロイメントが運用環境に切り替わるとき、および運用環境のデプロイメントがステージングに 切り替わるときに、これら 2 つのデプロイメントで更新が開始されます。

正常にデプロイをステージングし、ステージング環境のデプロイで VIP をスワップすることによって運用環境のデプロイをアップグレードしました。

## <a name="additional-resources"></a>その他のリソース
* [Azure の VIP スワップによりサービス アップグレードを運用環境にデプロイする方法]

[Azure クラシック ポータル]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
[cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
[vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
[Azure の VIP スワップによりサービス アップグレードを運用環境にデプロイする方法]: cloud-services-how-to-manage.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production



<!--HONumber=Feb17_HO2-->


