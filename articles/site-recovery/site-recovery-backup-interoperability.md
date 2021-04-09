---
title: Azure Site Recovery と Azure Backup の使用のサポート
description: Azure Site Recovery と Azure Backup を一緒に使用する方法の概要を示します。
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: c334eee34eb878135d3d81ab15d03618c6604846
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135187"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Azure Site Recovery と Azure Backup の使用

この記事では、[Site Recovery サービス](site-recovery-overview.md)と [Azure Backup サービス](../backup/backup-overview.md)を一緒に使用するためのサポートについて説明します。

**操作** | **Site Recovery のサポート** | **詳細**
--- | --- | ---
**サービスを一緒にデプロイする** | サポートされています | サービスは相互運用可能であり、一緒に構成できます。
**ファイルのバックアップ/復元** | サポートされています | VM のバックアップとレプリケーションが有効になっていて、バックアップが取られる場合、ソース側の VM または VM グループでのファイルの復元には何の問題もありません。 レプリケーションは通常どおり続行され、レプリケーションの正常性に変化はありません。
**ディスクの復元** | 現在サポートされていません | バックアップしたディスクを復元する場合、VM のレプリケーションを無効にしてから、再度有効にする必要があります。
**VM の復元** | 現在サポートされていません | VM または VM グループを復元する場合、VM のレプリケーションを無効にしてから、再度有効にする必要があります。  


