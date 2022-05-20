# Technical security validation of the most common vaccination certificate apps in Latvia

## Resources
### Methodology 
- [OWASP Mobile Application Security Verification Standard 1.4.2 (MASVS)](https://github.com/OWASP/owasp-masvs/releases/download/v1.4.2/OWASP_MASVS-v1.4.2-en_WIP_.docx) (Word document)
- [MASVS](https://github.com/OWASP/owasp-masvs) at GitHub
### Courses
- [MOBISEC](https://mobisec.reyammer.io/) at [EURECOM French Graduate school](https://www.eurecom.fr/)
- [Reverse Engineering iOS Apps](https://github.com/ivRodriguezCA/RE-iOS-Apps) by [Ivan Rodriguez](https://ivrodriguez.com/)
- [HackTricks](https://book.hacktricks.xyz/welcome/readme) Ed. by [Carlos Polop](https://github.com/carlospolop)
- [Hacking Mobile Devices](https://samsclass.info/128/128_S15.shtml) at [City College of San Francisco](https://www.ccsf.edu)

## Static Analysis Tools
- [Mobile Security Framework MobSF](https://github.com/MobSF/Mobile-Security-Framework-MobSF)
- [ImmuniWeb Mobile App Security Test Community Ed.](https://www.immuniweb.com/mobile/)

## Dynamic Analysis
- iOS tools like FLEXLoader (FLEX + RHRevealLoader by Xueshi Qiao), Apps Manager, Filza
- Android tools like LogCat, Developer Assistant, Apk Analyzer, Dexplorer, Dev Tools, Dissassembler, SysLog, EX Explorer, MT Manager
- BurpSuite (MitM)
- "Frida" server and "Objection" injection CLI (iOS and Android)

### "Frida" tutorials for Android:
  - [Frida Android Documentation](https://frida.re/docs/android/)
  - [Get started with FRIDA (Android dynamic analysis)](https://medium.com/@parfaitayassor/get-started-with-frida-android-dynamic-analysis-cf10b18d5e67) by AshAy
  - [Getting Started with Frida](https://medium.com/@briskinfosec/getting-started-with-frida-de44d932ae7) by Briskinfosec

## Some quick OSINT on the Apps
- LV app: https://api.covid19sertifikats.lv/api-cert/swagger/index.html#/ValueSet
- CH apps: https://www.ncsc.admin.ch/dam/ncsc/de/dokumente/dokumentation/covid-certificate-test/swiss-covid_public_security_test_current_findings.pdf 

Line25: https://github.com/admin-ch/CovidCertificate-Management-Service/blob/c1965356760315ce618b25c3db390d820bc503a2/src/main/java/ch/admin/bag/covidcertificate/CCManagementServiceApplication.java

## IDs of the tested apps (as of May 4, 2022)
iOS:
```
Version / Title / ID (Comment)
1.4.6    -  Coronapas      dk.sum.ssicpas (developer didn't authorize API test) 
4.1.0    -  Covid Cert     ch.admin.bag.covidcertificate.wallet (developer didn't authorize API test) 
4.0.0    -  Covid Check    ch.admin.bag.covidcertificate.verifier (developer didn't authorize API test) 
1.3.10   -  Covid19Verify  lv.verification.dgc (developer didn't authorize API test) 
1.18     -  GreenCheck     at.itsv.mobile.cochap (developer not interested in pentest)                
1.4.2    -  GreenPass      com.italinnovation.greenPass (developer didn't authorize API test) 
2.3.2    -  Grüner Pass    at.gv.brz.wallet (developer didn't authorize API test) 
```
Android:
```
Version / Title / ID (Comment)
1.4.6    -  Coronapas      dk.sum.ssicpas (developer didn't authorize API test) 
4.1.0    -  Covid Cert     ch.admin.bag.covidcertificate.wallet (developer didn't authorize API test) 
4.0.0    -  Covid Check    ch.admin.bag.covidcertificate.verifier (developer didn't authorize API test) 
1.3.10   -  Covid19Verify  lv.verification.dgc (developer didn't authorize API test) 
1.18     -  GreenCheck     at.itsv.mobile.cochap (developer not interested in pentest)
2.0.1    -  GreenPass      com.italinnovation.green_pass (developer didn't authorize API test) 
2.3.2    -  Grüner Pass    at.gv.brz.wallet (developer didn't authorize API test) 
```

## iOS app decryption recipes
- Jailbreak with checkra1n (most reliable tool IMHO for older phones with iOS 14.x)
- Install app manager like Cydia or Zebra and import sources
- clone https://github.com/AloneMonkey/frida-ios-dump
- iproxy 2222 44 & sleep 3
- python3 ./dump.py app_id

## Android app decryption recipes
- Jailbreak with Magisk
- sudo apt-get install android-tools-adb android-tools-fastboot (on Linux) OR brew install android-platform-tools (on MacOS)
- adb version
- sudo adb start-server
- enable developer menu and USB debugging on the Android device 
- adb devices
- adb shell pm list packages -f -3 (lists 3rd party packages)
- adb shell pm path com.example.someapp (get the path)
- adb pull /data/app/com.example.someapp-2.apk path/to/desired/destination
example:
```
adb pull /data/app/ch.admin.bag.covidcertificate.wallet-NiAioUODHi220A37oOKJpA==/base.apk /home/ejus/Desktop/covid/apk/2/ch.admin.bag.covidcertificate.wallet
adb pull /data/app/ch.admin.bag.covidcertificate.verifier-WpcXFR96LOcSIyX-jrSyJQ==/base.apk /home/ejus/Desktop/covid/apk/2/ch.admin.bag.covidcertificate.verifier
adb pull /data/app/at.itsv.mobile.cochap-uJgbHaO6dRk5tHAbDdpRCA==/base.apk /home/ejus/Desktop/covid/apk/2/at.itsv.mobile.cochap
adb pull /data/app/lv.verification.dgc-gmYHc8tIublP9eecMl2MjA==/base.apk /home/ejus/Desktop/covid/apk/2/lv.verification.dgc
adb pull /data/app/com.italinnovation.green_pass-Ij3k1HCqqH96nu-UXgsIKA==/base.apk /home/ejus/Desktop/covid/apk/2/com.italinnovation.green_pass
adb pull /data/app/dk.sum.ssicpas-7D4OV4X_1tUMGk6i8R14gQ==/base.apk /home/ejus/Desktop/covid/apk/2/dk.sum.ssicpas
adb pull /data/app/at.gv.brz.wallet-Ev23EnkNfQ0PH-BT4JceBQ==/base.apk /home/ejus/Desktop/covid/apk/2/at.gv.brz.wallet
```


