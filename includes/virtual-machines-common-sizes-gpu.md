NC と NV サイズは、GPU 対応インスタンスとも呼ばれます。 これらは、NVIDIA の GPU カードを含む特殊な仮想マシンで、さまざまなシナリオとユース ケース用に最適化されています。 NV サイズは、リモートの視覚化、ストリーミング、ゲーム、エンコーディング、および OpenGL や DirectX などのフレームワークを使用する VDI シナリオ用に最適化されています。 NC サイズは、CUDA や OpenCL ベースのアプリケーションやシミュレーションなどの、コンピューティング集中型およびネットワーク集中型のアプリケーション、アルゴリズム用にさらに最適化されています。 


NV インスタンスは、NVIDIA の Tesla M60 GPU カードおよびデスクトップ アクセラレータ アプリケーションや仮想デスクトップ向けの NVIDIA GRID を備えていて、お客様は、データやシミュレーションを視覚化することができます。 NV インスタンスでは、グラフィックス処理を要するワークフローを視覚化して優れたグラフィックス機能を活用し、さらにエンコードやレンダリングなどの単精度のワークロードを実行することもできます。 Tesla M60 は、1080p H.264 のストリームを最大 36 備えたデュアル GPU 設計の 4096 CUDA コアを提供します。 

NC インスタンスは NVIDIA の Tesla K80 カードを備えています。 エネルギー調査アプリケーション向け CUDAやクラッシュ シミュレーション、レイ トレーシング レンダリング、深層学習などを活用することで、データをさらに高速に処理できるようになりました。 Tesla K80 は、倍精度で最大 2.91 テラフロップ、単精度で最大 8.93 テラフロップのパフォーマンスを実現する、デュアル GPU 設計の 4992 CUDA コアを提供します。

## <a name="nv-instances"></a>NV インスタンス

| サイズ | CPU コア数 | メモリ: GiB | ローカル SSD: GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 |
| Standard_NV12 |12 |112 |680 | 2 |
| Standard_NV24 |24 |224 |1440 | 4 |

1 GPU = M60 カードの 2 分の 1 相当。

**サポートされているオペレーティング システム**

* Windows Server 2016、Windows Server 2012 R2。詳細については、[Windows 向けの N シリーズ ドライバーのセットアップ](../articles/virtual-machines/windows/n-series-driver-setup.md)に関するページをご覧ください。

## <a name="nc-instances"></a>NV インスタンス

| サイズ | CPU コア数 | メモリ: GiB | ローカル SSD: GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 |

1 GPU = K80 カードの 2 分の 1 相当。

* RDMA 対応

**サポートされているオペレーティング システム**

* Windows Server 2016、Windows Server 2012 R2。詳細については、[Windows 向けの N シリーズ ドライバーのセットアップ](../articles/virtual-machines/windows/n-series-driver-setup.md)に関するページをご覧ください。
* Ubuntu 16.04 LTS。詳細については、[Linux 向けの N シリーズ ドライバーのセットアップ](../articles/virtual-machines/linux/n-series-driver-setup.md)に関するページをご覧ください。

<br>

