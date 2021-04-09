---
title: Forcepoint 製品を Azure Sentinel に接続する | Microsoft Docs
description: Forcepoint 製品を Azure Sentinel に接続する方法について説明します。
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: eb099a786a84f9b7d0a6f0dc6e6df9c3459af295
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "77588231"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>Azure Sentinel に Forcepoint 製品を接続する

> [!IMPORTANT]
> Azure Sentinel の Forcepoint 製品データ コネクタは、現在パブリック プレビューです。 この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


このアーティクルでは、Forcepoint 製品を Azure Sentinel に接続する方法について説明します。 

Forcepoint データ コネクタを使用すると、Forcepoint Cloud Access Security Broker と Forcepoint Next Generation Firewall ログを Azure Sentinel に接続できます。 このようにして、ユーザー定義のログを Azure Sentinel にリアルタイムで自動的にエクスポートできます。 コネクタを使用すると、Forcepoint 製品によって記録されたユーザー アクティビティの可視性が向上します。 また、Azure ワークロードやその他のフィードのデータとの相関関係も有効になり、Azure Sentinel 内の Workbooks を使用して監視機能が向上します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Forcepoint 製品のログを Syslog エージェントに転送する 

Syslog エージェントを介して、CEF 形式で Syslog メッセージを Azure ワークスペースに転送するように Forcepoint 製品を構成します。

1. 次のインストール ガイドの説明に従って、Forcepoint 製品と Azure Sentinel の統合を設定します。
 - [Forcepoint CASB 統合ガイド](https://frcpnt.com/casb-sentinel)
 - [Forcepoint NGFW 統合ガイド](https://frcpnt.com/ngfw-sentinel)

2. CommonSecurityLog を検索して、DeviceVendor は名前に 「Forcepoint」が含まれている Log Analytics で関連するスキーマを使用します。 

3. 「[手順 3:接続の検証](connect-cef-verify.md)」に進みます。



## <a name="next-steps"></a>次のステップ

このドキュメントでは、Forcepoint 製品 を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。

- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。

- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。