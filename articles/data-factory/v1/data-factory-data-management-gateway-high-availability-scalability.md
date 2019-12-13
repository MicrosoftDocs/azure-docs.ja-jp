---
title: Azure Data Factory のデータ管理ゲートウェイによる高可用性
description: この記事では、ノードを追加することでデータ管理ゲートウェイをスケールアウトしたり、1 つのノードで実行できる同時実行ジョブ数を増やすことでスケールアップしたりできる方法について説明します。
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 25dbb01a4b018a51390be664472aceadea0a9524
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932021"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Data Management Gateway - 高可用性とスケーラビリティ (プレビュー)
> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 Data Factory サービスの現在のバージョンを使用している場合は、[セルフホステッド IR](../create-self-hosted-integration-runtime.md) に関するページを参照してください。 


この記事を参照し、Data Management Gateway/Integration を使用して可用性と拡張性の高いソリューションを構成できます。    

> [!NOTE]
> この記事では、Integration Runtime (初期の Data Management Gateway) の基本を理解していることを前提とします。 まだ理解が不十分な場合は、「[Data Management Gateway](data-factory-data-management-gateway.md)」をご覧ください。
> 
> **このプレビュー機能は、Data Management Gateway バージョン 2.12.xxxx.x 以降で正式にサポートされています**。 バージョン 2.12.xxxx.x またはそれ以降を使用していることを確認してください。 Data Management Gateway の最新バージョンは、[こちら](https://www.microsoft.com/download/details.aspx?id=39717)からダウンロードできます。

## <a name="overview"></a>概要
ポータルから、1 つの論理ゲートウェイを複数のオンプレミス コンピューターにインストールされているデータ管理ゲートウェイと関連付けることができます。 これらのコンピューターは、**ノード**と呼ばれます。 最大で **4 つのノードを**を 1 つの論理ゲートウェイに関連付けできます。 1 つの論理ゲートウェイと複数のノード (ゲートウェイがインストールされているオンプレミス コンピューター) を関連付ける利点は次のとおりです。  

- オンプレミスとクラウドのデータ ストア間で、データ移動のパフォーマンスが向上する。  
- 何らかの理由によってノードの 1 つがダウンした場合、データの移動に他のノードを引き続き使用できる。 
- メンテナンスのためにノードの 1 つをオフラインにする必要がある場合、データの移動に他のノードを引き続き使用できる。

また、オンプレミスとクラウドのデータ ストア間のデータ移動の機能をスケールアップするために、1 つのノードで実行できる**同時実行のデータ移動ジョブ**の数を構成することもできます。 

これらのノードの状態は、Azure Portal を使用して監視できます。これを活用して、論理ゲートウェイから 1 つのノードを追加または削除するかどうかを決定します。 

## <a name="architecture"></a>アーキテクチャ 
次の図は、Data Management Gateway のスケーラビリティと可用性の機能について、アーキテクチャの概要を示しています。 

![Data Management Gateway - 高可用性とスケーラビリティ](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

**論理ゲートウェイ**は、Azure Portal でデータ ファクトリに追加するゲートウェイです。 以前は、Data Management Gateway がインストールされているオンプレミスの Windows コンピューター を1 つだけ、論理ゲートウェイと関連付けることができました。 このオンプレミスのゲートウェイ コンピューターは、ノードと呼ばれます。 現在は、最大で **4 つの物理ノード**を論理ゲートウェイと関連付けできます。 複数のノードを備えた論理ゲートウェイは、**マルチノード ゲートウェイ**と呼ばれます。  

これらのノードはすべて、**アクティブ**です。 すべてのノードが、オンプレミスとクラウドのデータ ストア間でデータを移動するためのデータ移動ジョブを処理できます。 ノードの 1 つは、ディスパッチャーとワーカーの両方として機能します。 グループ内のその他のノードは、ワーカー ノードです。 **ディスパッチャー** ノードは、データ移動タスク/ジョブをクラウド サービスからプルして、ワーカー ノード (自身を含む) にディスパッチします。 **ワーカー**ノードは、オンプレミスとクラウドのデータ ストア間でデータを移動するためのデータ移動ジョブを実行できます。 すべてのノードがワーカーです。 1 つのノードだけが、ディスパッチとワーカーの両方を担うことができます。    

通常は 1 つのノードから開始して、既存のノードがデータ移動の負荷によって圧迫されたら、さらにノードを追加して**スケールアウト**します。 また、ノードでの実行を許可されている同時実行ジョブの数を増やして、ゲートウェイ ノードのデータ移動の機能を**スケールアップ**することもできます。 この機能は、単一ノードのゲートウェイでも利用できます (スケーラビリティおよび可用性の機能が無効になっている場合でも、利用できます)。 

複数のノードを持つゲートウェイでは、すべてのノード間でデータ ストアの資格情報を継続的に同期しています。 ノード間の接続に問題がある場合、資格情報が同期できなくなる可能性があります。ゲートウェイを使用しているオンプレミスのデータ ストアに対して資格情報を設定すると、ディスパッチャー/ワーカー ノードで資格情報が保存されます。 ディスパッチャー ノードは、他のワーカー ノードと同期しています。 このプロセスは、**資格情報の同期**と呼ばれます。ノード間の通信チャネルは、パブリック SSL/TLS 証明書によって**暗号化**できます。 

## <a name="set-up-a-multi-node-gateway"></a>マルチノード ゲートウェイの設定
このセクションでは、次の 2 つの記事に目を通し、これらの記事の概念を理解していることを前提とします。 

- [Data Management Gateway](data-factory-data-management-gateway.md) に関する記事 - ゲートウェイの概要を丁寧に説明しています。
- [オンプレミスとクラウドのデータ ストア間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事 - 単一のノードでゲートウェイを使用するための詳細な手順を紹介したチュートリアルを含みます。  

> [!NOTE]
> オンプレミスの Windows コンピューターにデータ管理ゲートウェイをインストールする前に、[この記事のメイン ページ](data-factory-data-management-gateway.md#prerequisites)に示されている前提条件を確認してください。

1. [チュートリアル](data-factory-move-data-between-onprem-and-cloud.md#create-gateway)では、論理ゲートウェイの作成時に、**高可用性とスケーラビリティ**の機能を有効にしてください。 

    ![Data Management Gateway - 高可用性とスケーラビリティの有効化](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. **[構成]** ページで、**高速セットアップ**または**手動セットアップ** のいずれかのリンクを使用して、最初のノード (オンプレミスの Windows コンピューター) にゲートウェイをインストールします。

    ![Data Management Gateway - 高速または手動セットアップ](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > 高速セットアップ オプションを使用する場合、ノード間の通信は暗号化せずに行われます。 ノード名は、コンピューター名と同じです。 ノード間通信を暗号化する必要がある場合、またはノード名を自分で指定したい場合は、手動セットアップを使用してください。 後からノード名を編集することはできません。
3. **高速セットアップ**を選択した場合 :
    1. ゲートウェイが正常にインストールされると、次のメッセージが表示されます。

        ![Data Management Gateway - 高速セットアップに成功](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. [こちらの手順](data-factory-data-management-gateway.md#configuration-manager)に従って、ゲートウェイのデータ管理構成マネージャーを起動します。 ゲートウェイの名前、ノード名、状態などを確認します。

        ![Data Management Gateway - インストール成功](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. **手動セットアップ**を選択した場合 :
    1. Microsoft ダウンロード センターからインストール パッケージをダウンロードして実行し、お使いのコンピューターにゲートウェイをインストールします。
    2. **[構成]** ページから、**認証キー**を使用してゲートウェイを登録します。
    
        ![Data Management Gateway - インストール成功](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. **[新しいゲートウェイ ノード**] ページで、ゲートウェイ ノードに対してカスタムの**名前**を指定できます。 既定では、ノード名はコンピューター名と同じです。    

        ![Data Management Gateway - 名前の指定](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. 次のページで、**ノード間通信の暗号化を有効にする**かどうかを選択できます。 暗号化を無効にする (既定値) 場合は、 **[スキップ]** をクリックします。

        ![Data Management Gateway - 暗号の有効化](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > 暗号化モードの変更は、論理ゲートウェイにあるゲートウェイ ノードが単一の場合にのみ、サポートされます。 ゲートウェイに複数のノードがある場合に暗号化モードを変更するには、1 つのノード以外のすべてのノードを削除し、暗号化モードを変更してからノードを再追加するという手順になります。
        > 
        > TLS/SSL 証明書を使用するための要件一覧については、「[TLS/SSL 証明書の要件](#tlsssl-certificate-requirements)」セクションをご覧ください。 
    5. ゲートウェイが正常にインストールされたら、[構成マネージャーの起動] をクリックします。
    
        ![手動セットアップ - 構成マネージャーの起動](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. ノード (オンプレミス Windows コンピューター) の Data Management Gateway 構成マネージャーを確認します。接続状態、**ゲートウェイ名**、および**ノード名**が表示されます。  

        ![Data Management Gateway - インストール成功](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Azure VM でゲートウェイをプロビジョニングしている場合は、[こちらの Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway)を使用できます。 このスクリプトは、論理ゲートウェイを作成し、Data Management Gateway ソフトウェアがインストールされている VM を設定し、論理ゲートウェイと共に VM を登録します。 
6. Azure Portal で、 **[ゲートウェイ]** ページを起動します。 
    1. ポータルにあるデータ ファクトリ ホーム ページで、 **[リンクされたサービス]** をクリックします。
    
        ![データ ファクトリのホーム ページ](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. **ゲートウェイ**を選択して、 **[ゲートウェイ]** ページを表示します。
    
        ![データ ファクトリのホーム ページ](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. 次のような **[ゲートウェイ]** ページが表示されます。   

        ![単一のノードを備えたゲートウェイのビュー](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. ツール バーの **[ノードの追加]** をクリックして、論理ゲートウェイにノードを追加します。 高速セットアップを使用する計画の場合は、ゲートウェイにノードとして追加されるオンプレミス コンピューターから、この手順を実行します。 

    ![Data Management Gateway - ノードの追加メニュー](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. 手順は、最初のノードの設定とほぼ同じです。 手動インストール オプションを選択した場合は、構成マネージャーの UI を使用してノード名を設定できます。 

    ![構成マネージャー - 2 番目のゲートウェイのインストール](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. ゲートウェイがノードに正常にインストールされると、構成マネージャー ツールに次の画面が表示されます。  

    ![構成マネージャー - 2 番目のゲートウェイを正常にインストール](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. ポータルで **[ゲートウェイ]** ページを開くと、現時点では 2 つのゲートウェイ ノードが表示されます。 

    ![ポータルで表示されるノードが 2 つのゲートウェイ](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. ゲートウェイ ノードを削除するには、ツールバーで **[ノードの削除]** をクリックして、削除するノードを選択し、ツールバーから **[削除]** をクリックします。 この操作によって、選択したノードがグループから削除されます。 この操作によって、データ管理ゲートウェイ ソフトウェアはノード (オンプレミス Windows コンピューター) からアンインストールされないことに注意してください。 ゲートウェイをアンインストールするには、オンプレミスのコントロール パネルで **[プログラムの追加と削除]** を使用します。 ノードからゲートウェイをアンインストールすると、ポータルでは自動的に削除されます。   

## <a name="upgrade-an-existing-gateway"></a>既存のゲートウェイのアップグレード
既存のゲートウェイをアップグレードして、高可用性とスケーラビリティの機能を使用することができます。 この機能は、バージョン 2.12.xxxx 以降のデータ管理ゲートウェイを備えたノードだけで動作します。 コンピューターにインストールされているデータ管理ゲートウェイのバージョンは、Data Management Gateway 構成マネージャーの **[ヘルプ]** タブで確認できます。 

1. 手順に従い、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=39717)から MSI セットアップ パッケージをダウンロードして実行することで、オンプレミス コンピューターのゲートウェイを最新バージョンに更新します。 詳細については、[インストール](data-factory-data-management-gateway.md#installation)のセクションをご覧ください。  
2. Azure Portal に移動します。 お使いのデータ ファクトリの **[データ ファクトリ] ページ**を起動します。 リンクされたサービス タイルをクリックして、 **[リンクされたサービス] ページ**を起動します。 ゲートウェイを選択して、 **[ゲートウェイ] ページ**を起動します。 次の画像に示すように、 **[プレビュー機能]** を有効にします。 

    ![Data Management Gateway - プレビュー機能の有効化](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. ポータルでプレビュー機能を有効にしたら、すべてのページを閉じます。 **[ゲートウェイ] ページ**をもう一度開き、新しいプレビューのユーザー インターフェイス (UI) を表示します。
 
    ![Data Management Gateway - プレビュー機能の有効化に成功](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Data Management Gateway - UI のプレビュー](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > アップグレード時は、最初のノード名はコンピューターの名前です。 
3. ノードを 1 つ追加します。 **[ゲートウェイ]** ページで **[ノードの追加]** をクリックします。  

    ![Data Management Gateway - ノードの追加メニュー](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    前のセクションの手順に従って、ノードを設定します。 

### <a name="installation-best-practices"></a>インストールのベスト プラクティス

- コンピューターが休止状態にならないように、ゲートウェイ用のホスト コンピューターの電源プランを構成します。 ホスト コンピューターが休止状態の場合、ゲートウェイはデータ要求に応答しません。
- ゲートウェイに関連付けられている証明書をバックアップします。
- 最適なパフォーマンスを得るために、すべてのノードの構成をほぼ同じにします (推奨) 。 
- 高可用性を確保するために、少なくとも 2 つのノードを追加します。  

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL 証明書の要件
統合ランタイム ノード間の通信を保護するために使用される TLS/SSL 証明書の要件を次に示します。

- 証明書は必ず、公的に信頼されている X509 v3 証明書とします。 公的 (第三者) 証明機関 (CA) によって発行された証明書を使用することを推奨します。
- 統合ランタイムの各ノードは、資格情報マネージャー アプリケーションを実行するクライアント マシンだけでなく、この証明書を信頼する必要があります。 
  > [!NOTE]
  > Copy Wizard/ Azure Portal から資格情報を安全に設定するとき、資格情報マネージャー アプリケーションが使用されます。 また、オンプレミス/プライベート データ ストアと同じネットワークにあるあらゆるマシンから起動できます。
- ワイルドカード証明書がサポートされます。 FQDN 名が **node1.domain.contoso.com** の場合、証明書のサブジェクト名として * **.domain.contoso.com** を使用できます。
- サブジェクトの別名の最後の項目のみが使用され、現行の制限に起因してその他はすべて無視されるため、SAN 証明書は推奨されません。 例: SAN が **node1.domain.contoso.com** と **node2.domain.contoso.com** の SAN 証明書がある場合、FQDN が **node2.domain.contoso.com** のマシンでのみこの証明書を使用できます。
- SSL 証明書のために、Windows Server 2012 R2 でサポートされている任意のキー サイズをサポートします。
- CNG キーを使用する証明書はサポートされていません。

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>よくあるご質問: どのようなときにこの暗号化を有効にしないほうがよいでしょうか。
暗号化を有効にすると、(公開証明書を持つ) インフラストラクチャの特定のコストを増やします。そのため、以下の場合、暗号化を有効にしないことがあります。
- 信頼されているネットワークまたは IP/SEC のような透過的暗号化のあるネットワークで統合ランタイムが実行されているとき。 このチャネル通信は信頼されているネットワーク内に制限されるため、追加の暗号化は必要ではないことがあります。
- 統合ランタイムが運用環境で実行されていないとき。 TLS/SSL 証明書コストの削減に役立ちます。


## <a name="monitor-a-multi-node-gateway"></a>マルチノード ゲートウェイの監視
### <a name="multi-node-gateway-monitoring"></a>マルチノード ゲートウェイを監視する
Azure Portal では、ゲートウェイ ノードの状態と共に、各ノードでのリソース使用率 (CPU、メモリ、ネットワーク(入力/出力) など) のほぼリアルタイムのスナップショットを表示できます。 

![Data Management Gateway - マルチノードの監視](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

**[ゲートウェイ]** ページの **[高度な設定]** を有効にすると、 **[ネットワーク]** (入力/出力)、 **[ロールと資格情報のステータス]** (ゲートウェイの問題のデバッグに役立ちます)、および **[同時実行ジョブ]** (実行中/制限) (パフォーマンス調整時に、状況に応じて変更できます) などの高度なメトリックを確認できます。 次の表に、**ゲートウェイ ノード**一覧の列の説明を示します。  

監視のプロパティ | 説明
:------------------ | :---------- 
名前 | ゲートウェイに関連付けられている論理ゲートウェイとノードの名前です。  
Status | 論理ゲートウェイとゲートウェイ ノードの状態です 例:オンライン、オフライン、制限あり、など。これらの状態の詳細については、「[ゲートウェイの状態](#gateway-status)」セクションをご覧ください。 
Version | 論理ゲートウェイと各ゲートウェイ ノードのバージョンを示します。 論理ゲートウェイのバージョンは、グループ内のノードで多数を占めるバージョンに基づいて決定されます。 論理ゲートウェイの設定の中にさまざまなバージョンのノードがある場合は、論理ゲートウェイと同じバージョン番号を持つノードのみが適切に機能します。 それ以外のノードは制限モードであり、手動で更新する必要があります (自動更新に失敗する場合のみ)。 
使用可能なメモリ | ゲートウェイ ノードで使用可能なメモリです。 この値は、ほぼリアルタイムのスナップショットです。 
CPU 使用率 | ゲートウェイ ノードの CPU 使用率。 この値は、ほぼリアルタイムのスナップショットです。 
ネットワーク (入力/出力) | ゲートウェイ ノードのネットワーク使用率。 この値は、ほぼリアルタイムのスナップショットです。 
同時実行ジョブ (実行中/制限) | 各ノードで実行されるタスクまたはジョブの数。 この値は、ほぼリアルタイムのスナップショットです。 上限は、各ノードの最大の同時実行ジョブ数を表します。 この値は、マシンのサイズに基づいて定義されます。 CPU/ メモリ/ ネットワークは使用率を超えておらず、アクティビティがタイムアウトになっている高度なシナリオでは、上限を引き上げて、同時実行ジョブの実行回数をスケールアップできます。この機能は、単一ノードのゲートウェイでも利用できます (スケーラビリティおよび可用性の機能が無効になっている場合でも、利用できます)。 詳細については、「[スケールに関する考慮事項](#scale-considerations)」セクションをご覧ください。 
役割 | ディスパッチャーとワーカーという 2 つのタイプの役割があります。 ノードはすべてワーカーであり、全部がジョブの実行に使用できることを意味します。 ディスパッチャー ノードは 1 つだけです。このノードは、クラウド サービスからタスク/ジョブをプルし、異なるワーカー ノード (自身を含む) にディスパッチするために使用されます。 

![Data Management Gateway - 高度なマルチノード監視](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>ゲートウェイの状態

次の表は、**ゲートウェイ ノード**の状態を示しています。 

Status  | コメント/シナリオ
:------- | :------------------
オンライン | ノードはデータ ファクトリ サービスに接続されています。
オフライン | ノードはオフラインです。
アップグレード中 | ノードは自動でアップデートされています。
制限あり | 接続の問題に起因します。 HTTP ポート 8050 の問題、サービス バス接続の問題、または資格情報の同期の問題に起因する可能性があります。 
非アクティブ | ノードは、多数を占める他のノードの構成とは違う構成になっています。<br/><br/> 他のノードに接続できない場合、ノードが非アクティブになることがあります。 


次の表は、**論理ゲートウェイ**の状態を示しています。 ゲートウェイの状態は、ゲートウェイ ノードの状態に依存します。 

Status | 説明
:----- | :-------
Needs Registration\(登録が必要\) | この論理ゲートウェイには、まだ登録されたノードがありません。
オンライン | ゲートウェイ ノードはオンラインです。
オフライン | オンライン状態のノードはありません。
制限あり | このゲートウェイに、正常な状態ではないノードが含まれています。 この状態は、一部のノードがダウンしている可能性を警告します。 <br/><br/>ディスパッチャー/ワーカー ノードでは、資格情報の同期の問題に起因する可能性があります。 

### <a name="pipeline-activities-monitoring"></a>パイプライン/ アクティビティの監視
Azure Portal では、パイプライン監視のエクスペリエンスにより細かなノード レベルの詳細を提供しています。 たとえば、どのアクティビティがどのノードで実行されたかを示します。 この情報は、ネットワーク調整に起因すると言われる、特定のノードでのパフォーマンスの問題を把握するうえで役立つ場合があります。 

![Data Management Gateway - パイプラインのマルチノード監視](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Data Management Gateway - パイプラインの詳細](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>スケールに関する考慮事項

### <a name="scale-out"></a>スケールアウト
**使用可能なメモリが低減**しており、**CPU 使用率が高い**場合、新しいノードを追加すると、コンピューター全体の負荷をスケールアウトできます。 タイムアウトまたはゲートウェイ ノードがオフライン状態であることに起因してアクティビティが失敗している場合は、ノードをゲートウェイに追加すると解消に役立ちます。
 
### <a name="scale-up"></a>スケールアップ
使用可能なメモリと CPU が効果的に活用されていないが、アイドル容量は 0 になっている場合は、ノードで実行できる同時実行ジョブの数を増やすことで、スケールアップを行うことができます。 また、ゲートウェイが過負荷になっているために、アクティビティがタイムアウトしている場合も、スケールアップを検討します。 次の画像に示すように、ノードの最大容量を増やすことができます。 最初は容量を 2 倍にすることをお勧めします。  

![Data Management Gateway - スケールに関する考慮事項](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>既知の問題/重大な変更点

- 現時点では、1 つの論理ゲートウェイに対して、最大で 4 つの物理ゲートウェイ ノードを保持できます。 パフォーマンス上の理由から 4 つを上回る数のノードが必要になる場合は、[ DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com)へ電子メールを送信します。
- 現在の論理ゲートウェイから切り替えるために、別の論理ゲートウェイの認証キーを使ってゲートウェイ ノードを再登録することはできません。 再登録するには、ノードからゲートウェイをアンインストールし、ゲートウェイを再インストールして、他の論理ゲートウェイの認証キーを使って登録します。 
- すべてのゲートウェイ ノードに対して HTTP プロキシが必要な場合、プロキシを diahost.exe.config および diawp.exe.config に設定し、サーバー マネージャーを使用して必ずすべてのノードが同じ diahost.exe.config および diawip.exe.config を保持するようにします。詳細については、[プロキシ設定の構成](data-factory-data-management-gateway.md#configure-proxy-server-settings)に関するセクションをご覧ください。 
- ゲートウェイ構成マネージャーでノード間通信の暗号化モードを変更するには、ポータルで、1 つ以外のすべてのノードを削除します。 その後、暗号化モードを変更してから、ノードを追加します。
- ノード間の通信チャネルを暗号化する場合は、公式の SSL 証明書を使用します。 自己署名証明書は、他のコンピューターの証明機関一覧で同じ証明書を信頼できない場合があるため、接続の問題を引き起こす可能性があります。 
- ノードのバージョンが論理ゲートウェイのバージョンよりも低い場合、論理ゲートウェイにゲートウェイ ノードを登録することはできません。 低いバージョンのノード(ダウングレード) を登録できるように、ポータルから論理ゲートウェイのすべてのノードを削除します。 論理ゲートウェイのすべてのノードを削除した場合は、その論理ゲートウェイに新しいノードを手動でインストールして登録してください。 この場合、高速セットアップはサポートされません。
- まだクラウドの資格情報を使用している既存の論理ゲートウェイに、高速セットアップを使用してノードをインストールすることはできません。 ゲートウェイ構成マネージャーで資格情報が格納された場所は、[設定] タブで確認できます。
- ノード間の暗号化が有効になっている既存の論理ゲートウェイに、高速セットアップを使用してノードをインストールすることはできません。 暗号化モードの設定には手動での証明書の追加を伴うため、高速でのインストールは選択できません。 
- オンプレミス環境からのファイル コピーの場合、ローカルホストまたはローカル ドライブは、すべてのノードからアクセス可能ではなくなるため、\\localhost または C:\files は使用できなくなります。 代わりに、\\ServerName\files をファイルの場所の指定に使用します。


## <a name="rolling-back-from-the-preview"></a>プレビューからのロールバック 
プレビューからロールバックするには、1 つ以外のすべてのノードを削除します。 どのノードを削除するかは関係ありませんが、必ず論理ゲートウェイ内に少なくとも 1 つのノードを保持します。 コンピューター上のゲートウェイをアンインストールするか、または Azure Portal を使用して、ノードを削除することができます。 Azure Portal の **[データ ファクトリ]** ページで、サービスのリンクをクリックして、 **[リンクされたサービス]** ページを起動します。 ゲートウェイを選択して、 **[ゲートウェイ]** ページを起動します。 [ゲートウェイ] ページで、ゲートウェイに関連付けられているノードを確認できます。 このページで、ゲートウェイからノードを削除できます。
 
削除した後、同じ Azure Portal ページの**プレビュー機能**をクリックして、プレビュー機能を無効にします。 ゲートウェイはリセットされ、1 つのノード GA (一般提供) ゲートウェイになりました。


## <a name="next-steps"></a>次の手順
以下の記事を確認してください。
- [Data Management Gateway](data-factory-data-management-gateway.md) に関する記事 - ゲートウェイの概要を丁寧に説明しています。
- [オンプレミスとクラウドのデータ ストア間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事 - 単一のノードでゲートウェイを使用するための詳細な手順を紹介したチュートリアルを含みます。 
