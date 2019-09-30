---
title: インクルード ファイル
description: インクルード ファイル
services: backup
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 9e9c09c1825f5c8383a708e8bd343146396f878e
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "67181575"
---
次の制限は、Azure Backup に適用されます。

| **制限** | **既定値** |
| --- | --- |
| コンテナーに登録できるサーバーまたはマシン。 | Windows Server/Windows クライアント/System Center Data Protection Manager:50 を使用しています。 <br/><br/> IaaS VM: 1,000。  |
| コンテナー ストレージ内のデータ ソースのサイズ。 |最大 54,400 GB。 この制限は、IaaS VM のバックアップには適用されません。 |
| Azure サブスクリプション内のバックアップ コンテナー。 |リージョンあたり 500 コンテナー。 |
| 毎日のバックアップのスケジュール数。 |Windows Server/クライアント: 1 日あたり 3 回。<br/> System Center DPM: 1 日あたり 2 回。 <br/> IaaS VM: 1 日あたり 1 回。  |
| Azure VM にアタッチするバックアップ用データ ディスク。 | 16 |
| Azure VM にアタッチする個々のバックアップ用データ ディスク。| 4,095 GB|
