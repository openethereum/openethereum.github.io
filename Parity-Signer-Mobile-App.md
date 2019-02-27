---
title: Parity Signer Mobile Application
---

![Parity Signer](images/logo-parity-signer.jpg)

## Turn your smartphone into a hardware wallet
[Parity Signer](https://github.com/paritytech/parity-signer) is a mobile application that turns a smartphone into a secure air-gapped wallet also called cold storage. It means that, once the app has been installed, users can create Ethereum accounts, sign transactions and transfer funds from and to these accounts without any sort of connectivity enabled on the device. Wifi, Mobile Network, Bluetooth can be shut down and even removed physically to ensure that the mobile phone containing these accounts will not be exposed to any online threat. Have a look at the tutorial to learn how to use [Parity Fether together with Parity Signer](Parity-Signer-Mobile-App-Fether-tutorial).

Any data transfer from or to the app will happen using QR code scanning. By doing so, the most sensitive piece of information, the private keys, will never leave the phone. The Parity Signer Mobile app can be used to store any Ethereum account, this includes ETH, ETC as well as Ether from various testnets (Kovan, Ropsten...).

![screenshot Parity Signer](images/Parity-Signer-android-0.png)

## Device security
Parity Signer is built to be used offline. The mobile device used to run the app will hold important information that needs to be kept securely stored. It is therefore advised to:
- Get a separate mobile device.
- Make a factory reset.
- Enable full-disk encryption on the device, with a reasonable password (might not be on by default, for example for older Android devices).
- Do not use any kind of biometrics such as fingerprint or face recognition for device decryption/unlocking, as those may be less secure than regular passwords.
- Once the app has been installed, enable airplane mode and make sure to switch off Wifi, Bluetooth or any connection ability of the device.
- Only charge the phone on a power outlet that is never connected to the internet. Only charge the phone with the manufacturer's charging adapter. Do not charge the phone on public USB chargers.

## How to get it and use it?

### Install the app
The app is available in beta for Android and iOs :
- [Android Play store](https://play.google.com/store/apps/details?id=io.parity.signer)
- [iOs app store](https://itunes.apple.com/us/app/parity-signer/id1218174838)
- [Signed APK from GitHub](https://github.com/paritytech/parity-signer/releases)

Please double check carefully the origin of the app, and make sure that the company distributing it is **Parity Technologies**. Usual security advice apply to this air-gapped wallet: 
- When creating an account using Parity Signer Mobile app, make sure to write down the recovery phrase and store it in safe places.
- Always double check the information of the transactions you are about to sign or send.
- Make sure to first transfer a small amount of Ether with the app and verify that everything is working as expected before transferring larger amounts of Ether.
 
Follow the [tutorial using Parity Fether](Parity-Signer-Mobile-App-Fether-tutorial) or [MyCrypto](Parity-Signer-Mobile-App-MyCrypto-tutorial) that will walk you through the creation of an account using the app and show you how to send funds from an air-gapped Ethereum account.

## What data does it collect?
None, it's as simple as that. The Parity Signer Mobile Android and iOS apps do not send any sort of data to Parity Technologies or any partner and work completely offline once installed.
