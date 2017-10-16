---
title: "Azure Stack での特権エンドポイントの使用 | Microsoft Docs"
description: "Azure Stack での特権エンドポイントの使用方法を説明します (Azure Stack オペレーター向け)。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 9769b12064216680bb1b2db8c1fd7449927c7771
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Azure Stack での特権エンドポイントの使用

*適用対象: Azure Stack 統合システムおよび Azure Stack 開発キット*

Azure Stack オペレーターは、管理ポータル、PowerShell、または Azure Resource Manager API を使用して、ほとんどの日常的な管理タスクを実行します。 ただし、あまり一般的でない一部の操作については、*特権エンドポイント*を使用する必要があります。 このエンドポイントは、あらかじめ構成されたリモート PowerShell コンソールであり、必要なタスクを実行するために十分な機能だけを提供します。 エンドポイントは PowerShell JEA (Just Enough Administration) を利用して、コマンドレットの限定的なセットのみを公開します。 特権エンドポイントにアクセスしてコマンドレットの限定的なセットを起動するために、低権限のアカウントが使用されます。 管理者アカウントは必要ありません。 セキュリティ強化のため、スクリプトは許可されません。

特権エンドポイントを使用して、次のようなタスクを実行することができます。

- [診断ログの収集](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool)などの低レベル タスクを実行するため。
- デプロイ後の Domain Name System (DNS) フォワーダーの追加、Graph 統合のセットアップ、Active Directory Federation Services (AD FS) 統合、証明書ローテーションなど、統合システムのための多くのデプロイ後データセンター統合タスクを実行するため。
- 統合システムの詳細なトラブルシューティングのために、サポート部門と連携して一時的な高レベル アクセスを取得するため。 

特権エンドポイントでは、PowerShell セッションで実行するすべてのアクション (および、それに対応する出力) がログに記録されます。 これにより、完全な透明性と操作の完全な監査が提供されます。 これらのログ ファイルは将来の監査目的のために保持できます。

> [!NOTE]
> Azure Stack Development Kit (ASDK) では、特権エンドポイントで利用可能なコマンドの一部を、開発キットのホスト上の PowerShell セッションから直接実行できます。 ただし、これは統合システム環境で特定の操作を実行するために利用可能な唯一の手段であるため、ログ収集など、特権エンドポイントを使用した一部の操作をテストすることが必要な場合があります。

## <a name="access-the-privileged-endpoint"></a>特権エンドポイントへのアクセス

特権エンドポイントには、特権エンドポイントをホストする仮想マシン上のリモート PowerShell セッションを介してアクセスします。 ASDK では、この仮想マシンの名前は AzS-ERCS01 です。 統合システムを使用している場合、権限エンドポイントの 3 つのインスタンスがあり、それぞれ異なるホスト上の仮想マシン (*Prefix*-ERCS01、*Prefix*-ERCS02、または *Prefix*-ERCS03) 内で動作することで、回復性を確保しています。 

統合システムに対してこの手順を開始する前に、IP アドレスによって、または DNS を介して権限エンドポイントにアクセスできることを確認してください。 Azure Stack の初期デプロイ後は、DNS 統合がまだセットアップされていないため、IP アドレスでしか権限エンドポイントにアクセスできません。 権限エンドポイントの IP アドレス を含む "AzureStackStampDeploymentInfo" という名前の JSON ファイルが、OEM ハードウェア ベンダーから提供されます。

権限エンドポイントへの接続は、ハードウェア ライフサイクル ホストから、または [Privileged Access Workstation](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) のような専用のロックダウンされたコンピューターからに限定して行うことをお勧めします。

1. 環境に応じて、次のいずれかを行います。

    - 統合システムでは、ハードウェア ライフサイクル ホストまたは Privileged Access Workstation 上で次のコマンドを実行して、信頼されたホストとして権限エンドポイントを追加します。

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - ADSK を実行している場合、開発キットのホストにサインインします。

