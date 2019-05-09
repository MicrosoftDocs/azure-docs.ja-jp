---
title: クラウド サービスを作成してデプロイする方法 | Microsoft Docs
description: Azure ポータルを使用してクラウド サービスを作成およびデプロイする方法について説明します。
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 56ea2f14-34a2-4ed9-857c-82be4c9d0579
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeconnoc
ms.openlocfilehash: a6cf2276da463f71f008c4bfb6eee4c232b18308
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551706"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>クラウド サービスを作成してデプロイする方法
Azure portal には、クラウド サービスを作成してデプロイする方法が 2 つ用意されています。"*簡易作成*" と "*カスタム作成*" です。

このトピックでは、簡易作成の方法を使って新しいクラウド サービスを作成し、その後、**[アップロード]** を使用して Azure にクラウド サービス パッケージをアップロードしてデプロイする方法について説明します。 この方法を使うと、Azure ポータルに、必要な事項をすべて完了するのに便利なリンクが操作の進行につれて表示されます。 クラウド サービスの作成時にデプロイする準備が整っている場合は、[カスタム作成] を使用して作成とデプロイを同時に実行できます。

> [!NOTE]
> Azure DevOps からクラウド サービスを発行する予定の場合は、[簡易作成] を使用した後、[Azure クイック スタート] またはダッシュボードから Azure DevOps 発行を設定する必要があります。 詳細については、[Azure DevOps を使用した Azure への継続的デリバリー][TFSTutorialForCloudService]に関するページを参照するか、**[クイック スタート]** ページのヘルプを参照してください。
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

サービス パッケージをエクスポートする前に、以下の 3 つのクラウド サービス機能について特別な構成が必要です。

* データ暗号化のために Secure Sockets Layer (SSL) を使用するクラウド サービスをデプロイする場合は、 [アプリケーションを SSL 用に構成](cloud-services-configure-ssl-certificate-portal.md#modify) します。
* ロール インスタンスに対するリモート デスクトップ接続を構成する場合は、リモート デスクトップ用に [ロールを構成](cloud-services-role-enable-remote-desktop-new-portal.md) します。
* クラウド サービスの詳細監視を構成する場合は、クラウド サービスの Azure Diagnostics を有効にします。 *最小監視* (既定の監視レベル) では、ロール インスタンス (仮想マシン) のホスト オペレーティング システムから収集したパフォーマンス カウンターが使用されます。 *詳細監視* は、アプリケーション処理時に発生する問題を詳しく分析できるように、ロール インスタンス内のパフォーマンス データに基づいて追加のメトリックを収集します。 Azure Diagnostics を有効にする方法については、 [Enabling Diagnostics in Azure (Azure における診断の有効化)](cloud-services-dotnet-diagnostics.md)を参照してください。

Web ロールまたは worker ロールのデプロイを伴うクラウド サービスを作成するには、 [サービス パッケージを作成する](cloud-services-model-and-package.md#servicepackagecspkg)必要があります。

## <a name="before-you-begin"></a>開始する前に
* Azure SDK をまだインストールしていない場合は、 **[Azure SDK のインストール]** をクリックして [Azure のダウンロード ページ](https://azure.microsoft.com/downloads/)を開き、アプリケーション コードの開発に使用する言語用の SDK をダウンロードします。 (これは後でも実行する機会があります)。
* 証明書を必要とするロール インスタンスがある場合は、証明書を作成します。 クラウド サービスには、秘密キーのある .pfx ファイルが必要です。 証明書は、クラウド サービスを作成してデプロイするときに、Azure にアップロードできます。

## <a name="create-and-deploy"></a>Create and deploy
1. [Azure Portal](https://portal.azure.com/) にログインします。
2. **[Create a resource > Compute] (リソースの作成 > 計算)** をクリックし、下へスクロールして **[クラウド サービス]** をクリックします。

    ![クラウド サービスの発行](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. 新しい **[クラウド サービス]** ウィンドウで、**[DNS 名]** の値を入力します。
4. 新しい **リソース グループ** を作成するか、または既存のリソース グループを選択します。
5. **[場所]** を選択します。
6. **[パッケージ]** をクリックします。 これにより、**[パッケージのアップロード]** ウィンドウが開きます。 必須フィールドに必要事項を入力します。 いずれかのロールに単一インスタンスが含まれている場合は、**[1 つ以上のロールに単一のインスタンスが含まれている場合でもデプロイします]** チェック ボックスがオンになっていることを確認してください。
7. **[デプロイの開始]** がオンになっていることを確認します。
8. **[OK]** をクリックして、**[パッケージのアップロード]** ウィンドウを閉じます。
9. 追加する証明書がない場合は、 **[作成]** をクリックします。

    ![クラウド サービスの発行](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>証明書のアップロード
デプロイメント パッケージが [証明書を使用するように構成](cloud-services-configure-ssl-certificate-portal.md#modify)されている場合は、ここで証明書をアップロードできます。

1. **[証明書]** を選択し、**[証明書の追加]** ウィンドウで SSL 証明書 .pfx ファイルを選択して、証明書の **[パスワード]** を指定します。
2. **[証明書のアタッチ]** をクリックし、**[証明書の追加]** ウィンドウで **[OK]** をクリックします。
3. **[クラウド サービス]** ウィンドウで **[作成]** をクリックします。 デプロイの状態が **[準備完了]** になったら、次の手順に進むことができます。

    ![クラウド サービスの発行](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>デプロイが正常に完了したことを確認する
1. クラウド サービス インスタンスをクリックします。

    サービスのステータスが、 **実行中**になっていることを確認します。
2. **[要点]** で **[サイトの URL]** をクリックして、Web ブラウザーでクラウド サービスを開きます。

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: https://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>次の手順
* [クラウド サービスの一般的な構成](cloud-services-how-to-configure-portal.md)
* [カスタム ドメイン名を構成する](cloud-services-custom-domain-name-portal.md)
* [クラウド サービスを管理する](cloud-services-how-to-manage-portal.md)
* [SSL 証明書を構成する](cloud-services-configure-ssl-certificate-portal.md)
