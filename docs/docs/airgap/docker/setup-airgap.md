---
title: Docker Airgap の使い方
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import Mermaid from '@theme/Mermaid';

## Docker Airgap とは？

Docker を利用し軽量な Ubuntu 環境を全自動で構築します

1. Docker Airgap は Windows / macOS / Linux いずれの環境でも同様の操作性を提供します
1. `cardano-cli`、`cardano-signer` コマンドが標準で付属します
1. 各コマンドを簡単に扱うためのツール `ctool` というツールが付属しています
    - `ctool` は `gtool` と連携しエアギャップでの作業効率を向上させます

## ctool とは？

SJGTool (gtool) と組み合わせて使用することでエアギャップでの作業効率を向上させます

エアギャップの共有ディレクトリから `cnode` ディレクトリにファイルを自動的にコピーをおこなったり、
`cnode` ディレクトリから共有ディレクトリにファイルを自動的にコピーするなど、
一手間二手間をを補完する事で作業効率をアップさせます。

### 出金トランザクション送信例

<Mermaid
  value={`sequenceDiagram;
    participant G as gtool<br/>BPのcnode;
    participant S as share;
    participant C as ctool<br/>エアギャップのcnode;
    Note over G: 出金処理開始;
    G->>S: tx.raw ファイルを転送;
    critical ctoolを使用すれば
      S-->>C: tx.raw ファイル自動コピー;
      Note over C: tx.raw 署名処理;
      Note over C: tx.signed 生成;
      C-->>S: tx.signed ファイル自動コピー;
    end
    S->>G: tx.signed ファイルを転送;
    Note over G: トランザクション送信;`}
/>

## 動作環境

- Windows
- macOS (Intel / Apple Silicon)
- Linux

## GitHub リポジトリ

[https://github.com/spo-kissa/cardano-airgap](https://github.com/spo-kissa/cardano-airgap)

## Docker Airgap をダウンロードする

[GitHub Releases](https://github.com/spo-kissa/cardano-airgap/releases)
ページから環境に応じたファイルをダウンロードします。

<Tabs groupId="operating-systems" queryString="os">
  <TabItem value="win" label="Windows">
    **airgap-xx.x.x.x-win.zip**
  </TabItem>
  <TabItem value="mac" label="macOS">
    **airgap-xx.x.x.x.tar.gz**
  </TabItem>
</Tabs>

## Docker Airgap を初期設定する

<Tabs groupId="operating-systems" queryString="os">
  <TabItem value="win" label="Windows">
    1. C ドライブ直下に `Cardano` ディレクトリを作成します

    2. `Cardano` ディレクトリ内に先ほどダウンロードした ZIP ファイルをコピーまたは移動します

    3. ZIP ファイルを右クリックし、メニューから `全て展開...` を選択します

    ![](/img/airgap/docker/win-zip-popup-menu.png)

    4. `圧縮(ZIP形式)フォルダーの展開` ダイアログで展開先を `C:\Cardano` にし `展開` を選択します

    ![](/img/airgap/docker/win-unzip-dialog.png)

    5. `C:\Cardano` ディレクトリ内に `airgap` ディレクトリが作成されている事を確認します

    ![](/img/airgap/docker/win-unzip-airgap-folder.png)

    6. `airgap` ディレクトリの名称を `airgap-ticker` に変更します

    7. `airgap-ticker` ディレクトリを右クリックし `ターミナルで開く` を選択します

    ![](/img/airgap/docker/win-open-terminal.png)

    8. ターミナルで以下のコマンドを実行します

    ```bash
    .\start.bat
    ```

    9. ターミナルのプロンプトが緑色で `cardano@xxxxxxxxxxxx:~$` と表示されれば、初期設定は完了です

      ※ `xxxxxxxxxxxx` の部分はランダムに設定されます

    ![](/img/airgap/docker/win-airgap-started.png)

  </TabItem>
  <TabItem value="mac" label="macOS">
    1. ターミナルアプリを起動し、以下のコマンドを実行すると、Finder が表示されます

    ```bash
    mkdir ~/Cardano
    cd ~/Cardano
    open .
    ```

    2. `Cardano` ディレクトリ内に先ほどダウンロードした `tar.gz` ファイルをコピーまたは起動します

    3. `tar.gz` ファイルをダブルクリックし解凍します

    4. `Cardano` ディレクトリ内に `airgap` ディレクトリが作成されている事を確認します

    5. `airgap` ディレクトリを `airgap-ticker` に変更します

    6. ターミナルで、以下のコマンドを実行します

    ```bash
    cd ~/Cardano/airgap-ticker
    ./start.sh
    ```

    7. ターミナルのプロンプトが緑色で `cardano@xxxxxxxxxxxx:~$` と表示されれば、初期設定は完了です

      ※ `xxxxxxxxxxxx` の部分はランダムに設定されます

  </TabItem>
</Tabs>

## Docker Airgap の共有フォルダ

**Docker Airgap では直接ネットワークには繋がらないように設定されています**
<Tabs groupId="operating-systems" queryString="os">
  <TabItem value="win" label="Windows">
    `Docker Airgap` の `/mnt/share/` ディレクトリと Windows の `airgap-ticker\share\` ディレクトリが共有ディレクトリとなり、ファイルやフォルダのやり取りが可能です

    <Mermaid
      value={`flowchart LR;
        A(**Airgap**<br/>/mnt/share) ==>|files| B(**Windows**<br/>airgap-ticker\\\\share);
        B ==>|files| A;`}
    />

  </TabItem>
  <TabItem value="mac" label="macOS">
    `Docker Airgap` の `/mnt/share/` ディレクトリと macOS の `airgap-ticker/share/` ディレクトリが共有ディレクトリとなり、ファイルやフォルダのやり取りが可能です

    <Mermaid
      value={`flowchart LR
        A(**Airgap**<br/>/mnt/share) ==>|files| B(**macOS**<br/>airgap-ticker/share);
        B ==>|files| A;`}
    />

  </TabItem>
</Tabs>

## Docker Airgap の終了のしかた

1. `Docker Airgap` のプロンプトで `exit` コマンドを実行しログアウトします

<Tabs groupId="operating-systems" queryString="os">
  <TabItem value="win" label="Windows">
    2. ターミナルで `exit` を実行しターミナル画面を閉じます
  </TabItem>
  <TabItem value="mac" label="macOS">
    2. ターミナルの左上の赤い✖ボタンをクリックしてターミナル画面を閉じます
  </TabItem>
</Tabs>