2. ハードウェア ライフサイクル ホストまたは Privileged Access Workstation 上で、昇格した Windows PowerShell セッションを開きます。 次のコマンドを実行して、権限エンドポイントをホストする仮想マシン上でリモート セッションを確立します。
 
    - 統合システム上で:
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      `ComputerName` パラメーターは、権限エンドポイントをホストする仮想マシンの 1 台の IP アドレスまたは DNS 名のどちらかです。 
    - ADSK を実行している場合:
     
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName azs-ercs01`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   入力を求められたら、次の資格情報を使用します。

      - **ユーザー名**: **&lt;*Azure Stack ドメイン*&gt;\cloudadmin** の形式で CloudAdmin アカウントを指定します。 (ASDK の場合、ユーザー名は **azurestack\cloudadmin** です。)
      - **パスワード**: インストール中に AzureStackAdmin ドメイン管理者アカウントのパスワードとして指定したものと同じパスワードを入力します。
    
3.  接続後、環境に応じて「**[*IP address or ERCS VM name*]: PS>**」または「**[azs-ercs01]: PS>**」にプロンプトが変わります。 ここから `Get-Command` を実行して、利用可能なコマンドレットの一覧を表示します。

    ![利用可能なコマンドの一覧を示す Get-Command コマンドレット出力](media/azure-stack-privileged-endpoint/getcommandoutput.png)

    これらのコマンドレットの多くは、統合システム環境での使用のみが意図されています (データセンター統合に関連するコマンドレットなど)。 ASDK では、次のコマンドレットが検証済みです。

    - Clear-Host
    - Close-PrivilegedEndpoint
    - Exit-PSSession
    - Get-AzureStackLog
    - Get-AzureStackStampInformation
    - Get-Command
    - Get-FormatData
    - Get-Help
    - Get-ThirdPartyNotices
    - Measure-Object
    - New-CloudAdminUser
    - Out-Default
    - Remove-CloudAdminUser
    - Select-Object
    - Set-CloudAdminUserPassword
    - Stop-AzureStack
    - Get-ClusterLog

4.  スクリプトは許可されていないため、パラメーター値のタブ補完は使用できません。 特定のコマンドレットについてパラメーターの一覧を取得するには、次のコマンドを実行します。

    ````PowerShell
    Get-Command <cmdlet_name> -Syntax
    ```` 
    何らかの種類のスクリプト操作を試みると、エラー **ScriptsNotAllowed** で操作は失敗します。 これは正しい動作です。

## <a name="close-the-privileged-endpoint-session"></a>特権エンドポイント セッションを閉じる

 前述のとおり、特権エンドポイントでは、PowerShell セッションで実行するすべてのアクション (および、それに対応する出力) がログに記録されます。 `Close-PrivilegedEndpoint` コマンドレットを使用してセッションを閉じる必要があります。 このコマンドレットは、エンドポイントを正しく閉じて、ログ ファイルを保管用の外部ファイル共有に転送します。

エンドポイント セッションを閉じるには:

1. 特権エンドポイントからアクセス可能な外部ファイル共有を作成します。 開発キット環境では、開発キットのホスト上にファイル共有を作成することができます。
2. `Close-PrivilegedEndpoint` コマンドレットを実行します。 
3. トランスクリプト ログ ファイルを保存するパスの指定を求められます。 作成済みのファイル共有を &#92;&#92;*servername*&#92;*sharename* の形式で指定します。 パスを指定しない場合、コマンドレットは失敗し、セッションは開いたままです。 

    ![トランスクリプトの保存先パスとして指定した場所を示す Close-PrivilegedEndpoint コマンドレットの出力](media/azure-stack-privileged-endpoint/closeendpoint.png)

トランスクリプト ログ ファイルがファイル共有に正常に転送された後、それらのファイルは特権エンドポイントから自動的に削除されます。 コマンドレット `Exit-PSSession` または `Exit` を使用して特権エンドポイント セッションを閉じた、または単に PowerShell コンソールを閉じた場合、トランスクリプト ログはファイル共有に転送されません。 それらは特権エンドポイントに残ります。 次に `Close-PrivilegedEndpoint` を実行してファイル共有をインクルードしたときに、以前のセッションのトランスクリプト ログも併せて転送されます。

## <a name="next-steps"></a>次のステップ
[Azure Stack の診断ツール](azure-stack-diagnostics.md)







