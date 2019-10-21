---
title: Azure Backup エージェントのアップグレード
description: この情報は、Azure Backup エージェントをアップグレードする理由と、アップグレードをダウンロードする場所について説明しています。
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: dacurwin
ms.openlocfilehash: ea1295b08aa77a3e1a03d944ddbcbf37b6d17702
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71251540"
---
## <a name="upgrade-the-mars-agent"></a>MARS エージェントをアップグレードする

2\.0.9083.0 より前のバージョンの Microsoft Azure Recovery Service (MARS) エージェントには、Azure Access Control Service (ACS) との依存関係があります。 MARS エージェントは、Azure Backup エージェントとも呼ばれます。 2018 年に、Azure では、[Azure Access Control Service (ACS) が非推奨になりました](../articles/active-directory/develop/active-directory-acs-migration.md)。 2018 年 3 月 19 日以降、2.0.9083.0 より前のすべてのバージョンの MARS エージェントでバックアップが失敗します。 バックアップの失敗を回避または解決するには、[MARS エージェントを最新バージョンにアップグレード](https://go.microsoft.com/fwlink/?linkid=229525)します。 MARS エージェントのアップグレードが必要なサーバーを識別するには、[MARS エージェントのアップグレードに関するバックアップ ブログ](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/)の手順に従ってください。 MARS エージェントは、ファイル、フォルダー、およびシステム状態データを Azure にバックアップするために使用します。 System Center DPM および Azure Backup Server は、MARS エージェントを使用してデータを Azure にバックアップします。
