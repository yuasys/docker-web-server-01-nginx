## 概要
- これはDockerを利用したローカル開発用のWebサーバーです
- WebサーバーにNGINXが利用されています
- 静的サイト（HTML/CSS/JavaScritp）開発に適しています

### 注意点
- NGINXの開発環境での学習用を想定しています

## 使い方
### １．ダウンロード
```
git clone https://github.com/yuasys/docker-web-server-01-nginx.git
```

起動や停止は、以下でディレクトリを移動してから行ってください。
```
cd docker-web-server-01-nginx
```

### ２．イメージを作成
```
docker build -t my-nginx .
```

オプションの解説  

> -t イメージ名:タグ をつける  
    ここではイメージ名はmy-nginx  
    タグ名はなし（なしの場合、デフォルトでlatestとなる）  
.  DockerFileの相対パス指定  
    ここではカレントディレクトリ内を指定している

### ３．起動
```
docker run --name web-server -p 8080:80 -d --rm my-nginx
```

起動後、以下のURLにアクセスして動作を確認できます。  
http://localhost:8080

※注意 VPSなどクラウド上のDocker環境で開発している場合はlocalhostの部分をVPSのurlまたはグローバルアドレスに適宜読み替えてください  
      例：http://xxx.xxxxx.xxx:8080

### ４．状態確認
```
docker ps
```
StateがUpになっていたらOKです。


### ５．コンテナの中に入って操作する
コンテナが動いているときに以下で、それぞれのコンテナの中で操作することができます。
```
docker exec -it web-server /bin/bash
```


コンテナから出るには以下を実行してください
```
exit
```


### ６．停止
```
docker stop web-server
```
