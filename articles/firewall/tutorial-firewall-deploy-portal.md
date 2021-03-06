---
title: Azure portal を使用して Azure Firewall をデプロイして構成する
description: このチュートリアルでは、Azure portal を使用して Azure Firewall をデプロイおよび構成する方法を学習します。
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: tutorial
ms.date: 7/11/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 05959143431a2cc11d79a4012f45eb565c1c91f2
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2018
ms.locfileid: "45575999"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>チュートリアル: Azure portal を使用して Azure Firewall をデプロイして構成する

Azure Firewall には、発信アクセスを制御する 2 種類のルールがあります。

- **アプリケーション ルール**

   サブネットからアクセスできる完全修飾ドメイン名 (FQDN) を構成することができます。 たとえば、サブネットから *github.com* へのアクセスを許可することができます。
- **ネットワーク ルール**

   送信元アドレス、プロトコル、宛先ポート、および送信先アドレスを含むルールを構成できます。 たとえば、サブネットから DNS サーバーの IP アドレスに向かうポート 53 (DNS) 宛てのトラフィックを許可するルールを作成できます。

ネットワーク トラフィックは、サブネットの既定ゲートウェイとしてのファイアウォールにルーティングしたときに、構成されているファイアウォール ルールに制約されます。

アプリケーション ルールとネットワーク ルールは "*ルール コレクション*" に格納されます。 ルール コレクションは、同じアクションと優先順位を共有するルールのリストです。  ネットワーク ルール コレクションはネットワーク ルールのリストであり、アプリケーション ルール コレクションはアプリケーション ルールのリストです。

ネットワーク ルール コレクションは、常にアプリケーション ルール コレクションより先に処理されます。 すべてのルールは処理を終了させるため、ネットワーク ルール コレクション内で一致が見つかると、そのセッションの後続のアプリケーション ルール コレクションは処理されません。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * テスト ネットワーク環境を設定する
> * ファイアウォールをデプロイする
> * 既定のルートを作成する
> * アプリケーション ルールを構成する
> * ネットワーク ルールを構成する
> * ファイアウォールをテストする



Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Azure Firewall の記事の例では、Azure Firewall パブリック プレビューを既に有効にしていることを前提としています。 詳細については、「[Enable the Azure Firewall public preview (Azure Firewall パブリック プレビューを有効にする)](public-preview.md)」を参照してください。

このチュートリアルでは、次の 3 つのサブネットを含む単一の VNet を作成します。
- **FW-SN** - このサブネットにはファイアウォールがあります。
- **Workload-SN** - このサブネットにはワークロード サーバーがあります。 このサブネットのネットワーク トラフィックは、ファイアウォールを通過します。
- **Jump-SN** - このサブネットには "ジャンプ" サーバーがあります。 ジャンプ サーバーには、リモート デスクトップを使用して接続できるパブリック IP アドレスがあります。 そこから、(別のリモート デスクトップを使用して) ワークロード サーバーに接続できます。

![チュートリアルのネットワーク インフラストラクチャ](media/tutorial-firewall-rules-portal/Tutorial_network.png)

このチュートリアルでは、容易にデプロイできるように簡素化されたネットワーク構成を使用します。 運用環境のデプロイでは、[ハブとスポーク モデル](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)を採用して、独自の VNet にファイアウォールを配置し、1 つ以上のサブネットを含む同じリージョンのピアリングされた VNet にワークロード サーバーを配置することをお勧めします。



## <a name="set-up-the-network-environment"></a>ネットワーク環境を設定する
最初に、ファイアウォールをデプロイするために必要なリソースを含めるリソース グループを作成します。 次に、VNet、サブネット、およびテスト サーバーを作成します。

