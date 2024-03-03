 # 使用するベースイメージを14-alpineに更新する。マルチステージビルドを利用して最終イメージをよりクリーンにする。
FROM docker.io/library/node:14-alpine AS build

RUN set -x && \
\
 # パッケージの更新
    apk upgrade -U --no-cache && \
\
 # ビルドに必要なパッケージの導入
    apk add -U --no-cache alpine-sdk python3 git bash && \
\
 # Gitの設定を変更しChinachuをクローンできるようにする。
    git config --global http.version HTTP/1.1 && \
\
 # Chinachuをサブモジュールごとクローン
    git clone --recursive https://github.com/chinachu/chinachu /src && \
\
 # 必要なディレクトリを作成
    mkdir /src/data/ /src/log/ /src/run/ /src/recorded/ /build/ && \
\
 # ビルドに必要なファイルのみを移動する。
    mv /src/common /src/package*json /build/ && \
    cd /build/ && \
\
 # Chinachuをビルド
    npm install && \
    npm update && \
    npm audit fix

 # processes.jsonをDockerや実行ユーザnodeでも使用可能なものに置き換える。
COPY ./processes.json /src/


####最終イメージ

 # node:14-alpineは既にメンテナンスされていない為、最新のパッケージを使用できるようにalpine:latestを取得する。
FROM docker.io/library/alpine:latest AS update_baseImage

 # ベースイメージ
FROM docker.io/library/node:14-alpine
LABEL maintainer "h-mineta <h-mineta@0nyx.net>"

 # パッケージリポジトリの設定を更新
COPY --from=update_baseImage /etc/apk/repositories /etc/apk/repositories

 # Chinachuのビルドとソースを、所有者をnodeとしてコピーする。
COPY --chown=node:node --from=build /src/ /app/
COPY --chown=node:node --from=build /build/ /app/

 # ワーキングディレクトリの設定
WORKDIR /app/

 # ポートの開放
EXPOSE 20772

 # パッケージの更新
RUN apk upgrade -U --no-cache && \

 # 動作に必要なパッケージを導入
    apk add -U --no-cache curl ffmpeg bash coreutils procps-ng ca-certificates && \
    npm install pm2 -g

 # コンテナ内の実行ユーザをnodeとする。
USER node:node

 # コンテナをchinachuが起動する実行ファイルとする。
ENTRYPOINT ["pm2-runtime"]
CMD ["./processes.json"]