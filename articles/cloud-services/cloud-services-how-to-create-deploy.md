---
title: "クラウド サービスを作成してデプロイする方法 | Microsoft Docs"
description: "Azure の簡易作成の方法によりクラウド サービスを作成してデプロイする方法について説明します。"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 0ea78ccc-5e7d-40f8-bdb6-478c0eb0e265
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 0b404af5b638ec2d543ce98b562b7df538652f70
ms.openlocfilehash: 71863e52dca951b1fc3847e1bba47977a6e49c7c
ms.lasthandoff: 01/05/2017


---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>クラウド サービスを作成してデプロイする方法
> [!div class="op_single_selector"]
> * [Azure ポータル](cloud-services-how-to-create-deploy-portal.md)
> * [Azure クラシック ポータル](cloud-services-how-to-create-deploy.md)
> 
> 

Azure クラシック ポータルには、クラウド サービスを作成してデプロイする方法として、"**簡易作成**" と "**カスタム作成**" の&2; つの方法が用意されています。

このトピックでは、簡易作成の方法を使って新しいクラウド サービスを作成し、その後、 **[アップロード]** を使用して Azure にクラウド サービス パッケージをアップロードしてデプロイする方法について説明します。 この方法を使うと、Azure クラシック ポータルに、必要な事項をすべて完了するのに便利なリンクが操作の進行につれて表示されます。 クラウド サービスの作成時にデプロイする準備が整っている場合は、 **[カスタム作成]**を使用して作成とデプロイを同時に実行できます。

> [!NOTE]
> Visual Studio Team Services (VSTS) からクラウド サービスを発行する予定の場合は、[簡易作成] を使用した後、 **[クイック スタート]** またはダッシュボードから VSTS 発行を設定する必要があります。 詳細については、「[Visual Studio Team Services を使用した Azure への継続的な配信][TFSTutorialForCloudService]」を参照するか、**[クイック スタート]** ページのヘルプを参照してください。
> 
> 

## <a name="concepts"></a>概念
Azure のクラウド サービスとしてアプリケーションをデプロイするには、3 つのコンポーネントが必要です。

* **サービス定義**  
  クラウド サービス定義ファイル (.csdef) では、ロールの数などのサービス モデルを定義します。
* **サービス構成**  
  クラウド サービス構成ファイル (.cscfg) では、ロール インスタンスの数など、クラウド サービスと個々のロールの構成設定を指定します。
* **サービス パッケージ**  
  サービス パッケージ (.cspkg) には、アプリケーション コード、構成、サービス定義ファイルが含まれます。

[ここ](cloud-services-model-and-package.md)では、これらのコンポーネントとパッケージを作成する方法の詳細について説明します。

