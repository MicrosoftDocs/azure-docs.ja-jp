---
title: Windows Virtual Desktop (クラシック) の GPU を構成する - Azure
description: Windows Virtual Desktop (クラシック) で GPU アクセラレーションを使用したレンダリングとエンコードを有効にする方法について説明します。
author: gundarev
ms.topic: how-to
ms.date: 03/30/2020
ms.author: denisgun
ms.openlocfilehash: fa7b80b021e00d25dea4f96432ae922c15474058
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95023056"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-classic"></a>Windows Virtual Desktop (クラシック) 用にグラフィックス処理装置 (GPU) のアクセラレーションを構成する

>[!IMPORTANT]
>この内容は、Azure Resource Manager Windows Virtual Desktop オブジェクトをサポートしていない Windows Virtual Desktop (クラシック) に適用されます。 Azure Resource Manager Windows Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../configure-vm-gpu.md)を参照してください。

Windows Virtual Desktop では、アプリのパフォーマンスとスケーラビリティを向上させるために、GPU アクセラレーションを使用したレンダリングとエンコードがサポートされています。 GPU アクセラレーションは、グラフィックを多用するアプリケーションの場合に特に重要です。

この記事で説明するようにして、GPU で最適化された Azure 仮想マシンを作成し、それをホスト プールに追加して、レンダリングとエンコードに GPU アクセラレーションを使うように構成します。 この記事では、Windows Virtual Desktop テナントを既に構成してあるものとします。

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>GPU で最適化する Azure 仮想マシンのサイズを選択する

Azure では、複数の [GPU 最適化済み仮想マシン サイズ](../../virtual-machines/sizes-gpu.md)が提供されています。 実際のホスト プールに適した選択は、特定のアプリ ワークロード、ユーザー エクスペリエンスの望ましい品質、コストなど、さまざまなの要因に依存します。 一般に、GPU が大きくて高機能であるほど、特定のユーザー密度でのユーザー エクスペリエンスはよくなります。

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>ホスト プールを作成し、仮想マシンをプロビジョニングして、アプリ グループを構成する

選択したサイズの VM を使って、新しいホスト プールを作成します。 手順については、「[チュートリアル:Azure Marketplace を使用してホスト プールを作成する](../create-host-pools-azure-marketplace.md)」をご覧ください。

Windows Virtual Desktop では、次のオペレーティング システムで GPU アクセラレーションを使用したレンダリングとエンコードがサポートされています。

* Windows 10 バージョン 1511 以降
* Windows Server 2016 以降

また、アプリ グループを構成するか、または新しいホスト プールを作成すると自動的に作成される ("Desktop Application Group" という名前の) 既定のデスクトップ アプリ グループを使う必要があります。 手順については、「[チュートリアル:Windows Virtual Desktop のアプリ グループを管理する](../manage-app-groups.md)」を参照してください。

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>サポートされているグラフィック ドライバーを仮想マシンにインストールする

