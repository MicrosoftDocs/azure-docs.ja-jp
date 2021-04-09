---
title: Azure Sentinel に Check Point データを接続する | Microsoft Docs
description: Syslog エージェントを介して Azure Sentinel ワークスペースに CEF 形式で Syslog メッセージを転送するように Check Point アプライアンスを構成します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 32cbdabef4d89c7fabb47a52ebf0589690b7ab33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85566063"
---
# <a name="connect-check-point-to-azure-sentinel"></a>Check Point を Azure Sentinel に接続する



この記事では、Check Point アプライアンスを Azure Sentinel に接続する方法について説明します。 Check Point データ コネクタを使用すると、Azure Sentinel に Check Point のログを簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、および調査の改善も行うことができます。 Azure Sentinel で Check Point を使用すると、組織のインターネット使用状況に関するより多くの分析情報が得られ、セキュリティ操作機能が強化されます。 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Syslog エージェントへの Check Point ログの転送

Syslog エージェントを介して Azure ワークスペースに CEF 形式で Syslog メッセージを転送するように Check Point アプライアンスを構成します。

1. 「[Check Point Log Export](https://aka.ms/asi-syslog-checkpoint-forwarding)」(Check Point ログのエクスポート) に移動します。
1. 下にスクロールして「**Basic Deployment**」(基本的なデプロイ) に移動し、次のガイドラインを使用して、接続を設定する指示に従います。
   - **Syslog ポート** を **514** に設定するか、エージェントに設定されているポートに設定します。
     - CLI で、**名前** と **ターゲット サーバーの IP アドレス** を、Syslog エージェント名と IP アドレスに置き換えます。
     - 形式を **CEF** に設定します。
1. R77.30 または R80.10 バージョンを使用している場合は、上にスクロールして「**Installations**」(インストール) に移動し、お使いのバージョン用の Log Exporter をインストールする指示に従います。
1. 「[手順 3:接続の検証](connect-cef-verify.md)」に進みます。
 

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Check Point アプライアンスを Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- 「[接続の検証](connect-cef-verify.md)」。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。


