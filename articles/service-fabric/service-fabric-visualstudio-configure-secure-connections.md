---
title: セキュリティ保護された Azure Service Fabric クラスター接続を構成する
description: Visual Studio を使用して、Azure Service Fabric クラスターでサポートされているセキュリティで保護された接続を構成する方法について説明します。
author: cawaMS
ms.topic: conceptual
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: d4d6b781d97d481793e69cf2ca97cca5b93ce432
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008533"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Visual Studio から Service Fabric クラスターにセキュリティ保護された接続を構成する
アクセス制御ポリシーが構成されている Azure Service Fabric クラスターに Visual Studio を使用して安全にアクセスする方法について説明します。

## <a name="cluster-connection-types"></a>クラスターの接続の種類
Azure Service Fabric クラスターでサポートされている接続には、**セキュリティ保護されていない** 接続と **x509 証明書に基づいて** セキュリティ保護された接続の 2 つの種類があります  (Service Fabric クラスターがオンプレミスでホストされている場合は、**Windows** 認証と **dSTS** 認証もサポートされます)。クラスターの作成中に、クラスターの接続の種類を構成する必要があります。 作成した接続の種類を後で変更することはできません。

Visual Studio Service Fabric Tools は、クラスターに接続して発行するために必要なすべての種類の認証をサポートしています。 セキュリティ保護された Service Fabric クラスターをセットアップする方法については、「 [Azure ポータルからの Service Fabric クラスターのセットアップ](service-fabric-cluster-creation-via-portal.md) 」を参照してください。

## <a name="configure-cluster-connections-in-publish-profiles"></a>発行プロファイルにクラスター接続を構成する
Visual Studio から Service Fabric プロジェクトを発行するには、**[Service Fabric アプリケーションの発行]** ダイアログ ボックスで Azure Service Fabric クラスターを選択します。 **[接続エンドポイント]** で、サブスクリプションの既存のクラスターを選択します。

![**[Service Fabric アプリケーションの発行]** ダイアログ ボックスを使用して Service Fabric 接続を構成します。][publishdialog]

**[Service Fabric アプリケーションの発行]** ダイアログ ボックスでは、クラスターの接続が自動的に検証されます。 メッセージに従って Azure アカウントにサインインします。 検証に成功した場合、システムに正しい証明書がインストールされていてクラスターに安全に接続できるか、クラスターがセキュリティ保護されていないことを意味しています。 検証に失敗した場合は、ネットワークに問題が発生しているか、システムが正しく構成されていないためにセキュリティ保護されたクラスターに接続できない可能性があります。

![**[Service Fabric アプリケーションの発行]** ダイアログ ボックスでは、既存の正しく構成された Service Fabric クラスター接続が検証されます。][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>セキュリティ保護されたクラスターに接続するには
1. 接続先のクラスターによって信頼されているクライアント証明書のいずれかにアクセスできることを確認します。 証明書は通常、Personal Information Exchange (.pfx) ファイルとして共有されています。 クライアントへのアクセスを許可するようにサーバーを構成する方法については、「 [Azure ポータルからの Service Fabric クラスターのセットアップ](service-fabric-cluster-creation-via-portal.md) 」を参照してください。
2. 信頼された証明書をインストールします。 そのためには、.pfx ファイルをダブルクリックするか、PowerShell スクリプト Import-PfxCertificate を使用して証明書をインポートします。 証明書を **Cert:\LocalMachine\My** にインストールします。 証明書のインポート時、すべての既定設定をそのまま使用して問題ありません。
3. プロジェクトのショートカット メニューで **[発行]** を選択し、**[Azure アプリケーションの公開]** ダイアログ ボックスを開いて、対象のクラスターを選択します。 自動的に接続が解決され、発行プロファイルにセキュリティ保護された接続パラメーターが保存されます。
4. 省略可能: 発行プロファイルを編集し、セキュリティで保護されたクラスター接続を指定できます。
   
   発行プロファイルの XML ファイルを手動で編集して証明書の情報を指定するため、証明書ストアの名前、ストアの場所、および証明書の拇印をメモしておいてください。 後で、証明書ストアの名前とストアの場所に対して値の指定が必要になります。 詳細については、「[方法: 証明書のサムプリントを取得する](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)」を参照してください。
   
   *ClusterConnectionParameters* パラメーターを使用して、Service Fabric クラスターに接続するときに使用する PowerShell のパラメーターを指定できます。 Connect-ServiceFabricCluster コマンドレットが受け取るパラメーターをすべて使用できます。 使用可能なパラメーターの一覧については、「 [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) 」を参照してください。
   
   リモート クラスターに発行する場合は、そのクラスターに適したパラメーターを指定する必要があります。 次は、セキュリティ保護されていないクラスターに接続する場合の例です。
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   次は、x509 証明書ベースのセキュリティ保護されたクラスターに接続する場合の例です。
   
   ```xml
   <ClusterConnectionParameters
   ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
   X509Credential="true"
   ServerCertThumbprint="0123456789012345678901234567890123456789"
   FindType="FindByThumbprint"
   FindValue="9876543210987654321098765432109876543210"
   StoreLocation="CurrentUser"
   StoreName="My" />
   ```
5. アップグレード パラメーターやアプリケーション パラメーター ファイルの場所など、他の必要な設定を編集してから、Visual Studio の **[Service Fabric アプリケーションの発行]** ダイアログ ボックスからアプリケーションを発行します。

## <a name="next-steps"></a>次のステップ
Service Fabric クラスターにアクセスする方法の詳細については、「 [Service Fabric Explorer を使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)」を参照してください。

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