## <a name="prepare-your-app"></a>アプリケーションの準備
クラウド サービスを展開する前に、アプリケーション コードとクラウド サービス構成ファイル (.cscfg) からクラウド サービス パッケージ (.cspkg) を作成する必要があります。 Azure SDK には、こういった必須のデプロイ ファイルを準備するためのツールが用意されています。 SDK は、 [Azure のダウンロード](https://azure.microsoft.com/downloads/) ページからアプリケーション コードの開発に使用する言語でインストールできます。

サービス パッケージをエクスポートする前に、以下の&3; つのクラウド サービス機能について特別な構成が必要です。

* データ暗号化のために Secure Sockets Layer (SSL) を使用するクラウド サービスをデプロイする場合は、 [アプリケーションを SSL 用に構成](cloud-services-configure-ssl-certificate.md#step-2-modify-the-service-definition-and-configuration-files) します。
* ロール インスタンスに対するリモート デスクトップ接続を構成する場合は、リモート デスクトップ用に [ロールを構成](cloud-services-role-enable-remote-desktop.md) します。
* クラウド サービスの詳細監視を構成する場合は、クラウド サービスの Azure 診断を有効にします。 *最小監視* (既定の監視レベル) では、ロール インスタンス (仮想マシン) のホスト オペレーティング システムから収集したパフォーマンス カウンターが使用されます。 詳細監視は、ロール インスタンス内のパフォーマンス データに基づいて追加のメトリックを収集して、アプリケーション処理時に発生する問題を詳しく分析できます。 Azure 診断を有効にする方法については、「 [Enabling Diagnostics in Azure (Azure における診断の有効化)](cloud-services-dotnet-diagnostics.md)」を参照してください。

Web ロールまたは worker ロールのデプロイを伴うクラウド サービスを作成するには、 [サービス パッケージを作成する](cloud-services-model-and-package.md#servicepackagecspkg)必要があります。

## <a name="before-you-begin"></a>開始する前に
* Azure SDK をまだインストールしていない場合は、 **[Azure SDK のインストール]** をクリックして [Azure のダウンロード ページ](https://azure.microsoft.com/downloads/)を開き、アプリケーション コードの開発に使用する言語用の SDK をダウンロードします。 (これは後でも実行する機会があります)。
* 証明書を必要とするロール インスタンスがある場合は、証明書を作成します。 クラウド サービスには、秘密キーのある .pfx ファイルが必要です。 クラウド サービスを作成してデプロイするときに、 [証明書を Azure にアップロード](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) できます。
* クラウド サービスをアフィニティ グループにデプロイすることを予定している場合は、アフィニティ グループを作成します。 アフィニティ グループを使用すると、自分のクラウド サービスおよびその他の Azure サービスをリージョンの同じ場所にデプロイすることができます。 アフィニティ グループは、Azure クラシック ポータルの **[ネットワーク]** 領域にある **[アフィニティ グループ]** ページで作成できます。

## <a name="how-to-create-a-cloud-service-using-quick-create"></a>方法: 簡易作成によるクラウド サービスの作成
1. [Azure クラシック ポータル](http://manage.windowsazure.com/)で、**[新規]**>**[Compute]**>**[クラウド サービス]**>**[簡易作成]** の順にクリックします。
   
    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)
2. **[URL]**ボックスに、運用デプロイのクラウド サービスにアクセスするパブリック URL で使用するサブドメイン名を入力します。 運用デプロイの URL 形式は、http://*myURL*.cloudapp.net. です。
3. **[リージョン/アフィニティ グループ]**で、クラウド サービスをデプロイするリージョンまたはアフィニティ グループを選択します。 リージョン内の他の Azure サービスと同じ場所にクラウド サービスをデプロイする場合は、アフィニティ グループを選択します。
4. **[クラウド サービスを作成する]**をクリックします。
   
    ![CloudServices_Region](./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png)
   
    処理の状態はウィンドウの下部にあるメッセージ領域で監視できます。
   
    **[Cloud Services]** 領域が開き、新しいクラウド サービスが表示されます。 状態が [作成済み] に変わると、クラウド サービスの作成が正常に完了しています。
   
    ![CloudServices_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)

## <a name="how-to-upload-a-certificate-for-a-cloud-service"></a>方法: クラウド サービスの証明書のアップロード
1. [Azure クラシック ポータル](http://manage.windowsazure.com/)で **[Cloud Services]** をクリックし、クラウド サービスの名前をクリックして、**[証明書]** をクリックします。
   
    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)
2. **[証明書をアップロードします]** または **[アップロード]** をクリックします。
3. **[ファイル]** の **[参照]** を使用して、証明書ファイル (.pfx) を選択します。
4. **[パスワード]**ボックスに、証明書の秘密キーを入力します。
5. **[OK]** (チェックマーク) をクリックします。
   
    ![CloudServices_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)
   
    アップロードの進捗状況は下図のメッセージ領域で監視できます。 アップロードが完了すると、証明書がテーブルに追加されます。 メッセージ領域で [OK] をクリックし、メッセージを閉じます。
   
    ![CloudServices_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

## <a name="how-to-deploy-a-cloud-service"></a>方法: クラウド サービスのデプロイ
1. [Azure クラシック ポータル](http://manage.windowsazure.com/)で **[Cloud Services]** をクリックし、クラウド サービスの名前をクリックして、**[ダッシュボード]** をクリックします。
2. **[新しい運用環境のデプロイをアップロードします]** または **[アップロード]** をクリックします。
3. **[デプロイ ラベル]**に、新しいデプロイの名前を入力します (たとえば、MyCloudServicev4)。
4. **[パッケージ]** で、**[参照]** を使って、使用するサービス パッケージ ファイル (.cspkg) を選択します。
5. **[構成]** で、**[参照]** を使って、使用するサービス構成ファイル (.cscfg) を選択します。
6. クラウド サービスにインスタンスが&1; つしかないロールがある場合は、 **[1 つ以上のロールに単一のインスタンスが含まれている場合でもデプロイする]** チェック ボックスをオンにして、デプロイを有効にして続行します。
   
    Azure でクラウド サービスの保守中およびサービス更新中に 99.95% のアクセスを保証できるのは、あらゆるロールに少なくとも 2 つのインスタンスがある場合だけです。 必要な場合、クラウド サービスをデプロイした後に、 **[スケール]** ページで、ロール インスタンスを追加できます。 詳細については、「 [サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)」を参照してください。
7. **[OK]** (チェックマーク) をクリックして、クラウド サービスのデプロイを開始します。
   
    ![CloudServices_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)
   
    展開の状態は、メッセージ領域で監視できます。 メッセージを非表示にするには、[OK] をクリックします。
   
    ![CloudServices_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

## <a name="verify-your-deployment-completed-successfully"></a>デプロイが正常に完了したことを確認する
1. **[ダッシュボード]**をクリックします。
   
    サービスのステータスが、 **実行中**になっていることを確認します。
2. **[概要]**で、サイト URL をクリックして、Web ブラウザーでクラウド サービスを開きます。
   
    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)

[TFSTutorialForCloudService]: cloud-services-continuous-delivery-use-vso.md

## <a name="next-steps"></a>次のステップ
* [クラウド サービスの一般的な構成](cloud-services-how-to-configure.md)
* [カスタム ドメイン名を構成する](cloud-services-custom-domain-name.md)
* [クラウド サービスを管理する](cloud-services-how-to-manage.md)
* [SSL 証明書を構成する](cloud-services-configure-ssl-certificate.md)


