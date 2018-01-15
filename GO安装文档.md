### 1.安装homebrew

    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

### 2.安装go

    brew update && brew upgrade
    brew install go
    brew install git
    brew install mercurial //可选安装
    
### 3.GOPATH设置

    sudo vi /etc/profile
    export GOPATH=/Users/iat/workspace/code/go
