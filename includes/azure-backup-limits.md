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
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554127"
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
