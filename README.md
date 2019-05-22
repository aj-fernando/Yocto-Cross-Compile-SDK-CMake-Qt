# Yocto-Cross-Compile-SDK-CMake-Qt
Guidelines for building a Yocto cross-compile toolchain SDK that can be installed on a different host machine and how to configure QtCreator IDE for cross compiling to the target device using qmake or cmake.

First we will look at, how to build the sdk toolchain. 

1) Go to yocto project folder and run either of following depending on your build. 

- Usual: 	source oe-init-build-env
- OsTree: 	source meta-updater/scripts/envsetup.sh intel-corei7-64


2) Bitbake the SDK for your image as follows

- **bitbake -c populate_sdk <image-name>**
- Image names can be “image-dev”, “core-image-minimal” and so on
- Add inherit populate_sdk_qt5 to your image recipe, if you need qmake support to build your code or need to cross-compile for a QT application.

3) Practice patience until the Yocto build is finished;)

4) Once done go to **<yocto-project-folder>/build/tmp/deploy/sdk** and check the content (there should be a bash script, host,target manifests and a json)

5) Now tar ball the sdk content tar -zcvpf yocto-thud-minnow-sdk.tar.gz * and upload/send/transfer to the host-machine you want to use for cross-compiling.

6) Go to the host-machine, download & extract the tar ball, run the script inside

- **./poky-sota-glibc-x86_64-image-dev-corei7-64-toolchain-2.6.2.sh**

7) Open a terminal and run the environment script and open the QtCreator from the same terminal.

- **source /opt/poky-sota/2.6.2/environment-setup-corei7-64-poky-linux**
- **~/Qt/Tools/QtCreator/bin/qtcreator &**

8) Inside QtCreator, click the Projects tab, Manage Kits, Options window will open

- Devices tab -> Generic Linux Device -> Start wizard ->
- Give a name for the kit, enter target device IP or hostname & target device user profile name(e.g. root) -> Next -> Deploy  public key (if preferred) -> Finished (now new device will be tested for sftp)
- Now add the compilers, debugger and cmake or qmake in Kits tab
- **GCC compiler**: Compilers tab -> Add -> GCC -> browse compiler path. e.g /opt/poky-sota/2.6.2/sysroots/x86_64-pokysdk-linux/usr/bin/x86_64-poky-linux/x86_64-poky-linux-gcc
- **G++ compiler**: Compilers tab -> Add -> G++ -> browse compiler path.  e.g 
- /opt/poky-sota/2.6.2/sysroots/x86_64-pokysdk-linux/usr/bin/x86_64-poky-linux/x86_64-poky-linux-g++
- **GDB debugger**: Debuggers tab -> Add -> GDB -> browse path e.g
/opt/poky-sota/2.6.2/sysroots/x86_64-pokysdk-linux/usr/bin/x86_64-poky-linux/x86_64-poky-linux-gdb
- **CMake** : CMake tab -> Add -> browse path e.g
/opt/poky-sota/2.6.2/sysroots/x86_64-pokysdk-linux/usr/bin/cmake
- **qMake** : (If you are using qmake only), Qt Versions tab -> Add -> 
/opt/poky-sota/2.6.2/sysroots/x86_64-pokysdk-linux/usr/bin/qmake
- **Environment** : this is optional! To get rid of step (7) in future. Sometimes does not work when there are if conditions. Click Change -> paste content /opt/poky-sota/2.6.2/environment-setup-corei7-64-poky-linux
- Now go to the Kits tab -> Add -> Give a name -> Select device type “Generic Linux Device” -> Select device name you created above, select GCC & G++ compilers, GDB debugger configured above, select CMake or qMake configured above and…… click Apply -> OK 

9) Now go to Edit tab, Run CMake on your project and build.

- If CMake gives error, try deleting all the content inside existing project build folder and re-run CMake or quit QtCreator, source environment script and restart QtCreator as step(7)

10) Once the build is finished, if executable cannot be deployed (for e.g. sftp problems)

- Projects tab -> Select the kit you created -> Click Run -> Click button Add Deploy step -> Run Custom Remote Command
- Add a scp command to transfer your executable such as 
scp <excutable-name> <username>@<IP address>:/location/inside/remote/target
