---
title: サポートされているオペレーティング システム、コンテナー エンジン - Azure IoT Edge
description: Azure IoT Edge デーモンとランタイムを実行できるオペレーティング システム、運用デバイス用にサポートされるコンテナー エンジンについて説明します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f59e2ca06f4ec435522cd06815b22d706a2d894c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772418"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge のサポートされるシステム

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

この記事では、公式またはプレビューの IoT Edge によってサポートされるシステムおよびコンポーネントについて、詳しく説明します。

## <a name="get-support"></a>サポートを受ける

Azure IoT Edge サービスの使用中に問題が発生した場合は、いくつかの方法でサポートを求めることができます。 サポートについては、次のいずれかのチャネルをお試しください。

**バグの報告** – Azure IoT Edge 製品に関する開発の大多数は、IoT Edge のオープン ソース プロジェクトで発生します。 バグはプロジェクトの[問題ページ](https://github.com/azure/iotedge/issues)で報告できます。 Azure IoT Edge for Linux on Windows に関するバグは、 [iotedge-eflow の問題ページ](https://github.com/azure/iotedge-eflow/issues)で報告できます。 修正プログラムでは、プロジェクトが製品の更新プログラムになるまでの時間を早めます。

**Microsoft カスタマー サポート チーム** – [サポート プラン](https://azure.microsoft.com/support/plans/)に加入しているユーザーは、[Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac) から直接サポート チケットを作成することで、Microsoft カスタマー サポート チームとやり取りをすることができます。

**機能の要望** – Azure IoT Edge 製品はその製品の [ユーザーの声のページ](https://feedback.azure.com/forums/907045-azure-iot-edge)を介して機能の要望を追跡します。

## <a name="container-engines"></a>コンテナー エンジン

Azure IoT Edge モジュールはコンテナーとして実装されているため、IoT Edge にはモジュールを起動するためのコンテナー エンジンが必要です。 Microsoft には、この要件を満たすために、moby-engine というコンテナー エンジンが用意されています。 このコンテナー エンジンは、Moby オープンソース プロジェクトをベースとします。 他にも有名なコンテナー エンジンとして、Docker CE や Docker EE が挙げられます。 これらも Moby オープンソース プロジェクトをベースとし、Azure IoT Edge と互換性があります。 Microsoft はこれらのコンテナー エンジンを使用するシステムに対してベスト エフォート サポートを提供しています。ただし、Microsoft はそれらで発生した問題の修正プログラムを配布することができません。 この理由から、Microsoft では運用システムで moby-engine を使用することを推奨しています。

<br>
<center>

![コンテナー ランタイムとしての Moby エンジン](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>オペレーティング システム

Azure IoT Edge はコンテナーを実行できるほとんどのオペレーティング システムで実行できます。ただし、それらのすべてのシステムが均等にサポートされているわけではありません。 オペレーティング システムは、ユーザーが受けられるサポートのレベルを表す階層別にグループ化されています。

* レベル 1 のシステムはサポートされています。 レベル 1 のシステムでは、
  * Microsoft がそのオペレーティング システムに対して自動テストを実施している
  * Microsoft がそれらのインストール パッケージを提供している
* レベル 2 のシステムは Azure IoT Edge と互換性があり、比較的簡単に使用できます。 レベル 2 のシステムでは、
  * Microsoft がそのプラットフォームで非公式なテストを実施している、またはパートナーがそのプラットフォーム上で Azure IoT Edge を正常に実行していることを把握している
  * 他のプラットフォーム用のインストール パッケージがそれらのプラットフォームで機能することがある

ホスト OS のファミリは、モジュールのコンテナー内で使用されるゲスト OS のファミリと常に一致する必要があります。 つまり、Linux コンテナーは Linux 上でのみ、Windows コンテナーは Windows 上でのみ使用できます。 Windows を使用している場合は、プロセス分離コンテナーのみがサポートされます (Hyper-V 分離コンテナーはサポートされません)。  

IoT Edge for Linux on Windows では、Windows ホストで実行されている Linux 仮想マシンで IoT Edge を使用します。 この方法で、Windows デバイス上で Linux モジュールを実行できます。

### <a name="tier-1"></a>レベル 1

次の表に示すシステムは、一般提供またはパブリック プレビューにおいて、Microsoft によってサポートされており、新しいリリースごとにテストされています。

Azure IoT Edge は、Linux コンテナーまたは Windows コンテナーとしてビルドされたモジュールをサポートします。 Linux コンテナーは、Linux デバイスにデプロイすることも、IoT Edge for Linux on Windows を使用して Windows デバイスにデプロイすることもできます。 Windows コンテナーは、Windows デバイスにのみ展開できます。

#### <a name="linux-containers"></a>Linux コンテナー

Linux コンテナーとしてビルドされたモジュールは、Linux デバイスまたは Windows デバイスにデプロイできます。 Linux デバイスの場合、IoT Edge ランタイムはホスト デバイスに直接インストールされます。 Windows デバイスの場合、IoT Edge ランタイムでビルドされた Linux 仮想マシンは、ホスト デバイスで実行されます。

[IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md) は現在パブリック プレビュー段階ですが、Windows デバイスで IoT Edge を実行する場合に推奨される方法です。

| オペレーティング システム | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | パブリック プレビュー |
| Windows 10 Pro | パブリック プレビュー |  |  |
| Windows 10 Enterprise | パブリック プレビュー |  |  |
| Windows 10 IoT Enterprise | パブリック プレビュー |  |  |
| Windows Server 2019 | パブリック プレビュー |  |  |

すべての Windows オペレーティング システムは、バージョン 1809 (ビルド 17763) 以降である必要があります。

>[!NOTE]
>Ubuntu Server 16.04 のサポートは、IoT Edge バージョン1.1 のリリースで終了しました。

#### <a name="windows-containers"></a>Windows コンテナー

>[!IMPORTANT]
>IoT Edge 1.1 LTS は、Windows コンテナーをサポートする最後のリリース チャネルです。 バージョン 1.2 以降では、Windows コンテナーはサポートされません。 Windows デバイスで IoT Edge を実行するには、[IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md) の使用またはこちらへの移行を検討してください。

Windows コンテナーとしてビルドされたモジュールは、Windows デバイスにのみ展開できます。

| オペレーティング システム | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Windows 10 IoT Enterprise | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019  | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019 | ![check1](./media/tutorial-c-module/green-check.png) |  |  |

すべての Windows オペレーティング システムはバージョン 1809 (ビルド 17763) である必要があります。 Windows の IoT Edge には、Windows の特定のビルドが必要です。これは、Windows コンテナーのバージョンがホストの Windows デバイスのバージョンと正確に一致する必要があるためです。 現在、Windows コンテナーはビルド 17763 のみを使用しています。

>[!NOTE]
>Windows 10 IoT Core のサポートは、IoT Edge バージョン1.1 のリリースで終了しました。

### <a name="tier-2"></a>レベル 2

次の表に示すシステムは、Azure IoT Edge と互換性があると見なされますが、Microsoft によってアクティブにテストまたは管理されてはいません。

| オペレーティング システム | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20.04 <sup>1</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspberry Pi OS Buster |  | ![Raspberry Pi OS Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspberry Pi OS Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> 「[Azure IoT Edge for Linux をインストールまたはアンインストールする](how-to-install-iot-edge.md)」の Ubuntu Server 18.04 のインストール手順は、Ubuntu 20.04 でそのまま機能します。

## <a name="releases"></a>リリース

IoT Edge のリリース アセットとリリース ノートは、[azure-iotedge リリース](https://github.com/Azure/azure-iotedge/releases) ページから入手できます。 このセクションでは、それらのリリース ノートの情報を基に、それぞれのバージョンのコンポーネントを視覚的にわかりやすく説明しています。

IoT Edge のコンポーネントは、個別にインストールまたは更新することができ、以前のバージョンのコンポーネントとの下位互換性を備えています。 次の表は、各リリースに含まれているコンポーネントの一覧です。

| Release | セキュリティ デーモン | Edge ハブ<br>Edge エージェント | Libiothsm | Moby |
|--|--|--|--|--|
| **1.1 LTS**<sup>1</sup> | 1.1.0<br>1.1.1 | 1.1.0<br>1.1.1 | 1.1.0<br>1.1.1 |   |
| **1.0.10** | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br>1.0.10.3<br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 |  |
| **1.0.9** | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 |  |
| **1.0.8** | 1.0.8 | 1.0.8<br>1.0.8.1<br>1.0.8.2<br>1.0.8.3<br>1.0.8.4<br>1.0.8.5 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 3.0.4 (ARMv7hl、CentOS)<br>3.0.5 |
| **1.0.6** | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

<sup>1</sup> IoT Edge 1.1 は、最初の長期サポート (LTS) リリース チャネルです。 このバージョンでは新機能は導入されていませんが、回帰に対するセキュリティ更新プログラムとバグの修正が適用されます。 IoT Edge 1.1 LTS では .NET Core 3.1 が使用され、これは [.NET Core および .NET 5 のリリース ライフサイクル](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)に合わせて 2022 年 12 月 3 日までサポートされます。

>[!IMPORTANT]
>長期サポート チャネルがリリースされたため、現在 1.0.x を実行しているすべてのお客様は、継続的なサポートを受けるためにデバイスを 1.1.x にアップグレードすることをお勧めします。

IoT Edge では、Microsoft.Azure.Devices.Client SDK が使用されます。 詳細については、[Azure IoT C# SDK の GitHub リポジトリ](https://github.com/Azure/azure-iot-sdk-csharp)または [Azure SDK for .NET のリファレンス コンテンツ](/dotnet/api/overview/azure/iot/client)を参照してください。 次に示したのは、各リリースのテストに使用されたクライアント SDK のバージョンの一覧です。

| IoT Edge のバージョン | Microsoft.Azure.Devices.Client SDK のバージョン |
|------------------|--------------------------------------------|
| 1.1 (LTS)      | 1.28.0                                     |
| 1.0.10           | 1.28.0                                     |
| 1.0.9            | 1.21.1                                     |
| 1.0.8            | 1.20.3                                     |
| 1.0.7            | 1.20.1                                     |
| 1.0.6            | 1.17.1                                     |
| 1.0.5            | 1.17.1                                     |

## <a name="virtual-machines"></a>Virtual Machines

Azure IoT Edge は仮想マシンで実行できます。 仮想マシンを IoT Edge デバイスとして使用することは、エッジ インテリジェンスで既存のインフラストラクチャを拡張しようとする場合によく行われます。 ホスト VM OS のファミリは、モジュールのコンテナー内で使用されるゲスト OS のファミリと一致する必要があります。 この要件は、Azure IoT Edge がデバイス上で直接実行されるときと同じです。 Azure IoT Edge は基盤となる仮想化テクノロジに依存しており、Hyper-V や vSphere などのプラットフォームを使用した VM で動作します。

<br>
<center>

![VM 内の Azure IoT Edge](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>最小システム要件

Azure IoT Edge は、Raspberry Pi3 のような小規模なデバイスから、サーバー グレード ハードウェアまで、幅広いデバイスで快適に動作します。 シナリオに適したハードウェアの選択は、実行するワークロードによって決まります。 デバイスを最終的に決定するまでには複雑なプロセスを要する場合もありますが、従来型のラップトップやデスクトップ上でソリューションのプロトタイプを作成することは簡単に開始できます。

プロトタイプ作成の過程で得られた経験は、最終的なデバイスの選択に役立ちます。 考慮すべき質問としては次のようなものがあります。

* ワークロード内のモジュールはいくつありますか。
* モジュールのコンテナーが共有するレイヤーはいくつありますか。
* モジュールはどの言語で作成されていますか。
* モジュールで処理されるデータはどのくらいですか。
* モジュールには、ワークロードを加速するための特殊なハードウェアが必要ですか。
* ソリューションの望ましいパフォーマンス特性はどのようなものですか。
* ハードウェア予算はどのくらいですか。
