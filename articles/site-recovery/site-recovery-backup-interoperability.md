---
title: Azure Site Recovery と Azure Backup の使用のサポート| Microsoft Docs
description: Azure Site Recovery と Azure Backup を一緒に使用する方法の概要を示します。
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: e902f70225ec0eb0caa98f7e19a16c87220cb6f9
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312888"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Azure Site Recovery と Azure Backup の使用

この記事では、[Site Recovery サービス](site-recovery-overview.md)と [Azure Backup サービス](https://docs.microsoft.com/azure/backup/backup-overview)を一緒に使用するためのサポートについて説明します。

**アクション** | **Site Recovery のサポート** | **詳細**
--- | --- | ---
**サービスを一緒にデプロイする** | サポートされています | サービスは相互運用可能であり、一緒に構成できます。
**ファイルのバックアップ/復元** | サポートされています | VM のバックアップとレプリケーションが有効になっていて、バックアップが取られる場合、ソース側の VM または VM グループでのファイルの復元には何の問題もありません。 レプリケーションは通常どおり続行され、レプリケーションの正常性に変化はありません。
**ディスクのバックアップ/復元** | 現在サポートされていません | バックアップしたディスクを復元する場合、VM のレプリケーションを無効にしてから、再度有効にする必要があります。
**VM のバックアップ/復元** | 現在サポートされていません | VM または VM グループをバックアップまたは復元する場合、VM のレプリケーションを無効にしてから、再度有効にする必要があります。  


