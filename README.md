# Nrf52840_DW3000
Our project is RC car autonomous driving using UWB positioning

## Project Introduction
This project implemented autonomous driving of an RC car using only UWB communication with one tag and three anchors. It was confirmed that various routes such as circles, triangles, and squares were tracked well, and the details of our research were written in 'Realization of RC Car Autonomous Driving Using UWB Positioning'.

Considering the advantages of using UWB communication, positioning is possible indoors where GPS does not work, and that positioning is possible with one UWB communication module instead of positioning using many sensors such as cameras and lidars in an autonomous driving environment, we decided on this project. proceeded to
In this project, 1 Tag, 3 Anchors were implemented as Single-Sided Two Way Ranging (SS-TWR). In the future, we plan to develop the project using AOA (Angle of Arrival) technology using only one tag and one anchor.

## Contents
- [How to install and run the project](#How-to-install-and-run-the-project)
- [How to use project](#How-to-use-project)
  
    -[Description of each file](#Descriptio-of-each-file)
  
    -[How to combined inititaor and PWM example](#How-to-combined-inititaor-and-PWM-example)
- [Team members and references](#Team-members-and-references)

## How to install and run the project

* SES(Segger Embedded Studio) installation 
-Download link
(https://www.segger.com/downloads/embedded-studio)

If you download and run the exe that fits your PC specifications from the link above, SEGGER Embedded Studio (SES) installation is complete.

* SDK(Software Development Kit) Download 
 (https://www.nordicsemi.com/Products/Development-software/nRF5-SDK/Download#infotabs)

Download the SDK needed to drive the nRF52840 to be used in the project.

* Run the example file.
If you go to the downloaded path, there are examples that you can run. You can select any one of them and run it.

Among the nRF5 chip series provided by Nordic, nRF52840 selects and uses pca10056. Let's use the blinky example where the LED blinks alternately to see if it works normally.

When executed, errors such as .text section is larger than specified size may occur. This is because the size of .text and .rodata among project configuration files is limited. This opens the flash_placement.xml file below with an editor such as visual studio.

Delete the part indicating that the size of .text and .rodata is limited. After deleting the size="0x4" part and building again and running, it runs well without any error message.

The above work is to create a development environment to proceed with a project using the nRF52840.
Since the project to proceed uses the nRF52840 connected to the DWM3000 as the main board, it is necessary to operate the nRF52840 development environment and the existing environment settings to operate the DWM3000.

* Install the API file provided by Qorvo
(https://www.qorvo.com/products/p/DWM3000#documents) 
Qorvo basically provides an API so that users can use various examples.

Download the DWM3000 API Software and API Guide from the link above. After downloading, decompress and configure according to the provided manual for use in the nRF52840-DK environment.

* Briefly, the procedure of the manual is as follows.

1.Install Segger, Download nRF52840 SDK

2.Move the SDK to the "<DW3000 API Root Directory>/API/Build_Platforms/nRF52840-DK" path.

At this time, <DW3000 API Root Directory> is the upper folder of the API folder downloaded from each personal computer. It is not an absolute name, so it is a value designated by the individual according to his or her environment.

3. In the file of "<DW3000 API Root Directory>/API/Build_Platforms/nRF52840-DK/dw3000_api.emProject", it is necessary to change the relative path into an absolute path. After opening the above file in edit mode in Notepad, Visual Studio, etc., find "macros=" and change each as follows.
      “NordicSDKDir=<DW3000 API Root Directory>/API/Build_Platforms/nRF52840-DK/SDK”
“DW3000APIDir=<DW3000 API Root Directory>/API”

4.Take a look at the examples provided.
Many examples are defined in "<DW3000 API Root Directory>/API/Src/example_selection.h", but all of them except for the example to be executed should be commented out.

You can complete the basic environment settings through the above process.

If you run it, you may get an error saying that the vfprintf.h file cannot be found. Then, Exclude SEGGER RTT. Also, if you right-click on Project 'dwm3000_api' on the left and change Library I/O from None to RTT in Option, the error will be resolved and the code will run fine.

## How to use project

  ### Description of each file
  
I'll just mention the simple way it works. If you do not understand, please refer to the commented <NOTES> in  all example files. For further development or further understanding, please refer to the DW3000 Family User    Manual or Nrf52 Manual.
 
* Anchor.c file. (Using ss-twr-responder in dw3000 api example)

rx_poll_msg and tx_resp_msg must be changed for Tag and Anchor to communicate with each other.
Among them, you need to change numbers 5, 6, 7, and 8 of the array. Numbers 5 and 6 of rx_poll_msg mean address of anchor, and number 7 and 8 mean address of tag. Numbers 5 and 6 of tx_resp_msg mean the tag address, and numbers 7 and 8 mean the address of the anchor. Depending on the response speed of Anchor, the communication speed of UWB can be faster and more accurate. For details, refer to the DW3000 Family User Manual.
Change the address to the desired number in hexadecimal.

* The initiator.c file. (Using ss-twr-initiator in dw3000 api example)

Since the RC car has to control the pwm while acting as a tag, the ss-twr-initiator.c file includes all the code for calculating the distance to the anchor, calculating the coordinates, and driving the rc car.

Change the addresses of tx_poll_msg and rx_resp_msg in ss-twr-initiator.c as mentioned above.

The way to communicate with 3 anchors in this project is to write 1:1 communication code 3 times. After communication with anchor number 1 is over, the address is changed to communicate with anchor number 2.

One of the methods for increasing the accuracy of UWB communication is to adjust ANT_DLY. TX_ANT_DLY literally means the delay caused by the antenna when transmitting, and RX_ANT_DLY means the delay caused by the antenna when receiving. How to adjust accurately is described in the dw3000 family user manual.

Finally, although there are various ways to adjust the UWB communication speed, there is a way to reduce RNG_DELAY_MS in a convenient way. RNG_DELAY_MS is the inter-ranging delay, which will initially be defined as 1 second. By reducing this, the communication speed can be increased. How to adjust accurately is described in the dw3000 family user manual.

* control_pwm.c file (using pwm_library_pca10056 in Nrf52840 SDK example)

Since the RC car has to control the pwm while acting as a tag, the ss-twr-initiator.c file includes all the code for calculating the distance to the anchor, calculating the coordinates, and driving the rc car.

The 'stop', 'go', 'go_right', 'go_left', and 'control_pwm_set' functions are defined in the 'control_pwm.c' file. The stop function is a code to stop the RC car when it goes in the wrong direction. The go function is a code to go straight by applying the same PWM signal to the right and left wheels. The go_right and go_left functions are codes that measure the angular velocity of the wheel by directly measuring the diameter and circumference of the wheel, generate different PWM signals according to the target point and the twisted angle of the RC car, and put different signals into the wheel. Since the left and right wheels need to be controlled, one timer is used on the nordic board and two pwm signals are separately generated. (The angular velocity of the wheel, generating the PWM signal was created using the differential kinematic theory to create the wheel control code.)

* main.c file(using dw3000_api in Nrf52840 SDK example)

This is the main statement to run the entire code. The main statement of the dw3000_api example of the Nordic SDK was used.

  ### How to combined inititaor and PWM example

* Create a file that drives the DC motor driver.

Enter the *<DWM 3000 API ROot Directory>/API/Src/examples>* folder and create a new folder. I set the name of the folder as control_pwm.

Place the control_pwm.c file in the created control_pwm folder.

To define the location of control_pwm.c file in dw3000_api.emProject
Run dw3000_api.emProject in *<<DW3000 API Root Directory>/API/Build_Platforms/nRF52840-DK>* again with visual studio.

Find 

        <folder Name="examples">* under *</folder> <folder Name="MAC_802_15_8"> <file            
        file_name="$(DW3000APIDir)/Src/MAC_802_15_8/mac_802_15_8.c" />

Then, add 

        </folder> <folder Name="control_pwm"> 
        <file file_name="$(DW3000APIDir)/Src/examples/control_pwm/control_pwm.c" />

and save.

* How to solve the error in running dw 3000_api.emProject by combining the control_pwm file and the UWB file. (Since there are many errors, I will only show a few examples. You can solve the remaining errors based on the methods in the examples below.)

ex) No such file or directory bsp.h

To define the location of bsp.h file in dw3000_api.emProject
Run dw3000_api.emProject in <<DW3000 API Root Directory>/API/Build_Platforms/nRF52840-DK> again with visual studio.

Find the location of the bsp.h file in the nordic sdk files.
Execute dw3000_api.emProject with visual studio, find c_user_include_directories and check if $(NordicSDKDir)/components/libraries/bsp is present in configuration.
If not, add $(NordicSDKDir)/components/libraries/bsp; and save it, and the error about bsp will disappear.

ex) No such file or directory app_button.h

To define the location of app_button.h file in dw3000_api.emProject
Run dw3000_api.emProject in <<DW3000 API Root Directory>/API/Build_Platforms/nRF52840-DK> again with visual studio.

Find the location of the app_button.h file in the nordic sdk files.
Execute dw3000_api.emProject with visual studio, find c_user_include_directories and check if $(NordicSDKDir)/components/libraries/button exists in configuration.
If there is no app_button error, add $(NordicSDKDir)/components/libraries/button; and save.

ex) 'NRFX_TIMER1_INST_IDX' undeclared here (not in a function); did you mean 'NRFX_TIMER0_INST_IDX'? 

Indicates that timer 1 is not activated. Therefore, timer 1 must be activated.
You need to declare the sdk_config.h file that activates it in <dw 3000_api.emProject>.

Execute dw3000_api.emProject with visual studio.

        </file>
              <file file_name="SEGGER/SEGGER_RTT_Syscalls_SES.c">
                <configuration Name="Debug" build_exclude_from_build="Yes" />
                
Under

        </file>
             <file file_name="<sdk_config.h Directory Path>/sdk_config.h" />

it adds

After adding, open sdk_config.h file and change NRFX_TIMER1_ENABLED = 1
TIMER1_ENABLED is also changed to 1.

ex) undefined reference to `app_pwm_enable'

Means that app_pwm is not active.
Enabled by changing NRFX_PWM_ENABLED = 1, NRFX_PWM1_ENABLED = 1, APP_PWM_ENABLED = 1, PWM_ENABLED = 1 in sdk_config.h file.







  
