## Automating Laboratory Data Entry

**Project Description:** In a previous laboratory role, I was required to perform a very repetitive data entry task for 96 - 288 patients. The laboratory management system had no way to automatically change the details of all these patients in one step, so I had to individually change them. Performing the data entry could take anywhere from 30 - 60 minutes, which was an unacceptable and inefficient use of time. I decided to research a method to speed up this process and settled on using the PyAutoGUI library.    

### 1. Suggest hypotheses about the causes of observed phenomena

import pyautogui
import time

# Enable failsafe, move mouse to top left corner
pyautogui.FAILSAFE = True

for _ in range(93):
    
    pyautogui.getWindowsWithTitle("TCRPbot Template - Excel")[0].activate()
    time.sleep(0.5)
    
    pyautogui.hotkey('ctrl', 'c')
    time.sleep(0.5)
    
    pyautogui.getWindowsWithTitle("      Evolution vLab")[0].activate()
    time.sleep(0.5)
        
    #Search section
    pyautogui.click(137, 213)
    time.sleep(0.5)
        
    pyautogui.hotkey('ctrl', 'v')
    time.sleep(0.5)
        
    pyautogui.press('enter')
    time.sleep(0.5)
        
    #Cancer panel
    button_location = pyautogui.locateCenterOnScreen('S:/Pathology/MOLPATH_DATA/.ASSAYS/TWIST_TCRP/Automation/Button.png')
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
    pyautogui.getWindowsWithTitle("      Evolution vLab")[0].activate()
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

# does the user want to continue after 93 loops?
user_input = input("Do you want to continue? (yes/no): ")
if user_input.lower() != "yes":
    print("Exiting the program.")













<img src="images/dummy_thumbnail.jpg?raw=true"/>

### 4. Provide a basis for further data collection through surveys or experiments

Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo. 

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).
