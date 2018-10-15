# Android reverse engineering 

This step will contain the following ...

- Getting the .apk file (if not already)
- Decompiling it to (.jar, .smali, .java files)
- Inserting logging statements
- Recompiling, aligning and signing

# Getting the .apk

## Gplaycli (preferred)
- Project link - https://github.com/matlink/gplaycli

Using gplaycli is the easiest way, just install it using `pip install gplaycli`, followed by `gplaycli -d com.eboks.activities`

This outputs the .apk file in the current directory.

## Online tools
This only tool can be used, to get the apk file - https://apps.evozi.com/apk-downloader/

Keep in mind, this might NOT be the newest version, but might be cached.

# Decompiling

## Decompiling to smali
- Project link - https://bitbucket.org/iBotPeaches/apktool/downloads/

Download the latest release of `apktool.jar`, and save it to the same directory as your `.apk`, I have the following structure.

```
- eboks/
 - apktool.jar
 - com.eboks.activities.apk
```

Use the following command, to get the smali code `java -jar apktool.jar d com.eboks.activities.jar`, and all the files are now in `com.eboks.activities`.

## Decompiling to jar
- Project link - https://github.com/pxb1988/dex2jar

Go to the releases, and download the latest release (not nightly builds and not just the source).
Extract it to a directory of your choice, I prefer the same as my .apk file, so I have the following structure

```
- eboks/
 - dex2jar/
 - com.eboks.activities.apk
```

Then just use the following command `./dex2jar/d2j-dex2jar.sh com.eboks.activities.apk`, which will result in a `.jar` with `-dex2jar.jar` appended to it.

## Decompiling to java
- Project link - https://bitbucket.org/mstrobel/procyon/downloads/

Download the latest release so you have the following structure (this requires the previous `.jar` file).

```
- eboks/
 - dex2jar/
 - procyon.jar
 - com.eboks.activities.apk
 - com.eboks.activities-dex2jar.jar
```

To get the `.java` files, do the following `java -jar procyon.jar -jar com.eboks.activities-dex2jar.jar -o java-classes`.


# Inserting logging statements
- Project link - https://github.com/eyJhb/IGLogger

This requires a bit more, since we need to use something called `iglogger`, download the `.smali` file, and place it in your apps smali root.

This would be `com.eboks.activities/smali/iglogger.smali`, to use it, you need to place various debug statements in the code.

Normally there will be a `.locals` in a function, so if you want to write a new string (to know what the next value will be), increase this, and use `.locals n-1`, to for your `vN`.

Example

```
.method private static iv()[B
    .locals 2

    .line 68
    new-instance v0, Ljava/security/SecureRandom;

    invoke-direct {v0}, Ljava/security/SecureRandom;-><init>()V

    const/16 v1, 0x10

    .line 69
    new-array v1, v1, [B

    .line 70
    invoke-virtual {v0, v1}, Ljava/security/SecureRandom;->nextBytes([B)V

    return-object v1
.end method
```

```
.method private static iv()[B
    .locals 3
    const-string v2, "!!!loginEncryptHelper.iv!!!"
    invoke-static {v2}, Liglogger;->d(Ljava/lang/String;)I 

    .line 68
    new-instance v0, Ljava/security/SecureRandom;

    invoke-direct {v0}, Ljava/security/SecureRandom;-><init>()V

    const/16 v1, 0x10

    .line 69
    new-array v1, v1, [B

    .line 70
    invoke-virtual {v0, v1}, Ljava/security/SecureRandom;->nextBytes([B)V
    invoke-static {v1}, Liglogger;->d([B)I

    return-object v1
.end method
```

This will print out the information needed.

# Recompiling, aligning and signing

## Recompiling
We here use `apktool` yet again - `java -jar apktool.jar b com.eboks.activities` (the folder name).
This gives us a files in `com.eboks.activities/dists/com.eboks.activities.apk`, this file needs to be zipaligned.

Install zipalign tool `apt install zipalign`, and use `zipalign -v -p 4 com.eboks.activities/dist/com.eboks.activities.apk app-alligned.apk`.

Now for the signing, this is a little more tricky... We need `apksigner` which is a part of the `Android SDK Tools`, which will not be covered here.
We also need to generate our signing keys using `keytools` (part of the Java JRE), where we can use this command `keytool -genkey -v -keystore my-release-key.jks -keyalg RSA -keysize 2048 -validity 10000 -alias my-alias`, I just use `123456` for all passwords prompts etc..

Now when all this is done, you should be able to sign your alligned apk using ...

```
echo -n 123456 | apksigner sign --ks my-release-key.jks --out app-release.apk app-alligned.apk 
```

Now just install it!

# Automated build script

```
rm builds/app-alligned.apk ; \
java -jar apktool.jar b -o builds/app.apk app && \
zipalign -v -p 4 builds/app.apk builds/app-alligned.apk && \
echo -n 123456 | apksigner sign --ks my-release-key.jks --out builds/app-release.apk builds/app-alligned.apk 
```
