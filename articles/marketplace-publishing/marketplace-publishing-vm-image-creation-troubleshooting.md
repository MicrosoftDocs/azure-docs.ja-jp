---
title: "VHD 作成中の一般的な問題をトラブルシューティングする方法 | Microsoft Docs"
description: "VHD 作成中の一般的なトラブルシューティングの質問と問題にお答えします。"
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: 
editor: 
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 09/26/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: c4e88a9fbb15dd90d619b159ae1065dfacc1907f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-troubleshoot-common-issues-encountered-during-vhd-creation"></a>VHD 作成中に発生する一般的な問題をトラブルシューティングする方法
この記事では、Azure Marketplace の発行者や共同管理者が仮想マシン ソリューションを発行または管理するときによく発生する問題や一般的な質問について説明します。

1. ホストの名前を変更するにはどうすればよいですか?
   
    VM を作成した後は、ユーザーはホストの名前を更新できません。
2. リモート デスクトップ サービスまたはそのログイン パスワードをリセットするにはどうすればよいですか?
   
   * [Windows VM の場合はこちらをご覧ください](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
   * [Linux VM の場合はこちらをご覧ください](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
3. 新しい SSH 証明書を生成するにはどうすればよいですか?
   
   [https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/) をご覧ください
4. 開いている VPN 証明書を構成するにはどうすればよいですか?
   
   [https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/](https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/) をご覧ください
5. Microsoft Azure 仮想マシン環境での Microsoft サーバー ソフトウェアの実行に関するサポート ポリシーはどのようなものですか (インフラストラクチャとしてのサービス)?
   
   [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672) をご覧ください
6. 仮想マシンには一意の識別子がありますか?
   
   Azure では、すべての VM に Azure VM の一意の ID がエンコードされています。 詳しくは、このブログとドキュメントをご覧ください。
7. VM で、スタートアップ タスクのカスタム スクリプト拡張機能を管理するにはどうすればよいですか?
   
   [https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/) をご覧ください
8. Premium Storage にアップロードされる VHD を使用して、Azure ポータルから VM を作成するにはどうすればよいですか?
   
   この機能はまだサポートされていません。
9. Azure Marketplace では 32 ビット アプリはサポートされていますか?
   
   サポート ポリシーについて詳しくは、[https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672) をご覧ください
10. VHD からイメージを作成しようとするたびに、PowerShell で "VHD は、既にリソースとしてイメージ リポジトリに登録されています" というエラーが発生します。 これまでイメージを作成したことはなく、Azure で同じ名前のイメージは見つかっていません。 解決するにはどうすればよいですか?
    
    この問題は通常、ユーザーがこの VHD から VM をプロビジョニングし、その VHD にロックがある場合に発生します。 この VHD から割り当てられている VM がないことを確認してください。 エラーが解決しない場合は、このリンクを使用して、または発行ポータルから、これに関するサポート チケットをお送りください (詳細については質問 11 の回答を参照)。