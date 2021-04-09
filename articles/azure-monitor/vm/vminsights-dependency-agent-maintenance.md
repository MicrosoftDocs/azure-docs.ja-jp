---
title: VM insights Dependency Agent をアップグレードする方法
description: この記事では、コマンドライン、設定ウィザード、およびその他の方法を使用して VM insights Dependency Agent をアップグレードする方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/16/2020
ms.openlocfilehash: acd1b6c8e5c1be76b29c93fddc57c799aef0a526
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046705"
---
# <a name="how-to-upgrade-the-vm-insights-dependency-agent"></a>VM insights Dependency Agent をアップグレードする方法

VM insights Dependency Agent の初期デプロイ後に、バグ修正や新機能のサポートを含む更新プログラムがリリースされます。  この記事は、使用できる方法と、手動またはオートメーションを使用してアップグレードを実行する方法を理解するために役立ちます。

## <a name="upgrade-options"></a>アップグレード オプション 

Windows および Linux 用 Dependency Agent は、配置のシナリオおよびマシンが実行されている環境に応じて、手動または自動で最新リリースにアップグレードできます。 エージェントをアップグレードするには、次の方法を使用できます。

|環境 |インストール方法 |アップグレード方法 |
|------------|--------------------|---------------|
|Azure VM | [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) および [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) 用の Dependency Agent VM 拡張機能 | プロパティ *autoUpgradeMinorVersion* を **false** に設定することによってオプトアウトするよう Azure Resource Manager テンプレートを構成していない限り、エージェントは既定で自動的にアップグレードされます。 自動アップグレードが無効なマイナー バージョンのアップグレードと、メジャー バージョンのアップグレードは、拡張機能をアンインストールしてから再インストールするという同じ方法で実行されます。 |
| カスタム Azure VM イメージ | Windows/Linux 用の Dependency Agent の手動インストール | コマンド ラインから Windows インストーラー パッケージまたは Linux 自己解凍およびインストール可能シェル スクリプト バンドルを実行して、エージェントの最新バージョンへの VM の更新を実行する必要があります。|
| Azure 以外の VM | Windows/Linux 用の Dependency Agent の手動インストール | コマンド ラインから Windows インストーラー パッケージまたは Linux 自己解凍およびインストール可能シェル スクリプト バンドルを実行して、エージェントの最新バージョンへの VM の更新を実行する必要があります。 |

## <a name="upgrade-windows-agent"></a>Windows エージェントをアップグレードする 

Dependency Agent VM 拡張機能を使用して、Windows VM 上のエージェントをインストールされていない最新バージョンに更新するには、コマンド プロンプト、スクリプト、またはその他のオートメーション ソリューションから実行するか、InstallDependencyAgent-Windows.exe セットアップ ウィザードを使用します。  

最新バージョンの Windows エージェントは[こちら](https://aka.ms/dependencyagentwindows)からダウンロードできます。

### <a name="using-the-setup-wizard"></a>セットアップ ウィザードの使用

1. 管理者権限を持つアカウントでコンピューターにサインオンします。

2. **InstallDependencyAgent-Windows.exe** を実行してセットアップ ウィザードを開始します。
   
3. **Dependency Agent のセットアップ** ウィザードに従って、以前のバージョンの Dependency Agent をアンインストールし、最新バージョンをインストールします。


### <a name="from-the-command-line"></a>コマンド ラインから

1. 管理者権限を持つアカウントでコンピューターにサインオンします。

2. 次のコマンドを実行します。

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    `/RebootMode=manual` パラメーターを指定すると、一部のプロセスで以前のバージョンのファイルが使用されており、ロックされている場合、アップグレードによってマシンが自動的に再起動されなくなります。 

3. アップグレードが正常に完了したことを確認するには、`install.log` で詳細な設定情報を確認します。 ログ ディレクトリは、 *%Programfiles%\Microsoft Dependency Agent\logs* にあります。

## <a name="upgrade-linux-agent"></a>Linux エージェントをアップグレードする 

Linux 上の以前のバージョンの Dependency Agent からのアップグレードはサポートされており、新規インストールと同じコマンドに従って実行されます。

最新バージョンの Linux エージェントは[こちら](https://aka.ms/dependencyagentlinux)からダウンロードできます。

1. 管理者権限を持つアカウントでコンピューターにサインオンします。

2. root としてコマンド `sh InstallDependencyAgent-Linux64.bin -s` を実行します。 

Dependency Agent が起動しない場合は、詳細なエラー情報のログを確認します。 Linux エージェントのログ ディレクトリは、 */var/opt/microsoft/dependency-agent/log* にあります。 

## <a name="next-steps"></a>次のステップ

VM の監視を一定期間停止する場合、または VM insights を完全に削除する場合は、「[VM insights で VM の監視を無効にする](../vm/vminsights-optout.md)」を参照してください。
