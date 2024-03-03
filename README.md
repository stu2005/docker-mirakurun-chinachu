# docker-mirakurun-chinachu

[Mirakurun](https://github.com/Chinachu/Mirakurun) + [Chinachu](https://github.com/Chinachu/Chinachu) の Docker コンテナ

## 前提条件

- [Docker](https://docs.docker.com/engine/install/), [Docker Compose](https://docs.docker.com/compose/install/linux/#install-using-the-repository)の導入が必須
- ホスト上の pcscd は停止する
- DVBドライバ非対応のチューナーはホストへのドライバのインストールが必須 ([例](https://github.com/tsukumijima/px4_drv))

## インストール手順

```sh
#取得
git clone https://github.com/chinachu/docker-mirakurun-chinachu
cd docker-mirakurun-chinachu

#各種設定
nano ./compose.yaml

#mirakurunをセットアップ
sudo docker compose run --rm -e SETUP=true mirakurun

#DVBドライバ非対応チューナー向け
cp ./mirakurun/opt/bin/startup.sample ./mirakurun/opt/bin/startup
sudo nano ./mirakurun/opt/bin/startup
chmod +x ./mirakurun/opt/bin/startup
sudo nano ./mirakurun/config/tuners.yml

#チャンネル設定
sudo nano ./mirakurun/config/channels.yml
```

## 起動

```sh
sudo docker compose up -d --remove-orphans
```

## 状態確認

```sh
sudo docker compose ps
```

## ログ

```sh
sudo docker compose logs -f [chinachu|mirakurun]
```

## 停止

```sh
sudo docker compose down
```

## 更新

```sh
sudo docker compose up -d --pull always --build --force-recreate --remove-orphans
sudo docker container prune -f
sudo docker image prune -f
sudo docker builder prune -f
sudo docker volume prune -f
sudo docker network prune -f
```

## 初期化
```sh
sudo docker compose down -v --rmi all --remove-orphans
```

## 設定

### Mirakurun

* ポート番号: 40772

### Chinachu

* ポート番号: 20772

### 各種ファイル保存先

* 録画データ

  `./recorded/`

* Mirakurun 設定ファイル
  
  `./mirakurun/config/`

* Mirakurun OPT
  
  `./mirakurun/opt/`

* Chinachu 設定ファイル

  `./chinachu/config/`

## License
This software is released under the MIT License, see LICENSE.