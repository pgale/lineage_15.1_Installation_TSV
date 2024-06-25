# The qualcomm driver needed to flash the Lenovo device in Windows is not signed. This means the 9008 driver shows as unsigned and as such won't allow you to flash the Lenovo device by the QFIL tool. #

You have 2 options at this point:

## 1. Used Advanced Startup to temporarily disable device driver signing (Note after each reboot Device Driver Signature enforcement is reenabled) ##
## 2. Self-sign the device driver (more complex, but if you are flashing multiple devices worthwhile) ##

## 1. Advanced Startup (If you have Bitlocker enabled this will prompt you for a recovery key or you can suspend bitlocker) ##
1. Open the Start Menu and while holding the shift key click restart. (if this doesnt work you can access the same features via recovery options in Windows Update)
2. When you boot into the Windows RE environment, select Troubleshoot and then Advanced Options. [visual guide her](https://www.tenforums.com/tutorials/156602-how-enable-disable-driver-signature-enforcement-windows-10-a.html)
3. Then select StartUp Options and restart.
4. When the boot screen comes up you will want to enter number 7 for "Disable Driver Enforcement Signature"
5. Once Windows loads you should be good to go, but check if the Yellow "!" has gone from the driver. Some machines who are AD or AAD joined will have Group Policies overriding your decision - which means you'll need option 2 below

##2. Self-sign the device driver
[The original post is here (but I have modified it as the post was out of date and had a couple of errors)](https://woshub.com/how-to-sign-an-unsigned-driver-for-windows-7-x64/)
###First we need to download the tools needed
1. Download the drivers in this repo (qcser.sys & qcser.inf) to `c:/DriverCert/qcser/`
2. To avoid downloading the WDK and Windows SDK I used the Enterprise WDK which is much easier. [Download the .iso here](https://go.microsoft.com/fwlink/?linkid=2271957)
3. Mount the ISO
4. From a admin CMD Prompt navigate to the mouted drive and run `Run LaunchBuildEnv.cmd`
5. In the environment created type `SetupVSEnv`, and then press Enter.
6. Navigate to "C:\Program Files\Microsoft Visual Studio\2022\Community\Common7\IDE\"
7. Run `devenv.exe`
###Now we can get into the driver signing.
8. Open up Powershell as an admin and run the following code (but take close attention to the " quotes " as they seem to be missed when copy and pasting.
```
$todaydate = Get-Date
$add3year = $todaydate.AddYears(3)
$cert = New-SelfSignedCertificate -Subject "WOSHUB” -Type CodeSigningCert -HashAlgorithm 'SHA256' -CertStoreLocation cert:\LocalMachine\My -notafter $add3year
```
9. Now again paying close attention to the quotes and - hypens run the following line by line to create and export the certificate
```
$CertPassword = ConvertTo-SecureString -String “abc123” -Force -AsPlainText
```
```
Export-PfxCertificate -Cert $cert -FilePath C:\DriverCert\myDrivers.pfx -Password $CertPassword
```
```
$certFile = Export-Certificate -Cert $cert -FilePath C:\DriverCert\drivecert.cer
```
10. Now lets import the certificate to your machine as a trusted cert, copying line by line:
```
Import-Certificate -CertStoreLocation Cert:\LocalMachine\AuthRoot -FilePath $certFile.FullName
```
```
Import-Certificate -CertStoreLocation Cert:\LocalMachine\TrustedPublisher -FilePath $certFile.FullName
```
```
signtool sign /tr http://timestamp.digicert.com /fd SHA256 /v /f C:\DriverCert\myDrivers.pfx /p abc123 "C:\DriverCert\qcser\qcser.cat"
```
11. Now the cert is trusted, we need to sign the driver. This time open a CMD prompt as an admin and run the following:
```
cd “C:\Program Files (x86)\Windows Kits\10\bin\10.0.22000.0\x86”
```
```
inf2cat.exe /driver:"C:\DriverCert\qcser" /os:10_X64 /verbose
```
```
cd "C:\Program Files (x86)\Windows Kits\10\bin\10.0.22000.0\x64"
```
```
signtool sign /tr http://timestamp.digicert.com /fd SHA256 /td SHA256 /v /f C:\DriverCert\myDrivers.pfx /p "abc123" "C:\DriverCert\qcser\qcser64.cat"
```
```
SignTool verify /v /pa c:\DriverCert\qcser\qcser64.cat
```

If you received any errors along the way be sure to check out [the full guide as mentioned here](https://woshub.com/how-to-sign-an-unsigned-driver-for-windows-7-x64/)
