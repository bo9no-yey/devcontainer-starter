# Ubuntu は最新 LTS の 26.04 (resolute) に固定。上げるときはこの FROM 行を書き換えて再ビルド
FROM mcr.microsoft.com/devcontainers/base:ubuntu26.04

# 名前付きボリュームのマウント先を先に作り、所有者を remoteUser (vscode) に合わせる。
# 空の名前付きボリュームは初回作成時にイメージ側のディレクトリの所有者を引き継ぐため、
# ここで作っておかないと root 所有になり、Claude Code が認証情報や設定を書き込めない
RUN mkdir -p /home/vscode/.claude && chown vscode:vscode /home/vscode/.claude
