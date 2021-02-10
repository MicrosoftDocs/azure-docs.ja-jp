---
title: Azure IoT Edge for Linux を Windows にインストールする | Microsoft Docs
description: Windows デバイスに Azure IoT Edge をインストールする手順
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 3470e07c1b5673efa6cd015e43e077828da1573e
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703667"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device-preview"></a>Windows デバイスに Azure IoT Edge for Linux をインストールしてプロビジョニングする (プレビュー)

Azure IoT Edge ランタイムを使用すると、デバイスを IoT Edge デバイスに変えることができます。 PC クラスから産業用サーバーまでのデバイスにランタイムをデプロイできます。 IoT Edge ランタイムを使用してデバイスを構成すると、クラウドからデバイスへのビジネス ロジックのデプロイを開始できます。 詳細については、「[Azure IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)」を参照してください。

Azure IoT Edge for Linux on Windows を使用すると、Linux 仮想マシンを使用して Windows デバイスで Azure IoT Edge を使用できます。 Azure IoT Edge の Linux バージョンと、それと共にデプロイされる Linux モジュールが、仮想マシン上で実行されます。 そこから、Windows アプリケーションとコードおよび IoT Edge ランタイムとモジュールが、自由にやりとりできます。

この記事では、Windows デバイスに IoT Edge を設定する手順を示します。 これらの手順により、Windows デバイスで実行される IoT Edge ランタイムが含まれる Linux 仮想マシンをデプロイした後、その IoT Hub デバイス ID を使用してデバイスをプロビジョニングします。

>[!NOTE]
>Windows 上の IoT Edge for Linux は、[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)段階にあります。

## <a name="prerequisites"></a>[前提条件]

* 有効なサブスクリプションがある Azure アカウント。 [Azure サブスクリプション](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)をお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。

* 以下の最小システム要件を備えた Windows デバイス:

  * Windows 10 バージョン 1809 以降、ビルド 17763 以降
  * Professional、Enterprise、または Server エディション
  * 最小 RAM 容量: 4 GB (8 GB を推奨)
  * 最小ストレージ容量: 10 GB

