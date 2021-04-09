---
title: Avere vFXT for Azure に関するその他のリンク
description: Avere クラスターのドキュメントや vFXT の管理のドキュメントなど、Avere vFXT for Azure のその他の情報について、これらのリソースで説明しています。
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 6f9a9f8e6d2a279871ded3dff614be99dd2901df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88271092"
---
# <a name="additional-documentation"></a>その他のドキュメント

この記事では、Avere コントロール パネル管理インターフェイスと関連トピックに関するその他のドキュメントへのリンクを示します。

## <a name="avere-cluster-documentation"></a>Avere クラスターのドキュメント

Avere クラスターに関するその他のドキュメントは、Web サイト (<https://azure.github.io/Avere/>) で確認できます。 これらのドキュメントでは、クラスターの機能と、クラスターの設定を構成する方法を理解できます。

* 「[FXT Cluster Creation Guide](<https://azure.github.io/Avere/#fxt_cluster>)」 (FXT クラスターの作成ガイド) は、物理ハードウェア ノードで構成されたクラスターを対象にしていますが、ドキュメントの一部の情報は vFXT クラスターにも関連しています。 特に、初めて vFXT クラスターを管理する管理者は以下のセクションを読むと役に立ちます。
  * 「[Customizing Support and Monitoring Settings](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>)」 (サポートと監視の設定のカスタマイズ) では、サポート アップロードの設定をカスタマイズする方法と、リモート監視を有効にする方法について説明しています。
  * 「[Configuring VServers and Global Namespace](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>)」 (VServers とグローバル名前空間の構成) には、クライアントに接続する名前空間の作成に関する情報が記載されています。
  * 「[Configuring DNS for the Avere cluster](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>)」 (Avere クラスター向け DNS の構成) では、ラウンドロビン DNS を構成する方法について説明しています。
  * 「[バックエンド ストレージの追加](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>)」では、コア ファイラーを追加する方法について説明しています。

* 「[Cluster Configuration Guide](<https://azure.github.io/Avere/#operations>)」 (クラスター構成ガイド) は、Avere クラスターの設定とオプションの詳細なリファレンスです。 vFXT クラスターには、これらのオプションのサブセットを使用しますが、同じ構成ページのほとんどが適用されます。

* 「[Dashboard Guide](<https://azure.github.io/Avere/#operations>)」 (ダッシュボード ガイド) では、Avere コントロール パネルのクラスター監視機能の使用方法について説明しています。

## <a name="vfxt-creation-and-management-documentation"></a>vFXT の作成と管理のドキュメント

スクリプト ベースのクラウド クラスターの作成および管理ユーティリティである vfxt.py の使用に関する詳細なガイドが、GitHub の「[Cloud cluster management with vfxt.py (vfxt.py でのクラウド クラスター管理)](https://github.com/Azure/AvereSDK/blob/master/docs/README.md)」で提供されています。
