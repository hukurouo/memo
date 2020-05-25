---
layout: post
title:  "GitFlowのまとめ"
date:   2020-05-25 19:46:44 +0900
categories: Git
tags: study memo
---

#### GitFlow
- ブランチの寿命
    - 長寿命ブランチ　→　develop、master
    - 短寿命ブランチ　→　feature、release、hotfix
    - develop→featureのマージを随時行う。
- リリースブランチの明確化　→　本番リリース可能なのはmasterのみ
- マージ時の動作保証
    - feature→developのマージは開発終了時のみとするフローシステム上の「契約」
    - developからmasterへのマージ時にreleaseブランチを作成し、動作を確認する。
- マージタイミングの保証
    - masterブランチは常にリリース可能であるというフローシステム上の「契約」
    - リリースタイミングが来ない機能はfeatureおよびdevelopに保留する。

#### GithubFlow
- ブランチの寿命
    - 長寿命ブランチ　→　master
    - 短寿命ブランチ　→　feature
    - develop→featureのマージを随時行う。
- リリースブランチの明確化　→　本番リリース可能なのはmasterのみ
- マージ時の動作保証
    - masterへのマージ時にpull requestを発行し、コードレビューを行う
- マージタイミングの保証
    - リリースタイミングが来ない機能はfeatureに保留する。

#### GitlabFlow
- ブランチ
    - feature/hotfixは機能開発、不具合対応ブランチ
    - masterはメインのブランチ
    - pre-production(オプションブランチ)はリリース前のテスト用(GitFlowで言うreleaseブランチ)
    - productionはリリース済みのコード置き場
- 機能開発/不具合対応
    - 機能開発するとき、masterブランチからfeature/NAMEブランチを切って開発に進む
    - 開発が終わったらfeatureブランチからmasterにMerge Requestを作成
    - masterをステージング環境へデプロイして、確認する
    - プリプロダクションへデプロイしたい場合、masterブランチからpre-productionブランチへのMerge Requestを作成 マージ済みになるとデプロイする
    - pre-productionブランチからproductionブランチへのMerge Requestを作成 マージ済みになるとデプロイする
