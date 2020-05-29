# CodeTestTask_290520
Version นี้  ปรับแก้ มี  4 Tasks มีการส่งข้อมูล Readtime  แต่ ยังไม่มี การอ่านข้อมูลจาก SD card มาส่ง (History) 

    TaskHandle_t CheckWiFi_and_Mqtt;
    TaskHandle_t packDataWR_Ram_Send;
    TaskHandle_t writeRamToSDcard;
    TaskHandle_t ledStatus;

CheckWiFi_and_Mqtt  
 ตรวจสอบการเชื่อมต่อ Wifi -> และ Mqtt (ถ้าหลุดการเชื่อมต่อ วนLoop เชื่อมต่อ)

packDataWR_Ram_Send 

packdata เข้า Ram อ่านส่ง realtime ถ้าส่งได้ Clear ข้อมูลใน RAM
 ถ้าส่งไม่ได้  (เนื่องจาก ปัญหา WiFi และ Mqtt connect) Packdata เขียนลง Ram ต่อเนื่อง จนถึง 32KB
 เขียนลง SDcard
 
writeRamToSDcard ตอยตรวจสอบข้อมูลถ้า ใน Ram ถึง 32KB เขียนลง SDcard


ledStatus LED Status การทำงานบอร์ด ติด/ดับ สลับ ทุก 500 ms  


วิธีทดสอบ  
     1 ตัด Net wifi ที่บอร์ดต่อ จะเข้า case นี้ใน void task_CheckWiFi_and_Mqtt(void* Param)
    if(WiFi.status() != WL_CONNECTED){
        wifi_setup();
      }
     2 ตัดไม่ให้เชื่อมต่อ MQTT Cloud ได้ โดย comment บรรทัดที่ 790 ใน void setup()
    // client.setServer(mqtt_server,mqttPort); //For debug mqtt connect if COMMEND

     ผลการ Debug  เมื่อ ส่งข้อมูลไม่ได้ ข้อ 1 หรือ ข้อ 2  จะทำการเขียน Ram ต่อเนื่องตาม address 
     เมื่อสาวะการเชื่อมต่อ กลับมาปรกติ จะส่งข้อมูลใน  Ram ที่ยังไม่ถึง 32KB ก่อน write ลง SDcard พร้อม Clear RAM 
     ตาม Task packDataWR_Ram_Send 

*** เท่าที่ทดสอบ ข้อมูล Realtime Update ประมาณ 2 วินาที 

หมายุเหตุ 
 Version นี้ยังไม่มีการตรวจสอบ กับ DataBase Server ยังไม่มีการเก็บ Log ใน Server 
  ต้องเขียน เงื่อนไข เพิ่ม Check เมื่อ Database Server พร้อม เป็นขั้นต่อไป 
![img](https://iotfmx.com/imgtest/test1_29.png)
![img](https://iotfmx.com/imgtest/test2_29.png)
