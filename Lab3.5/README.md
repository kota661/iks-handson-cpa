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
    
   <details><summary>答え</summary>    
    
    ```shell
   git clone https://github.com/kota661/pacman.git
   ```
   
   </details>

2. デプロイに必要なマニフェストファイルが有る`v1`のディレクトリに移動します。

   <details><summary>ヒント</summary>   
 
    > ディレクトリの移動は`cd`コマンドを使います
 
    </details>
    
   <details><summary>答え</summary>    
 
   ```shell
   cd pacman/v1
   ```
   
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
    
   <details><summary>答え</summary>    
 
    ```shell
   kubectl apply -f deployment.yml
   ```
   
    </details>   
     
4. アプリが正しくデプロイできたか確認してみましょう。

   <details><summary>ヒント1</summary>   
 
   > クラスターの情報を取得するには`kubectl get` コマンドを利用します
 
   </details>
     
   <details><summary>ヒント2</summary>   
 
    > アプリはKubernetesでは`pod`という単位でデプロイされています
 
    </details>
    
   <details><summary>答え</summary>
 
   `kubectl get pod`
   
    </details>

4. 外部からアプリにアクセスできるようにするために、`Service`を作成します。ここではnode port方式を取ります。ディレクトリ内の`service-nodeport.yml`を使ってアプリを外部公開しましょう。

   <details><summary>ヒント1</summary>  
 
   >  マニフェストファイルの適用は`kubectl apply` コマンドを利用します
 
    </details>
 
    <details><summary>ヒント2</summary>  
    
    > -f オプションを使うと適用するファイルを指定することができます

    </details>
   
    <details><summary>ヒント3</summary>  
 
     > kubectl apply -f {ファイル名}
 
    </details>
    
   <details><summary>答え</summary>
 
    ```shell
   kubectl apply -f service-nodeport.yml
   ```

    </details>

6. `Service`が正しくデプロイできたか確認してみましょう。また、同時に外部からアプリにアクセスするための公開ポートも確認しておきましょう

    <details><summary>ヒント1</summary>  

    > クラスターの情報を取得するには`kubectl get` コマンドを利用します

    </details>
    
    <details><summary>ヒント2</summary>  

    > Serviceの情報は`service`または`svc`と指定すると取得できます
    
    </details>
    
    <details><summary>ヒント3</summary>  

    > 公開ポートは`PORT(S)`欄に書かれています
    
    </details>
    
   <details><summary>答え</summary>
 
   ```shell
   kubectl get svc pacman
   ```

   > 出力結果

   ```
   NAME     TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
   pacman   NodePort   172.21.19.21   <none>        80:31000/TCP   118m
   ```

   上記の場合は **31000** がサービス公開ポートです。
   
    </details>   
    

5. アプリにアクセスするためにIPアドレスを確認します。IKSのワーカーノードのPublic IPを確認しましょう。

    <details><summary>ヒント</summary>  

    > ibmcloud ks workers mycluster

    </details>
    
   <details><summary>答え</summary>    
    
    ```shell
   ID                                                 Public IP       Private IP      Machine Type        State    Status   Zone    Version
   kube-tok02-cr31c5bba7f8184f08817919fb0ee5d818-w1   169.56.30.123   10.129.177.57   u2c.2x4.encrypted   normal   Ready    tok02   1.13.5_1517*
   ```

   上記の場合は **169.56.30.123** がワーカーノードのPublicIPです。
   
    </details>   


7. IPアドレスと公開ポートを組み合わせて、ブラウザからアプリにアクセスしてみましょう。

    <details><summary>ヒント</summary>  
 
   > http://{ワーカーノードのPublicIP}:{サービス公開ポート}
   
   </details>
   
   <details><summary>答え</summary>   
   
   > 上記結果の場合は http://169.56.30.123:31000 です   
   
   </details>
   


### Lab 1 で学んだ `kubectl run` を使って命令的にデプロイする場合

1. `kubectl run` コマンドを使ってアプリをデプロイしましょう。必要な情報は冒頭の「基本情報」に書いてあります。

    <details><summary>ヒント1</summary>
 
   - `—image`オプションでコンテナイメージとバージョン（タグ）を指定します
   - `—port`オプションでコンテナの公開ポートを指定します
 
   </details>
   
    <details><summary>ヒント2</summary>   

   - kubectl run pacman --image={イメージ名} --port={公開ポート名}
   
   </details>
   
    <details><summary>答え</summary>      
   
   ```shell
   kubectl run pacman --image=kota661/pacman:v1 --port=80
   ```   

   </details>

