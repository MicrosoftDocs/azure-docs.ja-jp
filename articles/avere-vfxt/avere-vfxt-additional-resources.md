---
title: Avere vFXT for Azure に関するその他のリンク
description: Avere vFXT for Azure に関する追加情報へのリンク
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: v-erkell
ms.openlocfilehash: 2efbe7ddc39b8bde76ee4a135f3f44af0864a374
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188574"
---
# <a name="additional-documentation"></a>その他のドキュメント

この記事では、Avere コントロール パネル管理インターフェイスと関連トピックに関するその他のドキュメントへのリンクを示します。 

## <a name="avere-cluster-documentation"></a>Avere クラスターのドキュメント

Avere クラスターに関するその他のドキュメントは、Web サイト (<https://azure.github.io/Avere/>) で確認できます。 これらのドキュメントでは、クラスターの機能と、クラスターの設定を構成する方法を理解できます。 

* 「[FXT Cluster Creation Guide](<https://azure.github.io/Avere/#fxt_cluster>)」 (FXT クラスターの作成ガイド) は、物理ハードウェア ノードで構成されたクラスターを対象にしていますが、ドキュメントの一部の情報は vFXT クラスターにも関連しています。 特に、初めて vFXT クラスターを管理する管理者は以下のセクションを読むと役に立ちます。
  * 「[Customizing Support and Monitoring Settings](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>)」 (サポートと監視の設定のカスタマイズ) では、サポート アップロードの設定をカスタマイズする方法と、リモート監視を有効にする方法について説明しています。 
  * 「[Configuring VServers and Global Namespace](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>)」 (VServers とグローバル名前空間の構成) には、クライアントに接続する名前空間の作成に関する情報が記載されています。
  * 「[Configuring DNS for the Avere cluster](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>)」 (Avere クラスター向け DNS の構成) では、ラウンドロビン DNS を構成する方法について説明しています。
  * 「[Adding Backend Storage](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>)」 (バックエンド ストレージの追加) では、コア ファイラーを追加する方法について説明しています。

* 「[Cluster Configuration Guide](<https://azure.github.io/Avere/#operations>)」 (クラスター構成ガイド) は、Avere クラスターの設定とオプションの詳細なリファレンスです。 vFXT クラスターには、これらのオプションのサブセットを使用しますが、同じ構成ページのほとんどが適用されます。

* 「[Dashboard Guide](<https://azure.github.io/Avere/#operations>)」 (ダッシュボード ガイド) では、Avere コントロール パネルのクラスター監視機能の使用方法について説明しています。

## <a name="vfxt-creation-and-management-documentation"></a>vFXT の作成と管理のドキュメント

クラウド クラスターの作成および管理ユーティリティである vfxt.py の使用に関する詳細なガイドが、GitHub の「[Cloud cluster management with vfxt.py (vfxt.py でのクラウド クラスター管理)](https://github.com/Azure/AvereSDK/blob/master/docs/README.md)」で提供されています。  
