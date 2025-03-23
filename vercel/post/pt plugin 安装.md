---
category: 技术
来源: https://github.com/pt-plugins/PT-Plugin-Plus/wiki/install-from-crx
title: pt plugin 安装
---
> [!important] 目录
> 
> - [[#pt plugin 安装]]
> - [[#]]

### pt plugin 安装

https://github.com/pt-plugins/PT-Plugin-Plus

  

原因是 defaults 写入的 policy 并非 mandatory... 可通过写入 `/Library/Managed Preferences/com.google.Chrome.plist` 或安装下面的这个 描述文件来设置成 mandatory

保存为 `foobar.mobileconfig`, 安装，在 Preference -> Profile 中激活即可

  

![[00-Assets/foobar 3.mobileconfig|foobar 3.mobileconfig]]

```Shell
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>PayloadContent</key>
    <array>
        <dict>
            <key>PayloadContent</key>
            <dict>
                <key>com.google.Chrome</key>
                <dict>
                    <key>Forced</key>
                    <array>
                        <dict>
                            <key>mcx_preference_settings</key>
                            <dict>
                                <key>ExtensionInstallAllowlist</key>
                                <array>
                                    <string>dmmjlmbkigbgpnjfiimhlnbnmppjhpea</string>
                                </array>
                            </dict>
                        </dict>
                    </array>
                </dict>
            </dict>
            <key>PayloadEnabled</key>
            <true/>
            <key>PayloadIdentifier</key>
            <string>MCXToProfile.7e2bec75-299e-44ff-b405-628007abffff.alacarte.customsettings.bdac4880-d25f-4cdd-8472-05473f005e7e</string>
            <key>PayloadType</key>
            <string>com.apple.ManagedClient.preferences</string>
            <key>PayloadUUID</key>
            <string>bdac4880-d25f-4cdd-8472-05473f005e7e</string>
            <key>PayloadVersion</key>
            <integer>1</integer>
        </dict>
    </array>
    <key>PayloadDescription</key>
    <string>Included custom settings:
com.google.Chrome
</string>
    <key>PayloadDisplayName</key>
    <string>MCXToProfile: com.google.Chrome</string>
    <key>PayloadIdentifier</key>
    <string>com.google.Chrome</string>
    <key>PayloadOrganization</key>
    <string></string>
    <key>PayloadRemovalDisallowed</key>
    <true/>
    <key>PayloadScope</key>
    <string>System</string>
    <key>PayloadType</key>
    <string>Configuration</string>
    <key>PayloadUUID</key>
    <string>7e2bec75-299e-44ff-b405-628007abffff</string>
    <key>PayloadVersion</key>
    <integer>1</integer>
</dict>
</plist>
```

###