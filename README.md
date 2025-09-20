EDUCATION PURPOSE ONLY

Projet description
This project is used to brute-force wifi password using GPU.

Tools needed

1. CUDA toolkit

2. Hashcat

3. Network adaptor that supports monitor mode (In this project, i am using TP-Link AC600)

4. Kali linux VM

5. Windows Host Machine

Procedure

1. In the Windows host machine :

    We need to install Hashcat, which is used to crack the password, and CUDA toolkit in order to utilize the processing of the GPU to crack the password.

    You can refer to this link on how to install Hashcat and CUDA toolkit : https://www.youtube.com/watch?v=b5zQ6xTQGfY&t=578s

    08:31 - Downloading Hashcat

    10:51 - Downloading NVIDIA & CUDA Toolkit

    We need to install the driver on Kali Linux for the network adapter. We must use a network adapter that supports monitor mode, or else this hack would not work.

    You can refer to this link on how to install the driver for the network adapter TP-Link AC600 : https://www.youtube.com/watch?v=NQckErucvhI

2. In the Kali Linux VM :

    <img width="657" height="307" alt="image" src="https://github.com/user-attachments/assets/e036f1d4-f55b-458d-a845-b5a0381865d1" />

    Enter this command to kill any interfering processes that may interfere : **sudo airmon-ng check kill**

    Enter this command to enable monitor mode : **sudo airmon-ng start wlan0**

    Enter this command if we want to stop the monitor mode : sudo airmon-ng stop wlan0mon

    <img width="790" height="410" alt="image" src="https://github.com/user-attachments/assets/57fa2c2e-9024-4b14-b651-eb0e1092364d" />

    Enter this command to see the wifi available : **sudo airodump-ng wlan0**

    In this case, we select TEST-WIFI as the target network. We should take note of the BSSID and the CH used for the target network.

    <img width="817" height="202" alt="image" src="https://github.com/user-attachments/assets/73bae0d0-efc8-4a04-93db-4a5131fb3223" />

    Enter this command to only focus on the target network : **sudo airodump-ng -c `<CH>` --bssid `<BSSID>` -w capture wlan0**

    We need to take note of the BSSID and the Station. BSSID is the target network, and Station is the device connected to it. In order to perform this hack, we must carry out the deauthentication process, where we simply kick out the connected device and ask them to re-enter the password to capture the handshake, which we then can use Hashcat to perform the brute-force attack using GPU.

    <img width="777" height="160" alt="image" src="https://github.com/user-attachments/assets/332d75e0-94ad-43a1-a849-f4178461a96e" />

    Enter this command to carry out deauthentication process : **sudo aireplay-ng --deauth 5 -a `<BSSID>` -c `<CLIENT_MAC>` wlan0**

    In this process, the connected device will be forced to disconnect from the network and reconnect, allowing us to capture the handshake.

    <img width="783" height="198" alt="image" src="https://github.com/user-attachments/assets/4347b5a2-3525-4361-8ec6-23bf53d39cce" />

    In this image, the WPA handshake has been successfully captured.

    <img width="811" height="100" alt="image" src="https://github.com/user-attachments/assets/5b9dd72c-1fa5-4202-966d-ef184058b781" />

    We enter this command : **hcxpcapngtool -o TEST-WIFI-Captured.22000 capture-02.cap**

    This command allows us to convert that data into Hashcat’s 22000 format.

    Then, we need to transfer the TEST-WIFI-Captured file into the Windows host to crack the file. It is important that the capture file is in the same directory as the Hashcat file that has been downloaded into the Windows host.

3. In the Windows host

    <img width="1450" height="198" alt="image" src="https://github.com/user-attachments/assets/aad5970f-bbca-4c9f-8600-6cbc6952e554" />

    We enter this command : **hashcat.exe -m 22000 -a 3 TEST-WIFI-Captured.22000 ?d?d?d?d?d?d?d?d?d**

    This will crack a password of up to 9 digits.
   
    If we want to crack up to 9 of any characters we use this command : hashcat.exe -m 22000 -a 3 TEST-WIFI-Captured.22000 ?a?a?a?a?a?a?a?a?a

    <img width="997" height="148" alt="image" src="https://github.com/user-attachments/assets/dd0e84d7-baba-4aa7-928f-5b56c4a76ea0" />

    In this picture, it shows us that we successfully cracked the password, where the password is "123456789".

    Learning experience

    In this project, we must learn that we must put a very secure password to prevent this type of attack. With the evolving of wifi technology such as WPA3, it can prevent such attacks, where WPA3 uses the SAE handshake, which generates a unique secret per session, making captured handshakes useless for offline brute-force attacks. Only online guessing is possible, which is much slower.
