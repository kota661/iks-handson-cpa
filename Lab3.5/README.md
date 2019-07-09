# Lab 3.5) 腕試し：IKS上にアプリをデプロイしてみよう


Lab1-3で学んできたことを元に指定のコンテナイメージをK8sにデプロイしてみましょう。
なお、このLabでは1つ1つの手順は記載していません。これまでの操作を思い出しながら試してみましょう。



![pacman](./images/pacman.png)



## アプリを公開するために必要な基本情報

* コンテナイメージ
  * kota661/pacman
* Image Tags
  * v1
* 公開ポート
  * 80



## アプリをIKSにデプロイする

これまで習った以下２パターンどちらで実行していただいても構いません。

* Lab 3で学んだ マニフェストファイルを使って**宣言的**にデプロイする

* Lab 1 で学んだ `kubectl run` を使って**命令的**にデプロイする

  


### Lab 3で学んだ マニフェストファイルを使用する場合
1. マニフェストファイルを取得します。次のGitリポジトリを手元のPCにダウンロードします。
    https://github.com/kota661/pacman.git
   
   <details><summary>ヒント</summary>
 
   > リポジトリのダウンロードは`git clone`コマンドを使います
 
    </details>

2. デプロイに必要なマニフェストファイルが有る`v1`のディレクトリに移動します。

   <details><summary>ヒント</summary>   
 
    > ディレクトリの移動は`cd`コマンドを使います
 
    </details>

3. まずはアプリをデプロイします。ディレクトリ内の`deployment.yml`を使ってアプリをIKS上にデプロイしましょう。

     <details><summary>ヒント1</summary>   
 
     > マニフェストファイルの適用は`kubectl apply` コマンドを利用します
 
    </details>
     
    <details><summary>ヒント2</summary>   
    
      > -f オプションを使うと適用するファイルを指定することができます
    
    </details>
 
    <details><summary>ヒント3</summary>
    
   > kubectl apply -f {ファイル名}
    
    </details>
     
4. アプリが正しくデプロイできたか確認してみましょう。

   <details><summary>ヒント1</summary>   
 
   > クラスターの情報を取得するには`kubectl get` コマンドを利用します
 
   </details>
     
   <details><summary>ヒント2</summary>   
 
    > アプリはKubernetesでは`pod`という単位でデプロイされています
 
    </details>

4. 外部からアプリにアクセスできるようにするために、`Service`を作成します。ディレクトリ内の`service.yml`を使ってアプリを外部公開しましょう。

   <details><summary>ヒント1</summary>  
 
   >  マニフェストファイルの適用は`kubectl apply` コマンドを利用します
 
    </details>
 
    <details><summary>ヒント2</summary>  
    
    > -f オプションを使うと適用するファイルを指定することができます

    </details>
   
    <details><summary>ヒント3</summary>  
 
     > kubectl apply -f {ファイル名}
 
    </details>

6. `Service`が正しくデプロイできたか確認してみましょう。また、同時に外部からアプリにアクセスするための公開ポートも確認しておきましょう

    <details><summary>ヒント1</summary>  

    > クラスターの情報を取得するには`kubectl get` コマンドを利用します

    </details>
    
    <details><summary>ヒント2</summary>  

    > kubectl get svc pacman
    
    </details>
    
    <details><summary>ヒント3</summary>  

    > 公開ポートは`PORT(S)`欄に書かれています
    
    </details>
    

5. アプリにアクセスするためにIPアドレスを確認します。IKSのワーカーノードのPublic IPを確認しましょう。

    <details><summary>ヒント</summary>  

    > ibmcloud ks workers mycluster

    </details>

7. IPアドレスと公開ポートを組み合わせて、ブラウザからアプリにアクセスしてみましょう。

    <details><summary>ヒント</summary>  
 
   > http://{ワーカーノードのPublicIP}:{サービス公開ポート}
   
   </details>
   

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



### Lab 1 で学んだ kubectl run を使って命令的にデプロイする場合

1. `kubectl run` コマンドを使ってデプロイしましょう
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
1. kubectl run コマンドを使ってデプロイしましょう

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





## オプション： より遊べるようにライフを３つ&hearts;&hearts;&hearts;にバージョンアップしよう



1プレー で ライフが１つしかなく、すぐにゲームが終わってしまうというレビューが多数寄せられています。

そこでv2を開発し1プレー で３つのライフに改良されました。現在稼働中のv1の環境をv2にアップグレードしましょう



* Lab 3で学んだ マニフェストファイルを使用する場合

  1. deployment.ymlのimageをv1からv2に変更

     > 変更前  image: kota661/pacman:v1
     > 変更後  image: kota661/pacman:v2

  2. kubectl apply コマンドを使って変更したdeployment.ymlを適用する

     > kubectl apply -f {ファイル名}

* Lab 1 で学んだ kubectl run を使って命令的にデプロイする場合

  1.  kubectl set image コマンドを使って v2 のイメージを指定

     > kubectl set image deploy/pacman pacman=kota661/pacman:v2

