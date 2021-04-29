# stockalerter
This is a stock alerter, supporting the asus tuf 3080 and soon the 3080 founders.
Note it will scream in your ear!
```py
import urllib.request
from bs4 import BeautifulSoup
from ctypes import cast, POINTER
from comtypes import CLSCTX_ALL
from pycaw.pycaw import AudioUtilities, IAudioEndpointVolume
import time
import os
import win32api

os.add_dll_directory(r'C:\Users\eddie\vcpkg\vcpkg.exe')

import  vlc

buy_url = {'newegg': "https://www.newegg.com/asus-geforce-rtx-3080-tuf-rtx3080-10g-gaming/p/N82E16814126453?Description=asus%20tuf%203080&cm_re=asus_tuf%203080-_-14-126-453-_-Product&quicklink=true", 'amazon': 'amazon.com'}

def newegg_check_stock():
    content = urllib.request.urlopen('https://www.newegg.com/asus-geforce-rtx-3080-tuf-rtx3080-10g-gaming/p/N82E16814126453?Description=asus%20tuf%203080&cm_re=asus_tuf%203080-_-14-126-453-_-Product&quicklink=true')
    read_content = content.read()
    soup = BeautifulSoup(read_content,'html.parser')
    WebPage = soup.find(class_="fa-exclamation-triangle")
    if "OUT OF STOCfK" in str(WebPage):
        return False
    return True
   
  """  
def amazon_check_stock():
    content = urllib.request.urlopen('https://www.amazon.com/ASUS-Graphics-DisplayPort-Military-Grade-Certification/dp/B08HH5WF97?tag=52421_iceleadscom-20&ascsubtag=s16196345763010lvma52421')
    read_content = content.read()
    soup = BeautifulSoup(read_content,'html.parser')
    WebPage = soup.find(class_="a-size-medium a-color-price")
    if "Currently unavailable." in str(WebPage):
        return False
    return True
    """
    
def check_activity():
    while True:
        idle_time = (win32api.GetTickCount() - win32api.GetLastInputInfo()) / 1000.0
        is_in_stock()
        if idle_time < 60:
            time.sleep(20)
        time.sleep(10)


def check_stock():
    in_stock = False
    current_stock = []
    stock = {'newegg': False, 'amazon': False}
    current_stock = []
    stock['newegg'] = newegg_check_stock()
    #stock['amazon'] = amazon_check_stock()
    #kinda broken rn
    my_data = [current_stock]
    for company_stock in stock:
        if stock[company_stock]:
            in_stock = True
            my_data.append(True)
            current_stock.append(company_stock)
    if in_stock:
        return my_data
    return my_data

def is_in_stock():  
    data = check_stock()  
    in_stock = data[1]
    current_stock = data[0]
    devices = AudioUtilities.GetSpeakers()
        
    interface = devices.Activate(IAudioEndpointVolume._iid_, CLSCTX_ALL, None)
        
    volume = cast(interface, POINTER(IAudioEndpointVolume))
        
    volume.SetMasterVolumeLevel(-0.0, None)
    while in_stock:
        stock_check = check_stock()
        if not stock_check:
            break
        for available_stock in current_stock:
            print(buy_url[available_stock])
        player = vlc.MediaPlayer("stock_alert.mp3")
        player.play()
        time.sleep(21)
        player.stop()
            
check_activity()
```


Made by Eddie, please be nice!
