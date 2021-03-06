---
title: Azure Migrate で Azure に移行するためにオンプレミスの VMware VM を検出して評価する | Microsoft Docs
description: Azure Migrate サービスを使って Azure に移行するためにオンプレミスの VMware VM を検出して評価する方法について説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 08/20/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 00d416d6211d9a67a69eb22620bdac6a501e23e7
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666666"
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Azure に移行するためにオンプレミスの VMware VM を検出して評価する

[Azure Migrate](migrate-overview.md) サービスは、Azure への移行についてオンプレミスのワークロードを評価します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure Migrate でオンプレミスの VM を検出するために使用するアカウントを作成します。
> * Azure Migrate プロジェクトを作成します。
> * 評価のためにオンプレミスの VMware VM を検出するように、オンプレミスのコレクター仮想マシン (VM) をセットアップします。
> * VM をグループ化して、評価を作成します。


Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="prerequisites"></a>前提条件

- **VMware**: 移行する予定の VM は、バージョン 5.5、6.0、または 6.5 を実行している vCenter Server で管理する必要があります。 また、コレクター VM を展開するには、バージョン 5.0 以降を実行している 1 つの ESXi ホストが必要です。
- **vCenter Server アカウント**: vCenter Server にアクセスするには、読み取り専用アカウントが必要です。 Azure Migrate はこのアカウントを使ってオンプレミスの VM を検出します。
- **アクセス許可**: vCenter Server 上で、.OVA 形式でファイルをインポートして VM を作成するためのアクセス許可が必要です。
- **統計情報の設定**: デプロイを始める前に、vCenter Server の統計設定をレベル 3 に設定する必要があります。 レベル 3 より低い場合は、評価は機能しますが、ストレージとネットワークのパフォーマンス データが収集されません。 この場合のサイズは、CPU とメモリのパフォーマンス データと、ディスクおよびネットワーク アダプターの構成データに基づいて勧められます。

## <a name="create-an-account-for-vm-discovery"></a>VM 検出用のアカウントを作成する

Azure Migrate は、評価対象の VM を自動的に検出するために、VMware サーバーにアクセスする必要があります。 次の特性を備えた VMware アカウントを作成します。 Azure Migrate のセットアップ時に、そのアカウントを指定します。

- ユーザーの種類: 読み取り専用以上
- アクセス許可: データ センター オブジェクト –> 子オブジェクトへの伝達、ロール=読み取り専用
- 詳細: ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。
- アクセスを制限するには、子オブジェクトへの伝達特権を持つアクセスなしロールを子オブジェクト (vSphere ホスト、データストア、VM、ネットワーク) に割り当てます。


## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする

[Azure Portal](https://portal.azure.com) にログインします。

## <a name="create-a-project"></a>プロジェクトの作成

1. Azure Portal で、**[リソースの作成]** をクリックします。
2. 「**Azure Migrate**」を検索し、検索結果でサービス **Azure Migrate** を選択します。 **[Create]** をクリックします。
3. プロジェクト名およびプロジェクトの Azure サブスクリプションを指定します。
4. 新しいリソース グループを作成します。
5. プロジェクトを作成する場所を指定して、**[作成]** をクリックします。 Azure Migrate プロジェクトを作成できるのは、米国中西部または米国東部リージョンに限られます。 ただし、対象となる任意の Azure の場所について移行を計画することもできます。 プロジェクト用に指定された場所は、オンプレミスの VM から収集されたメタデータを格納するためにのみ使用します。

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)



## <a name="download-the-collector-appliance"></a>コレクター アプライアンスをダウンロードする

Azure Migrate は、コレクター アプライアンスと呼ばれるオンプレミスの VM を作成します。 この VM はオンプレミスの VMware VM を検出し、それについてのメタデータを Azure Migrate サービスに送信します。 コレクター アプライアンスをセットアップするには、.OVA ファイルをダウンロードし、それをオンプレミスの vCenter サーバーにインポートして、VM を作成します。

