---
title: Azure 仮想マシンの拡張機能とその機能 | Microsoft Docs
description: Azure VM 拡張機能と、それらを Azure 仮想マシンで使用する方法を説明します
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: 3a8c571d3c6bb55fdd8b2d097b71b83afab5ca00
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705916"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Azure 仮想マシンの拡張機能とその機能
Azure 仮想マシン (VM) の拡張機能は、Azure VM でデプロイ後の構成と自動化のタスクを実行する小さなアプリケーションです。ユーザーは既存のイメージを使用でき、後でそれをデプロイの一部としてカスタマイズできるため、カスタムのイメージのビルド作業をなくすことができます。

Azure プラットフォームでは、VM の構成、監視、セキュリティ、およびユーティリティというさまざまなアプリケーションの多くの拡張機能をホストします。 パブリッシャーは、アプリケーションを取得してから、それを拡張機能にまとめて、インストールを簡略化するため、必須のパラメーターを指定するだけで済みます。 

 ファースト パーティとサード パーティの拡張機能には豊富な選択肢があり、拡張機能リポジトリにアプリケーションが存在しない場合は、カスタム スクリプト拡張機能を使用して、独自のスクリプトやコマンドで VM を構成することができます。

拡張機能が使用される主なシナリオの例:
* VM 構成。Powershell DSC (Desired State Configuration)、Chef、Puppet、およびカスタム スクリプト拡張機能を使用して、VM 構成エージェントをインストールし、VM を構成できます。 
* ウイルス対策製品 (Symantec、ESET など)。
* VM 脆弱性ツール (Qualys、Rapid7、HPE など)。
* VM およびアプリ監視ツール (DynaTrace、Azure Network Watcher、Site24x7、Stackify など)。

拡張機能は、新しい VM のデプロイにバンドルできます。 たとえば、大規模なデプロイ、VM プロビジョンでのアプリケーションの構成、またはデプロイ後のサポート対象の拡張機能で運用するシステムに対する実行の一部にすることができます。

## <a name="how-can-i-find-what-extensions-are-available"></a>使用可能な拡張機能を見つける方法
使用可能な拡張機能は、Portal の VM ブレードの拡張機能で確認できますが、ここには一部しか表示されません。完全な一覧は、CLI ツールを使用して確認できます。[Linux 用の VM 拡張機能の検出](features-linux.md)および [Windows 用の VM 拡張機能の検出](features-windows.md)に関するページをご覧ください。

## <a name="how-can-i-install-an-extension"></a>拡張機能をインストールする方法
Azure VM 拡張機能は、Azure CLI、Azure PowerShell、Azure Resource Manager テンプレート、および Azure portal のいずれかを使用して管理できます。 拡張機能を試してみるには、Azure Portal に移動し、カスタム スクリプト拡張機能を選択し、コマンド/スクリプトを渡して拡張機能を実行します。

CLI または Resource Manager テンプレートで Portal に追加したものと同じ拡張機能が必要な場合は、[Windows カスタム スクリプト拡張機能](custom-script-windows.md)および [Linux カスタム スクリプト拡張機能](custom-script-linux.md)などの別の拡張機能のドキュメントをご覧ください。

## <a name="how-do-i-manage-extension-application-lifecycle"></a>拡張アプリケーションのライフサイクルを管理する方法
拡張機能をインストールまたは削除するために、VM に直接接続する必要はありません。 Azure の拡張アプリケーションのライフサイクルは VM の外部で管理され、Azure プラットフォームに統合されているため、拡張機能の統合の状態も取得します。

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>拡張機能について検討すべきその他の項目
拡張機能はアプリケーションをインストールし、アプリケーションのように何らかの要件があります。拡張機能の場合はサポートされている Windows や Linux の OS の一覧があり、Azure VM エージェントがインストールされている必要があります。 個別の VM 拡張アプリケーションによっては、エンドポイントへのアクセスなどの独自の環境要件がある場合があります。

## <a name="next-steps"></a>次の手順
* Linux エージェントと拡張機能のしくみの詳細については、「[Linux 用の Azure VM 拡張機能とその機能](features-linux.md)」をご覧ください。
* Windows ゲスト エージェントと拡張機能のしくみの詳細については、「[Windows 用の Azure VM 拡張機能とその機能](features-windows.md)」を参照してください。  
* Windows ゲスト エージェントをインストールする場合は、[Azure Windows 仮想マシン エージェントの概要](agent-windows.md)に関するページをご覧ください。  
* Linux エージェントをインストールする場合は、[Azure Linux 仮想マシン エージェントの概要](agent-linux.md)に関するページをご覧ください。  

