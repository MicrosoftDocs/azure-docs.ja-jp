---
title: 接続された VHD による Azure VM の予期せぬ再起動のトラブルシューティング | Microsoft Docs
description: VM の予期しない再起動をトラブルシューティングする方法。
keywords: ssh 接続が拒否される, ssh エラー, azure ssh, SSH 接続に失敗する
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: d3b54941d38424e71ac800d2e750ac9bbc96cde9
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086878"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>接続された VHD による VM の予期しない再起動のトラブルシューティング

Azure 仮想マシン (VM) に、同じストレージ アカウントにある VHD が多数接続されている場合、個々のストレージ アカウントのスケーラビリティ ターゲットを超える可能性があり、これは VM が予期せず再起動する原因となります。 ストレージ アカウントの分単位メトリック (**TotalRequests**/**TotalIngress**/**TotalEgress**) で、ストレージ アカウントのスケーラビリティ ターゲットを超えるスパイクが発生していないかどうかを調べます。 ストレージ アカウントで調整が行われたかどうかを調べるには、「[メトリックが PercentThrottlingError の増加を示す](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError)」を参照してください。

一般に、VHD に対する仮想マシンの入力操作または出力操作は、基礎ページ BLOB に対する **Get Page** または **Put Page** 操作と解釈できます。 そのため、使用環境の IOPS の推定値を使用すると、単一のストレージ アカウントに配置できる VHD の数をアプリケーション固有の動作に基づいて調整できます。 1 つのストレージ アカウントに配置するディスクは 40 以下にすることをお勧めします。 ストレージ アカウントにおけるスケーラビリティ ターゲット、特に使用しているストレージ アカウント タイプの合計要求レートと合計帯域幅についての詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](../../storage/common/storage-scalability-targets.md)」を参照してください。

ストレージ アカウントのスケーラビリティ ターゲットを超える場合、複数のストレージ アカウントに VHD を配置して、個別アカウントそれぞれのアクティビティを減らします。