1. Azure Migrate プロジェクトで、**[作業の開始]** > **[検出と評価]** > **[マシンの検出]** の順にクリックします。
2. **[マシンの検出]** で **[ダウンロード]** をクリックし、.OVA ファイルをダウンロードします。
3. **[プロジェクトの資格情報をコピーします]** で、プロジェクトの ID とキーをコピーします。 これらの情報はコレクターを構成するときに必要になります。

    ![.ova ファイルをダウンロードする](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>コレクター アプライアンスを確認する

.OVA ファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、OVA のハッシュを生成します。
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 生成されたハッシュは、次の設定と一致する必要があります。

  OVA バージョン 1.0.9.14 の場合

    **アルゴリズム** | **ハッシュ値**
    --- | ---
    MD5 | 6d8446c0eeba3de3ecc9bc3713f9c8bd
    SHA1 | e9f5bdfdd1a746c11910ed917511b5d91b9f939f
    SHA256 | 7f7636d0959379502dfbda19b8e3f47f3a4744ee9453fc9ce548e6682a66f13c

  OVA バージョン 1.0.9.12 の場合

    **アルゴリズム** | **ハッシュ値**
    --- | ---
    MD5 | d0363e5d1b377a8eb08843cf034ac28a
    SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
    SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

  OVA バージョン 1.0.9.8 の場合

    **アルゴリズム** | **ハッシュ値**
    --- | ---
    MD5 | b5d9f0caf15ca357ac0563468c2e6251
    SHA1 | d6179b5bfe84e123fabd37f8a1e4930839eeb0e5
    SHA256 | 09c68b168719cb93bd439ea6a5fe21a3b01beec0e15b84204857061ca5b116ff

    OVA バージョン 1.0.9.7 の場合

    **アルゴリズム** | **ハッシュ値**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c

    OVA バージョン 1.0.9.5 の場合

    **アルゴリズム** | **ハッシュ値**
    --- | ---
    MD5 | fb11ca234ed1f779a61fbb8439d82969
    SHA1 | 5bee071a6334b6a46226ec417f0d2c494709a42e
    SHA256 | b92ad637e7f522c1d7385b009e7d20904b7b9c28d6f1592e8a14d88fbdd3241c  

    OVA バージョン 1.0.9.2 の場合

    **アルゴリズム** | **ハッシュ値**
    --- | ---
    MD5 | 7326020e3b83f225b794920b7cb421fc
    SHA1 | a2d8d496fdca4bd36bfa11ddf460602fa90e30be
    SHA256 | f3d9809dd977c689dda1e482324ecd3da0a6a9a74116c1b22710acc19bea7bb2  

## <a name="create-the-collector-vm"></a>コレクター VM を作成する

ダウンロードしたファイルを vCenter Server にインポートします。

1. vSphere Client コンソールで、**[File]\(ファイル\)** > **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** の順にクリックします。

    ![OVF をデプロイする](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. [Deploy OVF Template]\(OVF テンプレートのデプロイ\) ウィザードで **[Source]\(ソース\)** を選択し、.OVA ファイルの場所を指定します。
3. **[Name]\(名前\)** と **[Location]\(場所\)** で、コレクター VM のフレンドリ名と、VM がホストされるインベントリ オブジェクトを指定します。
5. **[Host/Cluster]\(ホスト/クラスター\)** で、コレクター VM が実行するホストまたはクラスターを指定します。
7. ストレージで、コレクター VM の保存先を指定します。
8. **[Disk Format]\(ディスク フォーマット\)** で、ディスクの種類とサイズを指定します。
9. **[Network Mapping]\(ネットワーク マッピング\)** で、コレクター VM の接続先となるネットワークを指定します。 このネットワークには、Azure にメタデータを送信するためのインターネット接続が必要です。
10. 設定を再確認したら、**[Finish]\(完了\)** をクリックします。

## <a name="run-the-collector-to-discover-vms"></a>コレクターを実行して VM を検出する

1. vSphere Client コンソールで、[VM] を右クリックして **[Open Console]\(コンソールを開く\)** を選択します。
2. アプライアンスの設定で、言語、タイム ゾーン、パスワードを指定します。
3. デスクトップにある **[Run collector]\(コレクターの実行\)** ショートカットをクリックします。
4. コレクター UI の上部バーの **[Check for updates]\(更新プログラムの確認\)** をクリックし、コレクターが最新のバージョンで実行されていることを確認します。 ない場合は、リンクから最新のアップグレード パッケージをダウンロードしてコレクターを更新することもできます。
5. Azure Migrate Collector で、**[Set Up Prerequisites]\(前提条件の設定\)** を開きます。
    - ライセンス条項に同意し、サード パーティの情報を確認します。
    - VM がインターネットにアクセスできることをコレクターがチェックします。
    - VM がプロキシ経由でインターネットにアクセスしている場合は、**[Proxy settings]\(プロキシの設定\)** をクリックし、プロキシ アドレスとリスニング ポートを指定します。 プロキシで認証が必要な場合は、資格情報を指定します。 インターネット接続要件と、コレクターがアクセスする URL の一覧については、[こちら](https://docs.microsoft.com/azure/migrate/concepts-collector#internet-connectivity)を参照してください。

    > [!NOTE]
    > プロキシのアドレスを、 http://ProxyIPAddress または http://ProxyFQDN の形式で入力する必要があります。 サポートされるのは HTTP プロキシのみです。 インターセプトするプロキシがあるときに、プロキシ証明書をインポートしていない場合は、初回のインターネット接続は失敗することがあります。プロキシ証明書を信頼された証明書としてコレクター VM にインポートすることでこの問題を修正する方法については、[こちら](https://docs.microsoft.com/azure/migrate/concepts-collector#internet-connectivity-with-intercepting-proxy)を参照してください。

    - コレクター サービスが実行されていることをコレクターがチェックします。 このサービスは、既定でコレクター VM にインストールされています。
    - VMware PowerCLI をダウンロードしてインストールします。

6. **[Specify vCenter Server details]\(vCenter Server 詳細の指定\)** で、次の操作を行います。
    - vCenter サーバーの名前 (FQDN) または IP アドレスを指定します。
    - **[ユーザー名]** と **[パスワード]** で、コレクターが vCenter サーバーの VM を検出するために使用する読み取り専用の資格情報を指定します。
    - **[コレクション スコープ]** で、VM 検出のスコープを選択します。 コレクターは、指定されたスコープ内の VM のみを検出できます。 スコープは、指定のフォルダー、データセンター、またはクラスターに設定できます。 VM の数は 1,500 台を超えないようにします。 大規模な環境を検出する方法の詳細については、[こちら](how-to-scale-assessment.md)を参照してください。

7. **[Specify migration project]\(移行プロジェクトの指定\)** で、ポータルからコピーした Azure Migrate プロジェクトの ID とキーを指定します。 ID とキーをコピーしなかった場合は、コレクター VM から Azure Portal を開きます。 プロジェクトの **[概要]** ページで、**[マシンの検出]** をクリックして値をコピーします。  
8. **[View collection progress]\(収集の進行状況の表示\)** で検出を監視し、VM から収集されたメタデータがスコープ内にあることを確認します。 コレクターがおおよその検出時間を表示します。 Azure Migrate Collector によって収集されるデータの詳細については、[こちら](https://docs.microsoft.com/azure/migrate/concepts-collector#what-data-is-collected)を参照してください。

> [!NOTE]
> コレクターは、オペレーティング システムの言語およびコレクター インターフェイスの言語として、"英語 (米国)" のみをサポートしています。
> 評価するマシンの設定を変更する場合は、評価を実行する前に、検出をもう一度トリガーします。 コレクターで、**[コレクションをもう一度開始します]** オプションを使用してこの操作を実行します。 コレクションが完了したら、ポータルで評価の **[再計算]** オプションを選択して、更新された評価結果を取得します。



### <a name="verify-vms-in-the-portal"></a>ポータル内での VM の特定

検出時間は検出している VM の数によって異なります。 通常、VM が 100 台の場合、コレクターが実行を終了した後、検出が完了するまで約 1 時間かかります。

1. Azure Migrate プロジェクト内で、**[管理]** > **[マシン]** の順にクリックします。
2. 検出対象の VM がポータルに表示されていることを確認します。


## <a name="create-and-view-an-assessment"></a>評価を作成して表示する

VM が検出された後、それらをグループ化して、評価を作成します。

1. プロジェクトの **[概要]** ページで、**[+ 評価の作成]** をクリックします。
2. **[すべて表示]** をクリックして、評価のプロパティを確認します。
3. グループを作成し、グループ名を指定します。
4. グループに追加するマシンを選びます。
5. **[評価の作成]** をクリックして、グループと評価を作成します。
6. 評価が作成された後、**[概要]** > **[ダッシュボード]** でそれを表示します。
7. **[評価のエクスポート]** をクリックし、Excel ファイルとしてダウンロードします。

### <a name="assessment-details"></a>評価の詳細

評価に含まれる情報は、オンプレミス VM が Azure と互換性があるかどうか、Azure で VM を実行するにはどのくらいの VM サイズが適切か、および推定月間 Azure コストがどのくらいかです。

![評価レポート](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Azure の準備状況

評価の Azure 対応性ビューには、各 VM の対応状態が表示されます。 VM のプロパティに応じて、各 VM は次のようにマークされます。
- Azure に対応
- Azure に条件付きで対応
- Azure に未対応
- 対応不明

準備ができている VM の場合、Azure Migrate は Azure での VM のサイズを推奨します。 Azure Migrate による推奨サイズは、評価プロパティで指定されたサイズ変更の設定基準に依存します。 サイズ変更の設定基準が「パフォーマンス ベース」である場合は、VM (CPU とメモリ) とディスク (IOPS とスループット) のパフォーマンス履歴を考慮してサイズのレコメンデーションが行われます。 サイズ変更の設定基準が「オンプレミス」の場合、Azure Migrate は VM やディスクのパフォーマンス データを考慮しません。 Azure の VM サイズのレコメンデーションは、オンプレミスの VM のサイズを見て行われ、ディスク サイズの設定はアセスメント プロパティで指定されたストレージの種類の基づいて行われます (既定はプレミアム ディスクです)。 Azure Migrate でのサイズ変更方法の詳細については、[こちら](concepts-assessment-calculation.md)を参照してください。

Azure に未対応か条件付きで対応の VM の場合、Azure Migrate には対応の問題の説明と修復の手順が表示されます。

Azure Migrate が (データを利用できないために) Azure 対応性を識別できない VM は、対応不明とマークされます。

Azure Migrate では、Azure 対応性とサイズ変更の他に、VM の移行に使用できるツールの提案も行います。 そのためには、オンプレミス環境に対して、より詳細な検出を行う必要があります。 オンプレミス マシンにエージェントをインストールすることによって、より詳細な検出を実行する方法を[こちら](how-to-get-migration-tool.md)でご覧ください。 オンプレミス マシンにエージェントがインストールされていない場合は、[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) を使用したリフトアンドシフト移行が提案されます。 Azure Migrate は、オンプレミスのマシンにエージェントがインストールされている場合、そのマシン内で実行されているプロセスに注目し、そのマシンがデータベース マシンであるかどうかを特定します。 データベース マシンである場合、移行ツールとして [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) が、それ以外の場合は Azure Site Recovery が提案されます。

  ![アセスメントの準備状態](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>月額料金の見積もり

このビューには、Azure で VM を実行する合計計算コストとストレージ コストと、各マシンの詳細が表示されます。 コストの見積もりは、特定のマシンに関して Azure Migrate が提示するサイズの推奨情報とそのディスク数、および評価のプロパティを加味して計算されます。

> [!NOTE]
> Azure Migrate に提供されているコスト見積もりは、サービスとしての Azure インフラストラクチャ (IaaS) VM としてオンプレミスの VM を実行するために用意されています。 Azure Migrate では、サービスとしてのプラットホーム (PaaS) のコスト、またはサービスとしてのソフトウェア (SaaS) のコストは考慮されません。

コンピューティングとストレージの見積もり月額コストが、グループ内の全 VM について集計されます。

![評価の VM コスト](./media/tutorial-assessment-vmware/assessment-vm-cost.png)

#### <a name="confidence-rating"></a>信頼度レーティング

Azure Migrate のパフォーマンスベースの各評価は、1 つ星から 5 つ星 (1 つ星が最低で 5 つ星が最高) の範囲の信頼度レーティングに関連付けられています。 信頼度レーティングは、評価の計算に必要なデータ ポイントの可用性に基づいて、評価に割り当てられます。 評価の信頼度レーティングは、Azure Migrate による推奨サイズの信頼性を判断する目安となります。 信頼度レーティングは、オンプレミスの評価としては適用されません。

サイズ変更がパフォーマンス ベースの場合、Azure Migrate には VM の CPU とメモリの使用率データが必要です。 さらに、VM に接続されている各ディスクについて、ディスクの IOPS とスループットのデータが必要です。 同様に、サイズ変更をパフォーマンス ベースで行う場合、Azure Migrate には VM に接続されている各ネットワーク アダプターについても、ネットワークの入出力が必要です。 上記の使用率の数値のいずれかが vCenter Server にない場合、Azure Migrate による推奨サイズは信頼できないことがあります。 次のように、使用可能なデータ ポイントの割合に応じて、評価の信頼度レーティングが決まります。

   **データ ポイントの可用性** | **信頼度レーティング**
   --- | ---
   0% - 20% | 1 つ星
   21% - 40% | 2 つ星
   41% - 60% | 3 つ星
   61% - 80% | 4 つ星
   81% - 100% | 5 つ星

以下のいずれかの理由により、評価ですべてのデータ ポイントを使用することができない場合があります。
- vCenter Server の統計情報設定が、レベル 3 に設定されていません。 vCenter Server の統計設定がレベル 3 未満である場合、ディスクおよびネットワークのパフォーマンス データが vCenter Server から収集されません。 この場合、ディスクとネットワークに関して Azure Migrate から提示される推奨情報は、使用率ベースではありません。 ディスクの IOPS/スループットを考慮しないと、Azure Migrateは、Azure でプレミアム ディスクが必要であるかどうかを判断できないため、この場合、Azure Migrate はすべてのディスクに Standard ディスクを推奨します。
- vCenter Server の統計設定は、検出を開始する前に、短期間レベル 3 に設定されていました。 たとえば、今日、統計設定レベルを 3 に変更し、明日 (24 時間後) にコレクター アプライアンスを使用して検出を開始するシナリオを考えてみましょう。 1 日の評価を作成するのであれば、すべてのデータ ポイントが使用でき、評価の信頼度レーティングは 5 つ星になります。 しかし、評価プロパティのパフォーマンス期間を 1 か月に変更すると、過去 1 か月のディスクおよびネットワークのパフォーマンス データは利用できないため、信頼度レーティングは低下します。 過去 1 か月間のパフォーマンス データを考慮する場合は、検出を開始する前の 1 か月間、vCenter Server の統計設定をレベル 3 に保つことをお勧めします。
- 評価の計算対象である期間中に、いくつかの VM がシャットダウンされました。 いくつかの VM でしばらくの間電源がオフになっていた場合、vCenter Server はその期間のパフォーマンス データを使用できなくなります。
- 評価の計算対象である期間中に、いくつかの VM が作成されました。 たとえば、過去 1 か月間のパフォーマンス履歴の評価を作成しているのに、ほんの 1 週間前にいくつかの VM が環境内に作成されたとします。 このような場合、新しい VM のパフォーマンス履歴は、期間全体にわたっては存在しません。

> [!NOTE]
> 評価の信頼度レーティングが 4 つ星未満の場合は、vCenter Server の統計設定レベルを 3 に変更し、評価対象として考慮する期間 (1 日/1 週間/1 か月) 待機してから検出と評価を行うことをお勧めします。 そのようにできない場合は、パフォーマンス ベースのサイズ変更を信頼できないことがあるため、評価プロパティを変更して、"*オンプレミスと同じサイズ*" に切り替えることをお勧めします。

## <a name="next-steps"></a>次の手順

- [要件に基づいて、評価をカスタマイズする方法](how-to-modify-assessment.md)を学習します。
- [マシンの依存関係マッピング](how-to-create-group-machine-dependencies.md)を使って、信頼度の高い評価グループを作成する方法を確認します。
- 評価の計算方法の[詳細を確認](concepts-assessment-calculation.md)します。
- 大規模な VMware 環境の検出と評価の方法を[確認](how-to-scale-assessment.md)します。
- Azure Migrate に関する FAQ の[詳細を確認](resources-faq.md)します。
