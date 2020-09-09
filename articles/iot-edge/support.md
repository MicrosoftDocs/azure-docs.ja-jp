---
title: サポートされているオペレーティング システム、コンテナー エンジン - Azure IoT Edge
description: Azure IoT Edge デーモンとランタイムを実行できるオペレーティング システム、運用デバイス用にサポートされるコンテナー エンジンについて説明します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/17/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ccfd85c5976642d30b3f21d2d2afeb868ba49bf9
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87924616"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge のサポートされるシステム

この記事では、公式またはプレビューの IoT Edge によってサポートされるシステムおよびコンポーネントについて、詳しく説明します。

Azure IoT Edge サービスの使用中に問題が発生した場合は、いくつかの方法でサポートを求めることができます。 サポートについては、次のいずれかのチャネルをお試しください。

**バグの報告** – Azure IoT Edge 製品に関する開発の大多数は、IoT Edge のオープン ソース プロジェクトで発生します。 バグはプロジェクトの[問題ページ](https://github.com/azure/iotedge/issues)で報告できます。 修正プログラムはプロジェクトが製品の更新プログラムになるまでの時間を早めます。

**Microsoft カスタマー サポート チーム** – [サポート プラン](https://azure.microsoft.com/support/plans/)に加入しているユーザーは、[Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac) から直接サポート チケットを作成することで、Microsoft カスタマー サポート チームとやり取りをすることができます。

**機能の要望** – Azure IoT Edge 製品はその製品の[ユーザーの声のページ](https://feedback.azure.com/forums/907045-azure-iot-edge)を介して機能の要望を追跡します。

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

<br>
<center>

![ホスト OS がゲスト OS に一致する](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>レベル 1

次の表に示すシステムは、一般提供またはパブリック プレビューにおいて、Microsoft によってサポートされており、新しいリリースごとにテストされています。 

| オペレーティング システム | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | パブリック プレビュー  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | パブリック プレビュー |
| [Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/windows-iot-core) ビルド 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Enterprise](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise) ビルド 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19) ビルド 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server) ビルド 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

上記の Windows オペレーティング システムは、Windows 上で Windows コンテナーを実行するデバイスの要件です。これは、運用環境でサポートされる唯一の構成です。 Windows 用の Azure IoT Edge インストール パッケージを使用すると、Windows 上で Linux コンテナーを使用できます。ただし、この構成は開発およびテスト専用です。 詳細については、「[Windows で IoT Edge を使用し、Linux コンテナーを実行する](how-to-install-iot-edge-windows-with-linux.md)」をご覧ください。

### <a name="tier-2"></a>レベル 2

次の表に示すシステムは、Azure IoT Edge と互換性があると見なされますが、Microsoft によってアクティブにテストまたは管理されてはいません。

| オペレーティング システム | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20.04 <sup>2</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10 システム (Raspian Buster を含む) は、IoT Edge がサポートしていないバージョンの OpenSSL を使用します。 IoT Edge をインストールする前に、次のコマンドを実行して以前のバージョンをインストールしてください。

```bash
sudo apt-get install libssl1.0.2
```

<sup>2</sup> [Azure IoT Edge リリース リポジトリ](https://github.com/Azure/azure-iotedge/releases)からの Debian 9 パッケージは、Ubuntu 20.04 でそのまますぐに使用できます。

## <a name="releases"></a>リリース

IoT Edge のリリース アセットとリリース ノートは、[azure-iotedge リリース](https://github.com/Azure/azure-iotedge/releases) ページから入手できます。 このセクションでは、それらのリリース ノートの情報を基に、それぞれのバージョンのコンポーネントを視覚的にわかりやすく説明しています。

IoT Edge のコンポーネントは、個別にインストールまたは更新することができ、以前のバージョンのコンポーネントとの下位互換性を備えています。 次の表は、各リリースに含まれているコンポーネントの一覧です。

| Release | セキュリティ デーモン | Edge ハブ<br>Edge エージェント | Libiothsm | Moby |
|--|--|--|--|--|
| **1.0.9** | 1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 |  |
| **1.0.8** | 1.0.8 | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl、CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

IoT Edge では、Microsoft.Azure.Device.Client SDK が使用されます。 詳細については、[Azure IoT C# SDK の GitHub リポジトリ](https://github.com/Azure/azure-iot-sdk-csharp)または [Azure SDK for .NET のリファレンス コンテンツ](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet)を参照してください。 次に示したのは、各リリースのテストに使用されたクライアント SDK のバージョンの一覧です。

* **IoT Edge 1.0.9**: クライアント SDK 1.21.1
* **IoT Edge 1.0.8**: クライアント SDK 1.20.3
* **IoT Edge 1.0.7**: クライアント SDK 1.20.1
* **IoT Edge 1.0.6**: クライアント SDK 1.17.1
* **IoT Edge 1.0.5**: クライアント SDK 1.17.1

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
