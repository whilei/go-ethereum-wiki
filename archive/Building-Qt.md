Ethereum(Go) Requires QML 5.4+

## Mac OS X

Please see [build instruction for OSX](https://github.com/ethereumproject/go-ethereum/wiki/Building-Instructions-for-Mac)

## Linux

### Ubuntu 14.04

```
sudo apt-get install pkg-config
sudo add-apt-repository ppa:ethereum/ethereum-qt
sudo apt-get update
sudo apt-get install -y qtbase5-dev qtbase5-private-dev libqt5opengl5-dev qtdeclarative5-dev qml-module-qtquick-controls qml-module-qtquick-dialogs libqt5webengine5-dev
```

Now it's time to build Qt:

```
go get -u github.com/obscuren/qml
cd $GOPATH/src/github.com/obscuren/qml && git checkout v1
go build
```

If you receive an error about not being able to find Qt* items, check that PKG_CONFIG_PATH and LD_LIBRARY_PATH have been set (`echo $PKG_CONFIG_PATH` and `echo $LD_LIBRARY_PATH`) and if not, running `source /opt/qt54/bin/qt54-env.sh` will set the variables for the current session.