# Android Certificate Pinning

## Allow user CAs for Android 6.0+
`res/xml/network_security_config.xml`

```
<?xml version="1.0" encoding="utf-8"?> 
<network-security-config> 
    <base-config cleartextTrafficPermitted="true">
        <trust-anchors>
            <certificates src="system" />
            <certificates src="user" />
        </trust-anchors>
    </base-config>
</network-security-config>
```

Append to `AndroidManifest.xml`

```
<?xml version="1.0" encoding="utf-8"?>
<manifest ... >
    <application android:networkSecurityConfig="@xml/network_security_config"
                    ... >
        ...
    </application>
</manifest>
```
