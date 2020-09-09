---
title: Azure AD Connect クラウド プロビジョニング エージェント:自動アップグレード | Microsoft Docs
description: この記事では、Azure AD Connect クラウド プロビジョニング エージェントの組み込みの自動アップグレード機能について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d0f7093f44a284ec26907d7c4bcfb2bdfd04763
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85360913"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect クラウド プロビジョニング エージェント:自動アップグレード

Azure Active Directory (Azure AD) Connect クラウド プロビジョニング エージェントのインストールを常に最新の状態に保つことは、自動アップグレード機能を使用すれば簡単です。

エージェントは次の場所にインストールされます。"Program files\Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe"

バージョンを確認するには、実行可能ファイルを右クリックして、[プロパティ]、[詳細] の順に選択します。

![エージェント ファイルのバージョン](media/how-to-automatic-upgrade/agent1.png)

エージェント アップデーターは次の場所にインストールされます。"Program files\Azure AD Connect Provisioning Agent Updater\AzureADConnectAgentUpdater.exe"

バージョンを確認するには、実行可能ファイルを右クリックして、[プロパティ]、[詳細] の順に選択します。

![エージェントアップデーターのバージョン](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>エージェントのアンインストール
エージェントを削除するには、 **[プログラムのアンインストールまたは変更]** に移動して、次のものをアンインストールします。

- **Microsoft Azure AD Connect エージェント アップデーター**
- **Microsoft Azure AD Connect プロビジョニング エージェント**
- **Microsoft Azure AD Connect Provisioning Agent Package**

![エージェントの削除](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド プロビジョニングとは](what-is-cloud-provisioning.md)

