# Lab 3.5) 指定のイメージをk8sにデプロイしてみよう


Lab1-3で学んたことを元に指定のコンテナイメージをK8sにデプロイしてみましょう



![pacman](./images/pacman.png)



## 基本情報

* Container Image
  * kota661/pacman
* Image Tags
  * v1
* 公開ポート
  * 80



## 方法

これまで習った以下２パターンどちらで実行していただいても構いません

* Lab 3で学んだ マニフェストファイルを使用する場合

* Lab 1 で学んだ kubectl run を使って命令的にデプロイする場合

  


## Lab 3で学んだ マニフェストファイルを使用する場合
1. マニフェストファイルを取得
   * Gitリポジトリ
    https://github.com/kota661/pacman.git
   
   > git clone {リポジトリ名}

2. デプロイに必要なマニフェストファイルが有るv1のディレクトリに移動

   > cd pacman/v1

3. deployment.ymlを使ってアプリをデプロイ

   > kubectl apply コマンドを利用します
   > -f オプションを使ってdeployment.ymlを指定しましょう
   > kubectl apply -f {ファイル名}

4. service.ymlを使ってアプリを公開

   >  kubectl apply コマンドを利用します
   > -f オプションを使ってdeployment.ymlを指定しましょう
   > kubectl apply -f {ファイル名}

5. アプリアクセスのためのワーカーノードのPublicIPを確認

   > ibmcloud ks workers mycluster

6. アプリアクセスのためのサービス公開ポートを確認

   > kubectl get svc

7. アプリにアクセス

   > http://{ワーカーノードのPublicIP}:{サービス公開ポート}
   
   

<details><summary>答えを表示</summary>
<p>

1. マニフェストファイルを取得

   ```shell
   git clone https://github.com/kota661/pacman.git
   ```

2. デプロイに必要なマニフェストファイルが有るv1のディレクトリに移動

   ```shell
   cd pacman/v1
   ```

3. deployment.ymlを使ってアプリをデプロイ

   ```shell
   kubectl apply -f deployment.yml
   ```

4. service.ymlを使ってアプリを公開

   ```shell
   kubectl apply -f service.yml
   ```

5. アプリアクセスのためのワーカーノードのPublicIPを確認

   ```shell
   ibmcloud ks workers mycluster
   ```

   > 出力結果

   ```shell
   ID                                                 Public IP       Private IP      Machine Type        State    Status   Zone    Version
   kube-tok02-cr31c5bba7f8184f08817919fb0ee5d818-w1   169.56.30.123   10.129.177.57   u2c.2x4.encrypted   normal   Ready    tok02   1.13.5_1517*
   ```

   上記の場合は **169.56.30.123** がワーカーノードのPublicIPです。

6. アプリアクセスのためのサービス公開ポートを確認

   ```shell
   kubectl get svc pacman
   ```

   > 出力結果

   ```
   NAME     TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
   pacman   NodePort   172.21.19.21   <none>        80:31000/TCP   118m
   ```

   上記の場合は **31000** がサービス公開ポートです。

7. アプリにアクセス

   http://{ワーカーノードのPublicIP}:{サービス公開ポート}

   > 上記結果の場合は http://169.56.30.123:31000 です


</p>
</details>



## Lab 1 で学んだ kubectl run を使って命令的にデプロイする場合

1. kubectl run コマンドを使ってデプロイしましょう実行
   > —imageオプションでContainer Imageを指定します
   > —portオプションでContainerの公開ポートを指定します
   > kubectl run pacman --image={イメージ名} --port={公開ポート名}

2. デプロイした deployment の名前を確認
  
    > kubectl get deployment

3. kubectl expose コマンドを使ってアプリを公開

   > --portオプションで公開ポートを設定します
   > --typeオプションでNodePortを指定します
   > kubectl expose deployment {デプロイメント名} --port {公開ポート} --type NodePort

4. アプリアクセスのためのワーカーノードのPublicIPを確認

   > ibmcloud ks workers mycluster

5. アプリアクセスのためのサービス公開ポートを確認

   > kubectl get svc

6. アプリにアクセス

   > http://{ワーカーノードのPublicIP}:{サービス公開ポート}
   
   


<details><summary>答えを表示</summary>
<p>
1. kubectl run コマンドを使ってデプロイしましょう実行しましょう

   ```shell
   kubectl run pacman --image=kota661/pacman:v1 --port=80
   ```

2. デプロイした deployment の名前を確認しましょう

   ```shell
   kubectl get deployment
   ```

3. kubectl expose コマンドを使ってアプリにアクセスできるようにしましょう

   ```shell
   kubectl expose deployment pacman --port 80 --type NodePort
   ```

4. アプリアクセスのためのワーカーノードのPublicIPを確認

   ```shell
   ibmcloud ks workers mycluster
   ```

   > 出力結果

   ```shell
   ID                                                 Public IP       Private IP      Machine Type        State    Status   Zone    Version
   kube-tok02-cr31c5bba7f8184f08817919fb0ee5d818-w1   169.56.30.123   10.129.177.57   u2c.2x4.encrypted   normal   Ready    tok02   1.13.5_1517*
   ```

   上記の場合は **169.56.30.123** が取得したいワーカーノードのPublicIPです。

5. アプリアクセスのためのサービス公開ポートを確認

   ```shell
   kubectl get svc
   ```

   > 出力結果

   ```
   NAME     TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
   pacman   NodePort   172.21.19.21   <none>        80:31000/TCP   118m
   ```

   上記の場合は **31000** がサービス公開ポートです。

6. アプリにアクセス

   http://{ワーカーノードのPublicIP}:{サービス公開ポート}

   > 上記結果の場合は http://169.56.30.123:31000 です

</p>
</details>





## Option： より遊べるようにライフを３つ&hearts;&hearts;&hearts;にバージョンアップしよう



1プレー で ライフが１つしかなく、すぐにゲームが終わってしまうというレビューが多数寄せられています。

そこでv2を開発し1プレー で３つのライフに改良されました。v1の環境をv2にアップグレードしましょう



* Lab 3で学んだ マニフェストファイルを使用する場合

  1. deployment.ymlのimageをv1からv2に変更

     > 変更前  image: kota661/pacman:v1
     > 変更後  image: kota661/pacman:v2

  2. kubectl apply コマンドを使って変更したdeployment.ymlを適用する

     > kubectl apply -f {ファイル名}

* Lab 1 で学んだ kubectl run を使って命令的にデプロイする場合

  1.  kubectl set image コマンドを使って v2 のイメージを指定

     > kubectl set image deploy/pacman pacman=kota661/pacman:v2