Windows Virtual Desktop で Azure N シリーズ VM の GPU 機能を利用するには、適切なグラフィック ドライバーをインストールする必要があります。 「[サポートされているオペレーティング システムとドライバー](../../virtual-machines/sizes-gpu.md#supported-operating-systems-and-drivers)」の手順に従って、手動で、または Azure VM 拡張機能を使用して適切なグラフィック ベンターのドライバーをインストールします。

Windows Virtual Desktop でサポートされているのは、Azure によって配布されたドライバーのみです。 さらに、NVIDIA GPU を搭載した Azure VM の場合、Windows Virtual Desktop でサポートされているのは [NVIDIA GRID ドライバー](../../virtual-machines/windows/n-series-driver-setup.md#nvidia-grid-drivers)のみです。

ドライバーをインストールした後は、VM を再起動する必要があります。 上記の説明の検証手順を使って、グラフィック ドライバーが正常にインストールされたことを確認します。

## <a name="configure-gpu-accelerated-app-rendering"></a>GPU アクセラレーションを使用するアプリ レンダリングを構成する

既定では、マルチセッション構成で実行されるアプリとデスクトップは CPU でレンダリングされ、使用可能な GPU はレンダリングに利用されません。 セッション ホストのグループ ポリシーを構成して、GPU アクセラレーションを使用するレンダリングを有効にします。

1. ローカル管理者特権を持つアカウントを使って、VM のデスクトップに接続します。
2. [スタート] メニューを開き、「gpedit.msc」と入力して、グループ ポリシー エディターを開きます。
3. ツリーで、 **[コンピューターの構成]**  >  **[管理用テンプレート]**  >  **[Windows コンポーネント]**  >  **[リモート デスクトップ サービス]**  >  **[リモート デスクトップ セッション ホスト]**  >  **[リモート セッション環境]** に移動します。
4. ポリシー **[すべてのリモート デスクトップ サービス セッションにハードウェアの既定のグラフィックス アダプターを使用する]** を選択し、このポリシーを **[有効]** に設定して、リモート セッションでの GPU レンダリングを有効にします。

## <a name="configure-gpu-accelerated-frame-encoding"></a>GPU アクセラレーションを使用するフレーム エンコードを構成する

リモート デスクトップでは、リモート デスクトップ クライアントへの送信用にアプリとデスクトップでレンダリングされるすべてのグラフィックスが (GPU または CPU のどちらでレンダリングされるかに関係なく) エンコードされます。 既定では、リモート デスクトップによるこのエンコードに、使用可能な GPU は利用されません。 セッション ホストのグループ ポリシーを構成して、GPU アクセラレーションを使用するフレーム エンコードを有効にします。 上記の手順に続けて次のようにします。

1. ポリシー **[リモート デスクトップ接続で H.264/AVC 444 グラフィック モードを優先する]** を選択し、このポリシーを **[有効]** に設定して、リモート セッションで H.264/AVC 444 コーデックを強制的に行います。
2. ポリシー **[リモート デスクトップ接続用に H.264/AVC ハードウェア エンコードを構成する]** を選択し、このポリシーを **[有効]** に設定して、リモート セッションで AVC/H.264 に対するハードウェア エンコードを有効にします。

    >[!NOTE]
    >Windows Server 2016 では、 **[AVC ハードウェア エンコードを優先]** オプションを **[常に試行]** に設定します。

3. グループ ポリシーを編集したので、強制的にグループ ポリシーを更新します。 コマンド プロンプトを開き、次のように入力します。

    ```batch
    gpupdate.exe /force
    ```

4. リモート デスクトップ セッションからサインアウトします。

## <a name="verify-gpu-accelerated-app-rendering"></a>GPU アクセラレーションを使用するアプリ レンダリングを検証する

アプリでレンダリングに GPU が使われていることを確認するには、次のいずれかを試します。

* NVIDIA GPU を搭載した Azure VM の場合は、「[ドライバーのインストールの確認](../../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation)」の説明に従って `nvidia-smi` ユーティリティを使用して、アプリ実行時の GPU 使用率を確認します。
* サポートされているオペレーティング システムのバージョンでは、タスク マネージャーを使って GPU の利用を確認できます。 [パフォーマンス] タブで GPU を選択し、アプリで GPU が利用されているかどうかを確認します。

## <a name="verify-gpu-accelerated-frame-encoding"></a>GPU アクセラレーションを使用するフレーム エンコードを検証する

リモート デスクトップで GPU アクセラレーションを使用するエンコードが使われていることを検証するには:

1. Windows Virtual Desktop クライアントを使って VM のデスクトップに接続します。
2. イベント ビューアーを起動し、次のノードに移動します。 **[アプリケーションとサービス ログ]**  >  **[Microsoft]**  >  **[Windows]**  >  **[RemoteDesktopServices RdpCoreCDV]**  >  **[Operational]**
3. GPU アクセラレーションを使用するエンコードが使われているかどうかを確認するには、イベント ID 170 を探します。 [AVC ハードウェア エンコーダーが有効になりました: 1] と表示される場合は、GPU エンコードが使われています。
4. AVC 444 モードが使われているかどうかを確認するには、イベント ID 162 を探します。 [AVC 対応: 1、初期プロファイル: 2048] と表示される場合は、AVC 444 が使われています。

## <a name="next-steps"></a>次のステップ

これらの手順により、1 つのセッション ホスト (1 つの VM) で GPU アクセラレーションが稼働状態になるはずです。 さらに大きなホスト プールで GPU アクセラレーションを有効にするには、いくつか追加の考慮事項があります。

* 多数の VM でのドライバーのインストールと更新を簡素化するには、[VM 拡張機能](../../virtual-machines/extensions/overview.md)を使用することを検討してください。 NVIDIA GPU が搭載された VM には [NVIDIA GPU ドライバー拡張機能](../../virtual-machines/extensions/hpccompute-gpu-windows.md)を使用し、AMD GPU が搭載された VM には AMD GPU ドライバー拡張機能を使用します。
* 多数の VM でのグループ ポリシーの構成を簡素化するには、Active Directory グループ ポリシーを使うことを検討します。 Active Directory ドメインでのグループ ポリシーのデプロイについては、「[Working with Group Policy Objects (グループ ポリシー オブジェクトの操作)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731212(v=ws.11))」をご覧ください。