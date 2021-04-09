---
title: Azure Data Box Gateway 一般提供リリース ノート | Microsoft Docs
description: 一般提供リリースを実行している Azure Data Box Gateway の重大な未解決の問題と解決策について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 2984f7990b9570c5ec57633de7f7e50162fb6f46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96581318"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Azure Data Box Edge/Azure Data Box Gateway 一般提供リリース ノート

## <a name="overview"></a>概要

次のリリース ノートでは、Azure Data Box Edge および Azure Data Box Gateway の一般提供 (GA) リリースの重大な未解決の問題と解決済みの問題を識別します。 

リリース ノートは継続的に更新されます。対応策を必要とする重大な問題が見つかった場合は、それらの問題が追加されます。 Data Box Edge/Data Box Gateway をデプロイする前に、リリース ノートに含まれている情報を注意深く確認してください。

この GA リリースは、次のソフトウェア バージョンに対応します。

- **Data Box Gateway 1903 (1.5.814.447)**
- **Data Box Edge 1903 (1.5.814.447)**


## <a name="whats-new"></a>新機能

- **新しい仮想ディスク イメージ** - Azure Portal で新しい VHDX および VMDK を入手できるようになりました。 新しい Data Box Gateway GA デバイスをプロビジョニング、構成、およびデプロイするには、これらのイメージをダウンロードしてください。 以前のプレビュー リリースで作成された Data Box Gateway デバイスをこのバージョンに更新することはできません。 詳細については、[Azure Data Box Gateway のデプロイ準備](data-box-gateway-deploy-prep.md)に関する記事をご覧ください。
- **NFS サポート** - NFS サポートは現在プレビュー段階にあり、Data Box Edge および Data Box Gateway デバイスにアクセスする v3.0 および v4.1 クライアントで使用できます。
- **ストレージ回復性** - ストレージ回復性機能により、Data Box Edge デバイスは 1 台のデータ ディスクの障害に耐えることができます。 現在、この機能はプレビュー段階にあります。 ストレージ回復性は、ローカル Web UI で、**[ストレージの設定]** の **[Resilient] (回復性がある)** オプションを選択することによって有効にすることができます。


## <a name="known-issues-in-ga-release"></a>GA リリースの既知の問題

次の表は、このリリースを実行している Data Box Gateway の既知の問題の概要を示しています。

| いいえ。 | 特徴量 | 問題 | 対応策/コメント |
| --- | --- | --- | --- |
| **1.** |ファイルの種類 | 文字ファイル、ブロック ファイル、ソケット、パイプ、シンボリック リンクのファイルの種類はサポートされていません。  |これらのファイルをコピーすると、結果として NFS 共有に長さ 0 のファイルが作成されます。 このようなファイルはエラー状態のままになり、*error.xml* にも報告されます。 <br> ディレクトリへのシンボリック リンクにより、ディレクトリがオフラインとマークされなくなります。 その結果、ディレクトリがオフラインであり、関連付けられているすべてのコンテンツが Azure に完全にアップロードされたことを示す灰色のクロスがディレクトリに表示されなくなることがあります。 |
| **2.** |削除 | このリリースのバグのため、NFS 共有が削除される場合に共有を削除できないことがあります。 共有の状態として *Deleting* が表示されます。  |これが発生するのは、サポートされないファイル名を共有が使用している場合のみです。 |
| **3.** |コピー | エラーによりデータ コピーが失敗する: ファイル システム制限のため、要求された操作を完了できませんでした。  |128 KB を超えるファイル サイズに関連付けられた代替データ ストリーム (ADS) はサポートされていません。   |


## <a name="next-steps"></a>次のステップ

- [Azure Data Box Gateway のデプロイを準備](data-box-gateway-deploy-prep.md)します。
- [Azure Data Box Edge のデプロイを準備](../databox-online/azure-stack-edge-deploy-prep.md)します。
