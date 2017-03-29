---

<a name="Setup_DevEnv"></a>

    [azure-iot-sdks](https://github.com/Azure/azure-iot-sdks) GitHub public repository.

-   [Provision your device and get its credentials][lnk-manage-iot-hub]
<a name="PrepareDevice"></a>
# Step 2: Prepare your Device

-   Make sure desktop is ready as per instructions given in [Prepare your development environment](#Setup_DevEnv).
[OEM USB Drivers](<https://developer.android.com/studio/run/oem-usb.html>) document.
    -   On host PC, run adb shell and execute following commands:
    ```
        ./ adb shell
        ps | grep avatar
    ```

<a name="Build"></a>
# Step 3: Build and Run the sample

Please find Azure Android java device sample code [here][android-sample-code].

<a name="Step_3_1"/>
## 3.1 Modify the Samples for screenless device

The sample application will require user interaction to click a button on screen to receive messages from IoT hub. If the target device is a screenless device, you may modify the sample code to have the app to receive message right after sending messages without user interactions.

Following is the example of modification:

In [MainActivity.java][mainactivity-source-code]:
```java
public class MainActivity extends AppCompatActivity {

    String connString = "[device connection string]";
    //IotHubClientProtocol curTestProtocol = IotHubClientProtocol.HTTPS;
    //IotHubClientProtocol curTestProtocol = IotHubClientProtocol.MQTT;

    // setup a variable to switch the usage of Azure connection protocol globally.
    IotHubClientProtocol curTestProtocol = IotHubClientProtocol.HTTPS;

    . . .
    public void SendMessage() throws URISyntaxException, IOException {
        . . .
        // Comment/uncomment from lines below to use HTTPS or MQTT protocol
        //IotHubClientProtocol protocol = IotHubClientProtocol.HTTPS;
        //IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
        IotHubClientProtocol protocol = curTestProtocol;
        . . .
    }
    . . .
    public void btnReceiveOnClick(View v) throws URISyntaxException, IOException {
      . . .
      // Comment/uncomment from lines below to use HTTPS or MQTT protocol
      //IotHubClientProtocol protocol = IotHubClientProtocol.HTTPS;
      //IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
      IotHubClientProtocol protocol = curTestProtocol;
      . . .
    }
    . . .
}
```

```java
protected void onCreate(Bundle savedInstanceState) {
    . . .
    try {
        SendMessage();
        btnReceiveOnClick(null); // call button click event handler
    }
    . . .
}
```

```java
public void btnReceiveOnClick(View v) throws URISyntaxException, IOException {
    . . .
    try {
        // Thread.sleep(2000);
        int maxMsgCount = 1; // exit the while loop when this number of messages is received.
        long maxTimeout = 10000; // in milliseconds
        long elapsedTime = 0;
        System.out.println("Start to receive messages from IoTHub...");
        while (maxMsgCount > counter.get()) {
            Thread.sleep(500);
            elapsedTime += 500;
            if (elapsedTime >= maxTimeout) {
                System.out.println("Timeout!");
                break;
            }
        }
        System.out.println("Stop receiving messages from IoTHub!");
    }
    . . .
}
```

<a name="Step_3_2"/>
## 3.2 Build the Sample

1.  Start a new instance of Android Studio and open Android project from here:

        azure-iot-sdks/java/device/samples/android-sample/

2.  Go to **MainActivity.java**, replace the **[device connection string]** placeholder with connection string of the device you have created in [Provision your device and get its credentials][lnk-manage-iot-hub] and save the file.  An example of IoT Hub Connection String is as below:
         HostName=[YourIoTHubName];SharedAccessKeyName=[YourAccessKeyName];SharedAccessKey=[YourAccessKey]

3. Build your project by going to **Build** menu **> Make Project**.


<a name="Step_3_3"/>
## 3.3 Run and Validate the Samples

In this section you will run the Azure IoT client SDK samples to validate
communication between your device and Azure IoT Hub. You will send messages to the Azure IoT Hub service and validate that IoT Hub has successfully receive the data. You will also monitor any messages sent from the Azure IoT Hub to client.

<a name="Step_3_3_1"/>
### 3.3.1 Run the Sample:

-   Select one of your project's files and click Run  from the toolbar.
-   In the Choose Device window that appears, select the **Choose a running device** radio button, select your device, and click OK.
-   Android Studio will install the app on your connected device and starts it.

### 3.3.2 Send Device Events to IoT Hub:

-   See [Manage IoT Hub][lnk-manage-iot-hub] to learn how to observe the messages IoT Hub receives from the application.

<a name="Step_3_3_3"/>
### 3.3.3 Receive messages from IoT Hub

-   See [Manage IoT Hub][lnk-manage-iot-hub] to learn how to send cloud-to-device messages to the application.

[lnk-setup-iot-hub]: ../setup_iothub.md
[lnk-manage-iot-hub]: ../manage_iot_hub.md
[android-sample-code]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/android-sample
[mainactivity-source-code]: https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/android-sample/app/src/main/java/com/iothub/azure/microsoft/com/androidsample/MainActivity.java