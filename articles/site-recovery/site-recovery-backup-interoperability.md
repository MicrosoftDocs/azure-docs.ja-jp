---
title: Azure Site Recovery と Azure Backup の使用のサポート
description: Azure Site Recovery と Azure Backup を一緒に使用する方法の概要を示します。
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: 2078aa33ca735b91b5923d94028a286427f107a6
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146870"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Azure Site Recovery と Azure Backup の使用

この記事では、[Site Recovery サービス](site-recovery-overview.md)と [Azure Backup サービス](https://docs.microsoft.com/azure/backup/backup-overview)を一緒に使用するためのサポートについて説明します。

**アクション** | **Site Recovery のサポート** | **詳細**
--- | --- | ---
**サービスを一緒にデプロイする** | サポートされています | サービスは相互運用可能であり、一緒に構成できます。
**ファイルのバックアップ/復元** | サポートされています | VM のバックアップとレプリケーションが有効になっていて、バックアップが取られる場合、ソース側の VM または VM グループでのファイルの復元には何の問題もありません。 レプリケーションは通常どおり続行され、レプリケーションの正常性に変化はありません。
**ディスクのバックアップ/復元** | 現在サポートされていません | バックアップしたディスクを復元する場合、VM のレプリケーションを無効にしてから、再度有効にする必要があります。
**VM のバックアップ/復元** | 現在サポートされていません | VM または VM グループをバックアップまたは復元する場合、VM のレプリケーションを無効にしてから、再度有効にする必要があります。  


