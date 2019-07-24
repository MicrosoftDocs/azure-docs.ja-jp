---
title: Azure Site Recovery を使用したディザスター リカバリーのための復旧計画にスクリプトを追加する | Microsoft Docs
description: VMM クラウド上にある Hyper-V VM のディザスター リカバリー用復旧計画に、VMM スクリプトを追加する方法について説明します。
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: a3d6f84de103596e27c22cbb11d709bb1a85dc91
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52836846"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>復旧計画への VMM スクリプトの追加

この記事では、System Center Virtual Machine Manager (VMM) スクリプトを作成し、[Azure Site Recovery](site-recovery-overview.md) の復旧計画に追加する方法について説明します。

コメントや質問は、この記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。

## <a name="prerequisites"></a>前提条件

復旧計画では、PowerShell スクリプトを使用できます。 復旧計画からアクセスできるようにするには、スクリプトを作成し、そのスクリプトを VMM ライブラリに配置する必要があります。 スクリプトの作成中は、次の点を考慮してください。

* スクリプトでは、try-catch ブロックを使用して例外を適切に処理する必要があります。
    - スクリプト内で例外が発生すると、実行が停止され、タスクが失敗したことが表示されます。
    - エラーが発生すると、スクリプトの残りの部分は実行されません。
    - 計画されていないフェールオーバーの実行時にエラーが発生した場合、復旧計画は続行されます。
    - 計画されたフェールオーバーの実行時にエラーが発生した場合、復旧計画は停止します。 スクリプトを修正し、想定どおりに実行されることを確認して、復旧計画を再び実行します。
        - `Write-Host` コマンドは、復旧計画スクリプトでは動作しません。 スクリプトで `Write-Host` コマンドを使用すると、スクリプトは失敗します。 出力を作成するには、続いてメイン スクリプトを実行するプロキシ スクリプトを作成します。 すべての出力がパイプ処理されるようにするには、**\>\>** コマンドを使用します。
        - スクリプトは、600 秒以内に実行制御を戻さないとタイムアウトとなります。
        - STDERR に何かが書き込まれると、スクリプトは "失敗" と分類されます。 この情報は、スクリプト実行についての詳細画面に表示されます。

* 復旧計画のスクリプトは VMM サービス アカウントのコンテキストで実行されます。 このアカウントに、スクリプトが配置されているリモート共有に対する読み取りアクセス許可が付与されていることを確認してください。 VMM サービス アカウントと同じレベルのユーザー権限で実行するようにスクリプトをテストします。
* VMM コマンドレットは、Windows PowerShell モジュール内で配布されます。 モジュールは、VMM コンソールと一緒にインストールされます。 モジュールをスクリプトに読み込むには、スクリプトで次のコマンドを使用します。 

    `Import-Module -Name virtualmachinemanager`

    詳細については、「[Windows PowerShell と VMM の概要](https://technet.microsoft.com/library/hh875013.aspx)」を参照してください。
* VMM デプロイに 1 つ以上のライブラリ サーバーが存在することを確認します。 既定では、VMM サーバーのライブラリ共有パスは、VMM サーバーにローカルに配置されます。 フォルダー名は MSCVMMLibrary です。

  ライブラリ共有パスがリモートである (または、ローカルであるが MSCVMMLibrary と共有されていない) 場合は、次の手順を実行して共有パスを構成します。ここでは、例として \\libserver2.contoso.com\share\ が使用されています。
  
  1. レジストリ エディターを開き、**HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration** に移動します。

  1. **ScriptLibraryPath** の値を **\\\libserver2.contoso.com\share\\** に変更します。 完全な FQDN を指定します。 共有場所にアクセス許可を設定します。 これは共有のルート ノードです。 ルート ノードを確認するには、VMM でライブラリのルート ノードに移動します。 開くパスはパスのルートです。 これは、変数で使用する必要があるパスです。

  1. VMM サービス アカウントと同じレベルのユーザー権限を持つユーザー アカウントを使用して、スクリプトをテストします。 このようなユーザー権限を使用して、スタンドアロンのテスト済みスクリプトが、復旧計画で実行される場合と同じ方法で実行されることを検証します。 VMM サーバーで、次の手順を実行して、実行ポリシーを bypass に設定します。

     a. 管理者として **64 ビット版 Windows PowerShell** コンソールを開きます。
     
     b. 「**Set-executionpolicy bypass**」と入力します。 詳細については、「[Set-ExecutionPolicy コマンドレットの使用](https://technet.microsoft.com/library/ee176961.aspx)」を参照してください。

     > [!IMPORTANT]
     > **Set-executionpolicy bypass** の設定は、64 ビット版 PowerShell コンソールでのみ使用してください。 32 ビット版 PowerShell コンソールで設定しても、スクリプトは実行されません。

## <a name="add-the-script-to-the-vmm-library"></a>VMM ライブラリへのスクリプトの追加

VMM ソース サイトが存在する場合、VMM サーバー上にスクリプトを作成できます。 また、そのスクリプトを復旧計画に含めることができます。

1. ライブラリ共有内に、新しいフォルダーを作成します。 たとえば、\<VMM サーバー名>\MSSCVMMLibrary\RPScripts を作成します。 このフォルダーを、ソース VMM サーバーとターゲット VMM サーバーに配置します。
1. スクリプトを作成します。 たとえば、スクリプトに RPScript と名前を付けます。 スクリプトが期待どおりに動作することを確認します。
1. ソース VMM サーバーとターゲット VMM サーバーの \<VMM サーバー名>\MSSCVMMLibrary フォルダーにスクリプトを配置します。

## <a name="add-the-script-to-a-recovery-plan"></a>復旧計画へのスクリプトの追加

復旧計画に VM またはレプリケーション グループを追加し、計画を作成したら、スクリプトをそのグループに追加できます。

1. 復旧計画を開きます。
1. **[ステップ]** リストで項目を選択します。 次に、**[スクリプト]** または **[手動アクション]** を選択します。
1. 選択した項目の前または後のどちらにスクリプトまたはアクションを追加するかを指定します。 スクリプト内で上下に移動するには、**[上へ移動]** ボタンと **[下へ移動]** ボタンを使用します。
1. VMM スクリプトを追加する場合は、**[Failover to VMM script (VMM へのフェールオーバー スクリプト)]** を選択します。 **[スクリプト パス]** に、共有への相対パスを入力します。 たとえば、「**\RPScripts\RPScript.PS1**」と入力します。
1. Azure Automation Runbook を追加する場合は、Runbook がある Automation アカウントを指定します。 次に、使用する Azure Runbook スクリプトを選択します。
1. 復旧計画のテスト フェールオーバーを実行して、スクリプトが期待どおりに動作することを確認します。


## <a name="next-steps"></a>次の手順
* フェールオーバーの実行については、[こちら](site-recovery-failover.md)を参照してください。

