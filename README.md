# Agama Desktop App
Desktop App for SuperNET DAPPs

## Alpha 1
Fixed receive panel, still depends on external service at port 3000.

#### For Developers
You must have `node.js` and `npm` installed on your machine.

Check versions:
nodejs --version
v8.9.4

Note that Easy-DEX-GUI is quite sensitive to the node version. 9.x fails for me, and older versions do as well. Make sure you're on 8.9.x. Installing nvm is probably a good idea, especially if you already have node installed but it is not a compatible version.

Other essential dependencies:
sudo apt-get install -y build-essential

1) Clone Agama Desktop App with EasyDEX-GUI submodule
```shell
git clone https://github.com/veruscoin/agama --recursive --branch dev --single-branch
```
with this command you git clone agama - but explicitly just the dev branch (therefore --single-branch) which we also use for the release packages.
It also gets the wrong sub-project stuff, so fix that next:
```shell
cd gui\EasyDEX-GUI
git remote add org https://github.com/VerusCoin/EasyDEX-GUI.git
git checkout dev
git pull org dev
```
2) Get the binary artfacts into place (linux version)
```shell 
cd agama
./binary_artifacts.sh
```
For Mac use ./binary_artifacts_mac.sh.
Note that we do not use the standard downloadable komodo executables, the version in veruscoin has to be used and the src/komodod and src/komodo-cli build outpyuts have to be manually patched into assets/bin/osx.
3) install the electron packager and prebuilt - note the ugly unsafe-perm and allow-root stuff for the prebuilt electron.
```shell
sudo npm install electron-packager -g
npm install electron-prebuilt -g --unsafe-perm=true
```
4) get webpack dependencies into place for the react stuff
```shell
npm install
```
5) Now get the react stuff installed and running
```shell
cd react
npm install
cd src
npm start
```
Brings up the dashboard and loads the react service using localhost:3000

6) start a new shell and go back to the react dir and build things (the EasyDEX-GUI)
```shell
cd agama/gui/EasyDEX-GUI/react
npm run build
```
8) Get the top level stuff set, after the cd you should be in the root agama dir. We also need electron, again with the nasty souding unsafe-perm and allow-root options, otherwise electron post install steps fail. Similarly, we need to get libgconf and webpack too.
```shell
cd ../../..
npm install webpack
npm install
```
7) At this point you can test things by running the wallet directly from electron:
```shell
npm start
```
This is a pretty wrapper around electron. I set the environment to production too.

8) Check that things work. Choose the Verus coin for a fast check. Check Komodo and BTCH at the same time as well. Note that loading Komodo the first time can take 10+ hours the first time to get the chain.

9) toggle dev and debug options in settings, this will switch thr Agama wallet to using the dev version of the React service that we just launched at port 3000. Once you set this the Agama wallet will not run without the service manaully started at port 3000 via npm start in the agama/gui/EasyDEX-GUI/react/src directory. To test a production version, turn dev back off, then exit theAgama wallet, then bring the react service down ("q" causes it to quit). Finally restart the Agama app without the dev service and it works normally.
Note the View menu Toggle Developers Tools option. Enabling that brings up the browser console which is useful. Variable examination and break points, console for the error log, and the ability to track network interactions are all useful. Code has been squashed so more work is needed to get breakpoints completely useful.

10) sync komodod and/or asset chains - now that the wallet is running if you choose Komodo native (or eventually Verus) it will load the assect chain. It's taking me 16 hours on a local VM to get it loaded the first time. It only takes 10 or 30 minutes to catch up on startup after that if things are going well.
11) If you modify code under gui/EasyDEX-GUI then you'll need to go to gui/EasyDEX-GUI/react/src and run npm run build again, then relaunch npm start from the agama dir. Changes to agama/routes just require relaunching npm start from the agama dir. If any dependencies change then you'll need to rerunnpm install in the appropriate directory.
12) Once you have your changes ready and working you can produce the Linux executable image
### Important dev notes
Coin info for Verus is stored in ~/.komodo/VRSC under Ubuntu
Coin info for Verus is stored under \Users\<username>\AppData\Roaming\Komodo\VRSC for Windows
Coin info is for Verus is stored under ~/Library/Application\ Support/Komodo/VRSC for Mac
#### Sockets.io
In dev mode backend is configured to send/receive messages from/to http://127.0.0.1:3000 address. If you open it as http://localhost:3000 sockets server will reject any messages. In non dev mode the service is launched internally and everything operates normally.

#### **Build the Wallet-App**
Refer to the original [electron-packager](https://github.com/electron-userland/electron-packager) repository for more detailed information.
Fairly useless advice

##### Linux
Change directory to agama and execute the following command to build the Linux app
```shell
cd agama
electron-packager . --platform=linux --arch=x64 --icon=assets/icons/agama_icons/128x128.png --out=build/ --buildVersion=0.11 --ignore=assets/bin/win64 --ignore=assets/bin/osx --overwrite
```

##### OSX
Change directory to agama and execute the following command to build the OSX app
```shell
cd agama
electron-packager . --platform=darwin --arch=x64 --icon=assets/icons/agama_icons/agama_app_icon.icns --out=build/ --buildVersion=0.2 --ignore=assets/bin/win64 --ignore=assets/bin/linux64 --overwrite
```

##### Windows
Change directory to agama and execute the following command to build the Windows app
```shell
cd agama
electron-packager . --platform=win32 --arch=x64 --icon=assets/icons/agama_icons/agama_app_icon.ico --out=build/ --buildVersion=0.2 --ignore=assets/bin/osx --ignore=assets/bin/linux64 --overwrite

# If generating 32bit desktop package
electron-packager . --platform=win32 --arch=ia32 --icon=assets/icons/agama_icons/agama_app_icon.ico --out=build/ --buildVersion=0.3 --ignore=assets/bin/osx --ignore=assets/bin/linux64 --overwrite

# To build both x64 and x86 desktop package
electron-packager . --platform=win32 --arch=all --icon=assets/icons/agama_icons/agama_app_icon.ico --out=build/ --buildVersion=0.3 --ignore=assets/bin/osx --ignore=assets/bin/linux64 --overwrite
```
change architecture build parameter to ```--arch=x64``` for 64 bit build


## Troubleshooting Instructions

### Windows DLL issues
On Windows it's noticed agama.exe complains about `VCRUNTIME140D.DLL` and `ucrtbased.dll` file.

Please see **windeps** directory and README file for instructions to install the required DLL files on Windows, and then try again running Agama App.

## Optional packages to make rpm and deb distros

electron-installer-debian

electron-installer-redhat

refer to ./make-deb.js and ./make-rpm.js

