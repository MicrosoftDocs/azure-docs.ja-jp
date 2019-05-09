---
title: Azure Sentinel プレビューに Windows セキュリティ イベント データを接続する | Microsoft Docs
description: Azure Sentinel に Windows セキュリティ イベント データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: d131d39a9d9770046fa3f9368250204e4d0b0162
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794484"
---
# <a name="connect-windows-security-events"></a>Windows セキュリティ イベントの接続 

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Sentinel ワークスペースに接続されている Windows サーバーからのすべてのセキュリティ イベントをストリーミングすることができます。 この接続により、ダッシュボードを表示し、カスタム アラートを作成し、調査を改善することができます。 これにより、組織のネットワークに関するより詳しい分析情報が得られ、セキュリティ運用機能が向上します。  ストリーミングするイベントを以下のように選択できます。

- **すべてのイベント** - すべての Windows セキュリティ イベントおよび AppLocker イベント。
- **一般** - 監査の目的で使用する標準的なイベント セット。
- **最小** - 潜在的な脅威を示す可能性がある小さいイベント セット。 このオプションを有効にすると、完全な監査証跡を取得できなくなります。
- **なし** - セキュリティ イベントまたは AppLocker イベントをストリーミングしない。

> [!NOTE]
> 
> - データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="set-up-the-windows-security-events-connector"></a>Windows セキュリティ イベントのコネクタを設定する

Windows セキュリティと Azure Sentinel を完全に統合するには、以下の手順を実行します。

1. Azure Sentinel ポータルで、**[データ コネクタ]** を選択し、次に **[Windows セキュリティ イベント]** タイルをクリックします。 
1. ストリーミングするデータの種類を選択します。
1. **[Update]** をクリックします。
6. Log Analytics で Windows セキュリティ イベントに関連するスキーマを使用するために、**SecurityEvent** を検索します。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、約 20 分かかる場合があります。 



## <a name="next-steps"></a>次の手順
このドキュメントでは、Windows セキュリティ イベントを Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。

