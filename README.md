# midicontrol
A small library that allows for very simple receiving of midi controls in interactive python scripts.

## Usage
```
import cv2
import numpy as np
import midicontrol

midicontrol.set_logging(True)

class MyImageSource(midicontrol.MidiControlManager):
    def __init__(self):
        super().__init__()
        # define some variables that will receive control changes
        self.red   = midicontrol.MidiControl(self, "red", 80, value=127, min=0, max=255)
        self.green = midicontrol.MidiControl(self, "green", 81, value=127, min=0, max=255)
        self.blue  = midicontrol.MidiControl(self, "blue", 82, value=127, min=0, max=255)

    def process(self):
        h,w = 480,640
        img = np.zeros((h,w,3), np.uint8)

        # access variable values:
        r,g,b = self.red.value, self.green.value, self.blue.value
        
        img[:,:,:] = (b,g,r)
        text_color = np.array((255 - b, 255 - g, 255 -r), np.uint8) / 2
        cv2.putText(img, "R = %03d  G = %03d  B = %03d"%(r,g,b), (70,h//2), cv2.FONT_HERSHEY_PLAIN, 2.0, text_color)
        return img
        
        
source = MyImageSource()

while True:
    # Poll for midi control changes:
    source.poll()

    # Do some processing:
    img = source.process()

    # Display the image:
    cv2.imshow("RGB Fun!", img)
    cv2.waitKey(10)
        
        ```
