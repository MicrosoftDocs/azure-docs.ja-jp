---
title: ホスト名の表示と変更 | Microsoft Docs
description: 名前解決のため、Azure Virtual Machines のホスト名、および Web ロールと Worker ロールを表示、変更する方法
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: ed250e3f32965fc450102fb14b93b93d6753ab3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222787"
---
# <a name="viewing-and-modifying-hostnames"></a>ホスト名の表示と変更
ロール インスタンスをホスト名で参照できるようにするには、各ロールのサービス構成ファイルでホスト名の値を設定する必要があります。 そのためには、使用するホスト名を **Role** 要素の **vmName** 属性に追加します。 **vmName** 属性の値は、各ロール インスタンスのホスト名に対するベースとして使用されます。 たとえば、**vmName** が *webrole* であり、そのロールに 3 つのインスタンスがある場合、インスタンスのホスト名は *webrole0*、*webrole1*、*webrole2* になります。 仮想マシンのホスト名は仮想マシン名に基づいて設定されるため、構成ファイルで仮想マシンのホスト名を指定する必要はありません。 Microsoft Azure サービスの構成の詳細については、「 [Azure Service Configuration Schema (.cscfg File) (Azure サービス構成スキーマ (.cscfg ファイル))](/previous-versions/azure/reference/ee758710(v=azure.100))

## <a name="viewing-hostnames"></a>ホスト名の表示
次のいずれかのツールを使用して、クラウド サービスで仮想マシンのホスト名とロール インスタンスを表示できます。

### <a name="service-configuration-file"></a>サービス構成ファイル
Azure ポータルのサービスの **[構成]** ブレードから、デプロイされているサービスのサービス構成ファイルをダウンロードできます。 その後、**Role name** 要素の **vmName** 属性で、ホスト名を確認できます。 このホスト名は各ロール インスタンスのホスト名に対するベースとして使用されることに留意してください。 たとえば、**vmName** が *webrole* であり、そのロールに 3 つのインスタンスがある場合、インスタンスのホスト名は *webrole0*、*webrole1*、*webrole2* になります。

### <a name="remote-desktop"></a>リモート デスクトップ
仮想マシンまたはロール インスタンスに対するリモート デスクトップ接続 (Windows)、Windows PowerShell リモート処理接続 (Windows)、または SSH 接続 (Linux と Windows) を有効にした後は、さまざまな方法でアクティブなリモート デスクトップ接続からホスト名を表示できます。

* コマンド プロンプトまたは SSH ターミナルで「hostname」と入力します。
* コマンド プロンプトで「ipconfig /all」と入力します (Windows のみ)。
* システム設定でコンピューター名を表示します (Windows のみ)。

### <a name="azure-service-management-rest-api"></a>Azure Service Management REST API
REST クライアントから次の手順を実行します。

1. Azure ポータルに接続するためのクライアント証明書があることを確認します。 クライアント証明書を取得するには、「[How to: Download and Import Publish Settings and Subscription Information (方法: 発行の設定とサブスクリプション情報をダウンロードしてインポートする)](/previous-versions/dynamicsnav-2013/dn385850(v=nav.70))」の手順に従ってください。 
2. x-ms-version という名前のヘッダー エントリの値を 2013-11-01 に設定します。
3. 次の形式で要求を送信します: https:\//management.core.windows.net/\<subscrition-id\>/services/hostedservices/\<service-name\>?embed-detail=true
4. 各 **RoleInstance** 要素の **HostName** 要素を検索します。

> [!WARNING]
> **InternalDnsSuffix** 要素を調べて、リモート デスクトップ セッションのコマンド プロンプトから ipconfig /all を実行して (Windows)、または SSH ターミナルから cat /etc/resolv.conf を実行して (Linux)、REST 呼び出しの応答からクラウド サービスの内部ドメイン サフィックスを表示することもできます。
> 
> 

## <a name="modifying-a-hostname"></a>ホスト名の変更
変更したサービス構成ファイルをアップロードすることにより、またはリモート デスクトップ セッションからコンピューターの名前を変更することにより、仮想マシンまたはロール インスタンスのホスト名を変更できます。

## <a name="next-steps"></a>次のステップ
[VM とロール インスタンスの名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Azure サービス構成スキーマ (.cscfg)](/previous-versions/azure/reference/ee758710(v=azure.100))

[Azure Virtual Network の構成スキーマ](/previous-versions/azure/reference/jj157100(v=azure.100))

[ネットワーク構成ファイルを使用した DNS 設定の指定](/previous-versions/azure/virtual-network/virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file)