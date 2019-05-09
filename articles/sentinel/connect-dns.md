---
title: Azure Sentinel Preview で DNS データを接続する | Microsoft Docs
description: Azure Sentinel で DNS データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: e34db2bdc78eb846cf4885b1ef083fd3b21e21b5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792053"
---
# <a name="connect-your-domain-name-server"></a>ドメイン ネーム サーバーへの接続

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Windows 上で実行されているドメイン ネーム サーバー (DNS) を Azure Sentinel に接続できます。 そのためには、DNS マシンにエージェントをインストールします。 DNS ログを使用すると、分析および監査ログや他の関連データを DNS サーバーから収集し、分析して関連付けることによって、組織の DNS インフラストラクチャに関するセキュリティ、パフォーマンス、および操作に関連する分析情報を得ることができます。

DNS ログ接続を有効にすると、次のことが可能になります。
- 悪意のあるドメイン名を解決しようとしているクライアントを特定する
- 古いリソース レコードを特定する
- クエリ対象になる頻度が高いドメイン名と話好きな DNS クライアントを特定する
- DNS サーバーの要求負荷を確認する
- 動的 DNS 登録エラーを確認する

## <a name="how-it-works"></a>動作のしくみ

DNS マシンにエージェントをインストールすることで、DNS 接続が可能になります。 エージェントは DNS からイベントを取得し、それらを Log Analytics に渡します。

## <a name="connect-your-dns-appliance"></a>DNS アプライアンスの接続

1. Azure Sentinel portal で、**[Data connectors]\(データ コネクタ\)** を選択し、**[DNS]** タイルを選択します。
1. DNS マシンが Azure にある場合は、次の手順を実行します。
    1. **[Download & install agent for Windows virtual machines]\(Windows 仮想マシン用エージェントのダウンロードとインストール\)** をクリックします。
    1. **[仮想マシン]** 一覧で、Azure Sentinel にストリーミングする DNS マシンを選択します。 これが Windows VM であることを確認します。
    1. 開いているその VM のウィンドウで、**[接続]** をクリックします。  
    1. **[DNS connector]\(DNS コネクタ\)** ウィンドウで **[有効化]** をクリックします。 

2. DNS マシンが Azure VM ではない場合は、次の手順を実行します。
    1. **[Download & install agent for Windows non-Azure machines]\(Azure 以外の Windows マシン用エージェントのダウンロードとインストール\)** をクリックします。
    1. **[ダイレクト エージェント]** ウィンドウで、**[Download Windows agent (64 bit)]\(Windows エージェントのダウンロード (64 ビット)\)** または **[Download Windows agent (32 bit)]\(Windows エージェントのダウンロード (32 ビット)\)** を選択します。
    1. DNS マシンにエージェントをインストールします。 **ワークスペース ID**、**主キー**、**2 次キー**をコピーし、インストール中に入力を要求されたらこれらを使用します。

3. Log Analytics で DNS ログに関連するスキーマを使用するために、**DnsEvents** を検索します。

## <a name="validate"></a>検証 

Log Analytics で、スキーマ **DnsEvents** を検索し、イベントが存在することを確認します。

## <a name="next-steps"></a>次の手順
このドキュメントでは、DNS オンプレミス アプライアンスを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。
