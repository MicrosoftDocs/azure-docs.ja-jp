---
title: "Azure Stack 上の App Service で FTP を有効にする | Microsoft Docs"
description: "Azure Stack 上の App Service で FTP を有効にするために完了する手順"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/6/2017
ms.author: erikje
ms.openlocfilehash: 9cadc57831ac7f7e5d32b10a4a87dab3fac02958
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="enable-ftp-in-app-service-on-azure-stack"></a>Azure Stack 上の App Service で FTP を有効にする

Azure Stack 上の App Service を正常にデプロイした後、テナントがアプリケーション ファイルとコンテンツをアップロードできるように FTP 発行を有効にする場合は、追加の手順を完了する必要があります。  今後のリリースで、これらの手順は自動化されます。

> [!NOTE]
> これらの手順を実行するのは、Azure Stack 上の App Service のリソース プロバイダーを構成するサービスまたはエンタープライズ管理者です。

## <a name="enable-ftp"></a>FTP を有効にする

1.  Azure Stack ポータルにサービス管理者としてログインします。
2.  **[ネットワーク インターフェイス]** に移動し、**[リソース グループ]** - **[AppService-ローカル]** で **[FTP-NIC]** を選択します。 ![Azure Stack ネットワーク インターフェイス][1]
3.  **[FTP NIC]** の **[パブリック IP アドレス]** を書き留めます。 
![Azure Stack ネットワーク インターフェイスの詳細][2]
4.  次に、**[仮想マシン]** に移動し、**[FTP0-VM]** を選択します。 ![Azure Stack の仮想マシン][3]
5.  VM へのリモート デスクトップ セッションを **[接続]** ボタンを使用して開き、App Service のデプロイ中に設定した管理者資格情報を使用して、セッションにログインします。  
![Azure Stack の仮想マシンの詳細][4]
6.  FTP VM (FTP0 VM) で**インターネット インフォメーション サービス (IIS) マネージャー**を開きます。
7.  **[サイト]** で、**[Hosting FTP Site (ホストFTP サイト)]** を選択します。
8.  **[FTP ファイアウォールのサポート]** を開きます。 ![App Service FTP0 VM の IIS マネージャー][5]
9.  FTP-NIC のパブリック IP アドレスを入力し、**[適用]** をクリックします。![IIS マネージャーの FTP ファイアウォール サポート][6]

## <a name="validate-the-enabling-of-ftp"></a>FTP の有効化を検証する

1.  Azure Stack ポータルに、サービス管理者またはテナントとしてログインします。
2.  **[App Services]** に移動し、作成した Web、モバイル、または API アプリを選択します。 ![App Services][7]
3.  アプリケーション詳細の中の **[FTP ホスト名]** と **[FTP/デプロイ ユーザー名]** を書き留めます。 ![App Service アプリの詳細][8]
> [!NOTE]
> **[FTP/デプロイ ユーザー名]** にエントリが表示されない場合は、先に **[デプロイ資格情報]** ブレードでデプロイ証明書を設定する必要があります。

4.  Windows エクスプローラーを開き、ファイル アドレス バーに FTP ホスト名を入力します (ftp://ftp.appservice.azurestack.local)。
5.  入力を求められたら、手順 3 で書き留めておいた**デプロイ資格情報**を入力します。機能が有効になっていれば、App Service アプリケーションのコンテンツを示すディレクトリの一覧が表示されます。 ![FTP ファイルの一覧表示][9]
<!--Image references-->
[1]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interfaces.png
[2]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interface-details.png
[3]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines.png
[4]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines-FTP0-VM.png
[5]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager.png
[6]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager-FTP-Firewall-Support.png
[7]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-services.png
[8]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-service-app-detail.png
[9]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-ftp-file-listing.png
