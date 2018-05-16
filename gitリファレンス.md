# Gitの使い方

## 基本的な用語 
- ローカルリポジトリ  
 ローカルPCに保存されているGitリポジトリ  
 インデックスからコミットされたファイル・ディレクトリ
 
- ワーキングツリー  
Gitの管理対象ディレクトリ配下のファイル・ディレクトリ  
インデックスに登録されていない

- インデックス  
 git add されたファイル・ディレクトリ

- HEAD  
 最新コミット  
 HEAD~ は１世代前　~複数で世代を指定  
 HEAD＾を使うと親を指定できる

- リモートリポジトリ
 他のPC等に存在するリポジトリ  
 GithubやBitbucketなどのサービスが主

- ブランチ  
 リポジトリのバージョンを枝分かれさせて記録するもの  
 これによって複数の変更を同時進行で記録できる

---

## Gitで管理する初期手順  

- 管理対象ディレクトリのルートへ移動  
 $ cd 管理対象ディレクトリ

- ローカルリポジトリの作成  
 $ git init

- 除外ファイルの作成　※必須ではないが推奨  
 $ vi .gitignore  
   /.vagrant  
   など

- インデックスへの登録（全登録）  
 $ git add -A

- ローカルリポジトリへの登録（コミット）  
 $ git commit -m "コメント"

---
## リモートリポジトリサービス用のSSH鍵の生成

- 鍵の生成
 $ ssh-keygen -t rsa -C 登録メールアドレス
 $ cat ~/.ssh/id_rsa.pub


## 基本的な使い方 

- ワーキングツリーの状態確認  
 $ git status  
 $ git status -s

- ファイルをインデックスに登録  
 $ git add -A			        	# 全登録    
 $ git add src/example1.py	# 特定のファイルだけ登録

- 管理対象から削除  
 $ git rm example1.py			# 特定のファイルだけ削除  
 $ git rm -r ./src				# ディレクトリの削除  
 $ git rm --cached example1.py	# 管理対象外にするがファイルは残す場合  
 ※ファイルを残す場合は.gitignoreに入れないとまた管理対象になる  
 $ git add -A 					# ファイルを削除してから全登録すると削除と同等

- コミット  
 $ git commit -m "コメント"
 
- コミットログを確認する  
 $ git log

- インデックスを取り消す  
 $ git reset HEAD  
 $ git reset HEAD example1.py
 
- 特定のコミットまで戻す（コミットの存在が消える）  
 $ git reset --hard HEAD~  
 $ git reset --hard コミットID

- 特定のコミットを取り消す（特定のコミットのみがなかったことになる）  
 $ git revert -n コミットID

- ブランチの確認  
 $ git branch -a

- ブランチの作成  
 $ git branch ブランチ名

- ブランチの切り替え  
 $ git checkout ブランチ名

- ブランチの作成&切り替え  
 $ git checkout -b ブランチ名

- ブランチの削除  
 $ git branch -d ブランチ名

- ブランチのマージ  
 $ git checkout マージ先のブランチ  
 $ git merge マージブランチ

---

## リモートリポジトリとの連携    

- リモートリポジトリのクローンの作成（ダウンロードとほぼ同等）  
 $ git clone リモートリポジトリのパス

- リモートリポジトリとローカルリポジトリの連携  
 $ git remote add ショートネーム　リモートリポジトリURL  
  ※ローカルリポジトリとリモートリポジトリの中間にリモート追跡リポジトリができる

- 連携しているリモートリポジトリの確認
 $ git remote show  
 $ git remote show origin  
 $ git remote -v

- ローカルリポジトリの内容をリモートリポジトリに反映させる  
 $ git push ショートネーム ローカルブランチ名

- リモートリポジトリの変更をリモート追跡リポジトリに取得する
 $ git fetch

- リモート追跡リポジトリをマージする  
 $ git merge ショートネーム/ブランチ

- git fetch & git mergeを一度に行う  
 $ git pull 

  
---

## よくあるトラブル  

- ブランチをマージしようとしたらコンフリクト（競合）が発生  
  $ git merge ブランチ名  
    Auto-merging test1.py  
    CONFLICT (content): Merge conflict in test1.py  
    Automatic merge failed; fix conflicts and then commit the result.    
  
  ※コンフリクトの内容を確認して編集する  
  $ git status
    both modified:      test1.py  
  $ vi test1.py  
  $ git add test1.py  
  $ git commit -m "comment"

- pushしたらエラー（Reject)が発生  
  $ git push ショートネーム ブランチ  
   ! [rejected]        master -> master (fetch first)  
    error: failed to push some refs to 'git@xxxx'  
    省略  
    hint: See the 'Note about fast-forwards' in ～  
  
  ※リモートの変更を取り込まずにpushしようとしたことが原因  
  ※先にリモートの変更を取り込んでからpushする  
  $ git fetch  
  $ git merge ショートネーム/ブランチ  
  $ git push ショートネーム ブランチ  

この２つのトラブルは併発する場合もあります。