* Windows Admin Center 用の Azure IoT Edge 拡張機能がインストールされている Windows Admin Center Insider Build へのアクセス:  <!-- The link below needs the language localization to work; otherwise broken -->
   1. [Windows Insider Preview](https://www.microsoft.com/en-us/software-download/windowsinsiderpreviewserver) にアクセスします。

   1. プレビューのドロップダウンで **Windows Admin Center Preview - Build 2012** を選択し、 **[Confirm]\(確認\)** を選択します。

      ![利用可能なプレビューのドロップダウン メニューから、Windows Admin Center Preview - Build 2012 を選択する。](./media/how-to-install-iot-edge-on-windows/select-windows-admin-center-preview-build.png)

   1. **[Select Language]\(言語の選択\)** ドロップダウンで **[English]\(英語\)** を選択して、 **[Confirm]\(確認\)** を選択します。

   1. **[Download Now]\(今すぐダウンロード\)** を選択して、*WindowsAdminCenterPreview2012.msi* をダウンロードします。

   1. *WindowsAdminCenterPreview2012.msi* を実行し、インストール ウィザードのプロンプトに従って Windows Admin Center をインストールします。 インストールが完了したら、Windows Admin Center を開きます。

   1. Windows Admin Center を初めて使用するときに、使用する証明書の選択を求められます。 証明書として **[Windows Admin Center Client]\(Windows Admin Center クライアント\)** を選択します。

   1. 次に、Azure IoT Edge 拡張機能をインストールします。 Windows Admin Center ダッシュボードの右上にある歯車アイコンを選択します。

      ![ダッシュボードの右上にある歯車アイコンを選択して、設定にアクセスする。](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. **[Settings]\(設定\)** メニューの **[Gateway]\(ゲートウェイ\)** で **[Extensions]\(拡張機能\)** を選択します。

   1. **[Feeds]\(フィード\)** タブを選択し、 **[Add]\(追加\)** を選択します。

   1. テキスト ボックスに「 https://aka.ms/wac-insiders-feed 」と入力し、 **[Add]\(追加\)** を選択します。

   1. フィードを追加した後、 **[Available extensions]\(利用できる拡張\)** タブに移動します。拡張機能一覧の更新に少し時間がかかる場合があります。

   1. **[Available extensions]\(利用できる拡張\)** タブの拡張機能の一覧で、**Azure IoT Edge** を見つけます。 それを選択し、拡張機能の一覧の上にある **[Install]\(インストール\)** プロンプトを選択します。

   1. インストールが完了すると、 **[Installed extensions]\(インストールされている拡張機能\)** タブのインストールされている拡張機能の一覧に Azure IoT Edge が表示されます。

## <a name="choose-your-provisioning-method"></a>プロビジョニング方法を選択する

Azure IoT Edge for Linux on Windows でサポートされているプロビジョニング方法は次のとおりです。

* お使いの IoT Edge デバイスの接続文字列を使用する手動プロビジョニング。 この方法を使用するには、「[IoT Hub に IoT Edge デバイスを登録する](how-to-register-device.md)」の手順に従ってデバイスを登録し、接続文字列を取得します。
  * 現在 IoT Edge for Linux on Windows では X.509 自己署名証明書がサポートされていないため、対称キー認証オプションを選択します。
* Device Provisioning Service (DPS) と対称キーを使用する自動プロビジョニング。 [DPS と対称キーによる IoT Edge デバイスの作成とプロビジョニング](how-to-auto-provision-symmetric-keys.md)に関する詳細を参照してください。
* DPS と X.509 証明書を使用する自動プロビジョニング。 [DPS と X.509 証明書による IoT Edge デバイスの作成とプロビジョニング](how-to-auto-provision-x509-certs.md)に関する詳細を参照してください。

少数のデバイスで始めるには、手動プロビジョニングが簡単です。 Device Provisioning Service は、多数のデバイスをプロビジョニングする場合に役立ちます。

DPS による方法のいずれかを使用してデバイスをプロビジョニングする場合は、上でリンクされている該当する記事の手順に従って、DPS のインスタンスを作成し、DPS のインスタンスを IoT Hub にリンクして、DPS の登録を作成します。 単一のデバイス用に "*個別登録*" を作成することも、デバイスのグループ用に "*グループ登録*" を作成することもできます。 登録の種類の詳細については、[Azure IoT Hub Device Provisioning Service の概念](https://docs.microsoft.com/azure/iot-dps/concepts-service#enrollment)に関するページを参照してください。

## <a name="create-a-new-deployment"></a>新しいデプロイを作成する

ターゲット デバイスに Azure IoT Edge for Linux on Windows のデプロイを作成します。

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Windows Admin Center 開始ページにある接続の一覧に、Windows Admin Center を実行している PC を表すローカル ホスト接続が表示されます。 その他の管理対象のサーバー、PC、またはクラスターもここに表示されます。

Windows Admin Center を使用して、ローカル デバイスまたはリモート マネージド デバイス上に、Azure IoT Edge for Linux on Windows をインストールして管理できます。 このガイドでは、Azure IoT Edge for Linux on Windows のデプロイのターゲット デバイスとしてローカル ホストを使用します。

ローカル デバイスではなくリモート ターゲット デバイスにデプロイしようとして、目的のターゲット デバイスが一覧に表示されない場合は、[指示に従ってデバイスを追加します](https://docs.microsoft.com/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)。

   ![ターゲット デバイスの一覧が表示されている Windows Admin Center の最初のダッシュボード](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. **[追加]** を選択します。

1. **[Add or create resources]\(リソースの追加または作成\)** ペインで、**Azure IoT Edge** のタイルを見つけます。 **[Create new]\(新規作成\)** を選択して、Azure IoT Edge for Linux on Windows の新しいインスタンスをデバイスにインストールします。

   IoT Edge for Linux on Windows がデバイスで既に実行されている場合は、 **[Add]\(追加\)** を選択して既存の IoT Edge デバイスに接続し、Windows Admin Center でそれを管理できます。

   ![Windows Admin Center の Azure IoT Edge のタイルで [Create New]\(新規作成\) を選択する](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. **[Create an Azure IoT Edge for Linux on Windows deployment]\(Azure IoT Edge for Linux on Windows のデプロイを作成する\)** ペインが開きます。 **[1. Getting Started]\(1. 作業の開始\)** タブで、ターゲット デバイスが最小要件を満たしていることを確認し、 **[次へ]** を選択します。

1. ライセンス条項を確認し、 **[同意する]** をオンにして、 **[次へ]** を選択します。

1. 必要に応じて、 **[Optional diagnostic data]\(オプションの診断データ\)** をオンまたはオフに切り替えることができます。

1. **Next:デプロイ**」に進みます。

   ![[Next: Deploy]\(次へ: デプロイ\) ボタンを、オプションの診断データの設定を切り替えた後で選択する](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. **[2. Deploy]\(2. デプロイ\)** タブの **[Select a target device]\(ターゲット デバイスの選択\)** で、一覧表示されているデバイスをクリックし、最小要件を満たしていることを確認します。 状態がサポート対象であることを確認した後、 **[次へ]** を選択します。

   ![デバイスを選択し、サポートされていることを確認する](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. **[2.2 Settings]\(2.2 設定\)** タブで、既定の設定をそのまま使用します。

1. **[2.3 Deployment]\(2.3 デプロイ\)** タブで、デプロイの進行状況を確認できます。 完全なプロセスには、Azure IoT Edge for Linux on Windows パッケージのダウンロード、パッケージのインストール、ホスト デバイスの構成、Linux 仮想マシンの設定が含まれます。 このプロセスの完了には数分かかる場合があります。 デプロイが成功したときの表示を次に示します。

   ![デプロイが成功すると、各ステップに緑色のチェックマークと [Complete]\(完了\) ラベルが表示される](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

デプロイが完了すると、デバイスをプロビジョニングする準備が整います。 **Next:Connect\(次へ: 接続\)** を選択して **3. Connect\(3. 接続\)** タブに進み、Azure IoT Edge デバイスのプロビジョニングを処理します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

まだ行っていない場合は、IoT Edge for Linux on Windows をターゲット デバイスにインストールします。

> [!NOTE]
> PowerShell を使用する次のプロセスでは、Azure IoT Edge for Linux on Windows のローカル ホストのデプロイを作成する方法を説明します。 PowerShell を使用してリモート ターゲット デバイスへのデプロイを作成するには、[リモート PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_remote) を使用してリモート デバイスへの接続を確立し、そのデバイスでこれらのコマンドをリモートで実行できます。

1. 管理者特権の PowerShell セッションで、次の各コマンドを実行し、IoT Edge for Linux on Windows をダウンロードします。

   ```azurepowershell-interactive
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzureEdgeForLinuxOnWindowsMSI" -OutFile $msiPath
   ```

1. IoT Edge for Linux on Windows をデバイスにインストールします。

   ```azurepowershell-interactive
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   > [!NOTE]
   > 上記のインストール コマンドに INSTALLDIR="<FULLY_QUALIFIED_PATH>" パラメーターと VHDXDIR="<FULLY_QUALIFIED_PATH>" パラメーターを追加することで、IoT Edge for Linux on Windows のカスタム インストールと VHDX ディレクトリを指定できます。

1. デプロイが正常に実行されるようにするには、ターゲット デバイスで実行ポリシーを `AllSigned` に設定する必要があります (まだ設定されていない場合)。 現在の実行ポリシーは、管理者特権の PowerShell プロンプトで次のコマンドを使用して確認できます。

   ```azurepowershell-interactive
   Get-ExecutionPolicy -List
   ```

   `local machine` の実行ポリシーが `AllSigned` になっていない場合は、次のようにして実行ポリシーを設定できます。

   ```azurepowershell-interactive
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. IoT Edge for Linux on Windows のデプロイを作成します。

   ```azurepowershell-interactive
   Deploy-Eflow
   ```

   <!-- Most likely temporary until cmdlet is fully documented -->
   > [!NOTE]
   > パラメーターを指定せずにこのコマンドを実行したり、必要に応じてパラメーターを使用してデプロイをカスタマイズしたりできます。 パラメーターとその意味を確認するには、PowerShell モジュール AzureEFLOW.psm1 を調べてください (C:\Program Files\WindowsPowerShell\Modules\AzureEFLOW の下を参照)。

1. ライセンス条項に同意するには、「Y」と入力します。

1. 必要に応じて、「O」または「R」を入力して **[Optional diagnostic data]\(オプションの診断データ\)** をオンまたはオフに切り替えます。 デプロイが成功したときの表示を次に示します。

   ![デプロイが成功すると、メッセージの最後に [Deployment successful]\(デプロイ成功\) と表示されます](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment.png)

デプロイが完了すると、デバイスをプロビジョニングする準備が整います。

---

デバイスをプロビジョニングするには、次のリンクを使用して、選択したプロビジョニング方法のセクションに移動します。

* [オプション 1: お使いの IoT Edge デバイスの接続文字列を使用する手動プロビジョニング](#option-1-provisioning-manually-using-the-connection-string)
* [オプション 2: Device Provisioning Service (DPS) と対称キーを使用する自動プロビジョニング](#option-2-provisioning-via-dps-using-symmetric-keys)
* [オプション 3:DPS と X.509 証明書を使用する自動プロビジョニング](#option-3-provisioning-via-dps-using-x509-certificates)

## <a name="provision-your-device"></a>デバイスをプロビジョニングする

デバイスのプロビジョニング方法を選択し、該当するセクションの指示に従います。 Windows Admin Center または管理者特権の PowerShell セッションを使用して、デバイスをプロビジョニングできます。

### <a name="option-1-provisioning-manually-using-the-connection-string"></a>オプション 1: 接続文字列を使用した手動プロビジョニング

このセクションでは、Azure IoT Edge デバイスの接続文字列を使用してデバイスを手動でプロビジョニングする方法について説明します。

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. **[Azure IoT Edge device provisioning]\(Azure IoT Edge デバイスのプロビジョニング\)** ペインで、プロビジョニング方法のドロップダウンから **[Connection String (Manual)]\(接続文字列 (手動)\)** を選択します。

1. [Azure portal](https://ms.portal.azure.com/) で、お使いの IoT Hub の **[IoT Edge]** タブに移動します。

1. デバイスのデバイス ID をクリックします。 **[プライマリ接続文字列]** フィールドをコピーします。

1. それを、Windows Admin Center のデバイス接続文字列のフィールドに貼り付けます。 次に、 **[Provisioning with the selected method]\(選択した方法でプロビジョニング\)** を選択します。

   ![デバイスの接続文字列を貼り付けた後、[Provisioning with the selected method]\(選択した方法でプロビジョニング\) を選択する](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. プロビジョニングが完了したら、 **[完了]** を選択します。 メイン ダッシュボードに自動的に戻ります。 これで、種類が `IoT Edge Devices` の新しいデバイスが一覧に表示されます。 IoT Edge デバイスを選択して、それに接続できます。 **[Overview]\(概要\)** ページでは、デバイスの **[IoT Edge Module List]\(IoT Edge モジュール一覧\)** と **[IoT Edge Status]\(IoT Edge の状態\)** を見ることができます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. [Azure portal](https://ms.portal.azure.com/) で、お使いの IoT Hub の **[IoT Edge]** タブに移動します。

1. デバイスのデバイス ID をクリックします。 **[プライマリ接続文字列]** フィールドをコピーします。

1. 次のコマンドのプレースホルダー テキストに貼り付けた後、ターゲット デバイス上の管理者特権の PowerShell セッションでそれを実行します。

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType manual -devConnString "<CONNECTION_STRING_HERE>"
   ```

---

### <a name="option-2-provisioning-via-dps-using-symmetric-keys"></a>オプション 2:対称キーを使用した DPS によるプロビジョニング

このセクションでは、DPS と対称キーを使用したデバイスの自動プロビジョニングについて説明します。

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. **[Azure IoT Edge device provisioning]\(Azure IoT Edge デバイスのプロビジョニング\)** ペインで、プロビジョニング方法のドロップダウンから **[Symmetric Key (DPS)]\(対称キー (DPS)\)** を選択します。

1. [Azure portal](https://ms.portal.azure.com/) で、お使いの DPS インスタンスに移動します。

1. **[概要]** タブで、 **[ID スコープ]** の値をコピーします。 それを Windows Admin Center のスコープ ID のフィールドに貼り付けます。

1. Azure portal の **[登録を管理します]** タブで、作成した登録を選択します。 登録の詳細で **[主キー]** の値をコピーします。 それを Windows Admin Center の対称キーのフィールドに貼り付けます。

1. Windows Admin Center の登録 ID のフィールドで、デバイスの登録 ID を指定します。

1. **[Provisioning with the selected method]\(選択した方法でプロビジョニング\)** を選択します。

   ![対称キーのプロビジョニングに必要なフィールドを入力した後、選択した方法でのプロビジョニングを選択します](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. プロビジョニングが完了したら、 **[完了]** を選択します。 メイン ダッシュボードに自動的に戻ります。 これで、種類が `IoT Edge Devices` の新しいデバイスが一覧に表示されます。 IoT Edge デバイスを選択して、それに接続できます。 **[Overview]\(概要\)** ページでは、デバイスの **[IoT Edge Module List]\(IoT Edge モジュール一覧\)** と **[IoT Edge Status]\(IoT Edge の状態\)** を見ることができます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. テキスト エディターに次のコマンドをコピーします。 プレースホルダーのテキストを実際の詳細情報に置き換えます。

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType symmetric -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

1. [Azure portal](https://ms.portal.azure.com/) で、お使いの DPS インスタンスに移動します。

1. **[概要]** タブで、 **[ID スコープ]** の値をコピーします。 コマンドの適切なプレースホルダー テキストにそれを貼り付けます。

1. Azure portal の **[登録を管理します]** タブで、作成した登録を選択します。 登録の詳細で **[主キー]** の値をコピーします。 コマンドの適切なプレースホルダー テキストにそれを貼り付けます。

1. デバイスの登録 ID を指定して、コマンドの適切なプレースホルダー テキストを置き換えます。

1. ターゲット デバイスで管理者特権の PowerShell セッションを使用してコマンドを実行します。

---

### <a name="option-3-provisioning-via-dps-using-x509-certificates"></a>オプション 3:X.509 証明書を使用した DPS によるプロビジョニング

このセクションでは、DPS と X.509 証明書を使用したデバイスの自動プロビジョニングについて説明します。

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. **[Azure IoT Edge device provisioning]\(Azure IoT Edge デバイスのプロビジョニング\)** ペインで、プロビジョニング方法のドロップダウンから **[X.509 Certificate (DPS)]\(X.509 証明書 (DPS)\)** を選択します。

1. [Azure portal](https://ms.portal.azure.com/) で、お使いの DPS インスタンスに移動します。

1. **[概要]** タブで、 **[ID スコープ]** の値をコピーします。 それを Windows Admin Center のスコープ ID のフィールドに貼り付けます。

1. Windows Admin Center の登録 ID のフィールドで、デバイスの登録 ID を指定します。

1. 証明書と秘密キー ファイルをアップロードします。

1. **[Provisioning with the selected method]\(選択した方法でプロビジョニング\)** を選択します。

   ![X.509 証明書のプロビジョニングに必要なフィールドを入力した後、選択した方法でのプロビジョニングを選択します](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. プロビジョニングが完了したら、 **[完了]** を選択します。 メイン ダッシュボードに自動的に戻ります。 これで、種類が `IoT Edge Devices` の新しいデバイスが一覧に表示されます。 IoT Edge デバイスを選択して、それに接続できます。 **[Overview]\(概要\)** ページでは、デバイスの **[IoT Edge Module List]\(IoT Edge モジュール一覧\)** と **[IoT Edge Status]\(IoT Edge の状態\)** を見ることができます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. テキスト エディターに次のコマンドをコピーします。 プレースホルダーのテキストを実際の詳細情報に置き換えます。

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType x509 -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -identityCertLocWin <ABSOLUTE_CERT_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityPkLocWin <ABSOLUTE_PRIVATE_KEY_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityCertLocWin <ABSOLUTE_CERT_DEST_PATH_ON_LINUX_MACHINE -identityPkLocVm <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_LINUX_MACHINE>
   ```

1. [Azure portal](https://ms.portal.azure.com/) で、お使いの DPS インスタンスに移動します。

1. **[概要]** タブで、 **[ID スコープ]** の値をコピーします。 コマンドの適切なプレースホルダー テキストにそれを貼り付けます。

1. デバイスの登録 ID を指定して、コマンドの適切なプレースホルダー テキストを置き換えます。

1. 適切なプレースホルダー テキストを、実際の証明書ファイルの絶対ソース パスに置き換えます。

1. 適切なプレースホルダー テキストを、実際の秘密キー ファイルの絶対ソース パスに置き換えます。

1. ターゲット デバイスで管理者特権の PowerShell セッションを使用してコマンドを実行します。

---

## <a name="verify-successful-configuration"></a>構成が成功したことを確認する

IoT Edge for Linux on Windows が IoT Edge デバイスに正常にインストールされ、構成されたことを確認します。

1. Windows Admin Center で接続されているデバイスの一覧から IoT Edge デバイスを選択して、それに接続します。
1. デバイスの概要ページに、デバイスに関するいくつかの情報が表示されます。

    1. **[IoT Edge Module List]\(IoT Edge モジュール一覧\)** セクションに、デバイスで実行中のモジュールが表示されます。 IoT Edge サービスが初めて開始されたときは、**edgeAgent** モジュールだけが実行中であると表示されるはずです。 edgeAgent モジュールが既定で実行され、デバイスにデプロイする追加モジュールのインストールと起動を支援します。
    1. **[IoT Edge Status]\(IoT Edge の状態\)** セクションにはサービスの状態が表示され、 **[active (running)]\(アクティブ (実行中)\)** と報告されるはずです。

1. IoT Edge サービスのトラブルシューティングを行う必要がある場合は、デバイス ページの **コマンド シェル** ツールを使用して仮想マシンに ssh (Secure Shell) で接続し、Linux コマンドを実行します。

    1. サービスのトラブルシューティングが必要な場合は、サービス ログを取得します。

       ```bash
       journalctl -u iotedge
       ```

    2. `check` ツールを使用して、デバイスの構成と接続の状態を確認します。

       ```bash
       sudo iotedge check
       ```

## <a name="next-steps"></a>次のステップ

[IoT Edge モジュールのデプロイ](how-to-deploy-modules-portal.md)に進んで、デバイスにモジュールをデプロイする方法を確認します。
