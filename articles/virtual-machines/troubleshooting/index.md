---
layout: LandingPage
description: 仮想マシンのデプロイのトラブルシューティングを行う方法について説明します。
title: Azure Virtual Machines のトラブルシューティングのドキュメント | Microsoft Docs
services: virtual-machines
author: genlin
manager: dcscontentpm
ms.assetid: ''
ms.service: virtual-machines
ms.tgt_pltfrm: na
ms.topic: landing-page
ms.date: 10/3/2018
ms.author: genli
ms.openlocfilehash: d7ceb3acb1d2e3d174f3b665ec6210d3ddac9970
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059159"
---
# <a name="troubleshooting-azure-virtual-machines"></a>Azure Virtual Machines のトラブルシューティング

## <a name="tools-for-troubleshooting"></a>トラブルシューティング用ツール

- [シリアル コンソール](serial-console-windows.md)
- [ブート診断](boot-diagnostics.md)
- [Windows VM:トラブルシューティングのために OS ディスクを別の VM にアタッチする](troubleshoot-recovery-disks-portal-windows.md)
- [Linux VM:トラブルシューティングのために OS ディスクを別の VM にアタッチする](troubleshoot-recovery-disks-portal-linux.md)

## <a name="cant-connect-to-the-vm"></a>VM に接続できない

### <a name="windows"></a>Windows

**一般的な解決方法**

- [RDP のリセット](reset-rdp.md)
- [RDP のトラブルシューティング](troubleshoot-rdp-connection.md)
- [RDP のトラブルシューティングの詳細](detailed-troubleshoot-rdp.md)

**RDP のエラー**

- [ライセンス サーバーがない](troubleshoot-rdp-no-license-server.md)
- [内部](Troubleshoot-rdp-internal-error.md)
- [認証エラー](troubleshoot-authentication-error-rdp-vm.md)
- [特定のエラーのトラブルシューティング](troubleshoot-specific-rdp-errors.md)

**VM の起動エラー**

* [BitLocker ブート エラー](troubleshoot-bitlocker-boot-error.md) 
* [起動時に "ファイル システムをチェックしています" と表示される](troubleshoot-check-disk-boot-error.md)
* [ブルー スクリーン エラー](troubleshoot-common-blue-screen-error.md)
* [VM の起動が停止し、"Windows の準備をしています](troubleshoot-vm-boot-configure-update.md)
* [ブルー スクリーンに "CRITICAL SERVICE FAILED" エラー](troubleshoot-critical-service-failed-boot-error.md)
* [再起動ループの問題](troubleshoot-reboot-loop.md)
* [VM の起動が Windows Update の段階で停止する](troubleshoot-stuck-updating-boot-error.md)
* [VM がセーフ モードで起動する](troubleshoot-rdp-safe-mode.md)

**その他**
- [Windows VM の VM パスワードをオフラインでリセットする](reset-local-password-without-agent.md)
- [構成を誤った後に NIC をリセットする](reset-network-interface.md)

### <a name="linux"></a>Linux

- [SSH のトラブルシューティング](troubleshoot-ssh-connection.md)
- [SSH のトラブルシューティングの詳細](detailed-troubleshoot-ssh-connection.md)
- [一般的なエラー メッセージ](error-messages.md)
- [Linux VM の VM パスワードをオフラインでリセットする](reset-password.md)

## <a name="vm-deployment-issues"></a>VM のデプロイの問題

- [割り当ての失敗](allocation-failure.md)
- VM を再デプロイする
    - [Linux](redeploy-to-new-node-linux.md)
    - [Windows](redeploy-to-new-node-windows.md)
- デプロイのトラブルシューティング
    - [Linux](troubleshoot-deploy-vm-linux.md)
    - [Windows](troubleshoot-deploy-vm-windows.md)
- [デバイス名が変更されている](troubleshoot-device-names-problems.md)
- [Windows VM エージェントのオフライン インストール](install-vm-agent-offline.md)
- [VM の再起動またはサイズ変更](restart-resize-error-troubleshooting.md)

## <a name="vm-performance-issue"></a>VM のパフォーマンスの問題
- [VM のパフォーマンスの問題](performance-diagnostics.md)
- Windows
    - [PerfInsights を使用する方法](how-to-use-perfinsights.md)
    - [パフォーマンス診断拡張機能](performance-diagnostics-vm-extension.md)
- Linux
    - [PerfInsights を使用する方法](how-to-use-perfinsights-linux.md)