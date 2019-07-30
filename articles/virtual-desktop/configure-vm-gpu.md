---
title: Windows Virtual Desktop プレビュー用に GPU を構成する - Azure
description: Windows Virtual Desktop プレビューで GPU アクセラレーションを使用したレンダリングとエンコードを有効にする方法です。
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: b6a4811f685803ecdc079a690d550618c071c4a6
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620193"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-preview"></a>Windows Virtual Desktop プレビュー用にグラフィックス処理装置 (GPU) のアクセラレーションを構成する

Windows Virtual Desktop プレビューでは、アプリのパフォーマンスとスケーラビリティを向上させるために、GPU アクセラレーションを使用したレンダリングとエンコードがサポートされています。 GPU アクセラレーションは、グラフィックを多用するアプリケーションの場合に特に重要です。

この記事で説明するようにして、GPU で最適化された Azure 仮想マシンを作成し、それをホスト プールに追加して、レンダリングとエンコードに GPU アクセラレーションを使うように構成します。 この記事では、Windows Virtual Desktop テナントを既に構成してあるものとします。

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>GPU で最適化する Azure 仮想マシンのサイズを選択する

Azure では、複数の [GPU 最適化済み仮想マシン サイズ](/azure/virtual-machines/windows/sizes-gpu)が提供されています。 実際のホスト プールに適した選択は、特定のアプリ ワークロード、ユーザー エクスペリエンスの望ましい品質、コストなど、さまざまなの要因に依存します。 一般に、GPU が大きくて高機能であるほど、特定のユーザー密度でのユーザー エクスペリエンスはよくなります。

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>ホスト プールを作成し、仮想マシンをプロビジョニングして、アプリ グループを構成する

選択したサイズの VM を使って、新しいホスト プールを作成します。 手順については、「[チュートリアル:Azure Marketplace を使用してホスト プールを作成する](/azure/virtual-desktop/create-host-pools-azure-marketplace)」をご覧ください。

Windows Virtual Desktop プレビューでは、次のオペレーティング システムで GPU アクセラレーションを使用したレンダリングとエンコードがサポートされています。

* Windows 10 バージョン 1511 以降
* Windows Server 2016 以降

また、アプリ グループを構成するか、または新しいホスト プールを作成すると自動的に作成される ("Desktop Application Group" という名前の) 既定のデスクトップ アプリ グループを使う必要があります。 手順については、「[チュートリアル:Windows Virtual Desktop プレビューのアプリ グループを管理する](/azure/virtual-desktop/manage-app-groups)」をご覧ください。

>[!NOTE]
>Windows Virtual Desktop プレビューでは、GPU 対応のプールに対しては "Desktop" アプリ グループの種類のみがサポートされています。 "RemoteApp" アプリ グループの種類は、GPU 対応のホスト プールではサポートされていません。

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>サポートされているグラフィック ドライバーを仮想マシンにインストールする

Windows Virtual Desktop プレビューで Azure N シリーズ VM の GPU 機能を利用するには、NVIDIA グラフィック ドライバーをインストールする必要があります。 「[Windows を実行している N シリーズ VM に NVIDIA GPU ドライバーをインストールする](/azure/virtual-machines/windows/n-series-driver-setup)」の説明に従って、手動で、または [NVIDIA GPU ドライバー拡張機能](/azure/virtual-machines/extensions/hpccompute-gpu-windows)を使って、ドライバーをインストールします。

Windows Virtual Desktop プレビュー用にサポートされているのは、Azure によって配布された [NVIDIA GRID ドライバー](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)だけであることに注意してください。

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

* 「[ドライバーのインストールの確認](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation)」で説明されている `nvidia-smi` ユーティリティを使って、アプリ実行時の GPU の利用を確認します。
* サポートされているオペレーティング システムのバージョンでは、タスク マネージャーを使って GPU の利用を確認できます。 [パフォーマンス] タブで GPU を選択し、アプリで GPU が利用されているかどうかを確認します。

## <a name="verify-gpu-accelerated-frame-encoding"></a>GPU アクセラレーションを使用するフレーム エンコードを検証する

リモート デスクトップで GPU アクセラレーションを使用するエンコードが使われていることを検証するには:

1. Windows Virtual Desktop クライアントを使って VM のデスクトップに接続します。
2. イベント ビューアーを起動し、次のノードに移動します。 **[アプリケーションとサービス ログ]**  >  **[Microsoft]**  >  **[Windows]**  >  **[RemoteDesktopServices RdpCoreTS]**  >  **[Operational]**
3. GPU アクセラレーションを使用するエンコードが使われているかどうかを確認するには、イベント ID 170 を探します。 [AVC ハードウェア エンコーダーが有効になりました: 1] と表示される場合は、GPU エンコードが使われています。
4. AVC 444 モードが使われているかどうかを確認するには、イベント ID 162 を探します。 [AVC 対応: 1、初期プロファイル: 2048] と表示される場合は、AVC 444 が使われています。

## <a name="next-steps"></a>次の手順

以上の手順のようにすると、単一セッション ホストの VM で GPU アクセラレーションが稼働状態になるはずです。 さらに大きなホスト プールで GPU アクセラレーションを有効にするには、いくつか追加の考慮事項があります。

* 多数の VM でのドライバーのインストールと更新を簡素化するには、[NVIDIA GPU ドライバー拡張機能](/azure/virtual-machines/extensions/hpccompute-gpu-windows)を使うことを検討します。
* 多数の VM でのグループ ポリシーの構成を簡素化するには、Active Directory グループ ポリシーを使うことを検討します。 Active Directory ドメインでのグループ ポリシーのデプロイについては、「[Working with Group Policy Objects (グループ ポリシー オブジェクトの操作)](https://go.microsoft.com/fwlink/p/?LinkId=620889)」をご覧ください。
