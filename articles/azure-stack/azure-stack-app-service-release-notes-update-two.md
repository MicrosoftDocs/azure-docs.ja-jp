---
title: App Service on Azure Stack Update 2 のリリース ノート | Microsoft Docs
description: App Service on Azure Stack Update 2 の内容、既知の問題、更新プログラムをダウンロードする場所について説明します。
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: sethm
ms.lastreviewed: 05/18/2018
ms.openlocfilehash: 98d084794accb99e0fecac85f7de861ec35bc15e
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58446023"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>App Service on Azure Stack update 2 のリリース ノート

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

これらのリリース ノートでは、Azure App Service on Azure Stack Update 2 における機能強化と修正点、および既知の問題について説明します。 既知の問題は、デプロイおよび更新プロセスに直接関係する問題と、ビルド (インストール後) に関する問題に分けられています。

> [!IMPORTANT]
> Azure App Service 1.2 をデプロイする前に、Azure Stack 統合システムに 1804 更新プログラムを適用するか、または最新の Azure Stack 開発キットをデプロイします。
>
>

## <a name="build-reference"></a>ビルドのリファレンス

App Service on Azure Stack Update 2 のビルド番号は **72.0.13698.10** です。

### <a name="prerequisites"></a>前提条件

> [!IMPORTANT]
> Azure App Service で Kudu の SSO の処理方法が改善されたため、Azure App Service の Azure Stack への新しいデプロイには、[3 つのサブジェクトのワイルドカード証明書](azure-stack-app-service-before-you-get-started.md#get-certificates)が必要になります。 新しいサブジェクトは、**\*.sso.appservice.\<region\>.\<domainname\>.\<extension\>** です。
>
>

デプロイを開始する前に、[前提条件に関するドキュメント](azure-stack-app-service-before-you-get-started.md)をご覧ください。

### <a name="new-features-and-fixes"></a>新機能と修正

Azure App Service on Azure Stack Update 2 には、次の機能強化と修正が含まれています。

- **App Service のテナント ポータル、管理ポータル、Functions ポータル、Kudu ツール**の更新。 Azure Stack Portal SDK バージョンと一致しています。

- **Azure Functions ランタイム**が **v1.0.11612** に更新されました。

- 信頼性を高めるためのコア サービスと、一般的な問題を簡単に診断できるようにするエラー メッセージの更新。

- **次のアプリケーション フレームワークとツールの更新**:
  - .NET Framework 4.7.1 の追加
  - **Node.js** の次のバージョンが追加されました。
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - **NPM** の次のバージョンが追加されました。
    - 5.6.0
  - パブリック クラウドで Azure App Service と一致するように更新された .NET コア コンポーネント。
  - 更新された Kudu

- デプロイ スロット機能の自動スワップを有効化: [自動スワップの構成](https://docs.microsoft.com/azure/app-service/deploy-staging-slots#configure-auto-swap)

- 実稼働環境で機能のテストを有効化: [実稼働環境でのテストの概要](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

- Azure Functions プロキシの有効化: [Azure Functions プロキシの操作](https://docs.microsoft.com/azure/azure-functions/functions-proxies)

- App Service 管理者の拡張機能 UX のサポートが以下に追加されました。
  - シークレット ローテーション
  - 証明書ローテーション
  - システム資格情報のローテーション
  - 接続文字列のローテーション

### <a name="known-issues-post-installation"></a>既知の問題 (インストール後)

- App Service が既存の仮想ネットワークにデプロイされ、ファイル サーバーがプライベート ネットワークでしか使用できない場合、worker はファイル サーバーに到達することができません。

ファイル サーバーに接続するために既存の仮想ネットワークと内部 IP アドレスへデプロイする場合は、送信セキュリティ規則を追加して、worker サブネットとファイル サーバー間の SMB トラフィックを有効にする必要があります。 これを行うには、管理者ポータルで WorkersNsg に移動し、次のプロパティを持つ送信セキュリティ規則を追加します。
* ソース:任意
* 送信元ポート範囲: *
* 変換先:IP アドレス
* 宛先 IP アドレス範囲:ファイル サーバーの IP の範囲
* 送信先ポート範囲:445
* プロトコル:TCP
* アクション:ALLOW
* 優先順位:700
* 名前:Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Azure App Service on Azure Stack を運用するクラウド管理者に関する既知の問題

[Azure Stack 1804 リリース ノート](azure-stack-update-1804.md)内のドキュメントをご覧ください。

## <a name="next-steps"></a>次の手順

- Azure App Service の概要については、「[Azure Stack 上の App Service の概要](azure-stack-app-service-overview.md)」をご覧ください。
- App Service on Azure Stack のデプロイの準備をする方法の詳細については、「[App Service on Azure Stack を開始する前に](azure-stack-app-service-before-you-get-started.md)」をご覧ください。