### <a name="create-a-resource-group"></a>リソース グループの作成
1. Azure Portal ([http://portal.azure.com](http://portal.azure.com)) にサインインします。
1. Azure portal のホーム ページで **[リソース グループ]** をクリックし、**[追加]** をクリックします。
2. **[リソース グループ名]** に「**Test-FW-RG**」と入力します。
3. **[サブスクリプション]** で、ご使用のサブスクリプションを選択します。
4. **[リソース グループの場所]** で、場所を選択します。 後で作成するすべてのリソースは同じ場所にある必要があります。
5. **Create** をクリックしてください。


### <a name="create-a-vnet"></a>VNet を作成する
1. Azure portal のホーム ページで、**[すべてのサービス]** をクリックします。
2. **[ネットワーク]** で、**[仮想ネットワーク]** をクリックします。
3. **[追加]** をクリックします。
4. **[名前]** に「**Test-FW-VN**」と入力します。
5. **[アドレス空間]** に「**10.0.0.0/16**」と入力します。
7. **[サブスクリプション]** で、ご使用のサブスクリプションを選択します。
8. **[リソース グループ]** で **[既存のものを使用]** を選択し、**[Test-FW-RG]** を選択します。
9. **[場所]** で、以前使用したのと同じ場所を選択します。
10. **[サブネット]** で、**[名前]** に「**AzureFirewallSubnet**」と入力します。

    ファイアウォールはこのサブネットに配置されます。サブネット名は AzureFirewallSubnet **でなければなりません**。
11. **[アドレス範囲]** に「**10.0.1.0/24**」と入力します。
12. 他の設定については既定値を使用し、**[作成]** をクリックします。

> [!NOTE]
> AzureFirewallSubnet サブネットの最小サイズは /25 です。

### <a name="create-additional-subnets"></a>追加のサブネットを作成する

次に、ジャンプ サーバーのサブネットとワークロード サーバーのサブネットを作成します。

1. Azure portal のホーム ページで **[リソース グループ]** をクリックし、**[Test-FW-RG]** をクリックします。
2. **[Test-FW-VN]** 仮想ネットワークをクリックします。
3. **[サブネット]**、**[+ サブネット]** の順にクリックします。
4. **[名前]** に「**Workload-SN**」と入力します。
5. **[アドレス範囲]** に「**10.0.2.0/24**」と入力します。
6. Click **OK**.

名前が **Jump-SN**、アドレス範囲が **10.0.3.0/24** の別のサブネットを作成します。

### <a name="create-virtual-machines"></a>仮想マシンを作成する

次に、ジャンプおよびワークロード仮想マシンを作成し、適切なサブネットに配置します。

1. Azure portal のホーム ページで、**[すべてのサービス]** をクリックします。
2. **[Compute]** で、**[仮想マシン]** をクリックします。
3. **[追加]**、**[Windows Server]**、**[Windows Server 2016 Datacenter]**、**[作成]** を順にクリックします。

**基本**

1. **[名前]** に「**Srv-Jump**」と入力します。
5. ユーザー名とパスワードを入力します。
6. **[サブスクリプション]** で、ご使用のサブスクリプションを選択します。
7. **[リソース グループ]** で **[既存のものを使用]** を選択し、**[Test-FW-RG]** をクリックします。
8. **[場所]** で、以前使用したのと同じ場所を選択します。
9. Click **OK**.

**サイズ**

1. Windows Server を実行するテスト仮想マシンの適切なサイズを選択します。 例: **[B2ms]** (8 GB RAM、16 GB のストレージ)。
2. **[選択]** をクリックします。

**設定**

1. **[ネットワーク]** の **[仮想ネットワーク]** で、**[Test-FW-VN]** を選択します。
2. **[サブネット]** で、**[Jump-SN]** を選択します。
3. **[パブリック受信ポートを選択]** で、**[RDP (3389)]** を選択します。 

    パブリック IP アドレスへのアクセスを制限する一方で、ポート 3389 を開いてリモート デスクトップをジャンプ サーバーに接続できるようにする必要があります。 
2. 他の設定については既定値のままにし、**[OK]** をクリックします。

**まとめ**

概要を確認し、**[作成]** をクリックします。 これが完了するまでに数分かかります。

このプロセスを繰り返して、**Srv-Work** という名前の別の仮想マシンを作成します。

次の表の情報を使用して、Srv-Work 仮想マシンの **[設定]** を構成します。 残りの構成は、Srv-Jump 仮想マシンと同じです。


|Setting  |値  |
|---------|---------|
|サブネット|Workload-SN|
|パブリック IP アドレス|なし|
|パブリック受信ポートを選択|パブリック受信ポートなし|


## <a name="deploy-the-firewall"></a>ファイアウォールをデプロイする

1. ポータルのホーム ページで、**[リソースの作成]** をクリックします。
2. **[ネットワーク]** をクリックし、**[おすすめ]** の後で **[すべて表示]** をクリックします。
3. **[ファイアウォール]** をクリックし、**[作成]** をクリックします。 
4. **[ファイアウォールの作成]** ページで、次の表を使用してファイアウォールを構成します。
   
   |Setting  |値  |
   |---------|---------|
   |Name     |Test-FW01|
   |サブスクリプション     |\<該当するサブスクリプション\>|
   |リソース グループ     |**[既存のものを使用]**: Test-FW-RG |
   |Location     |以前使用したのと同じ場所を選択します|
   |仮想ネットワークの選択     |**[既存のものを使用]**: Test-FW-VN|
   |パブリック IP アドレス     |**新規作成**。 パブリック IP アドレスは、Standard SKU タイプであることが必要です。|

2. **[Review + create]\(レビュー + 作成\)** をクリックします。
3. 概要を確認し、**[作成]** をクリックしてファイアウォールを作成します。

   デプロイが完了するまでに数分かかります。
4. デプロイが完了したら、**Test-FW-RG** リソース グループに移動し、**Test-FW01** ファイアウォールをクリックします。
6. プライベート IP アドレスをメモします。 後で既定のルートを作成するときにこれを使用します。


## <a name="create-a-default-route"></a>既定のルートを作成する

**Workload-SN** サブネットでは、送信の既定ルートがファイアウォールを通過するように構成します。

1. Azure portal のホーム ページで、**[すべてのサービス]** をクリックします。
2. **[ネットワーク]** で、**[ルート テーブル]** をクリックします。
3. **[追加]** をクリックします。
4. **[名前]** に「**Firewall-route**」と入力します。
5. **[サブスクリプション]** で、ご使用のサブスクリプションを選択します。
6. **[リソース グループ]** で **[既存のものを使用]** を選択し、**[Test-FW-RG]** を選択します。
7. **[場所]** で、以前使用したのと同じ場所を選択します。
8. **Create** をクリックしてください。
9. **[更新]** をクリックし、**[Firewall-route]** ルート テーブルをクリックします。
10. **[サブネット]**、**[関連付け]** の順にクリックします。
11. **[仮想ネットワーク]** をクリックし、**[Test-FW-VN]** を選択します。
12. **[サブネット]** で、**[Workload-SN]** をクリックします。
13. Click **OK**.
14. **[ルート]** をクリックし、**[追加]** をクリックします。
15. **[ルート名]** に「**FW-DG**」と入力します。
16. **[アドレス プレフィックス]** に「**0.0.0.0/0**」と入力します。
17. **[次ホップの種類]** で、**[仮想アプライアンス]** を選択します。

    Azure Firewall は実際はマネージド サービスですが、この状況では仮想アプライアンスが動作します。
1. **[次ホップ アドレス]** に、前にメモしておいたファイアウォールのプライベート IP アドレスを入力します。
2. Click **OK**.


## <a name="configure-application-rules"></a>アプリケーション ルールを構成する


1. **Test-FW-RG** を開き、**[Test-FW01]** ファイアウォールをクリックします。
1. **Test-FW01** ページの **[設定]** で、**[ルール]** をクリックします。
2. **[アプリケーション ルール コレクションの追加]** をクリックします。
3. **[名前]** に「**App-Coll01**」と入力します。
1. **[優先度]** に「**200**」と入力します。
2. **[アクション]** で、**[許可]** を選択します。

6. **[ルール]** の **[名前]** に「**AllowGH**」と入力します。
7. **[ソース アドレス]** に「**10.0.2.0/24**」と入力します。
8. **[プロトコル:ポート]** に「**http, https**」と入力します。 
9. **[ターゲットの FQDN]** に「**github.com**」と入力します
10. **[追加]** をクリックします。

> [!NOTE]
> Azure Firewall には、既定で許可されるインフラストラクチャ FQDN 用の組み込みのルール コレクションが含まれています。 これらの FQDN はプラットフォームに固有であり、他の目的には使用できません。 許可されるインフラストラクチャ FQDN には次の項目が含まれます。
>- ストレージのプラットフォーム イメージ リポジトリ (PIR) への Compute アクセス。
>- マネージド ディスク状態ストレージ アクセス。
>- Windows 診断
>
> この組み込みインフラストラクチャ ルール コレクションをオーバーライドするには、最後に処理される "*すべて拒否*" アプリケーション ルール コレクションを作成します。 このコレクションは、常にインフラストラクチャ ルール コレクションより先に処理されます。 インフラストラクチャ ルール コレクションに含まれていないものは既定で拒否されます。

## <a name="configure-network-rules"></a>ネットワーク ルールを構成する

1. **[ネットワーク ルール コレクションの追加]** をクリックします。
2. **[名前]** に「**Net-Coll01**」と入力します。
3. **[優先度]** に「**200**」と入力します。
4. **[アクション]** で、**[許可]** を選択します。

6. **[ルール]** の **[名前]** に「**AllowDNS**」と入力します。
8. **[プロトコル]** で **[UDP]** を選択します。
9. **[ソース アドレス]** に「**10.0.2.0/24**」と入力します。
10. [送信先アドレス] に「**209.244.0.3,209.244.0.4**」と入力します
11. **[宛先ポート]** に「**53**」と入力します。
12. **[追加]** をクリックします。

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>**Srv-Work** ネットワーク インターフェイスのプライマリおよびセカンダリ DNS アドレスを変更する

このチュートリアルのテスト目的で、プライマリおよびセカンダリ DNS アドレスを構成します。 これは、一般的な Azure Firewall 要件ではありません。 

1. Azure portal で、**Test-FW-RG** リソース グループを開きます。
2. **Srv-Work** 仮想マシンのネットワーク インターフェイスをクリックします。
3. **[設定]** で、**[DNS サーバー]** をクリックします。
4. **[DNS サーバー]** で、**[カスタム]** をクリックします。
5. **[DNS サーバーの追加]** テキスト ボックスに「**209.244.0.3**」と入力し、次のテキスト ボックスに「**209.244.0.4**」と入力します。
6. **[Save]** をクリックします。 
7. **Srv-Work** 仮想マシンを再起動します。


## <a name="test-the-firewall"></a>ファイアウォールをテストする

1. Azure portal で、**Srv-Work** 仮想マシンのネットワーク設定を確認し、プライベート IP アドレスをメモします。
2. リモート デスクトップを **Srv-Jump** 仮想マシンに接続し、そこから **Srv-Work** のプライベート IP アドレスへのリモート デスクトップ接続を開きます。

5. Internet Explorer を開き、 http://github.com を参照します。
6. セキュリティ アラートに対して **[OK]** をクリックし、**[閉じる]** をクリックします。

   GitHub のホーム ページが表示されます。

7. http://www.msn.com を参照します。

   ファイアウォールによってブロックされます。

これで、ファイアウォール ルールが動作していることを確認できました。

- 1 つの許可された FQDN は参照できますが、それ以外は参照できません。
- 構成された外部 DNS サーバーを使用して DNS 名を解決できます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

ファイアウォール リソースは、次のチュートリアルのために残しておいてもかまいませんが、不要であれば、**Test-FW-RG** リソース グループを削除して、ファイアウォール関連のすべてのリソースを削除してください。


## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * ネットワークのセットアップ
> * ファイアウォールを作成する
> * 既定のルートを作成する
> * アプリケーションおよびネットワーク ファイアウォール ルールを構成する
> * ファイアウォールをテストする

次に、Azure Firewall のログを監視することができます。

> [!div class="nextstepaction"]
> [チュートリアル: Azure Firewall のログを監視する](./tutorial-diagnostics.md)
