---
title: アプライアンスを使用した Azure へのデータ転送オプション |Microsoft Docs
description: データを Azure に転送するための適切なアプライアンスを選択する方法について説明します
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: d727ed62f5a869f6eb67400281d2660607756abf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58802696"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>StorSimple を、Azure File Sync および Data Box Edge データ転送オプションと比較する 
 
このドキュメントでは、Azure へのオンプレミス データ転送オプションの概要を示し、Data Box Edge、Azure File Sync およびStorSimple 8000 シリーズを比較します。

- **[Data Box Edge](/azure/databox-online/data-box-edge-overview)** – Data Box Edge は、Azure との間でデータを移動し、アップロード中にデータを前処理するための AI 対応の Edge コンピューティング機能を備えた、オンプレミスのネットワーク デバイスです。 Data Box Gateway は、同じデータ転送機能を備えた、デバイスの仮想バージョンです。
- **[Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide)** – Azure File Sync を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を維持したまま、Azure Files 内の組織のファイル共有を一元化できます。 Azure File Sync により、ご利用の Windows Server が Azure ファイル共有の高速キャッシュに変わります。 Azure File Sync の一般提供は、2018 年前半に発表されました。
- **[StorSimple](/azure/storsimple/storsimple-overview)**  – StorSimple は、Azure ストレージとの緊密な統合により、1 つのソリューションでプライマリ ストレージ、データ保護、アーカイブ、ディザスター リカバリーのためのストレージ インフラストラクチャの統合を支援するハイブリッド デバイスです。 StorSimple の製品ライフ サイクルは、[こちら](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)でご覧いただけます。

## <a name="comparison-summary"></a>比較の概要

|                           |StorSimple 8000   |Azure File Sync   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|概要         |階層化ハイブリッド ストレージとアーカイブ|クラウドを使った階層化とマルチサイトの同期機能を備えた、一般的なファイル サーバー ストレージ。  |データを前処理し、ネットワーク経由で Azure に送信するストレージ ソリューション。        |
|シナリオ        |ファイル サーバー、アーカイブ、バックアップのターゲット |ファイル サーバー、アーカイブ (マルチサイト)   |データ転送、ML の推論を含むデータの前処理、IoT、アーカイブ    |
|Edge コンピューティング     |使用できません。 |使用できません。 |Azure IoT Edge を使用したコンテナーの実行をサポート    |
|フォーム ファクター      |物理デバイス   |Windows Server にインストールされるエージェント |物理デバイス   |
|ハードウェア         |サービスの一部として、Microsoft から提供される物理デバイス | お客様側で準備 |サービスの一部として、Microsoft から提供される物理デバイス  |
|データ形式      |カスタム形式   |ファイル         |BLOB またはファイル    |
|プロトコルのサポート |iSCSI          |SMB、NFS    | SMB または NFS      |
|価格          |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>次の手順

- [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview) と [Azure Data Box Gateway](/azure/databox-online/data-box-gateway-overview) について学習する
- [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) について学習する
