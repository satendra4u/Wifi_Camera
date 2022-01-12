# Wifi_Camera

This application has been build on 64 MB RAM wifi camera to capture the motion of object, start the microcontroller, buffer the captured images, split in multiple frames and send it to the cloud for the anamoly detection.

The Camera Setting are intialized as per below settins:

uint8_t  serialNum;
uint8_t  camerabuff[CAMERABUFFSIZ+1];
uint8_t  bufferLen;
uint16_t frameptr;

// TCP Setting to send the data to the cloud

TCPClient client;
byte server[] = {10, 0, 0, 11 };  // Add your Server IP
byte c;
int port = 80;

**The camera buffer intitalization:**

//camerabuff[bufferLen] = 0;


  while ((timeout != counter) && (bufferLen != numbytes)){
    avail = Serial1.available();
    if (avail <= 0) {
      delay(1);
      counter++;
      continue;
    }
    counter = 0;
    // there's a byte!
    camerabuff[bufferLen++] = Serial1.read();
  }
  
  ** Also at the end of the loop after sending to cloude flush the buffer**
  

Below are the camera functions:

//---------- Camera Functions

boolean camReset() {
  uint8_t args[] = {0x0};

  return runCommand(VC0706_RESET, args, 1, 5, true);
}



//----------- Setup the MicroController
void setup() {

  Spark.function("camera", cameraFunc);
  //Particle.function("blink", blinkFunc);
  Spark.variable("temperature", &sparkTempF, INT);
  Serial.begin(9600);
  Serial.println("VC0706 Camera snapshot test");
    // Begin Camera
      common_init();
    // Try to locate the camera
    if (camBegin(38400)) {
      Serial.println("Camera Found:");
    } else {
      Serial.println("No camera found?");
      return;
    }

}



// call the below function when the POST request matches it

int cameraFunc(String command) {

    Spark.publish("CheckCamera", "Inside Camera Function",60, PRIVATE);
    Serial.println("Got the request...");
  // Set the picture size - you can choose one of 640x480, 320x240 or 160x120
  // Remember that bigger pictures take longer to transmit!
  //  camSetImageSize(VC0706_640x480);           // biggest
      camSetImageSize(VC0706_320x240);           // medium
  //  camSetImageSize(VC0706_160x120);           // small
}
