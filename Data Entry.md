## Automating Laboratory Data Entry

**Project Description:** In a previous laboratory role, I was required to perform a very repetitive data entry task for 96 - 288 patients. The laboratory management system (LIMS) had no way to automatically change the details of all these patients in one step, so I had to individually change them. Performing the data entry could take anywhere from 30 - 60 minutes, which was an unacceptable and inefficient use of time. I researched a method to speed up this process and settled on using the PyAutoGUI library.    

### 1. Planning Stage

The data entry task involved going into every patient on the LIMS, changing the test status to complete, and entering the run number. I began my project by going through the stages and working out how I could perform each step within the limitations of PyAutoGUI. I decided the easiest way would be to create an Excel template with two fields. The first field was the patient identifier, and the second was the run number. I then went through the process manually and made a note of every input.

### 2. Converting my Stages into a Python Script

I needed to convert my notes into a format that could work in Python. The script needed to first select the patient identifier from the template and search for this in the LIMS. Once within the patient file, the stage would be moved to complete on a drop-down menu, and the script would go back to the Excel document to acquire the run number. I used pyautogui.position() to produce the positions for mouse clicks, pyautogui.press to press keys, pyautogui.click to click the mouse, and pyautogui.hotkey for key combinations. I used a picture of a button and the pyautogui.moveTo command to find it on the screen.  The command pyautogui.getWindowsWithTitle was used to switch between the LIMS and my template. I also imported the time library, so I could add a 0.5 second gap between commands to ensure the script didn't run faster than the programs would allow.    

### 3. Adding Features

I wanted to add a safety feature, in case the script needed to be switched off suddenly. Luckily, pyautogui has one built in. Adding the line pyautogui.FAILSAFE = True means the script can be switched off by simply moving the mouse to the top left corner of the screen. I used a for loop to repeat the script for 93 patients, as they are performed in batches of this size. I added a user input stage at the end that required the user to state if they wanted to continue after 93 loops to ensure it didn't run indefinitely.   

### 4. The Code 

The code I produced can be seen below and has saved many hours of work for me and my colleagues.

```
import pyautogui
import time

# Enable failsafe, move mouse to top left corner
pyautogui.FAILSAFE = True

for _ in range(93):
    
    pyautogui.getWindowsWithTitle("TCRPbot Template - Excel")[0].activate()
    time.sleep(0.5)
    
    pyautogui.hotkey('ctrl', 'c')
    time.sleep(0.5)
    
    pyautogui.getWindowsWithTitle("LIMS")[0].activate()
    time.sleep(0.5)
        
    #Search section
    pyautogui.click(137, 213)
    time.sleep(0.5)
        
    pyautogui.hotkey('ctrl', 'v')
    time.sleep(0.5)
        
    pyautogui.press('enter')
    time.sleep(0.5)
        
    #Cancer panel
    button_location = pyautogui.locateCenterOnScreen('S:/XXXX/XXX/XXX/XXXX/XXX/Button.png')
    pyautogui.moveTo(button_location)
    pyautogui.click()
    time.sleep(0.5)
        
    pyautogui.press('f2')
    time.sleep(0.5)
        
    #Drop down menu
    pyautogui.click(660, 401)
    time.sleep(0.5)
        
   
    pyautogui.click(501, 437)
    time.sleep(0.5)
        
    #Back to samples
    pyautogui.getWindowsWithTitle("TCRPbot Template - Excel")[0].activate()
    time.sleep(0.5)
        
    pyautogui.press('right')
    time.sleep(0.5)
        
    pyautogui.hotkey('ctrl', 'c')
    time.sleep(0.5)
        
    #Back to LIMS, run box
    pyautogui.getWindowsWithTitle("LIMS")[0].activate()
    time.sleep(1)
    
    pyautogui.click(811, 411)
    time.sleep(1)

    pyautogui.press('right')
    time.sleep(0.5)
        
    pyautogui.hotkey('ctrl', 'v')
    time.sleep(0.5)
        
    pyautogui.press('f4')
        
    #Window Switch
    pyautogui.getWindowsWithTitle("TCRPbot Template - Excel")[0].activate()
    time.sleep(0.5)
        
    pyautogui.press('left')
    time.sleep(0.5)
    pyautogui.press('down')
    time.sleep(0.5)

#Does the user want to continue after 93 loops?
user_input = input("Do you want to continue? (yes/no): ")
if user_input.lower() != "yes":
    print("Exiting the program.")
```
