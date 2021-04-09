---
title: Azure Linux VM でスクリプトを実行する
description: このトピックは、仮想マシン内でスクリプトを実行する方法について説明します
services: automation
ms.service: virtual-machines
ms.collection: linux
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: how-to
ms.openlocfilehash: 633f4fa6254b2db7a802bb29bffc83b271216d49
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601756"
---
# <a name="run-scripts-in-your-linux-vm"></a>Linux VM でスクリプトを実行する

タスクを自動化する、または問題をトラブルシューティングするには、VM でコマンドを実行する必要がある場合があります。 次の記事は、VM 内でスクリプトやコマンドを実行できる機能の概要について簡単に説明します。

## <a name="custom-script-extension"></a>カスタム スクリプト拡張機能

[カスタム スクリプト拡張機能](../extensions/custom-script-linux.md)は、主にデプロイ後の構成とソフトウェアのインストールに使用されます。

* Azure 仮想マシンでスクリプトをダウンロードして実行します。
* Azure Resource Manager テンプレート、Azure CLI、REST API、PowerShell または Azure Portal を使用して実行できます。
* スクリプト ファイルは、Azure ストレージや GitHub からダウンロードするか、Azure Portal からの実行時にお使いの PC から指定できます。
* PowerShell スクリプトは Windows マシンで実行し、Bash スクリプトは Linux マシンで実行します。
* デプロイ後の構成、ソフトウェアのインストール、その他の構成タスクや管理タスクに役立ちます。

## <a name="run-command"></a>実行コマンド

[実行コマンド](run-command.md)機能では、スクリプトを使用して仮想マシンやアプリケーションの管理およびトラブルシューティングを行うことができます。この機能は、ゲスト ファイアウォールで RDP ポートや SSH ポートが開かれていないなど、マシンに到達不能なときにも使用できます。

* Azure 仮想マシンでスクリプトを実行します。
* [Azure Portal](run-command.md)、[REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)、[Azure CLI](/cli/azure/vm/run-command#az_vm_run_command_invoke)、または [PowerShell](/powershell/module/az.compute/invoke-azvmruncommand) を使用して実行できます。
* Azure Portal でスクリプトをすばやく実行し、出力を確認して、必要に応じて繰り返すことができます。
* スクリプトは直接入力するか、組み込みのスクリプトを使用できます。
* PowerShell スクリプトは Windows マシンで実行し、Bash スクリプトは Linux マシンで実行します。
* 仮想マシンやアプリケーション管理、および到達不能な仮想マシンでスクリプトを実行するのに役立ちます。

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

[Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) は、Automation アカウントに格納されているユーザーのカスタム スクリプトを使用して、マシン、アプリケーション、環境を管理する一般的な機能を提供します。

* Azure および Azure 以外のマシンでスクリプトを実行します。
* Azure Portal、Azure CLI、REST API、PowerShell、webhook を使用して実行できます。
* スクリプトは Automation アカウントで格納および管理されます。
* PowerShell、PowerShell ワークフロー、Python、またはグラフィカル runbook を実行します。
* スクリプトの実行時間に制限はありません。
* 複数のスクリプトを同時に実行できます。
* 完全なスクリプトの出力が返され、保存されます。
* ジョブ履歴は 90 日間使用できます。
* スクリプトはローカル システムとして、またはユーザーが指定した資格情報で実行できます。
* [手動インストール](../../automation/automation-windows-hrw-install.md)が必要です。

## <a name="serial-console"></a>シリアル コンソール

[Serial console](/troubleshoot/azure/virtual-machines/serial-console-linux) を使用すると、VM に接続されているキーボードと同じように、VM に直接アクセスできます。

* Azure 仮想マシンでコマンドを実行します。
* Azure Portal 内のマシンにテキスト ベースのコンソールを使用して実行できます。
* ローカル ユーザー アカウントを使用してマシンにログインします。
* マシンのネットワークやオペレーティング システムの状態に関係なく、仮想マシンへのアクセスが必要なときに役立ちます。

## <a name="next-steps"></a>次のステップ

お使いの VM 内でスクリプトやコマンドを実行できるさまざまな機能について確認します。

* [カスタム スクリプト拡張機能](../extensions/custom-script-linux.md)
* [実行コマンド](run-command.md)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)
* [Serial console](/troubleshoot/azure/virtual-machines/serial-console-linux)