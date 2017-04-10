---
title: "クラウド サービスの構成方法 (クラシック ポータル) | Microsoft Docs"
description: "Azure のクラウド サービスの構成方法について説明します。 クラウド サービスの構成の更新方法と、ロール インスタンスへのリモート アクセスの構成方法を紹介します。"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 4902f79d-ea91-41ca-89a4-7c818180ee5f
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 9cf1faabe3ea12af0ee5fd8a825975e30947b03a
ms.openlocfilehash: 838be613a35ac4af988e089efb57e45365cc7636


---
# <a name="how-to-configure-cloud-services"></a>Cloud Services の構成方法
> [!div class="op_single_selector"]
> * [Azure ポータル](cloud-services-how-to-configure-portal.md)
> * [Azure クラシック ポータル](cloud-services-how-to-configure.md)
> 
> 

クラウド サービスで最もよく使用される設定は Azure クラシックポータルで構成できます。 また、構成ファイルを直接更新する場合は、サービス構成ファイルをダウンロードして内容を更新し、更新したファイルをアップロードして、クラウド サービスの構成を更新します。 どちらの方法でも、構成の更新はすべてのロール インスタンスに適用されます。

Azure クラシック ポータルでは、 [Azure Cloud Services でのロールに対するリモート デスクトップ接続を有効にする](cloud-services-role-enable-remote-desktop.md)

Azure で構成の更新中に 99.95% の可用性を保証できるのは、各ロールに少なくとも 2 つのロール インスタンスがある場合だけです。 この場合、1 台の仮想マシンでクライアントからの要求を処理し、もう 1 台で更新を行うことができます。 詳細については、「 [サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)」を参照してください。

## <a name="change-a-cloud-service"></a>クラウド サービスの変更
1. [Azure クラシック ポータル](http://manage.windowsazure.com/)で **[Cloud Services]** をクリックし、クラウド サービスの名前をクリックして、**[構成]** をクリックします。
   
    ![[構成] ページ](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)
   
    **[構成]** ページでは、監視の構成、ロール設定の更新、およびロール インスタンスのゲスト オペレーティング システムとファミリの選択ができます。 
2. **[監視]**で、監視レベルの設定 ([詳細] または [最小])、および詳細監視に必要な診断接続文字列の構成を行います。
3. サービス ロールについては (ロールによりグループ分け)、次の設定を更新できます。
   
    * **[設定]** 
       - サービス構成ファイル (.cscfg) の *ConfigurationSettings* 要素で指定された各種構成設定値を変更します。

    * **証明書**  
         - ロールの SSL 暗号化で使用されている証明書の拇印を変更します。 証明書を変更するには、まず、 **[証明書]** ページで新しい証明書をアップロードする必要があります。 次に、ロール設定に表示される証明書文字列のサムプリントを更新します。
4. **[オペレーティング システム]** で、ロール インスタンスのオペレーティング システム ファミリまたはバージョンを変更することも、**[自動]** を選択して、現在のオペレーティング システム バージョンの自動更新を有効にすることもできます。 オペレーティング システムの設定は、Web ロールと Worker ロールには適用されますが、Virtual Machines は影響を受けません。
   
    デプロイ中に、すべてのロール インスタンスに最新のオペレーティング システムがインストールされ、オペレーティング システムは既定で自動更新されます。 
   
    コードの互換性を維持するために異なるバージョンのオペレーティング システムでクラウド サービスを実行する必要がある場合は、オペレーティング システム ファミリおよびバージョンを選択できます。 特定のバージョンのオペレーティング システムを選択すると、クラウド サービスで使用するオペレーティング システムの自動更新は中断されます。 オペレーティング システムに更新プログラムが適用されるようユーザーが責任を持つ必要があります。
   
    最新バージョンのオペレーティング システムでアプリケーションに生じる互換性の問題がすべて解決された場合、オペレーティング システムのバージョンを **[自動]**に設定して、オペレーティング システムの自動更新を有効にできます。 
   
    ![OS 設定](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)
5. 構成設定を保存してロール インスタンスに適用するには、**[保存]** をクリックします  (変更を取り消すには、**[破棄]** をクリックします)。**[保存]** と **[破棄]** は、設定を変更するとコマンド バーに追加されます。

## <a name="update-a-cloud-service-configuration-file"></a>クラウド サービス構成ファイルの更新
1. 現在の構成を含むクラウド サービス構成ファイル (.cscfg) をダウンロードします。 クラウド サービスの **[構成]** ページで、**[ダウンロード]** をクリックします。 続いて **[保存]** または **[名前を付けて保存]** をクリックして、ファイルを保存します。
2. サービス構成ファイルを更新した後、次のステップでファイルをアップロードして構成の更新内容を適用します。
   
   1. **[構成]** ページで、**[アップロード]** をクリックします。
      
       ![構成のアップロード](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)
   2. **[構成ファイル]** で、**[参照]** を使って、更新した .cscfg ファイルを選択します。
   3. クラウド サービスにインスタンスが 1 つしかないロールがある場合は、**[1 つ以上のロールに単一のインスタンスが含まれている場合でも構成を適用します]** チェック ボックスをオンにして、ロールの構成を更新できるようにして続行します。
      
       Azure では、各ロールに少なくとも 2 つのインスタンスを定義しない限り、サービス構成の更新中にクラウド サービスの最低 99.95% の可用性を保証することはできません。 詳細については、「 [サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)」を参照してください。
   4. **[OK]** (チェックマーク) をクリックします。 

## <a name="next-steps"></a>次のステップ
* 方法: [クラウド サービスをデプロイする](cloud-services-how-to-create-deploy.md)
* [カスタム ドメイン名を構成する](cloud-services-custom-domain-name.md)
* [クラウド サービスを管理する](cloud-services-how-to-manage.md)
* [Enable Remote Desktop Connection for a Role in Azure Cloud Services (Azure Cloud Services でのロールに対するリモート デスクトップ接続を有効にする)](cloud-services-role-enable-remote-desktop.md)
* [SSL 証明書を構成する](cloud-services-configure-ssl-certificate.md)




<!--HONumber=Nov16_HO3-->


