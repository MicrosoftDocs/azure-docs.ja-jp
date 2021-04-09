---
title: Service Fabric と VS での Windows コンテナーのデバッグ
description: Visual Studio 2019 を使用して Azure Service Fabric で Windows コンテナーをデバッグする方法を説明します。
ms.topic: article
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: 3e6e7785278b182cebb21115a70f35ade52303c3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "86247253"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>方法: Visual Studio 2019 を使用して Azure Service Fabric で Windows コンテナーをデバッグする

Visual Studio 2019 では、Service Fabric サービスとしてコンテナー内の .NET アプリケーションをデバッグすることができます。 この記事では、ご利用の環境を構成し、ローカルの Service Fabric クラスターで実行されているコンテナー内の .NET アプリケーションをデバッグする方法について説明します。

## <a name="prerequisites"></a>前提条件

* Windows 10 では、このクイックスタートに従って [Windows コンテナーを実行するように Windows 10 を構成](/virtualization/windowscontainers/quick-start/quick-start-windows-10)します。
* Windows Server 2016 では、このクイックスタートに従って [Windows コンテナーを実行するように Windows 2016 を構成](/virtualization/windowscontainers/quick-start/quick-start-windows-server)します。
* 「[Windows で開発環境を準備する](./service-fabric-get-started.md)」に従って、ローカルの Service Fabric 環境を設定します。

## <a name="configure-your-developer-environment-to-debug-containers"></a>コンテナーをデバッグするように開発者環境を構成する

1. 次の手順に進む前に、Docker for Window サービスが実行されていることを確認します。

1. コンテナー間での DNS 解決をサポートするには、マシン名を使用して、ローカルの開発クラスターを設定する必要があります。 リバース プロキシ経由でサービスに対応する場合は、以下の手順も必要です。
   1. PowerShell を管理者として開きます。
   2. SDK クラスターのセットアップ フォルダー (通常は `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`) に移動します。
   3. スクリプト `DevClusterSetup.ps1` を実行する

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > `-CreateOneNodeCluster` を使用して、1 ノード クラスターを設定することができます。 既定では、ローカルの 5 ノード クラスターが作成されます。
      >

      Service Fabric の DNS サービスの詳細については、「[Azure Service Fabric の DNS サービス](./service-fabric-dnsservice.md)」を参照してください。 コンテナーで実行されているサービスから Service Fabric リバース プロキシを使用する方法の詳細については、[コンテナーで実行されているサービスに対するリバース プロキシの特別な処理](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers)に関する記事をご覧ください。

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Service Fabric でコンテナーをデバッグする場合の既知の制限事項

Service Fabric でコンテナーをデバッグする場合の既知の制限事項と考えられる解決策を以下にリストします。

* ClusterFQDNorIP の localhost を使用する場合、コンテナーでの DNS 解決はサポートされません。
    * 解決策: コンピューター名を使用して、ローカル クラスターを設定します (上記参照)。
* 仮想マシンで Windows 10 を実行している場合、コンテナーに DNS 応答が返されません。
    * 解決策: Virtual Machines NIC で UDP チェックサム オフロード (IPv4 ) を無効にします。
    * Windows 10 を実行すると、マシン上のネットワーク パフォーマンスが低下します。
    * https://github.com/Azure/service-fabric-issues/issues/1061
* アプリケーションが Docker Compose を使用してデプロイされた場合、Windows 10 では DNS サービス名を使用して同じアプリケーション内のサービスを解決できません
    * 解決策: servicename.applicationname を使用して、サービス エンドポイントを解決します。
    * https://github.com/Azure/service-fabric-issues/issues/1062
* ClusterFQDNorIP の IP アドレスを使用する場合、ホスト上のプライマリ IP を変更すると、DNS 機能が中断されます。
    * 解決策: ホスト上に新しいプライマリ IP を使用してクラスターを再作成するか、コンピューター名を使用します。 この中断は仕様です。
* ネットワーク上でクラスターの作成時に使用された FQDN を解決できない場合、DNS が失敗します。
    * 解決策: ホストのプライマリ IP を使用して、ローカル クラスターを再作成します。 このエラーは仕様です。
* コンテナーをデバッグする場合、Docker ログは、Service Fabric Explorer を含む、Service Fabric API ではなく、Visual Studio の出力ウィンドウでのみ使用できます。

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Service Fabric の Docker コンテナーで実行されている .NET アプリケーションをデバッグする

1. Visual Studio を管理者として実行します。

1. 既存の .NET アプリケーションを開くか、新しいものを作成します。

1. プロジェクトを右クリックし、**[追加]、[コンテナー オーケストレーター サポート]、[Service Fabric]** の順に選択します。

1. **F5** キーを押してアプリケーションのデバッグを開始します。

    Visual Studio では、.NET および .NET Core のコンソールと ASP.NET プロジェクト タイプがサポートされています。

## <a name="next-steps"></a>次のステップ
Service Fabric とコンテナーの機能の詳細については、[Service Fabric コンテナーの概要](service-fabric-containers-overview.md)に関するページを参照してください。
