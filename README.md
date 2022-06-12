# build-apk

A tool used to build android APK files from scratch without use of gradle using the command line. Simply follow the usage guide and be aware that any invalid parameters will throw errors. After compilation all output files including the output apk will be generated inside the source directory of the project. For a signed apk, a keystore must be provided within the top level of the source directory. APK's generated with this tool are signed using a self-signed certificate.                                       

USAGE: `buildapk [SOURCE DIRECTORY] [OUTPUT APK NAME] [PACKAGE PATH] [KEYSTORE] [KEYSTORE ALIAS] [ENTRY FILE]`

## Command Setup 

1. First make sure that the Java Development Kit tools (JDK) is avaliable in your public path, as well as the Android Build Tools. These binaries are used by `buildapk` and have to be exposed:

`PATH=$PATH:~/java-jdk/bin      
 PATH=$PATH:~/android-build-tools/build-tools/32.0.0`

2. Paste the `buildapk()` function into your bashrc file, and then run `reset`.

```
buildapk() { 
if [ $1 = "help" ]
then 

HLP=$"buildapk v1.0 - Commandline based APK build tool...";

DESC=$"A tool used to build android APK files from scratch without use of gradle using the command line. Simply follow the usage guide and be aware that any invalid parameters will throw errors. After compilation all output files including the output apk will be generated inside the source directory of the project. For a signed apk, a keystore must be provided within the top level of the source directory. APK's generated with this tool are signed using a self-signed certificate."

USG=$"USAGE: buildapk [SOURCE DIRECTORY] [OUTPUT APK NAME] [PACKAGE PATH] [KEYSTORE] [KEYSTORE ALIAS] [ENTRY FILE]"

STR="${HLP}"$'\n\n'"${USG}"$'\n\n'"${DESC}"; 

echo "$STR";
else
PKG=".:$3/android.jar";

cd $1 && shopt -s extglob && eval "rm -rf !('AndroidManifest.xml'|'src'|'res'|'$4') && echo 'Access files removed...'" && jar -c -f a.zip && unzip a.zip && rm a.zip && aapt2 compile --dir res -o resources.zip && aapt2 link -o output.apk -I src/android.jar --manifest AndroidManifest.xml --java src -v resources.zip && cd src && cp android.jar MainActivity.java $3 && mv com ../ && cd ../ && javac -cp $PKG --release 14 $3/$6 && d8 --lib $3/android.jar $3/*.class && aapt a output.apk classes.dex && zipalign 4 output.apk unsigned.apk && zip -g unsigned.apk META-INF META-INF/MANIFEST.MF && apksigner sign --ks $4 --v1-signer-name CERT --out $2 --ks-key-alias $5 unsigned.apk && rm -rf output.apk unsigned.apk META-INF && cd ../ && echo $'\n\n'"$2 was signed and sucessfully generated in the input directory...";

fi
}
```

3. Run `buildapk` in the command line on your android project directory according to the command usage. 

## Generating a Keystore

To generate a signed apk, a java keystore is needed to generate a self signed certificate and private key. The public key is contained by the certificate. The keystore has to be placed inside the project directory to be picked up by "buildapk":

`keytool -genkeypair -v -keystore <filename>.keystore -alias <key-name> -keyalg RSA -keysize 2048 -validity 10000`

## Installing the Android Command-Line Tools

The Android Command line tools contains the binary `sdkmanager`. Once installed, `sdkmanager` needs to be used to install the other components of the android sdk needed to build an apk. The command line tools can be installed at "https://developer.android.com/studio#cmdline-tools".

## Notes 

* Run `buildapk help` to show usage details.
