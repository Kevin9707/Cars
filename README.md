#This code is a simple project that uses OpenCV to detect vehicles on the road. The code includes the ability to read frames from a given video file, detect vehicles, and count the number of vehicles detected.

Here are the main features of the project

Open the given movie file (traffic.mp4) with the cap variable, and set the frame rate.
To detect vehicles, calculate the difference between frames, binarize them to detect boundaries.
Find a valid boundary that represents a vehicle, and mark that boundary with a rectangle.
Find the center point of the vehicle and use it to track the vehicle's movement.
Detect if a vehicle passes over a given line height, and count the number of vehicles that have passed.
Display the result in a frame and output the number of vehicles detected.
The code uses comments to explain the role of each step and variable. We're using OpenCV functions and classes for image processing and computer vision, and we're outputting the movement of vehicles and the number of detected vehicles to the console as the program runs.

To run the project, you need the traffic.mp4 file and the OpenCV library. Add the movie file to your project folder, run the code, and you should see the vehicle detection results.This code is a simple project that uses OpenCV to detect vehicles on the road. The code includes the ability to read frames from a given video file, detect vehicles, and count the number of vehicles detected.

Here are the main features of the project

Open the given movie file (traffic.mp4) with the cap variable, and set the frame rate.
To detect vehicles, calculate the difference between frames, binarize them to detect boundaries.
Find a valid boundary that represents a vehicle, and mark that boundary with a rectangle.
Find the center point of the vehicle and use it to track the vehicle's movement.
Detect if a vehicle passes over a given line height, and count the number of vehicles that have passed.
Display the result in a frame and output the number of vehicles detected.
The code uses comments to explain the role of each step and variable. We're using OpenCV functions and classes for image processing and computer vision, and we're outputting the movement of vehicles and the number of detected vehicles to the console as the program runs.

To run the project, you need the traffic.mp4 file and the OpenCV library. Add the movie file to your project folder, run the code, and you should see the vehicle detection results.

import cv2
import numpy as np

cap = cv2.VideoCapture('traffic.mp4')

fps = cap.set(cv2.CAP_PROP_FPS,1)

min_contour_width=40

min_contour_height=40
offset=10
line_height=550
matches =[]
cars=0

def get_centroid(x, y, w, h):

    x1 = int(w / 2)
    y1 = int(h / 2)

    cx = x + x1
    cy = y + y1
    return cx,cy
    return [cx, cy]


cap.set(3,1920)
cap.set(4,1080)

if cap.isOpened():
    ret,frame1 = cap.read()
else:
    ret = False
ret,frame1 = cap.read()
ret,frame2 = cap.read()

while ret:
    d = cv2.absdiff(frame1,frame2)
    grey = cv2.cvtColor(d,cv2.COLOR_BGR2GRAY)

    blur = cv2.GaussianBlur(grey,(5,5),0)

    ret , th = cv2.threshold(blur,20,255,cv2.THRESH_BINARY)
    dilated = cv2.dilate(th,np.ones((3,3)))
    kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (2, 2))

    closing = cv2.morphologyEx(dilated, cv2.MORPH_CLOSE, kernel)
    contours,h = cv2.findContours(closing,cv2.RETR_TREE,cv2.CHAIN_APPROX_SIMPLE)
    for(i,c) in enumerate(contours):
        (x,y,w,h) = cv2.boundingRect(c)
        contour_valid = (w >= min_contour_width) and (
                h >= min_contour_height)

        if not contour_valid:
            continue
        cv2.rectangle(frame1,(x-10,y-10),(x+w+10,y+h+10),(255,0,0),2)

        cv2.line(frame1, (0, line_height), (1200, line_height), (0,255,0), 2)
        centroid = get_centroid(x, y, w, h)
        matches.append(centroid)
        cv2.circle(frame1,centroid, 5, (0,255,0), -1)
        cx,cy= get_centroid(x, y, w, h)
        for (x,y) in matches:
            if (line_height + offset) > y > (line_height - offset):
                cars=cars+1
                matches.remove((x,y))
                print(cars)

    cv2.putText(frame1, "Total Vehicles Detected: " + str(cars), (10, 90), cv2.FONT_HERSHEY_SIMPLEX, 1,
                (0, 170, 0), 2)


    cv2.imshow("OUTPUT" , frame1)
    if cv2.waitKey(1) == 27:
        break
    frame1 = frame2
    ret , frame2 = cap.read()

cv2.destroyAllWindows()
cap.release()



https://github.com/Kevin9707/Cars/assets/113577031/0ec93554-2da6-4730-9e8c-7f2bbc68b754