2. 正しくデプロイされたか deployment の状態を確認してみましょう。また、後ほどアプリを外部公開する際に必要な deployment の名前も確認しておきましょう。
  
  
    <details><summary>ヒント1</summary>  

    > クラスターの情報を取得するには`kubectl get` コマンドを利用します

    </details>
    
    <details><summary>ヒント2</summary>  

    > deploymentの情報は`deployment`または`deploy`と指定すると取得できます
    
    </details>    
    
    <details><summary>答え</summary>      
    
    > kubectl get deployment
    
    </details>     

3. `kubectl expose` コマンドを使い、アプリをNodePortを使って外部に公開してみましょう。必要な情報は冒頭の「基本情報」に書いてあります。

    <details><summary>ヒント1</summary>
 
   > --portオプションで公開ポートを設定します
   > --typeオプションでNodePortを指定します

    </details>     

    <details><summary>ヒント2</summary>
 
    > kubectl expose deployment {デプロイメント名} --port {公開ポート} --type NodePort
    
    </details>         
    
    <details><summary>答え</summary>    
 
    ```shell
   kubectl expose deployment pacman --port 80 --type NodePort
   ```
   
    </details> 
    
    
6. `Service`が正しくデプロイできたか確認してみましょう。また、同時に外部からアプリにアクセスするための公開ポートも確認しておきましょう

    <details><summary>ヒント1</summary>  

    > クラスターの情報を取得するには`kubectl get` コマンドを利用します

    </details>
    
    <details><summary>ヒント2</summary>  

    > Serviceの情報は`service`または`svc`と指定すると取得できます
    
    </details>
    
    <details><summary>ヒント3</summary>  

    > 公開ポートは`PORT(S)`欄に書かれています
    
    </details>
    
   <details><summary>答え</summary>
 
   ```shell
   kubectl get svc pacman
   ```

   > 出力結果

   ```
   NAME     TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
   pacman   NodePort   172.21.19.21   <none>        80:31000/TCP   118m
   ```

   上記の場合は **31000** がサービス公開ポートです。
   
    </details>   
    

5. アプリにアクセスするためにIPアドレスを確認します。IKSのワーカーノードのPublic IPを確認しましょう。

    <details><summary>ヒント</summary>  

    > ibmcloud ks workers mycluster

    </details>
    
   <details><summary>答え</summary>    
    
    ```shell
   ID                                                 Public IP       Private IP      Machine Type        State    Status   Zone    Version
   kube-tok02-cr31c5bba7f8184f08817919fb0ee5d818-w1   169.56.30.123   10.129.177.57   u2c.2x4.encrypted   normal   Ready    tok02   1.13.5_1517*
   ```

   上記の場合は **169.56.30.123** がワーカーノードのPublicIPです。
   
    </details>   


7. IPアドレスと公開ポートを組み合わせて、ブラウザからアプリにアクセスしてみましょう。

    <details><summary>ヒント</summary>  
 
   > http://{ワーカーノードのPublicIP}:{サービス公開ポート}
   
   </details>
   
   <details><summary>答え</summary>   
   
   > 上記結果の場合は http://169.56.30.123:31000 です   
   
   </details>
   

## オプション： より遊べるようにライフを３つ&hearts;&hearts;&hearts;にバージョンアップしよう



1プレー で ライフが１つしかなく、すぐにゲームが終わってしまうというレビューが多数寄せられています。

そこでv2を開発し1プレー で３つのライフに改良されました。現在稼働中のv1の環境をv2にアップグレードしましょう



* Lab 3で学んだ マニフェストファイルを使用する場合

  1. deployment.ymlのimageをv1からv2に変更しましょう

     > 変更前  image: kota661/pacman:v1
     > 変更後  image: kota661/pacman:v2
     

  2. kubectl apply コマンドを使って変更したdeployment.ymlを適用しましょう

     > kubectl apply -f {ファイル名}

* Lab 1 で学んだ kubectl run を使って命令的にデプロイする場合

  1.  kubectl set image コマンドを使って v2 のイメージを指定しましょう

     > kubectl set image deploy/pacman pacman=kota661/pacman:v2

